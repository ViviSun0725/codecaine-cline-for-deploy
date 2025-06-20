<script setup>
import { computed, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import Close from '../icons/Close.vue';

const route = useRoute()
const router = useRouter()

// 判斷 modal
const modalType = computed(() => route.query.modal)
const showModal = computed(() => modalType.value === 'login' || modalType.value === 'signup')

// 忘記密碼區塊開關
const isForgetPassword = ref(false)
const toggleIsForgetPassword = () => {
  isForgetPassword.value = !isForgetPassword.value
}

// 關閉 modal（清除網址 query）
const close = () => {
  const newQuery = { ...route.query }
  delete newQuery.modal
  router.replace({ path: route.path, query: newQuery })
}

// 切換登入/註冊
const handleToLogIn = () => {
  error.value = ""
  router.push({ path: route.path, query: { ...route.query, modal: 'login' } })
}
const handleToSignUp = () => {
  error.value = ""
  router.push({ path: route.path, query: { ...route.query, modal: 'signup' } })
}

// 輸入框綁定
const loginAccount = ref('')
const loginPassword = ref('')

const emailForResetPassword = ref('')

const signUpName = ref('')
const signUpUserName = ref('')
const signUpEmail = ref('')
const signUpPassword = ref('')

// 錯誤訊息
const error = ref("")

</script>

<template>
  <div
    v-if="showModal"
    class="fixed inset-0 bg-black/50 flex justify-center items-start z-50"
    @click.self="close"
  >
    <div class="relative bg-white rounded-xl mt-8 w-80 shadow-lg flex flex-col min-w-lg min-h-[70vh] max-h-[80vh] pt-7.5 px-15 overflow-y-auto">
      <div class="absolute top-0 left-0 right-0 h-2 rounded-t-xl bg-green-400"></div>
      <button class="absolute top-4 right-4 text-gray-500 hover:text-gray-700 z-10" @click="close">
        <Close class="w-3 h-3 cursor-pointer" />
      </button>

      <div>
        <h2 class="font-bold mb-4 text-slate-700 text-center text-4xl">Hold up!</h2>
        <p class="text-slate-400 text-center text-sm mb-4 tracking-tight">
          You’ll have to Log In or Sign Up (for free!) to save your Pen.<br />
          Don’t worry! All your work will be saved to your account.
        </p>
      </div>

      <div v-show="error">
        <p class="bg-red-500 text-white text-sm p-2 my-1">{{ error }}</p>
      </div>

      <!-- 登入 -->
      <main v-if="modalType === 'login'" class="flex-1">
        <form class="flex flex-col gap-4 text-zinc-900 text-sm">
          <div>
            <label for="account" class="text-slate-700">Username or Email</label>
            <input
              type="text"
              class="p-2.5 mt-1 bg-gray-100 w-full rounded-md h-11 outline-none focus:ring-2 focus:ring-stone-300"
              v-model="loginAccount"
            />
          </div>
          <div>
            <label for="password" class="text-slate-700">Password</label>
            <input
              type="password"
              class="p-2.5 mt-1 bg-gray-100 w-full rounded-md h-11 outline-none focus:ring-2 focus:ring-stone-300"
              v-model="loginPassword"
            />
          </div>
          <button type="button" class="w-full h-10 bg-emerald-400 rounded-md">Log In</button>
          <a href="#" class="text-slate-400 text-center" @click.prevent="toggleIsForgetPassword">Forget Password?</a>
        </form>

        <div
          class="rounded bg-[#2C303A] my-4 overflow-hidden transition-all duration-500 ease-in-out"
          :class="isForgetPassword ? 'max-h-[500px]' : 'max-h-0'"
        >
          <form class="p-5">
            <h2 class="mb-2 text-white font-bold">Reset Your Password</h2>
            <label for="resetEmail" class="text-slate-400">Username or Email</label>
            <input
              type="text"
              id="resetEmail"
              placeholder="your@email.com"
              class="p-2.5 mt-1 mb-3 bg-gray-100 w-full rounded-md h-12 outline-none focus:ring-2 focus:ring-stone-300 text-black"
              v-model="emailForResetPassword"
            />
            <button type="button" class="w-full h-10 bg-slate-500 text-white hover:bg-slate-400 rounded-md">
              Send Password Reset Email
            </button>
          </form>
        </div>
      </main>

      <!-- 註冊 -->
      <main v-else-if="modalType === 'signup'" class="flex-1 mb-4">
        <form class="flex flex-col gap-3 text-zinc-900 text-sm">
          <div>
            <label for="name" class="text-slate-700">Name</label>
            <input
              id="name"
              type="text"
              class="p-2.5 mt-1 bg-gray-100 w-full rounded-md h-11 outline-none focus:ring-2 focus:ring-stone-300"
              v-model="signUpName"
            />
          </div>
          <div>
            <label for="username" class="text-slate-700">Choose a username</label>
            <input
              id="username"
              type="text"
              class="p-2.5 mt-1 bg-gray-100 w-full rounded-md h-11 outline-none focus:ring-2 focus:ring-stone-300"
              v-model="signUpUserName"
            />
            <span class="text-xs text-gray-300">codepen.io/username</span>
          </div>
          <div>
            <label for="email" class="text-slate-700">Email</label>
            <input
              type="text"
              id="email"
              class="p-2.5 mt-1 bg-gray-100 w-full rounded-md h-11 outline-none focus:ring-2 focus:ring-stone-300"
              v-model="signUpEmail"
            />
          </div>
          <div>
            <label for="password" class="text-slate-700">Choose Password</label>
            <input
              type="password"
              id="password"
              class="p-2.5 mt-1 bg-gray-100 w-full rounded-md h-11 outline-none focus:ring-2 focus:ring-stone-300"
              v-model="signUpPassword"
            />
          </div>
          <button type="button" class="w-full h-10 bg-emerald-400 rounded-md">Sign Up</button>
        </form>
      </main>

      <!-- 底部切換連結 -->
      <div class="relative w-full text-zinc-900 bg-white text-center p-5 before:content-[''] before:absolute before:top-0 before:h-[1px] before:bg-zinc-200 before:left-[-60px] before:right-[-60px]">
        <a v-if="modalType === 'login'" href="#" @click.prevent="handleToSignUp">
          Need to create an account?
          <span class="text-teal-700 text-sm">Sign Up for CodePen</span>
        </a>
        <a v-else-if="modalType === 'signup'" href="#" @click.prevent="handleToLogIn">
          Already have an account?
          <span class="text-teal-700 text-sm">Log In</span>
        </a>
      </div>
    </div>
  </div>
</template>
