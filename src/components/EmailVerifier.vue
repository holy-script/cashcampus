<template>
  <div>
    <q-avatar
      rounded
      size="5rem"
      color="yellow"
      class="q-mt-lg q-mb-lg"
    >
      <img :src="!store.verified ? otpIcon : verifyIcon">
    </q-avatar>
    <br>
    <q-btn
      :label="!store.verified ? `Verify Code` : `Verified!`"
      color="deep-orange"
      @click="verify"
      :disable="store.verified"
    />
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";
import { useQuasar } from "quasar";
import { useRouter } from "vue-router";
import { useStore } from "stores/app";
import otpIcon from "assets/icons/icons8-password-100.png";
import verifyIcon from "assets/icons/icons8-verified-account-100.png";
import { api } from "src/boot/axios";

export default defineComponent({
  name: "EmailVerifier",
  setup() {
    const $q = useQuasar();
    const router = useRouter();
    const store = useStore();
    const otpVal = ref("");

    const verify = () => {
      $q.dialog({
        title: "OTP Verification",
        message: "Please input the code sent to your email address",
        dark: true,
        persistent: true,
        cancel: true,
        prompt: {
          filled: true,
          model: otpVal,
          type: "text",
          isValid: (val) => /^[0-9]{6,6}$/.test(val),
        },
      }).onOk((data) => {
        api
          .post("/api/verify", {
            otp: otpVal.value,
          })
          .then((res) => {
            $q.notify({ message: res.data.message, classes: ["unbounded"] });
            store.verify();
          })
          .catch(
            (err) =>
              $q.notify({
                message: err.response.data.message,
                classes: ["unbounded"],
              }) && console.log(err.response.status)
          );
      });
    };

    return {
      store,
      verify,
      otpIcon,
      verifyIcon,
    };
  },
});
</script>

<style lang="sass">
</style>
