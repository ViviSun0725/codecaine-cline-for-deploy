<script setup>
import { inject, ref, watch } from 'vue';
import Arrow from '../../assets/arrow.svg';
import { useWorkStore } from '@/stores/workStore';
import { storeToRefs } from 'pinia'

const title = inject('title')
const emit = defineEmits(['close', 'update:cdns', 'update:links'])
const tabs = [
  { label: 'HTML', key: 'html' },
  { label: 'CSS', key: 'css' },
  { label: 'JS', key: 'js' },
  { label: 'Pen Detail', key: 'detail', gapBefore: true },
  { label: 'Privacy', key: 'privacy' },
  { label: 'Behavior', key: 'behavior' },
  { label: 'Editor', key: 'editor' },
  { label: 'Template', key: 'template' },
  { label: 'Screenshot', key: 'screenshot' },
]
const workStore = useWorkStore()
const { currentWork } = storeToRefs(workStore)
const { toggleAutoSave, toggleAutoPreview } = workStore;
console.log(currentWork.value.isAutoSave);
const activeTab = ref('html')
const cdnInput = ref('')
const linkInput = ref('')
const props = defineProps({
  cdns: {
    type: Array,
    default: () => []
  },
  links: {
    type: Array,
    default: () => []  
  },


});
const cdns = ref([...props.cdns])
const links = ref([...props.links])

watch(cdns, (newValue) => {
  emit('update:cdns', newValue)
}, { deep: true, immediate: true })
watch(links, (newValue) => {
  emit('update:links', newValue)
}, { deep: true, immediate: true })

const srcDoc = ref('')

const isValidUrl = (url) => /^https?:\/\/.+/.test(url);
const addCDN = () => {
  const url = cdnInput.value.trim();
  if (!isValidUrl(url)) {
    alert('請輸入有效的 CDN URL（必須以 http 或 https 開頭）');
    return;
  }  if (cdns.value.includes(url)) {
    alert("這個 CDN 已經加入了！");
    return;
  }
  cdns.value.push(url);
  cdnInput.value = '';
}
const removeCDN = (index) => {
  cdns.value.splice(index, 1)
}
const addLink = () => {
  const url = linkInput.value.trim();
  if (!isValidUrl(url)) {
    alert('請輸入有效的 link URL（必須以 http 或 https 開頭）');
    return;
  }  if (links.value.includes(url)) {
    alert("這個 link 已經加入了！");
    return;
  }
  links.value.push(url);
  linkInput.value = '';
}
const removeLink = (index) => {
  links.value.splice(index, 1)
}

