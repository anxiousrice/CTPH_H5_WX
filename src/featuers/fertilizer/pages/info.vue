<template>
  <view-box class="fertilizerInfo">
    <x-header 
      slot="header" 
      title="测土配肥"
      style="width:100%;position:absolute;left:0;top:0;z-index:100;" 
      :left-options="{showBack: true,backText:'返回'}" 
    ></x-header>
    <div style="padding-top:46px;">
      <group title="完善信息后会为您提供免费的测土服务">
        <selector title="种植作物" :options="cropList" v-model="crop_id"></selector>
        <x-input title="作物品种 " name="crop_type" type="text" v-model="crop_type" placeholder="请填写作物品种" ref="crop_type" required></x-input>
        <x-input title="上季产量 " name="last_yeid" type="number" v-model="last_yeid" :is-type="isNum" placeholder="上季产量应为整数" ref="last_yeid" required>
          <p slot="right" style="height:100%;width:4rem;float:right">公斤/亩</p>
        </x-input>
        <selector title="测土机构" :options="shopList" v-model="shop_id" ></selector>
      </group>
      <div style="padding:1rem;">
        <x-button type="primary" action-type="button" @click.native="_finish">确定</x-button>
      </div>
    </div>
  </view-box>
</template>

<script>
import { Group, Cell ,XInput,XButton,Selector,XHeader,ViewBox } from 'vux'
import { mapActions,mapGetters } from 'vuex'

export default {
  components: {
    Group,XInput,XButton,Selector,XButton,XHeader,Cell,ViewBox
  },
  data () {
    return {
      cropList:[{key: '2', value: '单季稻'}, {key: '3', value: '早稻'}, {key: '4', value: '晚稻'}],
      last_yeid:"",
      crop_type:'',
      crop_id:'2',
      isNum:(value)=>{
        return {
          valid: Number.isInteger(value),
          msg: '不为整数'
        }
      }
    }
  },
  computed:{
    ...mapGetters(['Shop','Query']),
    //输入的验证
    disable(){
      return (this.$refs.crop_type.valid && this.$refs.last_yeid.valid)
    },
    shop_id(){
      return (this.shopList[0] ? this.shopList[0].key : '')
    },
    shopList(){
      let shopList = []
      this.Shop.forEach((value, index, array) => {
        shopList.push({'key':value.shop_id,'value':value.shop_name})
      })
      return shopList;
    }
	},
  beforeRouteEnter(to, from, next){
    next(vm => {
      vm.getShop()
      .then(()=>{
        vm.shop_id = vm.shopList[0].key
      })
    })
  },
  methods:{
    ...mapActions(['postFertilizerApply','getShop']),
    _finish(){
      if(!this.disable){
        this.$vux.toast.show({text: '参数不正确',type:'warn',time:'1000'})
        return
      }
      this.postFertilizerApply({
        "crop_id":this.crop_id,
        "crop_type":this.crop_type,
        "last_yeid":this.last_yeid,
        "shop_id":this.shop_id
      })
      .then((res)=>{
        this.$vux.toast.show({text: '提交成功'})
        setTimeout(()=>{
          this.$router.replace(`/fertilizer/wait/${this.Query.type}/`)
        },1000)
      })
    }
  }
}
</script>
<style>
.fertilizerInfo .weui_select{
   padding-right:0px !important;
}
</style>