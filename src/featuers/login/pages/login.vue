<template>
  <div class="login">
    <div class="header">
      <img class="logo" src="../../../assets/ant_logo.png" width="200" height="200">
    </div>
    <group class="weui_cells_form">
      <x-input name="mobile" placeholder="手机号" keyboard="number" v-model="mobile" is-type="china-mobile" required  ref="user_phone"></x-input>
      <x-input name="code" placeholder="验证码" keyboard="number" class="weui_vcode" v-model="code" style="padding:0 15px;">
      <x-button slot="right" :text="btnValue" :disabled="currentDown" type="primary"
                  style="width:118px;height:49px;border-radius:0;" @click.native="_sendCode"></x-button>
      </x-input>
    </group>
    <div style="padding:.5rem">
        <x-button type="primary" style="margin-top: 20px;height: 50px;line-height: 50px;" @click.native="startLogin">登录</x-button>
    </div>  
  </div>
</template>

<script>
  import {Group, Cell, XInput, XButton} from 'vux'
  import {mapActions, mapGetters} from 'vuex'

  export default {
    components: {
      Group, XInput, XButton, Cell
    },
    data () {
      return {
        mobile: '',
        code: '',
        btnValue: '获取验证码',
        currentDown: false,
      }
    },
    methods: {
      ...mapActions(["getRegisterCode", "getRegisterCodeResult"]),
      _sendCode(e){
        if(!this.$refs.user_phone.valid){
          this.$vux.toast.show({text: '错误的手机号',type:'warn',time:'1000'})
          return
        }
        this.getRegisterCode({
          phone: this.mobile
        })
        let time = 60
        let self = this
        this.currentDown = true
        let timeDown = setInterval(function () {
          time--;
          self.btnValue = '已发送(' + time + ')';
          if (time == '0') {
            clearInterval(timeDown)
            self.btnValue = '获取验证码'
            self.currentDown = false
          }
        }, 1000);
      },
      startLogin(){
        if(this.code.length != 6){
          this.$vux.toast.show({text: '错误的验证码',type:'warn',time:'1000'})
          return
        }
        this.getRegisterCodeResult({
          phone: this.mobile,
          code: this.code,
        })
      }
    }
  }
</script>

<style lang="less" scoped>
.login{
  .header{
    padding:2.5rem 0 .5rem;
    text-align: center;
    img{
      width:8rem;
      height:8rem;
    }
  }
  .weui_vcode .weui_icon_clear:before {
    top: -6px;
    position: relative;
  }
}
</style>