<template>
  <div
    class="layout transition-all duration-400 ease-in-out"
    :style="{ gridTemplateColumns: layoutColumns }"
  >
    <MainSidebar class="sidebar" @toggle="toggleSidebar" />
    <SubHeader class="header" />
    <RouterView v-slot="{ Component }">
      <component :is="Component" class="content" />
    </RouterView>
    <SubFooter class="footer" />

    <!-- 如果網址是 details 且 query.modal 存在，就顯示 modal -->
    <PenDetailModal
      v-if="modalStore.showDetailModal"
      :pen-id="modalStore.penId"
      :from="modalStore.from"
      @close="modalStore.closeModal"
    />
  </div>
  <!--routerview 內容一定要寫在div class="content"裡面!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!! 不然會跑掉-->
</template>

<script setup>
import { useModalStore } from "@/stores/useModalStore";
import { computed, ref, watch } from "vue";

import SubHeader from "@/components/SubHeader.vue";
import SubFooter from "@/components/SubFooter.vue";
import MainSidebar from "@/components/MainSidebar.vue";
import PenDetailModal from "@/components/PenDetailModal.vue";

const modalStore = useModalStore()

const isSidebarOpen = ref(
	localStorage.getItem("sidebarOpen") === "false" ? false : true
);

const layoutColumns = computed(() =>
	isSidebarOpen.value ? "160px 1fr" : "12px 1fr"
);

function toggleSidebar() {
	//切換true or false
	isSidebarOpen.value = !isSidebarOpen.value;
}

watch(isSidebarOpen, (val) => {
	localStorage.setItem("sidebarOpen", val);
});
</script>

<style scoped>
.layout {
	display: grid;
	grid-template-areas:
		"sidebar header"
		"sidebar content"
		"sidebar footer";
	grid-template-rows: 75px 1fr auto; /* 先寫死表示大概畫面 再自行修正*/
	min-height: 100vh; /* 讓畫面高度至少是100vh */
	overflow: hidden;
}

.sidebar {
	grid-area: sidebar;
}

.header {
	grid-area: header;
}

.content {
	grid-area: content;
	overflow-y: auto;
}

.footer {
	grid-area: footer;
}
</style>
