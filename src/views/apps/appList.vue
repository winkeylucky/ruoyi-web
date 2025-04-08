<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { NButton, NCard, NDivider,NTabs, NTabPane,NIcon,useMessage} from 'naive-ui';
import { chatSetting, gptsType, mlog, } from '@/api'
import to from 'await-to-js';
import { t } from '@/locales';
import { useRouter } from 'vue-router'
import { useAppStore, useChatStore, homeStore, gptConfigStore, gptsUlistStore } from '@/store'
import { getGpts } from '@/api/chatmsg';
const gptsFilterList = ref<gptsType[]>([]);
const router = useRouter()
const ms = useMessage()

onMounted(async () => {

});

const load= async ()=>{
const params = { pageNum: 1, pageSize: 20 };
const [err, result] = await to(getGpts(params));
if(err){
  console.log("err===",err)
}else{
  gptsFilterList.value = result.rows as unknown as gptsType[];
}
}

load()
const appStore = useAppStore()
const chatStore = useChatStore()

const goUseGpts= async ( item: gptsType)=>{
	gptConfigStore.setInit();
	const saveObj= {model:  `${ item.modelName }` ,gpts:item}
	gptConfigStore.setMyData(saveObj);
  chatStore.addHistory({ title: '新建对话', uuid: Date.now(), isEdit: false })
}

</script>

<template>
  <div class="flex h-full flex-col role-card">
    <n-tabs type="line" class="tab-bar">
      <n-tab-pane name="officialRecommend" tab="应用中心" />
    </n-tabs>

    <main class="flex-1 overflow-hidden " style="margin-left: 20px;">
      <div class="card-container">
        <n-card v-for="item in gptsFilterList" :key="item.id" class="card-item" bordered hoverable>
          <div class="flex justify-between">
            <div>
              <h3>{{ item.name }}</h3>
              <p class="ellipsis" :title="item.info">{{ item.info || '——' }}</p>
            </div>
            <!-- <n-avatar :size="48" :src="item.avatar" /> -->
           
            <n-icon size="48">
              <img :src="item.logo" alt="Icon" />
            </n-icon>
          </div>
          <n-divider />
          <div class="flex justify-between mt-4 button-list">
            <n-button  secondary round type="info" @click="goUseGpts(item)">
              立即体验
            </n-button>
            <n-button  secondary round  type="primary">
              关注
              <!-- {{ $t('voice.collection') }} -->
            </n-button>
          </div>
        </n-card>
      </div>
    </main>
  </div>
</template>
  
<style scoped>
.card-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start; /* 修改为左边对齐 */
}

.card-item {
  width: calc(26%);
  margin: 12px;
  border-radius: 10px;
  height: 28vh;
}

.pagination {
  position: absolute;
  right: 10px;
  bottom: 10px;
}

.ellipsis {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  width: 200px; /* Adjust width as needed */
}

.tab-bar {
  margin: 20px 0 20px 20px; /* 上下边距20px，左边边距20px */
  padding: 10px; /* 添加内边距 */
  border-radius: 8px; /* 添加圆角 */
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); /* 添加阴影 */
  transition: all 0.3s ease; /* 添加动效过渡 */
}


</style>
  
