
<template>
    <div class="home-page">
        <div class="home-page-wrapper">
            <div class="home-page-nav">
                <el-menu class="home-page-nav-menu" :default-active="activeIndex" :default-openeds="openeds" @select="handleSelect">
                    <el-scrollbar>
                        <el-sub-menu v-for="(item, index) in navLists" :key="index" :index="item.label">
                            <template #title>
                                <span style="font-weight: bold; font-size: 16px;">{{ item.label }}</span>
                            </template>
                            <el-menu-item v-for="(nav, index) in item.chindren" :key="index" :index="nav.label">{{
                            nav.label
                        }}</el-menu-item>
                        </el-sub-menu>
                    </el-scrollbar>
                </el-menu>

            </div>
            <el-scrollbar class="home-page-list" ref="scrollbarRef" @scroll="scroll">
                <div class="home-page-list-search">
                    <el-image style="width: 50px; height: 50px" :src="LogoUrl" />
                    <span class="home-page-departmenet">ZUOSHIYUE</span>
                </div>
                <el-input v-model="inputSearch" placeholder="不懂就搜，一搜就懂" class="home-page-list-search-input">
                    <template #prepend>
                        <el-select v-model="selectSearch" placeholder="Select" style="width: 120px;">
                            <el-option label="必应" value="bing" />
                            <el-option label="百度" value="baidu" />
                        </el-select>
                    </template>
                    <template #append>
                        <el-button style="width: 80px;" @click="buttonSearch">搜索</el-button>
                    </template>
                </el-input>
                <div v-for="(item, index) in navLists" :key="index">
                    <div class="home-page-list-layout" v-for="(model, i) in item.chindren" :key="i" :name="`${model.label}-card`">
                        <div class="home-page-list-nav-head" :name="model.label">
                            <span style="font-size: 16px; font-weight: bold;">{{ model.label }}</span>
                        </div>
                        <div class="home-page-list-wrapper">
                            <div class="home-page-list-card" v-for="(nav, j) in model.chindren" :key="j" @click="navSelectClicked(nav.value)">
                                <span>{{ nav.label }}</span>
                            </div>
                        </div>
                    </div>
                </div>
                <!-- 因为最后一项导航没有多余的空间，用于支撑与左侧菜单栏联动，然后滚动到顶部 -->
                <div :style="needSpaceStyle"></div>
            </el-scrollbar>
            <div class="home-page-backTop">
                <el-button v-show="isViewBackTopButton" class="home-page-backTop-button" :icon="CaretTop" circle plain @click="backTopClicked"></el-button>
            </div>
        </div>

    </div>
</template>
<script setup lang="ts">
import { nextTick, onMounted, ref } from 'vue'
import {
    ElButton,
    ElImage,
    ElInput,
    ElMenu,
    ElMenuItem,
    ElOption,
    ElScrollbar,
    ElSelect,
    ElSubMenu,
} from 'element-plus'
import { navLists } from '../assets/ts/navLists'
import LogoUrl from '../assets/images/logo.gif'
import { CaretTop } from '@element-plus/icons-vue'

const scrollbarRef = ref<InstanceType<typeof ElScrollbar>>()

const inputSearch = ref('')

const selectSearch = ref('dev')

const needSpaceStyle = ref({
    height: '0px',
})

// 获取左侧菜单最后一项，基于它加上需补充的空间高度(浏览器内高度-卡片整个高度)
let lastNavName = navLists.at(-1)?.chindren.at(-1)?.label

onMounted(() => {
    // 获取最后一个导航卡片的clientHeight，用于计算所需的高度
    let lastClientHeight = document.getElementsByName(`${lastNavName}-card`)[0]
        .clientHeight
    // 计算页面剩余空间的高度，减去40是为了调整间距或布局的其他部分
    let needSpaceHeight = window.innerHeight - lastClientHeight - 40
    // 动态设置needSpaceStyle的height属性，以适应不同的窗口大小和布局需求
    needSpaceStyle.value.height = `${needSpaceHeight}px`
})

const isViewBackTopButton = ref(false)

/**
 * 返回顶部按钮点击事件处理函数
 * 该函数将滚动条滚动到页面顶部
 */
const backTopClicked = () => {
    scrollbarRef.value!.setScrollTop(0)
}

/**
 * 打开搜索引擎结果页面
 * 根据用户选择的搜索引擎类型和输入的搜索内容，定向到对应的搜索结果页面
 */
const buttonSearch = () => {
    // 根据用户选择的搜索引擎类型，执行不同的搜索操作
    switch (selectSearch.value) {
        case 'bing':
            // 当用户选择必应搜索时，构造必应搜索结果页面的URL并打开
            window.open(`https://cn.bing.com/search?q=${inputSearch.value}`)
            break
        case 'baidu':
            // 当用户选择百度搜索时，构造百度搜索结果页面的URL并打开
            window.open(`https://www.baidu.com/s?wd=${inputSearch.value}`)
            break
        // 可以在此处添加更多的搜索引擎案例

        default:
            // 如果用户未选择任何搜索引擎或选择了未知的搜索引擎，不做任何操作
            break
    }
}

