<template>
  <div class="apps">
    <div class="table-buttons">
      <Button @click="showModel">新建</Button>
    </div>
    <Table  :columns="columns" :data="data"></Table>
    <Modal
      :footer-hide="true"
      title="新增应用"
      v-model="model"
      class-name="vertical-center-modal">
      <Form :label-width="80" :model="form" :rules="rules" ref="form" >
        <FormItem label="应用名称" prop="name" >
          <Input v-model="form.name" placeholder="请输入应用名称"></Input>
        </FormItem>
        <FormItem label="中文名称" prop="CName">
          <Input v-model="form.CName" placeholder="请输入应用中文名称"></Input>
        </FormItem>
        <FormItem label="应用编码" prop="code">
          <Input v-model="form.code" placeholder="请输入应用编码"></Input>
        </FormItem>
        <FormItem label="是否可用" prop="isActive">
          <Select v-model="form.isActive" placeholder="请选择">
            <Option :value="0">可用</Option>
            <Option :value="1">禁用</Option>
          </Select>
        </FormItem>
      </Form>
      <div class="model-button">
        <Button @click="model = false">取消</Button>
        <Button type="primary" @click="handlerCreateApp">确定</Button>
      </div>
    </Modal>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Apps',
  data () {
    return {
      query: {
        pageSize: 10,
        pageNumber: 1
      },
      form: {
        name: '',
        CName: '',
        code: '',
        isActive: 0
      },
      rules: {
        name: [
          { required: true, message: '请输入应用名称', trigger: 'blur' }
        ],
        code: [
          { required: true, message: '请输入应用编码', trigger: 'blur' }
        ]
        // isActive: [
        //   { required: true, message: '请选择应用状态', trigger: 'blur' }
        // ]
      },
      model: false,
      columns: [
        {
          title: '应用名称',
          key: 'name'
        },
        {
          title: '中文名称',
          key: 'CName'
        },
        {
          title: '应用编码',
          key: 'code'
        },
        {
          title: '创建时间',
          key: 'createAt'
        },
        {
          title: '是否可用',
          key: 'isActive'
        },
        {
          title: '操作'
        }
      ],
      data: [
      ]
    }
  },
  methods: {
    handlerCreateApp () {
      this.refForm.validate(valid => {
        if (valid) {
          axios.post('/api/app/create', this.form).then((res) => {
            console.info(res)
          })
        } else {

        }
      })
    },
    showModel () {
      this.refForm.resetFields()
      this.model = true
    }
  },
  mounted () {
    axios.post('/api/app/app_list', this.query).then((res) => {
      if (res && res.data && res.data.value) this.data = res.data.value
    })
  },
  computed: {
    refForm () {
      return this.$refs.form
    }
  }
}
</script>

<style scoped lang="less">
  .apps{
    background: white;
    padding: 20px;
  }
  .table-buttons{
    margin-bottom: 10px;
  }
  .vertical-center-modal{
    display: flex;
    align-items: center;
    justify-content: center;
    .ivu-modal{
      top: 0;
    }
  }
  .model-button{
    display: flex;
    justify-content: flex-end;
    .ivu-btn:not(:last-child){
      margin-right: 10px;
    }
  }
</style>
