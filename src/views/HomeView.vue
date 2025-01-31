<template>
  <div class="resume-generator flex">
    <!-- 左侧输入区 -->
    <div class="input-section w-1/2 p-6 bg-gray-100 overflow-y-auto">
      <el-form :model="resumeForm" label-width="100px">
        <!-- 个人信息 -->
        <el-card class="mb-4">
          <template #header>
            <div class="flex justify-between items-center">
              <span class="font-bold">个人信息</span>
            </div>
          </template>

          <el-form-item label="姓名">
            <el-input v-model="resumeForm.name" placeholder="请输入姓名"></el-input>
          </el-form-item>

          <el-form-item label="联系电话">
            <el-input v-model="resumeForm.phone" placeholder="请输入联系电话"></el-input>
          </el-form-item>

          <el-form-item label="邮箱">
            <el-input v-model="resumeForm.email" placeholder="请输入邮箱"></el-input>
          </el-form-item>
        </el-card>

        <!-- 教育经历 -->
        <el-card class="mb-4">
          <template #header>
            <div class="flex justify-between items-center">
              <span class="font-bold">教育经历</span>
              <el-button type="primary" size="small" @click="addEducation">
                添加教育经历
              </el-button>
            </div>
          </template>

          <el-form-item
            v-for="(edu, index) in resumeForm.education"
            :key="index"
            :label="`教育经历 ${index + 1}`"
          >
            <el-row :gutter="10">
              <el-col :span="8">
                <el-input v-model="edu.school" placeholder="学校"></el-input>
              </el-col>
              <el-col :span="8">
                <el-input v-model="edu.major" placeholder="专业"></el-input>
              </el-col>
              <el-col :span="6">
                <el-date-picker
                  v-model="edu.graduationDate"
                  type="year"
                  placeholder="毕业年份"
                ></el-date-picker>
              </el-col>
              <el-col :span="2">
                <el-button type="danger" @click="removeEducation(index)">删除</el-button>
              </el-col>
            </el-row>
          </el-form-item>
        </el-card>

        <!-- 工作经历 -->
        <el-card class="mb-4">
          <template #header>
            <div class="flex justify-between items-center">
              <span class="font-bold">工作经历</span>
              <el-button type="primary" size="small" @click="addWorkExperience">
                添加工作经历
              </el-button>
            </div>
          </template>

          <el-form-item
            v-for="(work, index) in resumeForm.workExperience"
            :key="index"
            :label="`工作经历 ${index + 1}`"
          >
            <el-row :gutter="10">
              <el-col :span="8">
                <el-input v-model="work.company" placeholder="公司"></el-input>
              </el-col>
              <el-col :span="8">
                <el-input v-model="work.position" placeholder="职位"></el-input>
              </el-col>
              <el-col :span="6">
                <el-date-picker
                  v-model="work.period"
                  type="monthrange"
                  range-separator="至"
                  start-placeholder="开始月份"
                  end-placeholder="结束月份"
                ></el-date-picker>
              </el-col>
              <el-col :span="2">
                <el-button type="danger" @click="removeWorkExperience(index)">删除</el-button>
              </el-col>
            </el-row>
            <el-input
              v-model="work.description"
              type="textarea"
              placeholder="工作描述"
              class="mt-2"
            ></el-input>
          </el-form-item>
        </el-card>

        <el-button type="success" @click="generateResume" class="w-full"> 生成简历 </el-button>
      </el-form>
    </div>

    <!-- 右侧预览区 -->
    <div class="preview-section w-1/2 p-6 bg-white">
      <div class="resume-preview border p-6">
        <h1 class="text-2xl font-bold text-center mb-4">{{ resumeForm.name || '简历预览' }}</h1>

        <!-- 个人信息预览 -->
        <div class="text-center mb-4">
          <p>{{ resumeForm.phone }} | {{ resumeForm.email }}</p>
        </div>

        <!-- 教育经历预览 -->
        <div v-if="resumeForm.education.length" class="mb-4">
          <h2 class="text-xl font-semibold border-b mb-2">教育经历</h2>
          <div v-for="(edu, index) in resumeForm.education" :key="index" class="mb-2">
            <div class="flex justify-between">
              <span class="font-bold">{{ edu.school }} - {{ edu.major }}</span>
              <span>{{ formatYear(edu.graduationDate) }}</span>
            </div>
          </div>
        </div>

        <!-- 工作经历预览 -->
        <div v-if="resumeForm.workExperience.length" class="mb-4">
          <h2 class="text-xl font-semibold border-b mb-2">工作经历</h2>
          <div v-for="(work, index) in resumeForm.workExperience" :key="index" class="mb-2">
            <div class="flex justify-between">
              <span class="font-bold">{{ work.company }} - {{ work.position }}</span>
              <span>{{ formatPeriod(work.period) }}</span>
            </div>
            <p class="text-sm text-gray-600 mt-1">{{ work.description }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'

// 定义接口
interface Education {
  school: string
  major: string
  graduationDate: Date | null
}

interface WorkExperience {
  company: string
  position: string
  period: [Date, Date] | null
  description: string
}

interface ResumeForm {
  name: string
  phone: string
  email: string
  education: Education[]
  workExperience: WorkExperience[]
}

// 使用响应式对象定义简历表单
const resumeForm = reactive<ResumeForm>({
  name: '',
  phone: '',
  email: '',
  education: [],
  workExperience: []
})

// 添加教育经历
const addEducation = () => {
  resumeForm.education.push({
    school: '',
    major: '',
    graduationDate: null
  })
}

// 删除教育经历
const removeEducation = (index: number) => {
  resumeForm.education.splice(index, 1)
}

// 添加工作经历
const addWorkExperience = () => {
  resumeForm.workExperience.push({
    company: '',
    position: '',
    period: null,
    description: ''
  })
}

// 删除工作经历
const removeWorkExperience = (index: number) => {
  resumeForm.workExperience.splice(index, 1)
}

// 生成简历
const generateResume = () => {
  // 这里可以添加导出PDF等功能
  console.log('生成简历', resumeForm)
}

// 格式化年份
const formatYear = (date: Date | null): string => {
  return date ? date.getFullYear().toString() : ''
}

// 格式化工作期间
const formatPeriod = (period: [Date, Date] | null): string => {
  if (!period) return ''
  const [start, end] = period
  return `${start.getFullYear()}.${start.getMonth() + 1} - ${end.getFullYear()}.${
    end.getMonth() + 1
  }`
}
</script>

<style scoped>
.resume-generator {
  height: 100vh;
}
.input-section,
.preview-section {
  overflow-y: auto;
}
</style>