onMounted(() => {
   
})

const activeIndex = ref('')

const openeds = ref(['通用', '工具', '娱乐'])

/**
 * 处理选项卡选择的函数
 * 
 * 该函数的目标是根据所选选项卡的key滚动到对应的位置
 * 它通过计算目标选项卡的顶部偏移量来设置滚动条的位置
 * 
 * @param key 当前选项卡的唯一标识符
 * @param keyPath 当前选项卡的路径，表示一系列的key
 */
const handleSelect = (key: string, keyPath: string[]) => {
    // 获取目标选项卡的顶部偏移量
    let offsetTop = document.getElementsByName(key)[0].offsetTop
    // 设置滚动条的顶部位置，以滚动到目标选项卡
    scrollbarRef.value!.setScrollTop(offsetTop - 20)
}
/**
 * 当导航选择项被点击时执行的函数
 * 该函数接收一个URL字符串作为参数，并在新的浏览器窗口中打开该URL
 * 
 * @param {string} href - 导航项的URL地址
 */
const navSelectClicked = (href: string) => {
    window.open(href)
}
/**
 * 处理滚动事件的函数
 * @param {Object} event - 滚动事件对象
 * @param {number} event.scrollTop - 滚动的垂直位置
 */
const scroll = ({ scrollTop }: any) => {
    // 当滚动超过300时，设置回到顶部的按钮可见
    if (scrollTop >= 300) {
        isViewBackTopButton.value = true
    }

    // 根据滚动位置动态更新回到顶部按钮的可见性
    isViewBackTopButton.value = scrollTop >= 500 ? true : false

    // 计算特定元素的偏移量，此处的数字代表特定元素的高度和页面顶部的距离
    // 937 - 247
    let offsetTop = document.getElementsByName('老版项目维护-card')
}
</script>
<style scoped>
.home-page {
    width: 100%;
    height: 100%;
    background-color: #edf0f7;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-items: center;
    overflow-y: scroll;
}

.home-page-wrapper {
    width: 1300px;
    height: 100%;
    background-color: #ffffff;
    display: flex;
    flex-direction: row;
}

.home-page-nav {
    width: 400px;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: #edf0f7;
}

.home-page-nav-menu {
    width: 200px;
    height: 800px;
    background-color: #edf0f7;
}

.home-page-list {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    background-color: #ffffff;
}

.home-page-list-search {
    width: 100%;
    height: 120px;
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
}

.home-page-departmenet {
    margin-left: 20px;
    font-size: 26px;
}

.home-page-list-search-input {
    width: 955px;
    height: 50px;
    margin-left: 20px;
    margin-bottom: 20px;
    text-align: center;
}

.home-page-list-layout {
    margin: 20px 40px 20px 20px;
}

.home-page-list-nav-head {
    width: 100%;
    height: 50px;
    padding-left: 15px;
    text-align: left;
    display: flex;
    align-items: center;
    background-color: #ecf5ff;
    border-radius: 4px;
    border-left: 5px solid #409eff;
    /* margin: 20px 0; */
}

.home-page-list-wrapper {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    margin-top: 20px;
}
.home-page-list-card {
    align-items: center;
    background-color: #f5f5f5;
    border: 1px solid transparent;
    border-radius: 16px;
    box-shadow: 4px 4px 8px 0px rgba(1, 1, 2, 0.13);
    cursor: pointer;
    display: flex;
    justify-content: space-between;
    max-width: 154px;
    padding: 8px;
    margin: 10px;
    text-align: center;
}

.home-page-list-card:hover {
    background-color: #1890ff;
    color: #ffffff;
}
.home-page-backTop {
    position: relative;
    background-color: #edf0f7;
}
.home-page-backTop-button {
    position: absolute;
    left: 50px;
    bottom: 50px;
}
</style>
<style>
.el-select .el-input__wrapper {
    cursor: pointer;
    height: 50px;
}

.el-input-group__prepend {
    box-shadow: none !important;
}

.el-menu {
    border-right: none !important;
    padding-bottom: 10px;
}

.el-sub-menu .el-icon svg {
    height: 0 !important;
}
.el-sub-menu__title {
    background-color: #ffffff;
}
.el-sub-menu .el-menu-item {
    border-radius: 8px;
    min-width: 0px !important;
    margin-left: 10px;
    margin-right: 10px;
}

.el-menu-item.is-active {
    border-radius: 8px;
    background-color: #ecf5ff;
    font-weight: bold;
    margin-left: 10px;
    margin-right: 10px;
}

.el-menu-item:hover {
    border-radius: 8px;
    margin-left: 10px;
    margin-right: 10px;
}
</style>