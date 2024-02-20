<template>
  <div>
    <p>
      <label for="phone">*휴대폰번호</label>
      <input
        v-model="mbr_hp"
        id="JOIN_HP"
        type="text"
        name="mbr_hp"
        style="ime-mode: disabled"
        maxlength="14"
        @input="formatPhoneNumber"
        @keypress="checkCode"
      />
      <button>인증번호 발송</button>
    </p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      rst: '',
    };
  },
  methods: {
    /* 휴대폰번호 포맷 변경 */
    formatPhoneNumber() {
      let cleanedPhoneNumber = this.mbr_hp.replace(/[^0-9]/g, '');

      // "-"을 적절한 위치에 추가하여 핸드폰 번호 형식으로 만들기
      let formattedPhoneNumber = '';
      for (let i = 0; i < cleanedPhoneNumber.length; i++) {
        if (i === 3 || i === 7) {
          formattedPhoneNumber += '-';
        }
        formattedPhoneNumber += cleanedPhoneNumber[i];
      }

      // formattedPhoneNumber을 다시 mbr_hp에 할당
      this.mbr_hp = formattedPhoneNumber;
    },
  },
};
</script>
