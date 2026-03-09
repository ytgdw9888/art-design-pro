<template>
  <!--
    Element Plus 全局配置：
    - size: 统一组件默认尺寸
    - locale: 根据用户语言切换语言包
    - z-index: 统一弹层/消息等的层级基准，避免被业务层遮挡
  -->
  <ElConfigProvider size="default" :locale="locales[language]" :z-index="3000">
    <!-- 路由出口：页面内容由 vue-router 渲染到这里 -->
    <RouterView></RouterView>
  </ElConfigProvider>
</template>

<script setup lang="ts">
  // 用户状态：读取语言偏好（zh/en）用于切换 Element Plus 语言包
  import { useUserStore } from './store/modules/user'

  // Element Plus 语言包
  import zh from 'element-plus/es/locale/lang/zh-cn'
  import en from 'element-plus/es/locale/lang/en'

  // 应用启动阶段的通用初始化
  // - systemUpgrade: 处理系统升级/变更提示、清理等（项目自定义逻辑）
  import { systemUpgrade } from './utils/sys'
  // - toggleTransition: 控制全局过渡/动画开关，避免初始化时闪烁
  import { toggleTransition } from './utils/ui/animation'
  // - checkStorageCompatibility: 检查 localStorage/sessionStorage 等可用性（无痕/禁用场景）
  import { checkStorageCompatibility } from './utils/storage'
  // - initializeTheme: 初始化主题（暗黑/亮色、主题色、CSS 变量等）
  import { initializeTheme } from './hooks/core/useTheme'

  const userStore = useUserStore()
  // 以 ref 形式解构，保持响应式（language 变化时 locale 会自动更新）
  const { language } = storeToRefs(userStore)

  // 语言码 -> Element Plus locale 映射
  const locales = {
    zh: zh,
    en: en
  }

  onBeforeMount(() => {
    // 首次渲染前：开启过渡保护并初始化主题，降低页面闪烁/样式跳变
    toggleTransition(true)
    initializeTheme()
  })

  onMounted(() => {
    // 挂载后：做环境检查与升级逻辑，最后关闭过渡保护
    checkStorageCompatibility()
    toggleTransition(false)
    systemUpgrade()
  })
</script>
