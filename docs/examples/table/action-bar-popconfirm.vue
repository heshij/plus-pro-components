<template>
  <div>
    <el-row>
      <el-text style="margin-right: 10px">操作按钮二次确认类型 </el-text>

      <el-radio-group v-model="confirmType">
        <el-radio value="popconfirm">popconfirm </el-radio>
        <el-radio value="messageBox">messageBox</el-radio>
      </el-radio-group>
    </el-row>

    <el-row>
      <el-text style="margin-right: 10px">操作按钮类型 </el-text>

      <el-radio-group v-model="type">
        <el-radio value="link">link </el-radio>
        <el-radio value="icon">icon</el-radio>
        <el-radio value="button">button</el-radio>
      </el-radio-group>
    </el-row>

    <PlusTable
      key="3"
      :columns="tableConfig"
      :table-data="tableData"
      :action-bar="{
        buttons: buttons2,
        type: type,
        confirmType: confirmType,
        width: type === 'button' ? 260 : type === 'icon' ? 160 : 200
      }"
      @clickAction="handleClickButton"
    />
  </div>
</template>

<script lang="ts" setup>
import { computed, ref } from 'vue'
import { useTable } from 'plus-pro-components'
import type { PlusColumn, ButtonsCallBackParams } from 'plus-pro-components'
import {
  View,
  Edit,
  Delete,
  DocumentCopy,
  WarnTriangleFilled,
  Operation
} from '@element-plus/icons-vue'

interface TableRow {
  id: number
  name: string
  status: string
  tag: string
  time: Date
}

const TestServe = {
  getList: async () => {
    const data = Array.from({ length: 3 }).map((item, index) => {
      return {
        id: index,
        name: index + 'name',
        status: String(index % 3),
        tag: index === 1 ? 'success' : index === 2 ? 'warning' : index === 3 ? 'info' : 'danger',
        time: new Date()
      }
    })
    return {
      data: data as TableRow[]
    }
  }
}

const type = ref('link')
const confirmType = ref('popconfirm')
const { buttons: buttons2, tableData } = useTable<TableRow[]>()

buttons2.value = [
  {
    // 打开
    text: '打开',
    code: 'open',
    props: {
      type: 'warning'
    },
    icon: Operation,
    confirm: {
      // eslint-disable-next-line @typescript-eslint/ban-ts-comment
      // @ts-ignore
      popconfirmProps: { width: 150, icon: WarnTriangleFilled, iconColor: 'red' },
      message: data => `确定打开id为${data.row.id}的数据吗？`
    }
  },
  {
    // 查看
    text: '查看',
    code: 'view',
    icon: View,
    props: {
      type: 'info'
    },
    show: (row: any) => row.status === '1'
  },
  {
    // 修改
    text: '修改',
    code: 'edit',
    icon: Edit,
    // props v0.1.16 版本新增函数类型
    props: (row: any) => ({
      type: 'primary',
      color: row.status === '1' ? 'red' : '#333'
    }),
    show: computed(() => true)
  },
  {
    // 删除
    text: '删除',
    code: 'delete',
    icon: Delete,
    // props v0.1.16 版本新增计算属性支持
    props: computed(() => ({ type: 'danger' })),
    confirm: {
      // eslint-disable-next-line @typescript-eslint/ban-ts-comment
      // @ts-ignore
      popconfirmProps: { width: 200, icon: WarnTriangleFilled, iconColor: 'red' },
      message: data => `确定删除id为${data.row.id}且行数为${data.index}的数据吗？`
    }
  },
  {
    text: '复制',
    code: 'copy',
    icon: DocumentCopy,
    props: {
      type: 'success'
    }
  }
]

const tableConfig: PlusColumn[] = [
  {
    label: '名称',
    prop: 'name'
  },
  {
    label: '状态',
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
    prop: 'tag',
    valueType: 'tag',
    fieldProps: (value: string) => {
      return { type: value }
    }
  },
  {
    label: '时间',
    prop: 'time',
    valueType: 'date-picker'
  }
]

const getList = async () => {
  try {
    const { data } = await TestServe.getList()
    tableData.value = data || []
  } catch (error) {}
}
getList()

const handleClickButton = (data: ButtonsCallBackParams) => {
  console.log(data)
}
</script>
