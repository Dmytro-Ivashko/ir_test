<template>
  <transition name="fade">
    <div class="vue-modal" v-show="open">
      <transition name="drop-in">
        <div class="vue-modal-inner" v-show="open">
          <div class="vue-modal-content">
            <slot />
            <h3 class="invite-others--header">Invite others</h3>
            <button type="button" class="close-button" @click="close">
              <icon-close />
            </button>
            <div class="input-email--container">
              <div class="invite-input-field--container">
                <input
                  v-model.lazy="guestEmail"
                  class="input-email-field"
                  :class="{ invalidEmail: !isValid && guestEmail }"
                  type="email"
                  placeholder="Enter people E-mails"
                />
                <button
                  @click="addedGuest"
                  type="submit"
                  class="input-email-field--submit-btn"
                  :disabled="!isValid"
                >
                  Add
                </button>
              </div>
              <div class="social-container">
                <p>or add from</p>
                <a
                  v-for="social in socialList"
                  :key="social.component"
                  :href="social.href"
                  class="invite-social--buttons"
                  target="_blank"
                >
                  <component :is="social.component" />
                </a>
              </div>
            </div>
            <hr class="hr-line" />

            <div class="guest-list--container">
              <div
                v-for="guest in guestList"
                :key="guest.email"
                class="guest-list-items"
              >
                <div class="guests-block">
                  <p class="guest--email">{{ guest.email }}</p>
                  <div class="role-selector--container">
                    <role-selector
                      :options="options"
                      @select="optionSelect(guest, $event)"
                      :selected="guest.role"
                    />
                  </div>
                </div>
                <button @click="removeGuest(guest)" class="remove-guest-button">
                  <icon-remove />
                </button>
              </div>
            </div>
            <div class="bottom-invite--container">
              <input
                type="text"
                class="bottom-invite--text-field"
                placeholder="Personal message (optional)"
              />
            </div>
            <div class="footer-invites--container">
              <p>{{ guestList.length }} recipients</p>
              <button
                @click="sendGuestList"
                type="submit"
                class="footer-invites--send-button"
              >
                Send
              </button>
            </div>
          </div>
        </div>
      </transition>
    </div>
  </transition>
</template>

<script>
import { onMounted, onUnmounted } from 'vue';
import RoleSelector from './RoleSelector.vue';
import IconClose from './svg-icons/Close.vue';
import IconYahoo from './svg-icons/Yahoo.vue';
import IconGmail from './svg-icons/Gmail.vue';
import IconAol from './svg-icons/Aol.vue';
import IconIcloud from './svg-icons/Icloud.vue';
import IconRemove from './svg-icons/Remove.vue';

export default {
  components: {
    RoleSelector,
    IconClose,
    IconYahoo,
    IconGmail,
    IconAol,
    IconIcloud,
    IconRemove,
  },
  data() {
    return {
      guestEmail: '',
      guestList: [],
      options: [
        {
          role: 'Guest',
          text: 'Default access level, can leave tributes, share media and stories',
        },
        {
          role: 'Administrator',
          text: 'Can control all aspects of the website, including moderating content posted by others and changing website settings',
        },
      ],
      socialList: [
        {
          component: 'icon-yahoo',
          href: 'https://ua.mail.yahoo.com',
        },
        {
          component: 'icon-gmail',
          href: 'https://gmail.com/',
        },
        {
          component: 'icon-aol',
          href: 'https://login.aol.com/',
        },
        {
          component: 'icon-icloud',
          href: 'https://icloud.com',
        },
      ],
    };
  },
  props: {
    open: {
      type: Boolean,
      required: false,
    },
  },
  setup(_, { emit }) {
    const close = () => {
      emit('close');
    };
    const handleKeyup = (event) => {
      if (event.keyCode === 27) {
        close();
      }
    };
    onMounted(() => document.addEventListener('keyup', handleKeyup));
    onUnmounted(() => document.removeEventListener('keydown', handleKeyup));
    return {
      close,
    };
  },

  created() {
    const emailsData = localStorage.getItem('emails-list');
    if (emailsData) this.guestList = JSON.parse(emailsData);
  },
  watch: {
    guestList: {
      deep: true,
      handler() {
        localStorage.setItem('emails-list', JSON.stringify(this.guestList));
      },
    },
  },
  computed: {
    isValid() {
      let emailReg = /\S+@\S+\.\S+/;
      return emailReg.test(this.guestEmail);
    },
  },
  methods: {
    optionSelect(guest, roleParams) {
      guest.role = roleParams.role;
    },
    addedGuest() {
      const newGuest = {
        email: this.guestEmail,
        role: 'Guest',
      };
      this.guestList.push(newGuest);
      this.guestEmail = '';
    },
    removeGuest(guest) {
      const newGuestList = this.guestList.filter((g) => g !== guest);
      this.guestList = newGuestList;
      this.setLocalStorageGuestList();
    },
    sendGuestList() {
      this.close();
      if(this.guestList.length) setTimeout(() => {
        // without toast library
        alert('Guest list success sended')
      }, 500); 
      this.guestList = [];
      localStorage.removeItem('emails-list');
    },
  },
};
</script>

