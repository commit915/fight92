<template>
  <div class="dashboard-container">
    <!-- <div class="app-container">基础题库管理</div> -->
    <el-card class="box-card">
      <el-row class="row">
        <el-col>
          <el-button type="info" plain @click="$router.push('/questions/new')">新增试题</el-button>
          <el-button type="info" plain>批量导入</el-button>
        </el-col>
      </el-row>
      <el-row :gutter="20" class="row">
        <el-col :span="6">
          学科
          <el-select v-model="searchForm.subjectID">
            <el-option
              v-for="item in subjectIDList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          难度
          <el-select v-model="searchForm.difficulty">
            <el-option
              v-for="item in difficultyList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          试题类型
          <el-select v-model="searchForm.questionType">
            <el-option
              v-for="item in questionTypeList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          标签
          <el-select v-model="searchForm.tags">
            <el-option
              v-for="item in tagsList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-col>
      </el-row>
      <el-row :gutter="20" class="row">
        <el-col :span="6">
          城市
          <el-select v-model="searchForm.province" @change="getCitysList(searchForm.province)">
            <!-- <el-option v-for="(val,index) in provinces()" :key="index" :value="val" :label="val"></el-option> -->
            <el-option v-for="item in provinces()" :key="item" :value="item" :label="item"></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          区
          <el-select v-model="searchForm.city">
            <el-option v-for="item in citysList" :key="item" :value="item" :label="item"></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          关键字
          <el-input v-model="searchForm.keyword" placeholder="请输入题目编号/题干" style="width:210px"></el-input>
        </el-col>
        <el-col :span="6">
          题目备注
          <el-input v-model="searchForm.tags" placeholder="请输入" style="width:210px"></el-input>
        </el-col>
      </el-row>
      <el-row :gutter="20" class="row">
        <el-col :span="6">
          企业简称
          <el-input v-model="searchForm.shortName" placeholder="请输入" style="width:210px"></el-input>
        </el-col>
        <el-col :span="6">
          方向
          <el-select v-model="searchForm.direction">
            <el-option
              v-for="(val,index) in directionList"
              :key="index"
              :value="index"
              :label="val"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          录入人
          <el-select v-model="searchForm.creatorID">
            <el-option
              v-for="item in usersList"
              :key="item.id"
              :label="item.username"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          二级目录
          <el-select v-model="searchForm.catalogID">
            <el-option
              v-for="item in directoryList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-col>
      </el-row>
      <el-row class="anniu row">
        <el-col>
          <el-button @click="deleteItem()">清除</el-button>
          <el-button type="primary" @click="getQuestionList()">搜索</el-button>
        </el-col>
      </el-row>
      <el-table :data="baseList" border style="width: 100%">
        <el-table-column type="index" label="序号"></el-table-column>
        <el-table-column prop="number" label="试题编号" width="120"></el-table-column>
        <el-table-column prop="subject" label="学科" width="120"></el-table-column>
        <el-table-column :formatter="questionTypeFMT" prop="questionType" label="题型" width="120"></el-table-column>
        <el-table-column prop="question" label="题干" width="120"></el-table-column>
        <el-table-column label="录入时间" width="120">
          <span slot-scope="stData">{{stData.row.addDate | parseTimeByString}}</span>
        </el-table-column>
        <el-table-column :formatter="difficultyFMT" prop="difficulty" label="难度" width="120"></el-table-column>
        <el-table-column prop="creator" label="录入人" width="120"></el-table-column>
        <el-table-column label="操作" width="200">
          <!-- 在外边加个template,一次性给所有a标签起作用，简化 -->
          <!-- el-table-column为子组件，利用作用域插槽slot-scope="stData" -->
          <template slot-scope="stData">
            <!-- <el-button type="text" size="small">预览</el-button>
            <el-button type="text" size="small">修改</el-button>
            <el-button type="text" size="small" @click="del(stData.row)">删除</el-button>
            <el-button type="text" size="small">加入精选</el-button> -->
            <!-- 这里如果用a标签，因为a标签会自动跳转，导致还未执行click事件就已经跳转，用修饰符.prevent阻止其默认行为 -->
            <a href="#">预览</a>
            <a href="#">修改</a>
            <a href="#"  @click.prevent="del(stData.row)">删除</a>
            <a href="#">加入精选</a>
          </template>
        </el-table-column>
      </el-table>
      <!-- <div class="block">
        <span class="demonstration"></span>
        <el-pagination
          :current-page="searchForm.page"
          :page-sizes="[10, 20, 30, 40]"
          :page-size="searchForm.pagesize"
          total="pageList.counts"
        ></el-pagination>
      </div>-->
    </el-card>
  </div>
