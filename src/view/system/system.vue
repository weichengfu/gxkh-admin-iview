<template>
  <div class="system">
    <div class="title-h2">小程序轮播图<span>(图片比例建议650X480,大小不超过2M)</span></div>
    <div class="imgList moudel">
      <div class="demo-upload-list" v-for="(item,key) in uploadList1">
        <template v-if="!item.showProgress || item.showProgress == 100">
          <img :src="item">
          <div class="demo-upload-list-cover">
            <Icon type="ios-eye-outline" @click.native="handleView(item)" size="30"></Icon>
            <Icon type="ios-trash-outline" @click.native="showRemove(key,1)" size="30"></Icon>
          </div>
        </template>
        <Progress v-if="item.showProgress" :percent="item.percentage" hide-info></Progress>
      </div>
      <Upload
        ref="upload1"
        :show-upload-list="false"
        :default-file-list="defaultList"
        :on-success="handleSuccess"
        :format="['jpg','jpeg','png']"
        :max-size="5048"
        :on-format-error="handleFormatError"
        :on-exceeded-size="handleMaxSize"
        :before-upload="handleBeforeUpload(1)"
        multiple
        type="drag"
        action="https://www.chmbkh.com/mobile/file/upload"
        :headers="headers"
        style="display: inline-block;width:150px;">
        <div class="upload">
          <Icon type="ios-camera" size="40"></Icon>
        </div>
      </Upload>
    </div>
    <div class="block"></div>
    <div class="title-h2">官网轮播图<span>(图片比例建议1920X991,大小不超过5M)</span></div>
    <div class="imgList moudel">
      <div class="demo-upload-list" v-for="(item,key) in uploadList2">
        <template v-if="!item.showProgress || item.showProgress == 100">
          <img :src="item">
          <div class="demo-upload-list-cover">
            <Icon type="ios-eye-outline" @click.native="handleView(item)" size="30"></Icon>
            <Icon type="ios-trash-outline" @click.native="showRemove(key,2)" size="30"></Icon>
          </div>
        </template>
        <Progress v-if="item.showProgress" :percent="item.percentage" hide-info></Progress>
      </div>
      <Upload
        ref="upload2"
        :show-upload-list="false"
        :default-file-list="defaultList"
        :on-success="handleSuccess"
        :format="['jpg','jpeg','png']"
        :max-size="5048"
        :on-format-error="handleFormatError"
        :on-exceeded-size="handleMaxSize"
        :before-upload="handleBeforeUpload(2)"
        multiple
        type="drag"
        action="https://www.chmbkh.com/mobile/file/upload"
        :headers="headers"
        style="display: inline-block;width:150px;">
        <div class="upload">
          <Icon type="ios-camera" size="40"></Icon>
        </div>
      </Upload>
    </div>
    <div class="block"></div>
    <div class="title-h2">产品图片<span>(图片比例建议800X600,大小不超过5M)</span></div>
    <div class="imgList moudel">
      <div v-for="(item,key) in uploadList3" class="upload-list">
        <div class="demo-upload-list" >
          <template v-if="!item.showProgress || item.showProgress == 100">
            <img :src="item.img">
            <div class="demo-upload-list-cover">
              <Icon type="ios-eye-outline" @click.native="handleView(item)" size="30"></Icon>
              <Icon type="ios-trash-outline" @click.native="showRemove(key,3)" size="30"></Icon>
            </div>
          </template>
          <Progress v-if="item.showProgress" :percent="item.percentage" hide-info></Progress>
        </div>
        <Input class="title-input" v-model="item.title"></Input>
      </div>
      <Upload
        ref="upload3"
        :show-upload-list="false"
        :default-file-list="defaultList"
        :on-success="handleSuccess"
        :format="['jpg','jpeg','png']"
        :max-size="5048"
        :on-format-error="handleFormatError"
        :on-exceeded-size="handleMaxSize"
        :before-upload="handleBeforeUpload(3)"
        multiple
        type="drag"
        action="https://www.chmbkh.com/mobile/file/upload"
        :headers="headers"
        style="display: inline-block;width:150px;">
        <div class="upload">
          <Icon type="ios-camera" size="40"></Icon>
        </div>
      </Upload>
      <div class="save">
        <Button type="primary" @click="saveProImg">保存</Button>
      </div>
    </div>
    <div class="block"></div>
    <div class="title-h2">计费设置</div>
    <div class="time moudel">
      <div class="item">
        <span>每小时计费：</span><InputNumber :max="99" :min="1" v-model="money" :disabled="disabled"></InputNumber>
        <Button type="primary" @click="editMoney" class="button">{{disabled?'编辑':'保存'}}</Button>
      </div>
      <div class="item">
        <span>选时选项：</span>
        <Dropdown>
          <a href="javascript:void(0)">
            可选项
            <Icon type="ios-arrow-down"></Icon>
          </a>
          <DropdownMenu slot="list">
            <DropdownItem v-for="item in timeList">{{item}}</DropdownItem>
          </DropdownMenu>
        </Dropdown>
        <Button type="primary" @click="editOption" class="button">编辑</Button>
      </div>
    </div>
    <div class="block"></div>
    <div class="title-h2">客服电话</div>
    <div class="customer moudel">
      <span>客服电话：</span><Input v-model="phone" :disabled="cusDisabled" style="width: 200px"></Input>
      <Button type="primary" @click="editCustomer" class="button">{{cusDisabled?'编辑':'保存'}}</Button>
    </div>
    <Modal title="View Image" v-model="visible">
      <img :src="imgName" v-if="visible" style="width: 100%">
    </Modal>
    <Modal title="编辑选项" v-model="edit" @on-ok="okAdd">
      <div class="editOption">
        <div class="item" v-for="(item,index) in newTimeList">
          <InputNumber :max="99" :min="1" v-model="newTimeList[index]" class="number" ></InputNumber>
          <Icon type="md-close" size="12" color="#fff" class="icon" @click="removeOption(index)" />
        </div>
        <Button type="primary" class="button" @click="addOption">添加</Button>
      </div>
    </Modal>
    <Modal title="删除图片" v-model="remove" class-name="vertical-center-modal" @on-ok="handleRemove">
      <p>是否确定删除图片？</p>
    </Modal>
  </div>
