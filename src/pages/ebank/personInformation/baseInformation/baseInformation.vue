<template>
  <view class="uni-container">
    <view class="pf-title pf-titleThree">
      <img src="@/static/images/firstroom/backArrow.svg" @click="navigateBack">
      <text>{{identity}}信息</text>
      <text class="pf-text" @click="nextStep(false)">暂存</text>
    </view>
    <!--信息完善进度条-start-->
    <view class="household-progress">
      <img src="@/static/images/firstroom/shortProgress02.svg" />
      <view class="uni-flex uni-row">
        <view class="flex-item active">户籍信息</view>
        <view class="flex-item active">基本信息</view>
        <view class="flex-item">工作信息</view>
      </view>
    </view>
    <!--信息完善进度条-end-->
    <view class="pf-content baseIn-content">
      <!--个人信息-start-->
      <view class="contract-ul marginT25">
        <view class="contract-li-tips">
          <view class="contract-li">
            <view>手机号码</view>
            <view>
              <input class="uni-input" placeholder="请输入" type="number" v-model.trim="personInfor.phone" @focus="verifyPhone.isTipShow=false" @blur="checkPhone(personInfor.phone)" />
              <img v-if="personInfor.phone!=''" src="@/static/images/firstroom/cleanUp.svg" @click="personInfor.phone=''">
            </view>
          </view>
          <view v-if="verifyPhone.isTipShow" class="contract-tips">{{verifyPhone.tipText}}</view>
        </view>
        <view class="contract-li-tips">
          <view class="contract-li">
            <view>个人年收入（元）</view>
            <view>
              <input class="uni-input" placeholder="请输入" type="number" v-model.trim="personInfor.yearIncome" @focus="verifyYearIncome.isTipShow=false" @blur="checkYearIncome(personInfor.yearIncome)" />
              <img v-if="personInfor.yearIncome!=''" src="@/static/images/firstroom/cleanUp.svg" @click="personInfor.yearIncome=''" />
            </view>
          </view>
          <view v-if="verifyYearIncome.isTipShow" class="contract-tips">{{verifyYearIncome.tipText}}</view>
        </view>
        <view class="contract-li">
          <view>居住状况</view>
          <view>
            <picker @change="familyStatusChange" :value="familyStatusIdx" :range-key="'value'" :range="familyStatusList">
              {{familyStatusList[familyStatusIdx].value}}
            </picker>
            <img src="@/static/images/firstroom/formChooseArrow.svg" />
          </view>
        </view>
        <view class="collateral-site">
          <view class="contract-li">
            <view>居住地址</view>
            <view class="colladdress">
              <picker mode="multiSelector" @columnchange="changeNextCol" :value="mulIndex" :range="mulArr">
                <view class="picker" v-if="isShow">
                {{mulArr[0][mulIndex[0]]}}，{{mulArr[1][mulIndex[1]]}}，{{mulArr[2][mulIndex[2]]}}
                </view>
              </picker>
              <img src="@/static/images/firstroom/formChooseArrow.svg" />
            </view>
          </view>
          <view class="collateral-flex">
            <textarea 
              placeholder="请输入详细地址"  
              v-model.trim="personInfor.detailAddress"
              auto-height fixed="true"/>
          </view>
        </view>
        <view class="contract-li">
          <view>最高学历</view>
          <view>
            <picker @change="eduexperienceChange" :value="eduexperienceIdx" :range-key="'value'" :range="eduexperienceList">
              {{eduexperienceList[eduexperienceIdx].value}}
            </picker>
            <img src="@/static/images/firstroom/formChooseArrow.svg" />
          </view>
        </view>
        <view class="contract-li">
          <view>最高学位</view>
          <view>
            <picker @change="edudegreeChange" :value="edudegreeIdx" :range-key="'value'" :range="edudegreeList">
              {{edudegreeList[edudegreeIdx].value}}
            </picker>
            <img src="@/static/images/firstroom/formChooseArrow.svg" />
          </view>
        </view>
        <view class="contract-li" v-if="identity=='共借人' || identity=='担保人'">
          <view>婚姻状况</view>
          <view>
            <picker @change="marriageChange" :value="marriageIdx" :range-key="'value'" :range="marriageList">
              {{marriageList[marriageIdx].value}}
            </picker>
            <img src="@/static/images/firstroom/formChooseArrow.svg" />
          </view>
        </view>
        <view class="contract-li" v-if="identity=='共借人' || identity=='担保人'">
          <view>与借款人关系</view>
          <view>
            <picker @change="relationShipChange" :value="relationShipIdx" :range-key="'value'" :range="relationShipList">
              {{relationShipList[relationShipIdx].value}}
            </picker>
            <img src="@/static/images/firstroom/formChooseArrow.svg" />
          </view>
        </view>
      </view>
      <!--个人信息-end-->
      <view class="contract-button">
        <button type="primary" @click="nextStep(true)">下一步</button>
      </view>
    </view>
  </view>
