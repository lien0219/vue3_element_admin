<template>
  <div class="dashboard-container container">
    <div :class="{ left: true, 'grow-width': widening }">
      <el-card shadow="never">
        <el-row justify="space-between">
          <el-col :span="18" :xs="24">
            <div class="search-component">
              <!--  -->
              <el-input
                v-model="searchQuery"
                placeholder="请输入搜索关键词"
                style="max-width: 600px"
                class="input-with-select"
                @input="handleInput"
                @keydown.down.prevent="handleKeyDown"
                @keydown.up.prevent="handleKeyUp"
                @keydown.enter="handleEnter"
              >
                <template #prepend>
                  <el-select v-model="selectedCategory" placeholder="选择类别" style="width: 115px">
                    <el-option
                      v-for="item in categories"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value"
                    />
                  </el-select>
                </template>
                <template #append>
                  <el-button :icon="Search" @click="performSearch" />
                </template>
              </el-input>

              <!--  -->
            </div>
            <div v-if="showSuggestions" class="suggestions">
              <div
                v-for="(suggestion, index) in filteredSuggestions"
                :key="index"
                class="suggestion-item"
                :class="{ active: index === activeIndex }"
                @click="selectSuggestion(suggestion)"
              >
                <span v-html="highlightMatch(suggestion)" />
              </div>
            </div>
            <!-- <el-table :data="searchResults" style="width: 100%; margin-top: 20px">
              <el-table-column prop="id" label="ID" width="180" />
              <el-table-column prop="name" label="名称" width="180" />
              <el-table-column prop="description" label="描述" />
            </el-table> -->
            <!-- <ForumList /> -->
          </el-col>
        </el-row>
      </el-card>
      <CommentReply v-for="(item, index) in searchResults" :key="index" :result-data="item" />
      <!-- 数据卡片 -->
      <!-- <el-row v-if="!widening" :gutter="10" class="mt-5">
        <el-col :xs="24" :sm="12" :lg="6">
          <el-card shadow="never">
            <template #header>
              <div class="flex-x-between">
                <span class="text-[var(--el-text-color-secondary)]">在线用户</span>
                <el-tag type="success" size="small">-</el-tag>
              </div>
            </template>

            <div class="flex-x-between mt-2">
              <span class="text-lg">{{ onlineUserCount }}</span>
              <svg-icon icon-class="user" size="2em" />
            </div>
            <div class="flex-x-between mt-2 text-sm text-[var(--el-text-color-secondary)]">
              <span>总用户数</span>
              <span>5</span>
            </div>
          </el-card>
        </el-col>

        <el-col v-for="(item, index) in visitStatsList" :key="index" :xs="24" :sm="12" :lg="6">
          <el-skeleton :loading="visitStatsLoading" :rows="5" animated>
            <template #template>
              <el-card>
                <template #header>
                  <div>
                    <el-skeleton-item variant="h3" style="width: 40%" />
                    <el-skeleton-item
                      variant="rect"
                      style="float: right; width: 1em; height: 1em"
                    />
                  </div>
                </template>

                <div class="flex-x-between">
                  <el-skeleton-item variant="text" style="width: 30%" />
                  <el-skeleton-item variant="circle" style="width: 2em; height: 2em" />
                </div>
                <div class="mt-5 flex-x-between">
                  <el-skeleton-item variant="text" style="width: 50%" />
                  <el-skeleton-item variant="text" style="width: 1em" />
                </div>
              </el-card>
            </template>
            <template v-if="!visitStatsLoading">
              <el-card shadow="never">
                <template #header>
                  <div class="flex-x-between">
                    <span class="text-[var(--el-text-color-secondary)]">
                      {{ item.title }}
                    </span>
                    <el-tag :type="item.tagType" size="small">
                      {{ item.granularity }}
                    </el-tag>
                  </div>
                </template>

                <div class="flex-x-between mt-2">
                  <div class="flex-y-center">
                    <span class="text-lg">{{ item.todayCount }}</span>
                    <span :class="['text-xs', 'ml-2', getGrowthRateClass(item.growthRate)]">
                      <el-icon>
                        <Top v-if="item.growthRate > 0" />
                        <Bottom v-else-if="item.growthRate < 0" />
                      </el-icon>
                      {{ formatGrowthRate(item.growthRate) }}
                    </span>
                  </div>
                  <svg-icon :icon-class="item.icon" size="2em" />
                </div>

                <div class="flex-x-between mt-2 text-sm text-[var(--el-text-color-secondary)]">
                  <span>总{{ item.title }}</span>
                  <span>{{ item.totalCount }}</span>
                </div>
              </el-card>
            </template>
          </el-skeleton>
        </el-col>
      </el-row> -->

      <!-- 测试卡片 -->
      <!-- <color-picker v-model="selectedColor" :predefine="predefineColors" alpha />
      <p>Selected Color: {{ selectedColor }}</p>
      <el-row :gutter="10" class="mt-5">
        <div class="cards-container">
          <CardComponent
            v-for="(card, index) in cards"
            :key="index"
            :icon="card.icon"
            :title="card.title"
            :backgroundColor="card.isRemoved ? '#ccc' : card.backgroundColor"
            @edit="() => handleEdit(index)"
            @remove="() => handleRemove(index)"
          >
            {{ card.content }}
          </CardComponent>
        </div>
      </el-row>
      <el-row v-if="!widening" :gutter="10" class="mt-5">
        <el-col :xs="24" :span="16">
          <VisitTrend id="VisitTrend" width="100%" height="400px" />
        </el-col>
        <el-col :xs="24" :span="8">
          <el-card>
            <template #header>
              <div class="flex-x-between">
                <div class="flex-y-center">通知公告</div>
                <el-link type="primary">
                  <span class="text-xs" @click="viewMoreNotice">查看更多</span>
                  <el-icon class="text-xs">
                    <ArrowRight />
                  </el-icon>
                </el-link>
              </div>
            </template>

            <el-scrollbar height="400px">
              <div v-for="(item, index) in notices" :key="index" class="flex-y-center py-3">
                <DictLabel v-model="item.type" code="notice_type" size="small" />
                <el-text
                  truncated
                  class="!mx-2 flex-1 !text-xs !text-[var(--el-text-color-secondary)]"
                >
                  {{ item.title }}
                </el-text>
                <el-link @click="viewNoticeDetail(item.id)">
                  <el-icon class="text-sm">
                    <View />
                  </el-icon>
                </el-link>
              </div>
            </el-scrollbar>
          </el-card>
        </el-col>
      </el-row>

      <NoticeDetail v-if="!widening" ref="noticeDetailRef" /> -->
    </div>
    <div v-if="!widening" class="right">
      <el-row :gutter="10" class="mt-5">
        <HamburgerIcon />
      </el-row>
    </div>
  </div>

  <!-- 原始内容 -->
  <div v-if="false" class="dashboard-container">
    <github-corner class="github-corner" />

    <el-card shadow="never">
      <el-row justify="space-between">
        <el-col :span="18" :xs="24">
          <div class="flex h-full items-center">
            <img
              class="w-20 h-20 mr-5 rounded-full"
              :src="userStore.userInfo.avatar + '?imageView2/1/w/80/h/80'"
            />
            <div>
              <p>{{ greetings }}</p>
              <p class="text-sm text-gray">今日天气晴朗，气温在15℃至25℃之间，东南风。</p>
            </div>
          </div>
        </el-col>

        <el-col :span="6" :xs="24">
          <div class="flex h-full items-center justify-around">
            <el-statistic v-for="item in statisticData" :key="item.key" :value="item.value">
              <template #title>
                <div class="flex items-center">
                  <svg-icon :icon-class="item.iconClass" size="20px" />
                  <span class="text-[16px] ml-1">{{ item.title }}</span>
                </div>
              </template>
              <template v-if="item.suffix" #suffix>/100</template>
            </el-statistic>
          </div>
        </el-col>
      </el-row>
    </el-card>

    <!-- 数据卡片 -->
    <el-row :gutter="10" class="mt-5">
      <el-col :xs="24" :sm="12" :lg="6">
        <el-card shadow="never">
          <template #header>
            <div class="flex-x-between">
              <span class="text-[var(--el-text-color-secondary)]">在线用户</span>
              <el-tag type="success" size="small">-</el-tag>
            </div>
          </template>

          <div class="flex-x-between mt-2">
            <span class="text-lg">{{ onlineUserCount }}</span>
            <svg-icon icon-class="user" size="2em" />
          </div>
          <div class="flex-x-between mt-2 text-sm text-[var(--el-text-color-secondary)]">
            <span>总用户数</span>
            <span>5</span>
          </div>
        </el-card>
      </el-col>

      <el-col v-for="(item, index) in visitStatsList" :key="index" :xs="24" :sm="12" :lg="6">
        <el-skeleton :loading="visitStatsLoading" :rows="5" animated>
          <template #template>
            <el-card>
              <template #header>
                <div>
                  <el-skeleton-item variant="h3" style="width: 40%" />
                  <el-skeleton-item variant="rect" style="float: right; width: 1em; height: 1em" />
                </div>
              </template>

              <div class="flex-x-between">
                <el-skeleton-item variant="text" style="width: 30%" />
                <el-skeleton-item variant="circle" style="width: 2em; height: 2em" />
              </div>
              <div class="mt-5 flex-x-between">
                <el-skeleton-item variant="text" style="width: 50%" />
                <el-skeleton-item variant="text" style="width: 1em" />
              </div>
            </el-card>
          </template>
          <template v-if="!visitStatsLoading">
            <el-card shadow="never">
              <template #header>
                <div class="flex-x-between">
                  <span class="text-[var(--el-text-color-secondary)]">
                    {{ item.title }}
                  </span>
                  <el-tag :type="item.tagType" size="small">
                    {{ item.granularity }}
                  </el-tag>
                </div>
              </template>

              <div class="flex-x-between mt-2">
                <div class="flex-y-center">
                  <span class="text-lg">{{ item.todayCount }}</span>
                  <span :class="['text-xs', 'ml-2', getGrowthRateClass(item.growthRate)]">
                    <el-icon>
                      <Top v-if="item.growthRate > 0" />
                      <Bottom v-else-if="item.growthRate < 0" />
                    </el-icon>
                    {{ formatGrowthRate(item.growthRate) }}
                  </span>
                </div>
                <svg-icon :icon-class="item.icon" size="2em" />
              </div>

              <div class="flex-x-between mt-2 text-sm text-[var(--el-text-color-secondary)]">
                <span>总{{ item.title }}</span>
                <span>{{ item.totalCount }}</span>
              </div>
            </el-card>
          </template>
        </el-skeleton>
      </el-col>
    </el-row>

    <el-row :gutter="10" class="mt-5">
      <el-col :xs="24" :span="16">
        <!-- 访问趋势统计图 -->
        <VisitTrend id="VisitTrend" width="100%" height="400px" />
      </el-col>
      <el-col :xs="24" :span="8">
        <el-card>
          <template #header>
            <div class="flex-x-between">
              <div class="flex-y-center">通知公告</div>
              <el-link type="primary">
                <span class="text-xs" @click="viewMoreNotice">查看更多</span>
                <el-icon class="text-xs">
                  <ArrowRight />
                </el-icon>
              </el-link>
            </div>
          </template>

          <el-scrollbar height="400px">
            <div v-for="(item, index) in notices" :key="index" class="flex-y-center py-3">
              <DictLabel v-model="item.type" code="notice_type" size="small" />
              <el-text
                truncated
                class="!mx-2 flex-1 !text-xs !text-[var(--el-text-color-secondary)]"
              >
                {{ item.title }}
              </el-text>
              <el-link @click="viewNoticeDetail(item.id)">
                <el-icon class="text-sm">
                  <View />
                </el-icon>
              </el-link>
            </div>
          </el-scrollbar>
        </el-card>
      </el-col>
    </el-row>

    <NoticeDetail ref="noticeDetailRef" />
  </div>