</template>

<script>
import Icons from '../../components/icons/icons'
import { getAllinfo, updateAppointInfo } from  '@/api/user'
export default {
  name: 'system',
  components: {Icons},
  data () {
    return {
      headers:{
        "ticket":app.$store.state.user.userId
      },
      remove:false,
      imgIndex:1, //操作的文件数组索引
      removeKey:0,
      defaultList: [
      ],
      imgName: '',
      visible: false,
      uploadList1: [],   //小程序轮播图
      uploadList2: [],  //官网轮播图
      uploadList3: [],  //产品图片
      edit: false,
      money: 0,
      timeList: [1, 2, 3, 4, 5, 6, 7],
      newTimeList: [],
      disabled:true,
      phone:'',
      cusDisabled:true,
      imgMap:{
        1:'carousel_img',
        2:'pc_carousel_img',
        3:'pc_product_img',
      },
    }
  },
  created () {
    // 组件实例化生命周期
    this.getAllinfo()
  },
  mounted () {
    this.uploadList1 = this.$refs.upload1.fileList
    this.uploadList2 = this.$refs.upload2.fileList
    this.uploadList3 = this.$refs.upload3.fileList
  },
  methods: {
    //请求loading
    loading () {
      this.$Spin.show({
        render: (h) => {
          return h('div', [
            h('Icon', {
              'class': 'demo-spin-icon-load',
              props: {
                type: 'ios-loading',
                size: 18
              }
            }),
            h('div', 'Loading')
          ])
        }
      })
    },
    //查询所有的配置信息
    getAllinfo () {
      this.loading()
      getAllinfo().then(res => {
        const data = res.data
        console.log('所有配置',res)
        this.phone = data.data[0].value
        this.money = +((data.data[1].value)/100)
        this.uploadList1 = data.data[2].value.split(',')
        this.timeList = data.data[3].value.split(',')
        this.uploadList2 = data.data[4].value.split(',')
        this.uploadList3 = JSON.parse(data.data[5].value)
        this.$Spin.hide()
      }).catch(err => {
        this.$Spin.hide()
        console.log(err)
      })
    },
    //修改指定配置信息
    updateAppointInfo (key,value) {
      updateAppointInfo(key,value).then(res => {
        const data = res.data
        console.log(res)
        if(data.code === 100){
          this.$Message.success('操作成功');
        }
      }).catch(err => {
        console.log(err)
      })
    },
    // 编辑选项
    editOption () {
      this.edit = true
      this.newTimeList = JSON.parse(JSON.stringify(this.timeList))
    },
    // 添加选项
    addOption () {
      this.newTimeList.push(1)
    },
    // 删除选项
    removeOption (index) {
      this.newTimeList.splice(index, 1)
    },
    // 确定添加
    okAdd () {
      this.timeList = JSON.parse(JSON.stringify(this.newTimeList))
      this.updateAppointInfo('time_option',this.timeList.join(','))
    },
    //编辑计费
    editMoney () {
      if(!this.disabled){
        console.log('发起请求保存')
        this.updateAppointInfo('unit_price',this.money*100)
      }
      this.disabled = !this.disabled
    },
    //编辑电话
    editCustomer () {
      if(!this.cusDisabled){
        console.log('发起电话请求保存')
        this.updateAppointInfo('customer_service_phone',this.phone)
      }
      this.cusDisabled = !this.cusDisabled
    },
    //查看大图
    handleView (name) {
      this.imgName = name
      this.visible = true
    },
    //删除弹窗
    showRemove (key,index) {
      this.removeKey = key
      this.imgIndex = index
      this.remove = true
    },
    //删除图片
    handleRemove () {
      var upload = 'upload'+this.imgIndex
      var uploadList = 'uploadList'+this.imgIndex
      // const fileList = this.$refs.upload.fileList
      this.$refs[upload].fileList.splice(this.removeKey, 1)
      this[uploadList].splice(this.removeKey, 1)
      // this.updateAppointInfo(this.imgMap[this.imgIndex],this[uploadList].join(','))
    },
    //上传成功
    handleSuccess (res, file) {
      console.log('小程序轮播图上传成功-------',res,file)
      var uploadList = 'uploadList'+this.imgIndex
      if(this.imgIndex == 3){
        for(let i=0,len=res.data.length;i<len;i++){
          this[uploadList].push({img:res.data[i].url,title:''})
        }
        // this.updateAppointInfo(this.imgMap[this.imgIndex],JSON.stringify(this[uploadList]))
      }else {
        for(let i=0,len=res.data.length;i<len;i++){
          this[uploadList].push(res.data[i].url)
        }
        this.updateAppointInfo(this.imgMap[this.imgIndex],this[uploadList].join(','))
      }
    },
    //保存产品图片
    saveProImg(){
      var isadd = true
      for(let i=0,len=this.uploadList3.length;i<len;i++){
        if(!this.uploadList3[i].title){
          isadd = false
          this.$Notice.warning({
            title: '标题不能为空',
            desc: '请填写产品图标题'
          })
          break
        }
      }
      if(isadd){
        this.updateAppointInfo(this.imgMap[3],JSON.stringify(this.uploadList3))
      }
    },
    handleFormatError (file) {
      this.$Notice.warning({
        title: '文件类型错误',
        desc: '文件' + file.name + ' 类型错误, 请选择 jpg 或者 png'
      })
    },
    handleMaxSize (file) {
      this.$Notice.warning({
        title: '文件大小超出限制',
        desc: '文件  ' + file.name + ' 太大, 请上传5M以下的文件'
      })
    },
    handleBeforeUpload (index) {
      var oldindex = index
      return (e)=>{
        this.imgIndex = oldindex
        console.log('上传之前的-----',index,e)
        const check = this.uploadList1.length < 5
        if (!check) {
          this.$Notice.warning({
            title: '最多只能选择5张图片.'
          })
        }
        return check
      }
    },
  }
}
</script>

