<template>
  <div>
    <el-row style="margin-bottom: 10px">
      <el-button @click="setSearchFieldsValue">设置搜索值</el-button>
      <el-button @click="getSearchFieldsValue"> 获取搜索值</el-button>
      <el-button @click="clearSearchFieldsValue">清空搜索值</el-button>
    </el-row>

    <PlusPage ref="plusPageInstance" :columns="tableConfig" :request="getList" />
  </div>
</template>

<script lang="ts" setup>
/* eslint-disable  @typescript-eslint/ban-ts-comment */
import type { PlusColumn, PageInfo, PlusPageInstance } from 'plus-pro-components'
import { ElButton, ElMessage } from 'element-plus'
import { ref } from 'vue'

const plusPageInstance = ref<PlusPageInstance | null>()

const getList = async (
  query: Partial<PageInfo> & {
    status?: string
    name?: string
  }
) => {
  console.log(query)

  const { page = 1, pageSize = 20, status, name } = query || {}
  const total = 1000
  const List = Array.from({ length: total }).map((item, index) => {
    return {
      id: index,
      name: index === 0 ? 'name'.repeat(20) : index + 'name',
      status: String(index % 3),
      tag: index === 1 ? 'success' : index === 2 ? 'warning' : index === 3 ? 'info' : 'danger',
      progress: 10,
      rate: index > 3 ? 2 : 3.5,
      switch: index % 2 === 0 ? true : false,
      img: 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg',
      time: new Date(),
      custom: 'custom' + index
    }
  })

  const mockList = List.filter(item => {
    if (status && status !== item.status) {
      return false
    }
    if (name && name !== item.name) {
      return false
    }

    return true
  })

  const pageList = mockList.filter(
    (item, index) => index < pageSize * page && index >= pageSize * (page - 1)
  )

  // 等待2s
  await new Promise(resolve => {
    setTimeout(() => {
      resolve('')
    }, 2000)
  })

  return { data: pageList, success: true, total: mockList.length }
}

const tableConfig: PlusColumn[] = [
  {
    label: '名称',
    tooltip: '名称最多显示6个字符',
    width: 120,
    prop: 'name',
    tableColumnProps: {
      showOverflowTooltip: true
    }
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
    label: '标签',
    width: 120,
    prop: 'tag',
    valueType: 'tag',
    fieldProps: (value: string) => {
      return { type: value }
    }
  },

  {
    label: '评分',
    width: 200,
    prop: 'rate',
    valueType: 'rate',
    hideInSearch: true,
    editable: true
  },
  {
    label: '开关',
    width: 100,
    prop: 'switch',
    hideInSearch: true,
    valueType: 'switch',
    editable: true
  },
  {
    label: '时间',
    prop: 'time',
    valueType: 'date-picker',
    hideInForm: true
  }
]

/**
 * 设置搜索值
 */
const setSearchFieldsValue = () => {
  // @ts-ignore
  plusPageInstance.value?.setSearchFieldsValue({ name: '小明', status: '0' })
}

/**
 * 获取搜索值
 */
const getSearchFieldsValue = () => {
  //  不传参数就是获取所有值，返回一个对象
  // @ts-ignore
  const values = plusPageInstance.value?.getSearchFieldsValue()
  ElMessage.info(JSON.stringify(values))

  //  传参数就是获取当前key所对应的值，返回一个值
  // @ts-ignore
  const name = plusPageInstance.value?.getSearchFieldsValue('name')
  ElMessage.info(name as string)
}
/**
 * 清空搜索值
 */
const clearSearchFieldsValue = () => {
  // @ts-ignore
  plusPageInstance.value?.clearSearchFieldsValue()
}
</script>