</template>

<script setup lang="ts">
defineOptions({
  name: "Dashboard",
  inheritAttrs: false,
});

import VisitTrend from "./components/VisitTrend.vue";

import WebSocketManager from "@/utils/websocket";
import router from "@/router";

import { useUserStore } from "@/store/modules/user";
import StatsAPI, { VisitStatsVO } from "@/api/system/log";
import NoticeAPI, { NoticePageVO } from "@/api/system/notice";

//////////////////////////////////////////////
import HamburgerIcon from "@/views/dashboard/components/HamburgerIcon.vue";
import CardComponent from "@/views/dashboard/components/cartList.vue";
import { Search } from "@element-plus/icons-vue";
import ForumList from "@/views/dashboard/components/comment/ForumList.vue";
import ColorPicker from "@/views/dashboard/components/colorSelect.vue";
import CommentReply from "@/views/dashboard/components/commentReply/index.vue";

//初始颜色
const selectedColor = ref("#ff0000");
const predefineColors = ref([
  "#ff4500",
  "#ff8c00",
  "#ffd700",
  "#90ee90",
  "#00ced1",
  "#1e90ff",
  "#c71585",
  "#FF0000",
]);

const widening = ref(false); //搜索区域宽度控制
const searchQuery = ref("");
const activeIndex = ref(-1);
const showSuggestions = ref(false);
const searchResults: any = ref([]);
const selectedCategory = ref("fruits");

