<template>
  <header
    class="bg-black text-white flex items-center justify-between px-4 py-2 relative border-b-3 border-b-[#2C303A]"
  >
    <!-- 左側 Tabs（登入後顯示） -->
    <div class="flex items-center" v-if="authStore.idToken">
      <div class="flex ml-2 space-x-px">
        <button
          v-for="tab in tabs"
          :key="tab"
          @click="setActiveTab(tab)"
          class="relative px-5 py-3 text-lg bg-gray-800 text-white hover:text-white focus:outline-none first:rounded-l-md last:rounded-r-md"
        >
          {{ tab }}
          <span
            class="absolute bottom-0 left-0 h-1 bg-green-400 transition-all duration-300"
            :class="activeTab === tab ? 'w-full' : 'w-0'"
          ></span>
        </button>
      </div>
    </div>

    <!-- 中間搜尋欄 -->
    <div
      v-if="!['search', 'search-category'].includes(route.name)"
      :class="[ 'flex-grow flex px-2', authStore.idToken ? 'justify-center' : 'justify-start' ]"
    >
      <div class="relative w-60">
        <i
          class="fas fa-search absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400"
        ></i>
        <input
          type="text"
          placeholder="Search CodePen..."
          class="bg-gray-800 text-white pl-10 pr-4 py-2 rounded-md w-full placeholder-gray-400 focus:outline-none transition-all duration-300"
          @focus="searchFocused = true"
          @blur="searchFocused = false"
        />

        <!-- 點擊時顯示選單 -->
        <div
          v-if="searchFocused"
          class="absolute left-0 mt-2 bg-gray-900 text-white min-w-full rounded-md shadow-lg border border-gray-700 z-50 flex space-x-2 px-2 py-2"
        >
          <button class="bg-gray-800 px-3 py-1 rounded hover:bg-gray-700" @click="goToPath('/your-work')">
            Your Work
          </button>
          <button class="bg-gray-800 px-3 py-1 rounded hover:bg-gray-700">
            Pens
          </button>
          <!-- <button class="bg-gray-800 px-3 py-1 rounded hover:bg-gray-700">Projects</button> -->
          <!-- Vivi: 留待確認要做專案模式再加上 -->
          <button class="bg-gray-800 px-3 py-1 rounded hover:bg-gray-700">
            Collections
          </button>
          <!-- Vivi: 可能會是第二階段要做的功能 所以先保留按鈕 -->
        </div>
      </div>
    </div>

    <!-- 右側登入或使用者資訊 -->
    <div class="flex items-center space-x-3">
      <!-- 未登入 -->
      <template v-if="!authStore.idToken">
        <button
          class="bg-green-500 text-black px-4 py-2 rounded-md hover:bg-green-400 font-semibold"
          @click="goToPath('/signup')"
        >
          Sign Up
        </button>
        <button
          class="bg-gray-700 px-4 py-2 rounded-md hover:bg-gray-600 font-semibold"
          @click="goToPath('/login')"
        >
          Log In
        </button>
      </template>

      <!-- 已登入 -->
      <template v-if="authStore.idToken">
        <!-- 收藏按鈕 -->
        <!-- Vivi: 我們目前先沒有要做收藏功能 -->
        <!-- <button
          class="flex items-center justify-center w-12 h-12 bg-gray-800 text-gray-400 hover:text-white rounded-md hover:bg-gray-700 transition-colors duration-200"
        >
          <i class="fas fa-bookmark text-2xl"></i>
        </button> -->

        <!-- 使用者頭像與下拉選單 -->
        <div class="relative" ref="menuRef">
          <img
            src="https://www.gravatar.com/avatar/00000000000000000000000000000000?d=mp&f=y"
            alt="user avatar"
            class="w-12 h-12 rounded-md cursor-pointer object-cover"
            @click="toggleMenu"
          />
          <div
            v-if="showMenu"
            class="absolute right-0 mt-2 w-56 bg-gray-900 text-white rounded-md shadow-lg border border-gray-700 z-50 overflow-hidden"
          >
            <ul class="flex flex-col text-sm">
              <li>
                <button class="px-4 py-2 text-left hover:bg-gray-700 w-full" @click="goToPath('/your-work')">
                  Your Work
                </button>
              </li>
              <li>
                <button class="px-4 py-2 text-left hover:bg-gray-700 w-full" @click="goToPath(`/user/${authStore.userId || 'me'}`)">
                  Profile
                </button>
              </li>

              <hr class="border-gray-700 my-1 mx-4" />

              <li class="flex items-center px-4 py-2 hover:bg-gray-700 cursor-pointer" @click="goToPath('/pen')">
                <i class="fas fa-pen mr-2 w-4 text-gray-400"></i>
                <span>New Pen</span>
              </li>

              <hr class="border-gray-700 my-1 mx-4" />

              <li
                class="flex items-center px-4 py-2 hover:bg-gray-700 cursor-pointer"
                @click="goToPath('/settings')"
              >
                <i class="fas fa-cog mr-2 w-4 text-gray-400"></i>
                <span>Settings</span>
              </li>

              <li
                class="flex items-center px-4 py-2 hover:bg-red-600 text-red-400 cursor-pointer"
                @click="handleLogout"
              >
                <i class="fas fa-sign-out-alt mr-2 w-4 cur"></i>
                <span>Log Out</span>
              </li>
            </ul>
          </div>
        </div>
      </template>
    </div>
  </header>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import { useRoute, useRouter } from "vue-router";
import { useAuthStore } from "../stores/useAuthStore";
import { signOut } from "firebase/auth";
import { auth } from "../config/firebase";

const authStore = useAuthStore();
const route = useRoute();
const router = useRouter();

const showMenu = ref(false);
const menuRef = ref(null);
const searchFocused = ref(false);

const tabs = ["Your Work", "Following", "Trending"];
const activeTab = ref("Your Work");

const toggleMenu = () => {
  showMenu.value = !showMenu.value;
};

const setActiveTab = (tab) => {
  activeTab.value = tab;

  if (tab === "Your Work") router.push("/your-work");
  else if (tab === "Following") router.push("/following");
  else if (tab === "Trending") router.push("/trending");
};

const goToPath = (path) => {
  showMenu.value = false;

  // 同步 activeTab 狀態
  if (path === "/your-work") activeTab.value = "Your Work";
  else if (path === "/following") activeTab.value = "Following";
  else if (path === "/trending") activeTab.value = "Trending";
  else activeTab.value = "";

  router.push(path);
};

const handleLogout = async () => {
  await signOut(auth);
  authStore.clearToken();
  router.push("/");
};

const handleClickOutside = (event) => {
  if (menuRef.value && !menuRef.value.contains(event.target)) {
    showMenu.value = false;
  }
};

onMounted(() => {
  document.addEventListener("click", handleClickOutside);
});
onUnmounted(() => {
  document.removeEventListener("click", handleClickOutside);
});
</script>
