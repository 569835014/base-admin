<template>
  <div class="apps">
    <Row :gutter="12">
      <Col :span="6">
        <Tree class="app-tree" :data="treeData" :render="renderContent"></Tree>
      </Col>
      <Col :span="16">
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
      </Col>
    </Row>
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
      ],
      buttonProps: {
        type: 'default',
        size: 'small'
      }
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
    },
    renderContent (h, { root, node, data }) {
      return h('span', {
        class: ['app-tree-children'],
        style: {
          display: 'inline-block',
          width: '100%',
          height: '36px',
          lineHeight: '36px'
        },
        on: {
          contextmenu (e) {
            console.info(data)
          }
        }
      }, [
        h('span', {
          class: ['app-tree-children-span'],
          on: {
            click () {
              this.queryResource(data)
            }
          }
        }, [
          h('span', [
            h('Icon', {
              props: {
                type: 'ios-paper-outline'
              },
              style: {
                marginRight: '8px'
              }
            }),
            h('span', data.title)
          ]),
          h('Dropdown', {
            class: ['tree-right-icon']
          }, [
            h('Icon', {
              props: {
                type: 'md-more'
              }
            }),
            h('DropdownMenu', {
              slot: 'list',
              style: {
                width: '100px'
              }
            }, [
              h('DropdownItem', {
              }, [
                h('span', [
                  h('Icon', {
                    style: {
                      marginRight: '5px'
                    },
                    props: {
                      size: 14,
                      type: 'md-create'
                    }
                  }),
                  h('span', '编辑')
                ])
              ]),
              h('DropdownItem', {
              }, [
                h('span', [
                  h('Icon', {
                    style: {
                      marginRight: '5px'
                    },
                    props: {
                      size: 14,
                      type: 'md-trash'
                    }
                  }),
                  h('span', '删除')
                ])
              ])
            ])
          ])
        ]),
        h('span', {
          style: {
            display: 'inline-block',
            float: 'right',
            marginRight: '32px'
          }
        }, [
        ])
      ])
    },
    queryResource (data) {
      console.info(data)
    }
  },
  mounted () {
    axios.post('/api/app/app_list', this.query).then((res) => {
      if (res && res.data && res.data.value) this.data = res.data.value
    })
  },
  asyncComputed: {
    async treeChildrenData () {
      try {
        const data = await axios.post('/api/app/app_list', this.query)
        if (data && data.data && data.data.value) {
          const treeJson = data.data.value
          return treeJson.map((json) => {
            return {
              ...json,
              title: json.CName,
              expand: false,
              children: []
            }
          })
        }
        return []
      } catch (e) {
        return []
      }
    }
  },
  computed: {
    treeData () {
      const treeList = [
        {
          title: '应用系统',
          expand: true,
          render: (h, { root, node, data }) => {
            return h('span', {
              class: ['app-tree-children'],
              style: {
                display: 'inline-block',
                width: '100%',
                height: '36px',
                lineHeight: '36px'
              }
            }, [
              h('span', [
                h('Icon', {
                  props: {
                    type: 'ios-folder-outline'
                  },
                  style: {
                    marginRight: '8px'
                  }
                }),
                h('span', data.title)
              ]),
              h('span', {
                style: {
                  display: 'inline-block',
                  float: 'right',
                  marginRight: '32px'
                }
              }, [
                h('Button', {
                  props: Object.assign({}, this.buttonProps, {
                    icon: 'ios-add'
                  }),
                  on: {
                    click: () => { this.append(data) }
                  }
                })
              ])
            ])
          },
          children: Array.isArray(this.treeChildrenData) ? this.treeChildrenData : []
        }
      ]
      return treeList
    },
    refForm () {
      return this.$refs.form
    }
  }
}
</script>
<style lang="less">
  .app-tree{
    width: 100%;
    overflow: hidden;
    font-size: 14px;
    .ivu-select-dropdown{
      width: 100px !important;
      left: 220px !important;
    }
    .ivu-tree li ul{
      font-size: 14px;
    }
  }
  .app-tree-children-span{
    padding: 0 0 0 10px;
    display: flex;
    margin-left: -10px;
    justify-content: space-between;
  }
  .app-tree-children{
    padding-left: 10px;
    transition: 0.5s;
    box-sizing: border-box;
    font-size: 13px;
    cursor: pointer;
  }
  .tree-right-icon{
    margin-right: 20px;
    width: 20px;
    height: 20px;
    font-size: 16px;
  }
  .app-tree-children:hover{
    background: rgb(236,246,254);
  }

</style>
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