</template>
<script>
  import pageHead from '@/components/page-head.vue';
  import { mapGetters,mapActions } from 'vuex'

  export default {
    data() {
      return {
        identity: "",
        personInfor:{
          phone:"",
          yearIncome:'',
          detailAddress: ''
        },
        verifyPhone: {
          isTipShow:false,
          tipText: '请输入11位手机号码',
        },
        verifyYearIncome: {
          isTipShow:false,
          tipText: '个人年收入不能为空',
        },
        verifyPostcode: {
          isTipShow:false,
          tipText: '居住地址邮编不能为空',
        },
        familyStatusList: [
          {
            key: 0,
            value: '请选择'
          }
        ],
        familyStatusIdx: 0,
        eduexperienceList: [
          {
            key: 0,
            value: '请选择'
          }
        ],
        eduexperienceIdx: 0,
        edudegreeList: [
          {
            key: 0,
            value: '请选择'
          }
        ],
        edudegreeIdx: 0,
        marriageList: [
          {
            key: 0,
            value: '请选择'
          }
        ],
        marriageIdx: 0,
        relationShipList: [
          {
            key: 0,
            value: '请选择'
          }
        ],
        relationShipIdx: 0,
        
        mulIndex: [0,0,0],
        mulArr:[],
        mulCodeArr:[],
        regionCode:'',
        isShow: false,
        // 假设json为后端返回的数据
        json: [],
        orderNoVal: '',  //订单编号
        applyNoVal: '', //申请编号
        isJump: true,
        preventResubmit: true,  //防重复提交
      }
    },
    computed: {
      ...mapGetters([
          'approvalIngList',
          'userInfor'
      ])
    },
    onLoad(option) {
      this.orderNoVal = this.approvalIngList.orderNo;
      this.applyNoVal = this.approvalIngList.serialNo;
      this.identity = option.identity;
      this.getFamilyStatusList();
      this.familyStatusIdx=this.selectInfo(this.familyStatusList,'0');
      this.getEduexperienceList();
      this.getEdudegreeList();
      this.getMarriageList();
      this.getRelationShipList();
      this.getCascadeData();
      if(this.identity=='共借人配偶' || this.identity=='担保人配偶'){
        this.relationShipIdx='0310'
      }
      if(localStorage.getItem('personalInformation')){
        this.getPersonalData();
      }

    },
    onShow() {},
    onUnload() {},
    methods: {
      ...mapActions(['queryApplyInfoCommit']),
      changeNextCol(e){
        // 列的值改变时触发   我这里是三列：车子类型  品牌名称  车型
        console.log('修改的列', e.target.column, '值为', e.target.value);
        // 监听用户操作，改变mulIndex的值
        this.mulIndex[e.target.column] = e.target.value;
        // mulArr[0]的值是不会随用户操作变更的，因此不需要改变  
        // mulArr[1]的值是由 mulIndex[0]的值决定的
        this.mulArr.splice(1,1,this.json[this.mulIndex[0]].children.map(function(v){return v.regionName}));
        this.mulCodeArr.splice(1,1,this.json[this.mulIndex[0]].children.map(function(v){return v.regionId}));
        // mulArr[2]的值是由 muIndex[1]的值决定的 
        this.mulArr.splice(2,1,this.json[this.mulIndex[0]].children[this.mulIndex[1]].children.map(function(v){return v.regionName}));
        this.mulCodeArr.splice(2,1,this.json[this.mulIndex[0]].children[this.mulIndex[1]].children.map(function(v){return v.regionId}));
        // {{mulArr[0][mulIndex[0]]}}，{{mulArr[1][mulIndex[1]]}}，{{mulArr[2][mulIndex[2]]}}
        console.log(this.mulArr[0][this.mulIndex[0]]+this.mulArr[1][this.mulIndex[1]]+this.mulArr[2][this.mulIndex[2]])
        console.log(this.mulCodeArr[2][this.mulIndex[2]])
        this.regionCode=this.mulCodeArr[2][this.mulIndex[2]];
        console.log(this.mulIndex)
      },

      //返回上一页
      navigateBack(){
        uni.showModal({
          title: '确定返回吗？',
          content: '数据还未提交，点确定后编辑过的内容将不保存！',
          success: function (res) {
            if (res.confirm) {
              uni.navigateBack();
            } else if (res.cancel) {
              console.log('用户点击取消');
            }
          }
        });
      },
      //下拉列表
      familyStatusChange: function(e) {
        this.familyStatusIdx = e.target.value
      },
      eduexperienceChange(e){
        this.eduexperienceIdx = e.target.value
      },
      edudegreeChange(e){
        this.edudegreeIdx = e.target.value
      },
      marriageChange(e){
        this.marriageIdx = e.target.value
      },
      relationShipChange(e){
        this.relationShipIdx = e.target.value
      },
      // 获取居住状况列表
      getFamilyStatusList(){
        let pdata={dictionaryName:"familyStatus"};
        this.queryDictionaryList(pdata,(res)=>{
          let resData=res.data.data;
          for(let key in resData){
            console.log(key);
              this.familyStatusList.push({
                key:key,
                value:resData[key]
              });
          }
        });
      },
      // 获取省市区三级级联数据
      getCascadeData(){
        let data={};
       // 6.10接口 获取省市区三级级联数据
        let posturl="/api/region/regionListAllQuery";
        yu.showLoading();
        this.interfaceRequest(posturl,data,"post",(res)=>{
          yu.hideLoading();
          let resArr=res.data.data;
          this.json=resArr.regionAllList;
          // 初始化picker默认值
          this.mulArr[0] = this.json.map(function(v){return v.regionName});
      　  this.mulArr[1] = this.json[this.mulIndex[0]].children.map(function(v){return v.regionName});
      　  this.mulArr[2] = this.json[this.mulIndex[0]].children[this.mulIndex[1]].children.map(function(v){return v.regionName});
          this.mulCodeArr[0] = this.json.map(function(v){return v.regionId});
      　  this.mulCodeArr[1] = this.json[this.mulIndex[0]].children.map(function(v){return v.regionId});
          this.mulCodeArr[2] = this.json[this.mulIndex[0]].children[this.mulIndex[1]].children.map(function(v){return v.regionId});
          this.regionCode=this.mulCodeArr[2][this.mulIndex[2]];
          this.isShow = true;

        },(err)=>{
          yu.hideLoading();
          yu.showToast({
            title: '7.8地区查询出现问题，请联系管理员',
            icon: 'none',
            duration: 3000
          });
        });
      },
      // 获取最高学历列表
      getEduexperienceList(){
        let pdata={dictionaryName:"eduexperience"};
        this.queryDictionaryList(pdata,(res)=>{
          let resData=res.data.data;
          for(let key in resData){
              this.eduexperienceList.push({
                key:key,
                value:resData[key]
              });
          }
        });
      },
      // 获取最高学位列表
      getEdudegreeList(){
        let pdata={dictionaryName:"edudegree"};
        this.queryDictionaryList(pdata,(res)=>{
          let resData=res.data.data;
          for(let key in resData){
              this.edudegreeList.push({
                key:key,
                value:resData[key]
              });
          }
        });
      },
      // 获取婚姻状况列表
      getMarriageList(){
        let pdata={dictionaryName:"marriage"};
        this.queryDictionaryList(pdata,(res)=>{
          let resData=res.data.data;
          for(let key in resData){
              this.marriageList.push({
                key:key,
                value:resData[key]
              });
          }
        });
      },
      // 获取与借款人关系列表
      getRelationShipList(){
        let pdata={dictionaryName:"relationShip"};
        this.queryDictionaryList(pdata,(res)=>{
          let resData=res.data.data;
          for(let key in resData){
              this.relationShipList.push({
                key:key,
                value:resData[key]
              });
          }
        });
      },
      // 下一步
      nextStep(isJump){
        if(!this.preventResubmit){
          yu.showModal({
            content: '操作进行中，请稍等',
            showCancel: false,
            cancelText: "确定",
            success: function (res) {
              if (res.confirm) {
                console.log('用户点击确定');
              }
            }
          });
          return;
        }
        this.preventResubmit = false;
        this.isJump = isJump;
        let personalInformation=JSON.parse(localStorage.getItem('personalInformation'));

        var marriage='',relationShip='';
        if(this.identity=='担保人' || this.identity=='共借人'){
          marriage=this.marriageList[this.marriageIdx].key
        }else if(this.identity=='担保人配偶' || this.identity=='共借人配偶' || this.identity=='借款人配偶'){
          marriage='20'
        }
        if(this.identity=='借款人配偶'){
          relationShip='0301'
        }else if(this.identity=='担保人' || this.identity=='共借人'){
          relationShip=this.relationShipList[this.relationShipIdx].key;
        }else if(this.identity=='担保人配偶' || this.identity=='共借人配偶'){
          relationShip='0310'
        }

        let data={
          userId: this.userInfor.loginCode,
          certType: personalInformation.certType,
          certId: personalInformation.certId,
          customerName: personalInformation.customerName,
          mobileTelephone: this.personInfor.phone,
          yearIncome: this.personInfor.yearIncome,
          familyStatus: this.familyStatusList[this.familyStatusIdx].key,
          regionCode: this.regionCode,
          familyAdd: this.personInfor.detailAddress,
          eduExperience: this.eduexperienceList[this.eduexperienceIdx].key,
          eduDegree: this.edudegreeList[this.edudegreeIdx].key,
          marriage: marriage,
          relationShip: relationShip,
          coord: this.mulIndex.join(','),
          scene: 'cstScene',
          applyNo: this.applyNoVal,
          listName: 'baseInfo'
        }
        console.log(data)
       // 3.7接口 个人信息更新
        let posturl="/api/credit/customerInfoUpdate";
        yu.showLoading();
        this.interfaceRequest(posturl,data,"post",(res)=>{
          this.preventResubmit = true;
          yu.hideLoading();
          let resArr=res.data.data;
          if(resArr.returnCode=='Success'){
            if(this.isJump){
              //this.pageJump('personInformation/workInformation/workInformation?identity='+this.identity);
              this.queryApplyInfoCommit({
                "orderNo": this.orderNoVal, 
                "applyNo": this.applyNoVal,
                'routerTrue': true,
                'routerTo': 'personInformation/workInformation/workInformation?identity='+this.identity,
                'routerJumpWay': 'pageJump'
              }); //重新调'申请信息查询'接口
            }else{
              yu.showToast({
                title: '暂存成功！',
                image: './static/images/perfectInformation/success.svg',
                duration: 2000
              });
              this.queryApplyInfoCommit({
                'orderNo': this.orderNoVal,
                'applyNo': this.applyNoVal,
              }); //重新调'申请信息查询'接口
            }
          }else{
            yu.showToast({
              title: resArr.returnDesc,
              icon: 'none',
              duration: 3000
            });
          }
        },(err)=>{
          this.preventResubmit = true;
          yu.hideLoading();
          yu.showToast({
            title: '3.7个人信息出现问题，请联系管理员',
            icon: 'none',
            duration: 3000
          });
        });
      },
      // 3.2接口 获取个人信息
      getPersonalData(){
        let personalInformation=JSON.parse(localStorage.getItem('personalInformation'));
          let data={
            certType: personalInformation.certType,
            certId: personalInformation.certId,
            customerName: personalInformation.customerName,
            scene: 'cstScene',
            applyNo: this.applyNoVal
          }
          let posturl="/api/front/perCustomerInfo";
          yu.showLoading();
          this.interfaceRequest(posturl,data,"post",(res)=>{
            yu.hideLoading();
            let resData=res.data.data;
            if(resData.returnCode=='Success'){
              this.personInfor.phone=resData.mobileTelephone;
              this.personInfor.yearIncome=resData.yearIncome;
              this.personInfor.regionCode=resData.regionCode;
              this.personInfor.detailAddress=resData.familyAdd;
              this.familyStatusIdx=this.selectInfo(this.familyStatusList,resData.familyStatus);
              this.eduexperienceIdx=this.selectInfo(this.eduexperienceList,resData.eduExperience);
              this.edudegreeIdx=this.selectInfo(this.edudegreeList,resData.eduDegree);
              this.marriageIdx=this.selectInfo(this.marriageList,resData.marriage);
              this.relationShipIdx=this.selectInfo(this.relationShipList,resData.relationShip);  //未返回
              if(resData.coord){
                this.mulIndex=resData.coord.split(',');
              }
            }else{
              yu.showToast({
                title: resData.returnDesc,
                icon: 'none',
                duration: 3000
              });
            }
          },(err)=>{
            yu.hideLoading();
            yu.showToast({
              title: '3.2个人信息出现问题，请联系管理员',
              icon: 'none',
              duration: 3000
            });
          });
      },
      // 下拉框回显
      selectInfo(arr,parmName){
        if(parmName){
          let selIdx=0;
          arr.forEach((item, index) => {
            if(item.key==parmName){
              selIdx=index
            }
          });
          return selIdx
        }else{
          return 0
        }
      }
    },
    mounted(){
        
    }
  };


  
</script>

<style lang='scss' scoped>
  @import '@/static/css/professionwf.less';
  .uni-container{
    background-color: #f6f8f9;
    padding: 30rpx 0;
    .baseIn-content{
      padding-top: 270rpx;
    
    }
  }
  .marginT25{
    margin-top: 25rpx;
  }
</style>
