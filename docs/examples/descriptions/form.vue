<template>
  <PlusDescriptions
    ref="plusDescriptionsInstance"
    :column="3"
    :columns="columns"
    :border="border"
    :editable="editable"
    :direction="direction"
    :data="descriptionsData"
    :descriptions-item-props="{ align: position, minWidth: '60px' }"
    :form-props="{ rules }"
    @formChange="formChange"
  >
    <template #title>
      <div>描述表单</div>
      <div class="right">
        <div>
          <span class="label">编辑</span>
          <el-checkbox v-model="editable"> 是否可编辑</el-checkbox>
        </div>

        <div>
          <span class="label"> label 位置</span>
          <el-radio-group v-model="position">
            <el-radio value="left">left</el-radio>
            <el-radio value="center">center</el-radio>
            <el-radio value="right">right</el-radio>
          </el-radio-group>
        </div>

        <div>
          <span class="label"> direction 方向</span>
          <el-radio-group v-model="direction">
            <el-radio value="vertical">vertical</el-radio>
            <el-radio value="horizontal">horizontal</el-radio>
          </el-radio-group>
        </div>

        <div>
          <span class="label">边框</span>
          <el-checkbox v-model="border"> 开启边框</el-checkbox>
        </div>
      </div>
    </template>
  </PlusDescriptions>

  <el-row v-if="editable" style="margin-top: 20px">
    <el-button type="primary" @click="handleSave"> 校验(提交) </el-button>
    <el-button @click="handleClear"> 移除校验 </el-button>
  </el-row>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import type { PlusColumn, RecordType, PlusDescriptionsInstance } from 'plus-pro-components'
import { set } from 'lodash-es'

const rules = {
  name: [
    {
      required: true,
      message: '请输入名称'
    }
  ],
  tag: [
    {
      required: true,
      message: '请输入标签'
    }
  ]
}

const position = ref('right')
const border = ref(true)
const editable = ref(true)
const direction = ref('horizontal')

const TestServe = {
  getList: async () => {
    const index = Math.random() * 10
    const data = {
      index,
      id: index,
      title: '序号' + (index + 1),
      name: '',
      status: '1',
      tag: '',
      progress: 30,
      rate: index > 3 ? 2 : 3.5,
      switch: index > 3 ? true : false,
      time: new Date(),
      code: `
    const getData = async params => {
      const data = await getData(params)
      return { list: data.data, ...data }
    }`
    }
    return {
      data
    }
  }
}

const columns: PlusColumn[] = [
  {
    label: '名称',
    width: 120,
    prop: 'name',
    valueType: 'copy'
  },
  {
    label: '标签',
    width: 120,
    prop: 'tag',
    valueType: 'tag'
  },
  {
    label: '状态',
    width: 120,
    prop: 'status',
    valueType: 'select',
    options: [
      {
        label: '未解决',
        value: '0',
        color: 'red'
      },
      {
        label: '已解决',
        value: '1',
        color: 'blue'
      },
      {
        label: '解决中',
        value: '2',
        color: 'yellow'
      },
      {
        label: '失败',
        value: '3',
        color: 'red'
      }
    ]
  },
  {
    label: '执行进度',
    width: 200,
    prop: 'progress',
    valueType: 'progress'
  },
  {
    label: '代码块',
    width: 250,
    prop: 'code',
    valueType: 'code'
  },
  {
    label: '评分',
    width: 200,
    prop: 'rate',
    valueType: 'rate'
  },
  {
    label: '开关',
    width: 100,
    prop: 'switch',
    valueType: 'switch'
  },
  {
    label: '时间',
    width: 190,
    prop: 'time',
    valueType: 'date-picker'
  }
]
const plusDescriptionsInstance = ref<PlusDescriptionsInstance | null>()
const descriptionsData = ref<RecordType>({})

const getList = async () => {
  try {
    const { data } = await TestServe.getList()
    descriptionsData.value = data || {}
  } catch (error) {}
}
getList()

const formChange = ({ value, prop }: { value: string; prop: string }) => {
  // 同步表单数据到描述
  set(descriptionsData.value, prop, value)
  console.log(descriptionsData.value, 'descriptionsData.value')
}

// 校验
const handleSave = async () => {
  try {
    await plusDescriptionsInstance.value?.validate()
  } catch (error) {
    console.warn(error)
  }
}

// 移除校验
const handleClear = () => {
  plusDescriptionsInstance.value?.clearValidate()
}
</script>

<style lang="scss" scoped>
.right {
  margin: 20px 0;
  display: flex;
  justify-content: flex-end;
  column-gap: 100px;
  .label {
    padding-right: 10px;
  }
}
</style>
