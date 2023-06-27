<template>
  <el-container>
    <host-sidebar></host-sidebar>
    <el-main>
      <el-breadcrumb separator-class="el-icon-arrow-right" style="margin-bottom:20px">
        <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item :to="{ path: '/host' }">任务节点</el-breadcrumb-item>
        <el-breadcrumb-item>节点列表</el-breadcrumb-item>
      </el-breadcrumb>
      <el-form :inline="true" >
        <el-row>
          <el-form-item label="">
            <el-input placeholder="请输入节点ID" v-model.trim="searchParams.id"></el-input>
          </el-form-item>
          <el-form-item label="">
            <el-input placeholder="请输入主机名" v-model.trim="searchParams.name"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="search()">搜索</el-button>
          </el-form-item>
        </el-row>
      </el-form>

      <el-row type="flex" justify="end">
        <el-button type="primary" icon="el-icon-edit" @click="toEdit(null)" v-if="this.$store.getters.user.isAdmin">新增</el-button>
        <el-button type="info" icon="el-icon-refresh" @click="refresh">刷新</el-button>
      </el-row>

      <el-table
        :data="hosts"
        tooltip-effect="dark"
        border
        style="width: 100%; margin: 20px 0;">
        <el-table-column
          prop="id"
          label="节点ID"
          width="100">
        </el-table-column>
        <el-table-column
          prop="alias"
          label="节点名">
        </el-table-column>
        <el-table-column
          prop="name"
          label="主机名">
        </el-table-column>
        <el-table-column
          prop="port"
          label="端口">
        </el-table-column>
        <el-table-column
          prop="remark"
          label="备注">
        </el-table-column>
        <el-table-column
          align="center"
          header-align="left"
          label="操作"
          width="180"
          v-if="this.isAdmin">
          <template slot-scope="scope">
            <el-row>
              <el-button size="small" type="primary" @click="toEdit(scope.row)">编辑</el-button>
              <el-button size="small" type="success" @click="toTasks(scope.row)">查看任务</el-button>
            </el-row>
            <br>
            <el-row>
              <el-button size="small" type="danger" @click="remove(scope.row)">删除</el-button>
              <el-button size="small" type="info" @click="ping(scope.row)">测试连接</el-button>
            </el-row>
          </template>
        </el-table-column>
      </el-table>

      <el-row type="flex" justify="end">
        <el-pagination
          background
          layout="prev, pager, next, sizes, total"
          :total="hostTotal"
          :page-size="20"
          @size-change="changePageSize"
          @current-change="changePage"
          @prev-click="changePage"
          @next-click="changePage">
        </el-pagination>
      </el-row>
    </el-main>
  </el-container>
</template>

<script>
import hostSidebar from './sidebar'
import hostService from '../../api/host'
export default {
  name: 'host-list',
  data () {
    return {
      hosts: [],
      hostTotal: 0,
      searchParams: {
        page_size: 20,
        page: 1,
        id: '',
        name: '',
        alias: ''
      },
      isAdmin: this.$store.getters.user.isAdmin
    }
  },
  components: {hostSidebar},
  created () {
    this.search()
  },
  methods: {
    changePage (page) {
      this.searchParams.page = page
      this.search()
    },
    changePageSize (pageSize) {
      this.searchParams.page_size = pageSize
      this.search()
    },
    search (callback = null) {
      hostService.list(this.searchParams, (data) => {
        this.hosts = data.data
        this.hostTotal = data.total
        if (callback) {
          callback()
        }
      })
    },
    remove (item) {
      this.$appConfirm(() => {
        hostService.remove(item.id, () => this.refresh())
      })
    },
    ping (item) {
      hostService.ping(item.id, () => {
        this.$message.success('连接成功')
      })
    },
    toEdit (item) {
      let path = ''
      if (item === null) {
        path = '/host/create'
      } else {
        path = `/host/edit/${item.id}`
      }
      this.$router.push(path)
    },
    refresh () {
      this.search(() => {
        this.$message.success('刷新成功')
      })
    },
    toTasks (item) {
      this.$router.push(
        {
          path: '/task',
          query: {
            host_id: item.id
          }
        })
    }
  }
}
</script>