</template>

<script>
import Vue from 'vue'

import { simple } from '@/api/hmmm/subjects.js'
import { simple as tagsimple } from '@/api/hmmm/tags.js'
import { simple as usersimple } from '@/api/base/users.js'
import { simple as directorysimple } from '@/api/hmmm/directorys.js'
import { provinces, citys } from '@/api/hmmm/citys.js'
import { list, remove } from '@/api/hmmm/questions.js'

import {
  // 设置别名,方便后边使用
  difficulty as difficultyList,
  questionType as questionTypeList,
  direction as directionList
} from '@/api/hmmm/constants.js'
export default {
  name: 'QuestionsList',
  data() {
    return {
      // 简易成员赋值,相当于difficultyList:difficultyList
      difficultyList,
      questionTypeList,
      directionList,
      subjectIDList: [],
      tagsList: [],
      // provincesList: [],
      citysList: [],
      usersList: [],
      baseList: [],
      directoryList: [],
      pageList: [],
      searchForm: {
        page: 1,
        pages: 1,
        pagesize: 10,
        count: 8,
        // addData: '',
        subjectID: '',
        difficulty: '',
        questionType: '',
        tags: '',
        province: '',
        city: '',
        keyword: '',
        remarks: '',
        shortName: '',
        direction: '',
        creatorID: '',
        catalogID: ''
      }
    }
  },
  watch: {
    searchForm: {
      handler: function(newV, oldV) {
        this.getList()
      },
      deep: true
    }
  },
  created() {
    this.getSubjectIDList()
    this.getTagsList()
    // this.getProvincesList()
    this.getCitysList()
    this.getUsersList()
    this.getList()
    this.getDirectoryList()
  },
  // watch: {
  //   'searchForm.province': function() {
  //     this.searchForm.city = ''
  //     this.citysList = citys()
  //   }
  // },
  methods: {
      del(question) {
       this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(async() => {
          // 加async,await為了保證先刪除成功之後,才刷新頁面
          let result = await remove(question)
          console.log(result)
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
          // 刷新頁面
          this.getQuestionList()

        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })          
        })
     
    },
    async getQuestionList() {
      let result = await list(this.searchForm)
      // console.log(result)
      this.baseList = result.data.items
    },
     difficultyFMT(row, column, cellValue, index) {
      // return cellValue
      // console.log(index)
      return this.difficultyList[cellValue - 1].label
    },
    questionTypeFMT(row, column, cellValue, index) {
      // return cellValue
      // console.log(cellValue)
      return this.questionTypeList[cellValue - 1].label
    },
    // 可以在methods中直接声明citys.js中的provinces()方法
    provinces,
     // async getProvincesList() {
    //   let result = await provinces()
    //   // console.log(result)
    //   this.provincesList = result
    // },
    getCitysList(province) {
      // let result = await citys(province)
      // console.log(province)
      // console.log(result)
      this.searchForm.city = ''
      this.citysList = citys(province)
    },
    async getDirectoryList() {
      let result = await directorysimple()
      // console.log(result)
      this.directoryList = result.data
    },
    deleteItem() {
      for (let k of Object.keys(this.searchForm)) {
        // console.log(Object.keys(this.searchForm))
        Vue.delete(this.searchForm, k)
      }
    },
    async getList() {
      let result = await list()
      console.log(result)
      this.baseList = result.data.items
      this.pageList = result.data
    },
    async getSubjectIDList() {
      let result = await simple()
      // console.log(result)
      this.subjectIDList = result.data
    },
    async getTagsList() {
      let result = await tagsimple()
      // console.log(result)
      this.tagsList = result.data
    },
    async getUsersList() {
      let result = await usersimple()
      // console.log(result)
      this.usersList = result.data
    }
  }
}
</script>

<style scoped>
.row {
  margin-bottom: 20px;
}
.anniu {
  margin-left: 1070px;
}
.block {
  margin-left: 700px;
  margin-top: 30px;
}
</style>
