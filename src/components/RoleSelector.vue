<template>
  <div class="role-selector">
    <div class="selected-block" @click="isRoleSelectorVisible = !isRoleSelectorVisible">
      <span>{{ selected }}</span>
      <icon-up-arrow v-if="isRoleSelectorVisible" class="arrow" />
      <icon-down-arrow v-else class="arrow" />
    </div>
    <div v-if="isRoleSelectorVisible" class="role--options">
      <div
        v-for="option in options"
        class="role--container"
        :key="option.role"
        @click="selectRole(option)"
      >
        <icon-checked-arrow
          v-if="option.role === selected"
          class="role--active"
        />
        <div>
          <span class="role--title">{{ option.role }}</span
          ><br />
          <span class="role--text">{{ option.text }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import IconCheckedArrow from './svg-icons/CheckedArrow.vue';
import IconDownArrow from './svg-icons/DownArrow.vue';
import IconUpArrow from './svg-icons/UpArrow.vue';

export default {
  name: 'RoleSelect',
  components: {
    IconCheckedArrow,
    IconDownArrow,
    IconUpArrow,
  },
  props: {
    options: {
      type: Array,
      default() {
        return [];
      },
    },
    selected: {
      type: String,
      default: '',
    },
  },
  data() {
    return {
      isRoleSelectorVisible: false,
    };
  },
  methods: {
    selectRole(option) {
      this.$emit('select', option);
      this.hideRoleSelector()
    },
    hideRoleSelector() {
      this.isRoleSelectorVisible = false;
    },
  },
  mounted() {
    document.addEventListener('click', this.hideRoleSelector, true);
  },
  beforeUnmount() {
    document.removeEventListener('click', this.hideRoleSelector);
  },
};
</script>

<style>
.role-selector {
  cursor: pointer;
}
.role--options {
  position: absolute;
  width: 318px;
  height: 188px;
  background-color: #fff8ef;
  box-shadow: 4px 4px 24px rgb(0 0 0 / 16%);
  border-radius: 6px;
  right: 21px;
  top: 14px;
  z-index: 1;
}
.selected-block {
  display: inline-flex;
  align-items: baseline;
  color: #3c1f1d;
  line-height: 20px;
}
.arrow {
  padding:0 13px 0 5px;
}
.role--title {
  color: #3c1f1d;
  font-size: 16px;
  line-height: 20px;
  font-weight: 600;
}
.role--text {
  font-size: 12px;
  line-height: 16px;
  color: #876a68;
  text-align: left;
}
.role--container {
  background: #fff8ef;
  padding: 15px 16px 16px 40px;
  border-radius: 6px;
}
.role--container:hover {
  background: #f6efe6;
}
.role--active {
  position: relative;
  top: 19px;
  left: -20px;
}
</style>
