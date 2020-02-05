<template>
  <div class="dashboard-container">
    <!-- <div class="app-container">基础题库管理</div> -->
    <el-card class="box-card">
      <el-row class="row">
        <el-col>
          <el-button type="info" plain>新增试题</el-button>
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
          <el-select v-model="searchForm.province" @change="getCitysList">
            <el-option v-for="(val,index) in provincesList" :key="index" :value="val" :label="val"></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          区
          <el-select v-model="searchForm.city">
            <el-option v-for="(val,index) in citysList" :key="index" :value="index" :label="val"></el-option>
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
          <el-input v-model="searchForm.catalogID" placeholder="请输入" style="width:210px"></el-input>
        </el-col>
      </el-row>
      <el-row class="anniu row">
        <el-col>
          <el-button @click="deleteItem()">清除</el-button>
          <el-button type="primary">搜索</el-button>
        </el-col>
      </el-row>
      <el-table :data="baseList" border style="width: 100%">
        <el-table-column prop="id" label="序号"></el-table-column>
        <el-table-column prop="number" label="试题编号" width="120"></el-table-column>
        <el-table-column prop="subjectID" label="学科" width="120"></el-table-column>
        <el-table-column prop="questionType" label="题型" width="120"></el-table-column>
        <el-table-column prop="question" label="题干" width="120"></el-table-column>
        <el-table-column prop="addDate" label="录入时间" width="120"></el-table-column>
        <el-table-column prop="difficulty" label="难度" width="120"></el-table-column>
        <el-table-column prop="number" label="使用次数" width="120"></el-table-column>
        <el-table-column prop="creator" label="录入人" width="120"></el-table-column>
        <el-table-column label="操作" width="200">
          <!-- <template slot-scope="scope"> -->
          <el-button @click="handleClick(scope.row)" type="text" size="small">预览</el-button>
          <el-button type="text" size="small">修改</el-button>
          <el-button type="text" size="small">删除</el-button>
          <el-button type="text" size="small">加入精选</el-button>
          <!-- </template> -->
        </el-table-column>
      </el-table>
      <div class="block">
        <span class="demonstration"></span>
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage4"
          :page-sizes="[10, 20, 30, 40]"
          :page-size="10"
          layout="total, sizes, prev, pager, next, jumper"
          :total="400"
        ></el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
import Vue from 'vue'
import { simple } from '@/api/hmmm/subjects.js'
import { simple as tagsimple } from '@/api/hmmm/tags.js'
import { simple as usersimple } from '@/api/base/users.js'
import { provinces, citys } from '@/api/hmmm/citys.js'
import { list } from '@/api/hmmm/questions.js'

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
      provincesList: [],
      citysList: [],
      usersList: [],
      baseList: [],
      searchForm: {
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
  created() {
    this.getSubjectIDList()
    this.getTagsList()
    this.getProvincesList()
    // this.getCitysList()
    this.getUsersList()
    this.getList()
  },
  methods: {
    deleteItem() {
      for (let k of Object.keys(this.searchForm)) {
        console.log(Object.keys(this.searchForm))
        Vue.delete(this.searchForm, k)
      }
    },
    async getList() {
      let result = await list()
      // console.log(result)
      this.baseList = result.data.items
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
    async getProvincesList() {
      let result = await provinces()
      // console.log(result)
      this.provincesList = result
    },
    async getCitysList(province) {
      let result = await citys(province)
      // console.log(province)
      // console.log(result)
      this.citysList = result
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
.row{
  margin-bottom: 20px;
}
.anniu {
  margin-left: 1070px;
}
.block{
  margin-left: 700px;
  margin-top: 30px;
}
</style>