<style>
body {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
.invite-others--header {
  width: 514px;
  height: 32px;
  font-family: 'Open Sans';
  font-style: normal;
  font-weight: 600;
  font-size: 24px;
  line-height: 32px;
  color: #3c1f1d;
}
.close-button {
  background: none;
  position: absolute;
  right: 0.63em;
  top: 0.6em;
  border: none;
  cursor: pointer;
  font-size: 24px;
  line-height: 32px;
}

.vue-modal {
  position: fixed;
  margin: auto auto;
  width: 100%;
  height: 100%;
  overflow-x: hidden;
  overflow-y: auto;
  background-color: rgba(100, 100, 100, 0.5);
  z-index: 1;
}

.vue-modal-inner {
  width: 598px;
  height: 776px;
  margin: 2em auto;
  background: #fff8ef;
  background-clip: padding-box;
  border-radius: 6px;
}

.vue-modal-content {
  position: relative;
  padding: 1em 1em 2em 1.5em;
  max-height: 743px;
}

.fade-leave-active {
  transition: opacity 0.5s;
}

.fade-leave-to {
  opacity: 0;
}

.drop-in-enter-from,
.drop-in-leave-to {
  opacity: 0;
  transform: translateY(-70px);
}

.drop-in-enter-active,
.drop-in-leave-active {
  transition: all 0.4s ease-out;
}

.invite-input-field--container {
  display: inline-flex;
  margin-top: 32px;
}

.input-email--container {
  width: 34.5em;
  height: 9.5em;
}

.input-email--container ::after {
  border: 1px solid #000;
}

.input-email-field {
  margin-right: 12px;
  padding: 8px 16px;
  width: 425px;
  height: 29px;
  background: #ffffff;
  border: 1px solid rgba(51, 51, 51, 0.16);
  border-radius: 6px;
  font-size: 16px;
  color: #acaaad;
}
.invalidEmail {
  border: 1px solid red;
}
.input-email-field label {
  font-family: 'Open Sans';
  font-style: normal;
  color: #acaaad;
  font-weight: 400;
  font-size: 16px;
  line-height: 24px;
}

.input-email-field--submit-btn {
  box-sizing: border-box;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  padding: 12px 24px;
  width: 79px;
  height: 48px;
  opacity: 0.56;
  border: 1px solid #d1cac1;
  border-radius: 6px;
  font-weight: 600;
  cursor: pointer;
}

.social-container {
  width: 298px;
  height: 49px;
  display: inline-flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 12px;
  color: #3c1f1d;
}

.invite-social--buttons {
  width: 40px;
  height: 40px;
  border: none;
  background-color: transparent;
  background-position: center;
  background-repeat: no-repeat;
  background-size: contain;
  padding: 0;
  box-sizing: border-box;
}

.hr-line {
  margin-left: -25px;
  width: 596px;
  background-color: #dfd8cf;
  border: 1px solid #dfd8cf;
}

.guest-list--container {
  width: 100%;
  height: 390px;
  overflow: auto;
}

.remove-guest-button {
  width: 20px;
  height: 27px;
  border: none;
  background-color: transparent;
  margin-left: 9px;
  cursor: pointer;
}

.guest-list-items {
  display: inline-flex;
  width: 100%;
  align-items: center;
  margin-top: 6px;
}

.guests-block {
  width: 93%;
  height: 50px;
  background: #f6efe6;
  border-radius: 6px;
  display: inline-flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 6px;
  position: relative;
  /*z-index: 0;*/
}

.guest--email {
  padding: 10px;
  color: #3c1f1d;
}

.bottom-invite--container {
  width: 100%;
  height: 96px;
}

.bottom-invite--text-field {
  padding: 12px 16px;
  width: 517px;
  height: 24px;
  background: #ffffff;
  border: 1px solid rgba(51, 51, 51, 0.16);
  border-radius: 6px;
  flex-grow: 1;
  margin-top: 23px;
}

.bottom-invite--text-field::placeholder {
  font-size: 16px;
  color: #acaaad;
}

.footer-invites--container {
  width: 98%;
  height: 50px;
  font-style: normal;
  font-weight: 400;
  font-size: 14px;
  line-height: 20px;
  color: #876a68;
  display: inline-flex;
  align-items: center;
  align-content: end;
  justify-content: flex-end;
}

.footer-invites--send-button {
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
}
.footer-invites--send-button:hover {
  cursor: pointer;
}

.role-selector--container {
  position: absolute;
  right: 0;
}

::-webkit-scrollbar {
  width: 5px;
}

::-webkit-scrollbar-track {
  box-shadow: inset 0 0 5px grey;
  border-radius: 10px;
}

::-webkit-scrollbar-thumb {
  background: #dfd8cf;
  border-radius: 10px;
}
</style>