const categories: any = [
  { value: "fruits", label: "水果" },
  { value: "vegetables", label: "蔬菜" },
  { value: "meats", label: "肉类" },
];

// 模拟的搜索建议数据
const suggestions: any = {
  fruits: [
    "苹果",
    "香蕉",
    "橙子",
    "葡萄",
    "西瓜",
    "芒果",
    "樱桃",
    "草莓",
    "蓝莓",
    "柠檬",
    "1111222334444",
    "332211",
    "abcissa",
    "one22",
  ],
  vegetables: ["胡萝卜", "西兰花", "茄子", "土豆", "番茄", "黄瓜", "菠菜", "洋葱", "大蒜", "南瓜"],
  meats: ["牛肉", "猪肉", "鸡肉", "羊肉", "鱼肉", "虾", "蟹", "鸭肉", "火腿", "香肠"],
};

const filteredSuggestions = computed(() => {
  return suggestions[selectedCategory.value].filter((item: any) =>
    item.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});

const handleInput = () => {
  showSuggestions.value = searchQuery.value.length > 0;
  activeIndex.value = -1;
};

const handleKeyDown = () => {
  if (activeIndex.value < filteredSuggestions.value.length - 1) {
    activeIndex.value++;
  }
};

const handleKeyUp = () => {
  if (activeIndex.value > 0) {
    activeIndex.value--;
  }
};

const handleEnter = () => {
  if (activeIndex.value > -1) {
    selectSuggestion(filteredSuggestions.value[activeIndex.value]);
  } else {
    performSearch();
  }
};

const selectSuggestion = (suggestion: any) => {
  searchQuery.value = suggestion;
  showSuggestions.value = false;
  performSearch();
};

const performSearch = () => {
  widening.value = true;
  // 模拟搜索操作
  searchResults.value = [
    {
      id: 1,
      name: searchQuery.value,
      description: `这是一个${categories.find((c: any) => c.value === selectedCategory.value).label}搜索结果`,
    },
    {
      id: 2,
      name: searchQuery.value + " 2",
      description: `这是另一个${categories.find((c: any) => c.value === selectedCategory.value).label}搜索结果`,
    },
  ];
};

const highlightMatch = (text: any) => {
  if (!searchQuery.value) return text;
  const regex = new RegExp(`(${searchQuery.value})`, "gi");
  return text.replace(regex, '<span class="highlight">$1</span>');
};

//测试卡片
// 定义卡片数据数组
const cards = ref([
  {
    icon: "el-icon-message",
    title: "卡片1",
    backgroundColor: "#f5f7fa",
    content: "这里是卡片1的内容...",
    isRemoved: false,
  },
  {
    icon: "el-icon-date",
    title: "卡片2",
    backgroundColor: "#e6f7ff",
    content: "这里是卡片2的内容...",
    isRemoved: false,
  },
  {
    icon: "el-icon-edit",
    title: "卡片3",
    backgroundColor: "#fff5f0",
    content: "这里是卡片3的内容...",
    isRemoved: false,
  },
  {
    icon: "el-icon-delete",
    title: "卡片4",
    backgroundColor: "#f0f9ff",
    content: "这里是卡片4的内容...",
    isRemoved: false,
  },
]);
// 过滤出未被移除的卡片
const activeCards = ref(cards.value.filter((card) => !card.isRemoved));

const handleEdit = (index: number) => {
  console.log(`编辑卡片 ${index}`);
  handleRestore(index);
};
let card: any = {};
const handleRemove = (index: number) => {
  console.log(`标记移除卡片 ${index}`);
  // 更新卡片的isRemoved状态
  card = cards.value.find((card, idx) => idx === index);
  card.isRemoved = true;
  // 重新排序卡片数组，将被标记为移除的卡片移动到数组的最后

  cards.value = cards.value.sort((a, b) =>
    !a.isRemoved && b.isRemoved ? -1 : a.isRemoved && !b.isRemoved ? 1 : 0
  );
  activeCards.value = cards.value.filter((card) => !card.isRemoved);
};

const handleRestore = (index: number) => {
  console.log(`恢复卡片 ${index}`);
  // 更新卡片的isRemoved状态
  card = cards.value.find((card, idx) => idx === index);
  card.isRemoved = false;
  // 重新排序卡片数组，将恢复的卡片移动到其原来的位置
  cards.value = cards.value.sort((a, b) =>
    !a.isRemoved && b.isRemoved ? -1 : a.isRemoved && !b.isRemoved ? 1 : 0
  );
  activeCards.value = cards.value.filter((card) => !card.isRemoved);
};

////////////////////////////////////////////////

const noticeDetailRef = ref();

const userStore = useUserStore();
const date: Date = new Date();
const greetings = computed(() => {
  const hours = date.getHours();
  if (hours >= 6 && hours < 8) {
    return "晨起披衣出草堂，轩窗已自喜微凉🌅！";
  } else if (hours >= 8 && hours < 12) {
    return "上午好，" + userStore.userInfo.nickname + "！";
  } else if (hours >= 12 && hours < 18) {
    return "下午好，" + userStore.userInfo.nickname + "！";
  } else if (hours >= 18 && hours < 24) {
    return "晚上好，" + userStore.userInfo.nickname + "！";
  } else {
    return "偷偷向银河要了一把碎星，只等你闭上眼睛撒入你的梦中，晚安🌛！";
  }
});

// 右上角数量
const statisticData = ref([
  {
    value: 99,
    iconClass: "message",
    title: "消息",
    key: "message",
  },
  {
    value: 50,
    iconClass: "todo",
    title: "待办",
    suffix: "/100",
    key: "upcoming",
  },
  {
    value: 10,
    iconClass: "project",
    title: "项目",
    key: "project",
  },
]);

const onlineUserCount = ref(0);

const visitStatsLoading = ref(true);
const visitStatsList = ref<VisitStats[] | null>(Array(3).fill({}));
interface VisitStats {
  title: string;
  icon: string;
  tagType: "primary" | "success" | "warning";
  growthRate: number;
  // 粒度
  granularity: string;
  // 今日数量
  todayCount: number;
  totalCount: number;
}
// 加载访问统计数据
const loadVisitStatsData = async () => {
  const list: VisitStatsVO[] = await StatsAPI.getVisitStats();

  if (list) {
    const tagTypes: ("primary" | "success" | "warning")[] = ["primary", "success", "warning"];
    const transformedList: VisitStats[] = list.map((item, index) => ({
      title: item.title,
      icon: getVisitStatsIcon(item.type),
      tagType: tagTypes[index % tagTypes.length],
      growthRate: item.growthRate,
      granularity: "日",
      todayCount: item.todayCount,
      totalCount: item.totalCount,
    }));
    visitStatsList.value = transformedList;
    visitStatsLoading.value = false;
  }
};

/** 格式化增长率 */
const formatGrowthRate = (growthRate: number): string => {
  if (growthRate === 0) {
    return "-";
  }

  const formattedRate = Math.abs(growthRate * 100)
    .toFixed(2)
    .replace(/\.?0+$/, "");
  return formattedRate + "%";
};

/** 获取增长率文本颜色类 */
const getGrowthRateClass = (growthRate: number): string => {
  if (growthRate > 0) {
    return "color-[--el-color-danger]";
  } else if (growthRate < 0) {
    return "color-[--el-color-success]";
  } else {
    return "color-[--el-color-info]";
  }
};

/** 获取访问统计图标 */
const getVisitStatsIcon = (type: string) => {
  switch (type) {
    case "pv":
      return "pv";
    case "uv":
      return "uv";
    case "ip":
      return "ip";
    default:
      return "pv";
  }
};

const notices = ref<NoticePageVO[]>([]);

// 查看更多
function viewMoreNotice() {
  router.push({ path: "/myNotice" });
}

// 阅读通知公告
function viewNoticeDetail(id: string) {
  noticeDetailRef.value.openNotice(id);
}

onMounted(() => {
  loadVisitStatsData();

  // 获取我的通知公告
  NoticeAPI.getMyNoticePage({ pageNum: 1, pageSize: 10 }).then((data) => {
    notices.value = data.list;
  });

  WebSocketManager.subscribeToTopic("/topic/onlineUserCount", (data) => {
    console.log("收到在线用户数量：", data);
    onlineUserCount.value = JSON.parse(data);
  });
});
</script>

<style lang="scss" scoped>
.dashboard-container {
  position: relative;
  padding: 24px;

  .github-corner {
    position: absolute;
    top: 0;
    right: 0;
    z-index: 1;
    border: 0;
  }
}

.container {
  display: flex;
  flex-wrap: nowrap;
  justify-content: space-between;
  width: 100%;
  padding: 10px;
  background-color: green;

  .left {
    width: 70%;
    background-color: pink;
  }

  .right {
    width: 29%;
    background-color: palegreen;
  }
}

/////////////
.search-component {
  width: 500px;
  margin: 0 auto;
}

.suggestions {
  max-height: 200px;
  overflow-y: auto;
  border: 1px solid #dcdfe6;
  border-top: none;
}

.suggestion-item {
  padding: 8px 12px;
  cursor: pointer;
}

.suggestion-item:hover,
.suggestion-item.active {
  background-color: #f5f7fa;
}

::v-deep .highlight {
  font-size: 20px;
  font-weight: bold;
  color: #409eff;
}

.input-with-select .el-input-group__prepend {
  background-color: var(--el-fill-color-blank);
}

/* 定义动画 */
// @keyframes widthGrow {
//   0% {
//     width: 70%;
//   }
//   50% {
//     width: 80%;
//   }
//   100% {
//     width: 100%;
//   }
// }

/* 应用动画 */
.grow-width {
  // animation-name: width-grow; /* 引用动画名称 */
  // animation-duration: 2s; /* 动画持续时间 */
  // animation-fill-mode: forwards; /* 动画完成后保持最后一帧状态 */
  // animation: 4s linear 0s infinite alternate widthGrow;
  // animation: 2s widthGrow forwards; /* 正确设置动画 */
}

.cards-container {
  display: flex;
  flex-flow: row nowrap;
  justify-content: flex-start;
}
</style>
