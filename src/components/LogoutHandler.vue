<template>
  <q-page-sticky
    position="bottom-left"
    :offset="[10, 10]"
  >
    <div
      v-if="store.loggedIn"
      style="position: relative;"
    >
      <q-btn
        id="logout"
        @click="confirm"
        color="blue"
      >
        <q-icon
          :name="`img:${logoutIcon}`"
          size="md"
        />
      </q-btn>
    </div>
  </q-page-sticky>

</template>

<script>
import { defineComponent } from "vue";
import { useQuasar } from "quasar";
import { useRouter } from "vue-router";
import { useStore } from "stores/app";
import logoutIcon from "assets/icons/icons8-logout-rounded-100.png";
import { api } from "boot/axios";

export default defineComponent({
  name: "LogoutHandler",
  setup() {
    const store = useStore();
    const $q = useQuasar();
    const router = useRouter();

    function confirm(evt) {
      $q.dialog({
        dark: true,
        title: "Confirm",
        message: "Are you sure about logging out?",
        cancel: true,
        persistent: true,
        class: ["unbounded"],
      }).onOk(() => {
        api.post("/auth/logout").then((res) => {
          $q.notify({
            message: res.data.message,
            color: "dark",
            progress: true,
          });
          store.logOut();
          router.push({
            name: "Home",
          });
        });
      });
    }

    return {
      store,
      confirm,
      logoutIcon,
    };
  },
});
</script>

<style lang="sass">
#logout
  width: 3rem
  height: 3rem
  border-radius: 7px
  background: #ff9634
  color: #001D3D

#bottomLeft
  width: 8px
  position: absolute
  bottom: 4.75px
  left: 1px
  background: #001D3D
  transform-origin: bottom
  transform: rotate(135deg)

#topRight
  width: 8px
  position: absolute
  top: 4.75px
  right: 1px
  transform-origin: top
  transform: rotate(315deg)
</style>