</script>
<template>
  <div class="fixed md:translate-y-4/7 translate-y-1/2 left-1/2 -translate-x-1/2 md:h-4/5 h-11/12 md:w-175 max-w-185 w-full pb-20 px-4">
    <div  class=" flex flex-col rounded-sm  bg-[#131417] text-white border-4 border-gray-600 h-full w-full " >
      <div class="mx-4 pt-4">
        <div class="flex justify-between">
          <span class="after:content-[''] after:border-b-2 after:border-green-400 after:absolute after:-bottom-0.5 after:left-0 after:w-full relative pb-1">Pen Settings</span>
          <button type="button" @click.prevent="emit('close')" class="right-1 bg-[#424655] w-7 flex justify-center h-4">
            <svg class="w-2.5" viewBox="0 0 100 100">
              <path fill="white"
                d="M96.8 83.7L63.1 50l33.7-33.7c3.6-3.6 3.6-9.4 0-13.1s-9.5-3.6-13.1 0L50 36.9 16.3 3.2C12.7-.4 6.9-.4 3.2 3.2s-3.6 9.5 0 13.1L36.9 50 3.2 83.7c-3.6 3.6-3.6 9.4 0 13.1s9.5 3.6 13.1 0L50 63.1l33.7 33.7c3.6 3.6 9.4 3.6 13.1 0s3.6-9.5 0-13.1z">
              </path>
            </svg>
          </button>
        </div>
        <div class="w-full h-0.5 bg-gray-600 mb-4"></div>
      </div>
      <div class="md:flex h-full px-4 block overflow-y-auto ">
        <ul class="md:w-1/4 flex md:flex-col md:overflow-y-auto pl-2 md:pl-0">
          <li v-for="tab in tabs" :key="tab.key" tabindex="0" @click.prevent="activeTab = tab.key"  class="whitespace-nowrap transition hover:bg-gray-600 px-1.5 md:pl-4 ml-1 md:ml-0 relative -left-4 before:content-none md:before:content-['']  before:absolute before:w-1 before:h-full before:left-0 focus:before:bg-green-500" :class="{ 'before:bg-green-500': activeTab === tab.key, 'md:mt-4': tab.gapBefore,  'bg-gray-600': activeTab === tab.key}">
            {{  tab.label }}
          </li>
        </ul>
        <div class="md:hidden w-full flex my-3 md:before:content-none before:content-[''] before:relative before:w-full before:h-0.5 before:bg-gray-700 before:mt-2"></div>
        <div class="md:w-3/4 w-full h-11/12 overflow-y-auto">

          <div v-show="activeTab === 'html'" class=" w-full flex flex-col gap-4">
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="">
                <label for="HTML Preprocessor">HTML Preprocessor</label>
              </div>
              <div class="relative">
                <select
                  class="appearance-none w-full border border-gray-300 rounded-sm px-4 py-2 bg-white shadow-sm focus:outline-none focus:ring-2 focus:ring-green-500 text-gray-500">
                  <option value="" disabled selected>None</option>
                  <option value="Haml">Haml</option>
                  <option value="Markdown">Markdown</option>
                  <option value="Slim">Slim</option>
                  <option value="Pug">Pug</option>
                </select>
                <div class="pointer-events-none absolute right-3 top-1/2 transform -translate-y-1/2 flex flex-col justify-around text-gray-500 text-xs leading-tight h-1/2">
                  <img :src="Arrow" class="w-3 rotate-180" alt="">
                  <img :src="Arrow" class="w-3" alt="">
                </div>
              </div>
            </div>
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="">
                <label for="Add Class(es) to <html>">Add Class(es) to &lt;html&gt;</label>
              </div>
              <div class="relative">
                <input
                  class="appearance-none w-full border border-gray-300 rounded-sm px-4 py-2 bg-white shadow-sm focus:outline-none focus:ring-2 focus:ring-green-500 text-gray-500 placeholder-gray-500"
                  placeholder="e.g. single post post-1234"/>
              </div>
            </div>
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="">
                <label for="Add Class(es) to <html>">Stuff for &lt;head&gt;</label>
              </div>
              <div class="relative">
                <textarea type="area"
                  class="appearance-none w-full h-24 border border-gray-300 rounded-sm px-4 py-2 bg-white shadow-sm focus:outline-none focus:ring-2 focus:ring-green-500 text-gray-500 placeholder-gray-500"
                  placeholder="e.g. <meta>, <link>, <script>"></textarea>
              </div>
            </div>
          </div>

          <div v-show="activeTab === 'css'" class=" w-full flex flex-col gap-4">
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="">
                <label for="CSS Preprocessor">CSS Preprocessor</label>
              </div>
              <div class="relative">
                <select
                  class="appearance-none w-full border border-gray-300 rounded-sm px-4 py-2 bg-white shadow-sm focus:outline-none focus:ring-2 focus:ring-green-500 text-gray-500">
                  <option value="" disabled selected>None</option>
                  <option value="Less">Less</option>
                  <option value="SCSS">SCSS</option>
                  <option value="Sass">Sass</option>
                  <option value="Stylus">Stylus</option>
                  <option value="PostCSS">PostCSS</option>
                </select>
                <div class="pointer-events-none absolute right-3 top-1/2 transform -translate-y-1/2 flex flex-col justify-around text-gray-500 text-xs leading-tight h-1/2">
                  <img :src="Arrow" class="w-3 rotate-180" alt="">
                  <img :src="Arrow" class="w-3" alt="">
                </div>
              </div>
            </div>
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="">
                <label for="CSS Base">CSS Base</label>
              </div>
              <div class="flex flex-col">
                <label>
                <input type="radio" name="CSS Base" value="Normalize" class="appearance-none w-3.5 h-3.5 border-1 border-gray-400 rounded-full checked:bg-blue-300 checked:border-gray-500"/>
                  Normalize
                </label>
                <label>
                <input type="radio" name="CSS Base" value="Reset" class="appearance-none w-3.5 h-3.5 border-1 border-gray-400 rounded-full checked:bg-blue-300 checked:border-gray-500"/>
                  Reset
                </label>
                <label>
                <input type="radio" name="CSS Base" value="Neither" class="appearance-none w-3.5 h-3.5 border-1 border-gray-400 rounded-full checked:bg-blue-300 checked:border-gray-500"/>
                  Neither
                </label>
              </div>
            </div>
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="">
                <label for="Vender Prefixing">Vender Prefixing</label>
              </div>
              <div class="flex flex-col">
                <label>
                <input type="radio" name="Vender Prefixing" value="Autoprefixer" class="appearance-none w-3.5 h-3.5 border-1 border-gray-400 rounded-full checked:bg-blue-300 checked:border-gray-500"/>
                  Autoprefixer
                </label>
                <label>
                <input type="radio" name="Vender Prefixing" value="Prefixfree" class="appearance-none w-3.5 h-3.5 border-1 border-gray-400 rounded-full checked:bg-blue-300 checked:border-gray-500"/>
                  Prefixfree
                </label>
                <label>
                <input type="radio" name="Vender Prefixing" value="Neither" class="appearance-none w-3.5 h-3.5 border-1 border-gray-400 rounded-full checked:bg-blue-300 checked:border-gray-500"/>
                  Neither
                </label>
              </div>
            </div>
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="">
                <label for="Vender Prefixing">Add External Stylesheets/Pens</label>
              </div>
              <div class="flex flex-col">
                <label>
                <input v-model="linkInput" @keyup.enter="addLink" type="text" placeholder="輸入 Link script URL" class="w-full border px-2 py-1 mb-2"/>
                </label>
                <button @click="addLink" class="mb-4 bg-gray-600 text-white px-3 py-1 rounded hover:bg-gray-700">➕ 加入 CDN</button>
                <ul class="mb-4 list-disc list-inside">
                  <li v-for="(link, index) in links" :key="link" class="flex items-center justify-between gap-2">
                    <span class="break-words max-w-[80%]">{{ link }}</span>
                    <button @click="removeLink(index)" class="text-red-500 hover:text-red-700 text-sm ">刪除</button>
                  </li>
                </ul>
              </div>
            </div>
          </div>

          <div v-show="activeTab === 'js'" class=" w-full flex flex-col gap-4">
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="">
                <label for="JavaScript Preprocessor">JavaScript Preprocessor</label>
              </div>
              <div class="relative">
                <select
                  class="appearance-none w-full border border-gray-300 rounded-sm px-4 py-2 bg-white shadow-sm focus:outline-none focus:ring-2 focus:ring-green-500 text-gray-500">
                  <option value="" disabled selected>None</option>
                  <option value="Script">Script</option>
                </select>
                <div class="pointer-events-none absolute right-3 top-1/2 transform -translate-y-1/2 flex flex-col justify-around text-gray-500 text-xs leading-tight h-1/2">
                  <img :src="Arrow" class="w-3 rotate-180" alt="">
                  <img :src="Arrow" class="w-3" alt="">
                </div>
              </div>
            </div>
            
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="">
                <label for="Vender Prefixing">Add External Scripts/Pens</label>
              </div>
              <div class="flex flex-col">
                <label>
                <input v-model="cdnInput" @keyup.enter="addCDN" type="text" placeholder="輸入 CDN script URL" class="w-full border px-2 py-1 mb-2"/>
                </label>
                <button @click="addCDN" class="mb-4 bg-gray-600 text-white px-3 py-1 rounded hover:bg-gray-700">➕ 加入 CDN</button>
                <ul class="mb-4 list-disc list-inside">
                  <li v-for="(cdn, index) in cdns" :key="cdn" class="flex items-center justify-between gap-2">
                    <span class="break-words max-w-[80%]">{{ cdn }}</span>
                    <button @click="removeCDN(index)" class="text-red-500 hover:text-red-700 text-sm ">刪除</button>
                  </li>
                </ul>
              </div>
            </div>
          </div>
          
          <div v-show="activeTab === 'detail'" class=" w-full flex flex-col gap-4">
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="">
                <label for="CSS Preprocessor">CSS Preprocessor</label>
              </div>
              <div class="relative">
                <input type="text" v-model="title" class="appearance-none w-full border border-gray-300 rounded-sm px-4 py-2 bg-white shadow-sm focus:outline-none focus:ring-2 focus:ring-green-500 text-gray-500 placeholder-gray-500" />
              </div>
            </div>
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="">
                <label for="Pen Description">Pen Description</label>
              </div>
              <div class="relative">
                <textarea class="w-full h-24 border border-gray-300 rounded-sm px-4 py-2 bg-white shadow-sm focus:outline-none focus:ring-2 focus:ring-green-500 text-sm text-gray-500 placeholder-gray-500" placeholder="Explain what's going on in your Pen here. This text is searchable, so it can also help others find your work. Remember to credit others where credit is due. Markdown supported." />
              </div>
            </div>
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="flex justify-between">
                <label for="Tags">Tags</label>
                <span class="text-xs align-text-bottom">comma separated, max of five</span>
              </div>
              <div class="relative">
                <input type="text" class="w-full border border-gray-300 rounded-sm px-4 py-2 bg-white shadow-sm focus:outline-none focus:ring-2 focus:ring-green-500 text-sm text-gray-500 placeholder-gray-500" />
              </div>
            </div>
          </div>
          <div v-show="activeTab === 'behavior'" class="md:w-3/4 w-full flex flex-col gap-4">
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="flex flex-col">
              <label for="autoSave">Auto Save</label>
              <span class="text-xs align-text-bottom mb-4 mt-1">If active, Pens will autosave every 30 seconds after being saved once.</span>
              </div>
              <label class="py-2 hover:cursor-pointer">
                <div class="relative inline-block w-13 h-7 ">
                  <input type="checkbox" class="opacity-0 w-0 h-0 peer" v-model="currentWork.isAutoSave"/>
                  <span
                    class="absolute pointer bg-gray-300 top-0 left-0 right-0 bottom-0 rounded-4xl peer-checked:bg-green-400  transition before:content-[''] before:h-8 before:w-8 before:left-0 before:bottom-[-2px] before:bg-white before:transition  before:absolute before:rounded-4xl  peer-checked:before:translate-x-6"
                    ></span>
                </div>
              </label>
              <span class="ml-2">{{ currentWork.isAutoSave ? 'on' : 'off' }}</span>              
            </div>
            <div class="relative editorSettingCard-linear-bgc py-3 px-4 w-full before:h-full before:w-1 before:bg-gray-500 before:content-[''] before:absolute before:top-0 before:left-0">
              <div class="flex flex-col">
              <label for="autoUpdatingPreview">Auto-Updating Preview</label>
              <span class="text-xs align-text-bottom mb-4 mt-1">If enabled, the preview panel updates automatically as you code. If disabled, use the "Run" button to update.</span>
              </div>
              <label class="py-2 hover:cursor-pointer">
                <div class="relative inline-block w-13 h-7 ">
                  <input type="checkbox" class="opacity-0 w-0 h-0 peer" v-model="currentWork.isAutoPreview"/>
                  <span
                    class="absolute pointer bg-gray-300 top-0 left-0 right-0 bottom-0 rounded-4xl peer-checked:bg-green-400  transition before:content-[''] before:h-8 before:w-8 before:left-0 before:bottom-[-2px] before:bg-white before:transition  before:absolute before:rounded-4xl  peer-checked:before:translate-x-6"
                    ></span>
                </div>
              </label>
              <span class="ml-2">{{ currentWork.isAutoPreview ? 'on' : 'off' }}</span>              
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="bg-gray-600 w-full flex flex-col py-4 px-2 ">
    <button type="submit" @click.prevent="emit('close')" class="self-end bg-green-400 text-black rounded-md p-3">Save & Close</button>
    </div>
  </div>

</template>