<style scoped lang="less">
  .demo-spin-icon-load{
    animation: ani-demo-spin 1s linear infinite;
  }
  .vertical-center-modal{
    display: flex;
    align-items: center;
    justify-content: center;

    .ivu-modal{
      top: 0;
    }
  }
  .system{
    padding-bottom: 50px;
  }
  .title-h2{
    font-size: 16px;
    line-height: 60px;padding-left: 20px;
    span{
      color: #999;font-size: 12px;margin-left: 10px;
    }
  }
  .block{
    background: #f5f7f9;
    height: 10px;
  }
  .moudel{
    margin-bottom: 30px;
    padding: 0 20px;
  }
  .upload{
    width: 150px;
    height: 128px;
    line-height: 128px;
  }
  .time{
    .item{
      margin-bottom: 15px;
      span{
        display: inline-block;width: 80px;text-align: right;
      }
      .button{
        margin-left: 15px;
      }
    }
  }
  .editOption{
    width: 450px;
    display: flex;flex-wrap: wrap;align-items: center;align-content: center;
    .item{
      position: relative;margin: 5px 20px;margin-bottom: 10px;
      .number{
        width: 50px;
      }
      .icon{
        cursor: pointer;position: absolute;top: -5px;right: -5px;background: red;border-radius: 20px;padding: 1px;
      }
    }
    .button{
      height: 32px;margin-left:20px;margin-bottom: 5px;
    }
  }
  .customer{
    display: flex;
    span{
      display: inline-block;width: 80px;text-align: right;
      line-height: 32px;
    }
    .button{
      margin-left: 15px;
    }
  }

  .demo-upload-list{
    display: inline-block;
    width: 150px;
    height: 130px;
    text-align: center;
    line-height: 130px;
    border: 1px solid transparent;
    border-radius: 4px;
    overflow: hidden;
    background: #fff;
    position: relative;
    box-shadow: 0 1px 1px rgba(0,0,0,.2);
    margin-right: 15px;
    position: relative;
  }
  .upload-list{
    display: inline-block;
    width: 155px;
    /*height: 130px;*/
    margin-bottom: 15px;
    margin-right: 15px;
    .demo-upload-list{
      margin-right: 0;
    }
  }
  .save{
    margin-top: 20px;
  }
  .demo-upload-list img{
    width: 150px;
    height: 130px;
  }
  .demo-upload-list-cover{
    display: none;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background: rgba(0,0,0,.6);
  }
  .demo-upload-list:hover .demo-upload-list-cover{
    display: block;
  }
  .demo-upload-list-cover i{
    color: #fff;
    font-size: 20px;
    cursor: pointer;
    margin: 0 2px;
  }
</style>
