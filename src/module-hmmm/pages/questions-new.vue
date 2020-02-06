<template>
  <div class="dashboard-container">
    <el-card class="box-card">
      <el-form ref="addFormRef" :model="addForm" label-width="200px">
        <el-form-item label="学科：">
          <el-select v-model="addForm.subjectID" placeholder="请选择">
            <el-option
              v-for="item in subjectIDList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="目录：">
          <el-select v-model="addForm.catalogID" placeholder="请选择">
            <el-option
              v-for="item in directoryList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="企业：">
          <el-select v-model="addForm.enterpriseID" placeholder="请选择">
            <el-option
              v-for="item in companyList"
              :key="item.id"
              :label="item.shortName"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="城市：">
          <el-select
            v-model="addForm.province"
            placeholder="请选择"
            @change="getCitysList(addForm.province)"
          >
            <el-option v-for="item in provinces()" :key="item" :value="item" :label="item"></el-option>
          </el-select>
          <el-select v-model="addForm.city" placeholder="请选择">
            <el-option v-for="item in citysList" :key="item" :value="item" :label="item"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="方向：">
          <el-select v-model="addForm.direction" placeholder="请选择">
            <el-option
              v-for="item in directionList"
              :key="item"
              :value="item"
              :label="item"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="题型：">
          <el-radio-group v-model="addForm.questionType">
            <el-radio
              v-for="item in questionTypeList"
              :key="item.value"
              :label="item.value+''"
            >{{item.label}}</el-radio>
          </el-radio-group>
          </el-form-item>
          <el-form-item label="难度：">
          <el-radio-group v-model="addForm.difficulty">
            <el-radio
              v-for="item in difficultyList"
              :key="item.value"
              :label="item.value+''"
            >{{item.label}}</el-radio>
          </el-radio-group>
        
        </el-form-item>
        <el-form-item label="题干：">
          <el-input type="textarea" v-model="addForm.question"></el-input>
        </el-form-item>
        <el-form-item label="选项：">
          <el-radio v-model="singleSelect" :label="0">
            A:
            <el-input type="text" v-model="addForm.options[0].title"></el-input>
          </el-radio>
          <br />
          <el-radio v-model="singleSelect" :label="1">
            B:
            <el-input type="text" v-model="addForm.options[1].title"></el-input>
          </el-radio>
          <br />
          <el-radio v-model="singleSelect" :label="2">
            C:
            <el-input type="text" v-model="addForm.options[2].title"></el-input>
          </el-radio>
          <br />
          <el-radio v-model="singleSelect" :label="3">
            D:
            <el-input type="text" v-model="addForm.options[3].title"></el-input>
          </el-radio>
        </el-form-item>
        <el-form-item label="答案：">
          <el-input type="textarea" v-model="addForm.answer"></el-input>
        </el-form-item>
        <el-form-item label="备注：">
          <el-input type="textarea" v-model="addForm.remarks"></el-input>
        </el-form-item>
        <el-form-item label="标签：">
          <el-input type="text" v-model="addForm.tags"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="tianjia()">提交</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
import { simple } from '@/api/hmmm/subjects.js'
import { simple as directorysimple } from '@/api/hmmm/directorys.js'
import { list } from '@/api/hmmm/companys.js'
import { provinces, citys } from '@/api/hmmm/citys.js'
import { add } from '@/api/hmmm/questions.js'

import {
  // 设置别名,方便后边使用
  difficulty as difficultyList,
  questionType as questionTypeList,
  direction as directionList
} from '@/api/hmmm/constants.js'
export default {
  name: 'QuestionsNew',
  data() {
    return {
      directionList,
      questionTypeList,
      difficultyList,
      subjectIDList: [],
      directoryList: [],
      companyList: [],
      citysList: [],
      singleSelect: '',
      addForm: {
        options: [
          // {code: '编号ABCD', title: '当前项目文字答案',
          //   img: '当前项目图片答案', isRight: boolean表明当前项目是否是答案},
          { code: 'A', title: '', img: '', isRight: false },
          { code: 'B', title: '', img: '', isRight: false },
          { code: 'C', title: '', img: '', isRight: false },
          { code: 'D', title: '', img: '', isRight: false }
        ],
        subjectID: '',
        catalogID: '',
        enterpriseID: '',
        province: '',
        city: '',
        direction: '',
        questionType: '1',
        difficulty: '1',
        question: '', // 题干
        answer: '', // 答案
        remarks: '', // 备注
        tags: '', // 标签
        videoURL: 'http://www.xxx.com' // 解析视频
      }
    }
  },
  watch: {
    singleSelect(newV, oldV) {
      for (var i = 0; i < 4; i++) {
        this.addForm.options[i].isRight = false
      }
      this.addForm.options[newV].isRight = true
    }
  },
  created() {
    this.getSubjectIDList()
    this.getDirectoryList()
    this.getCompanyList()
  },
  methods: {
    provinces,
    getCitysList(province) {
      // let result = await citys(province)
      // console.log(province)
      // console.log(result)
      this.addForm.city = ''
      this.citysList = citys(province)
    },
    async tianjia() {
      let result = await add(this.addForm)
      console.log(result)
      this.$message.success('添加试题成功')
      this.$router.push('/questions/list')
      // this.subjectIDList = result.data
    },
    async getSubjectIDList() {
      let result = await simple()
      // console.log(result)
      this.subjectIDList = result.data
    },
    async getDirectoryList() {
      let result = await directorysimple()
      // console.log(result)
      this.directoryList = result.data
    },
    async getCompanyList() {
      let result = await list()
      console.log(result)
      this.companyList = result.data.items
    }
  }
}
</script>

<style scoped>
</style>
