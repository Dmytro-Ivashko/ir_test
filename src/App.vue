<template>
  <div class="wrapper">
    <button @click="isOpen = true" class="open-modal-button">Invite Now</button>
    <invite-modal :open="isOpen" @close="isOpen = !isOpen" />
  </div>
</template>

<script>
import { ref } from 'vue';
import InviteModal from './components/InviteModal.vue';

export default {
  name: 'App',
  components: {
    InviteModal,
  },
  setup() {
    const isOpen = ref(false);
    return { isOpen };
  },
};
</script>

<style lang="scss">
#app {
  font-family: 'openSansRegular';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
.wrapper {
  display: flex;
  height: 100vh;
  justify-content: center;
  align-items: center;
}

.open-modal-button {
  box-sizing: border-box;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  padding: 12px 24px;
  width: 160px;
  height: 48px;
  background: linear-gradient(90deg, #64362d 0%, #82544b 100%);
  border: 1px solid #3e1007;
  border-radius: 6px;
  margin-left: 25px;
  color: #fff;
  font-size: 18px;
  font-weight: 400;
  cursor: pointer;
}
</style>
