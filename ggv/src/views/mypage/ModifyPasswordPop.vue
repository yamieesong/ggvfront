<template>
    <dl>
      <dt>
        <strong>{{title}}</strong>
      </dt> 
      <dd class="content">
  
        <div class="btn_areaC mt30">
          
          <a class="btnType blue" id="btnSaveNotice" name="btn" @click="saveNotice()"
            ><span>{{ btnNm }}</span></a
          >
          <a class="btnType gray" id="btnClose" name="btn" @click="closePop()"
            ><span>취소</span></a
          >
        </div>
      </dd>
    </dl>
</template>


<script>
import { closeModal } from "jenesius-vue-modal";

export default {
    props: { 
      title: String,
    },
    data: function () {
        return {
            items: [],
            btnNm: "확인",
            loginId: "",
            loginNm: "",
        };
    },
    mounted() {
      //let loginInfo = this.$store.state.loginInfo;
      //console.log(loginInfo)
    },
    methods: {
        closePop: function(){
          closeModal();
        },
    }
};
</script>