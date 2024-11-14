<template>
  <div>
    <PlusTable
      ref="plusTableInstance"
      :columns="tableConfig"
      :table-data="tableData"
      :title-bar="false"
    >
      <template #plus-header-summarize="scoped">
        <span style="color: red"> {{ scoped.label }}</span>
      </template>
      <template #plus-header-expertRating="scoped">
        <span style="color: red"> {{ scoped.label }}</span>
      </template>
    </PlusTable>
  </div>
</template>

<script lang="ts" setup>
import type { PlusColumn, PlusTableInstance } from 'plus-pro-components'
import { useTable } from 'plus-pro-components'
import { ref } from 'vue'

interface TableRow {
  id: number
  name: string
  status: string
  popularRating: number
  expertRating: number
  switch: boolean
  time: Date
  result: string
}

const TestServe = {
  getList: async () => {
    const data = Array.from({ length: 3 }).map((item, index) => {
      return {
        id: index,
        name: 'name-' + index,
        status: String(index % 3),
        popularRating: Math.ceil(Math.random() * 5),
        expertRating: Math.ceil(Math.random() * 5),
        switch: index % 2 === 0 ? true : false,
        time: new Date(),
        result: ['晋级', '淘汰'][index % 2]
      }
    })

    return { data: data as TableRow[] }
  }
}
const { tableData } = useTable<TableRow[]>()

const plusTableInstance = ref<PlusTableInstance | null>(null)

const tableConfig = ref<PlusColumn[]>([
  {
    label: '名称',
    prop: 'name',
    width: 120,
    tableColumnProps: { align: 'center' }
  },
  {
    label: '状态',
    prop: 'status',
    valueType: 'select',
    tableColumnProps: { align: 'center' },
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
    label: '日期',
    prop: 'time',
    valueType: 'date-picker',
    fieldProps: {
      format: 'YYYY-MM-DD HH:mm'
    }
  },
  {
    label: '总结',
    prop: 'summarize',
    tableColumnProps: { align: 'center' },
    children: [
      {
        label: '评分',
        width: 200,
        prop: 'rate',
        tableColumnProps: { align: 'center' },
        children: [
          {
            label: '大众评分',
            width: 200,
            prop: 'popularRating',
            editable: true,
            valueType: 'rate'
          },
          {
            label: '专家评分',
            width: 200,
            prop: 'expertRating',
            editable: true,
            valueType: 'rate'
          }
        ]
      },
      {
        label: '结果',
        width: 200,
        prop: 'result',
        valueType: 'tag',
        tableColumnProps: { align: 'center' },
        fieldProps: (value: string) => {
          return { type: value === '晋级' ? 'primary' : 'danger' }
        }
      }
    ]
  }
])

const getList = async () => {
  try {
    const { data } = await TestServe.getList()
    tableData.value = data.map(item => ({ ...item }))
  } catch (error) {}
}
getList()
</script>
