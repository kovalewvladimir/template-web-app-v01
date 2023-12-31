<script setup lang="ts">
import { useWindowSize } from '@vueuse/core'
import { ComponentSize, ElConfigProvider } from 'element-plus'
import { onMounted, PropType, provide, watch } from 'vue'

import { useDesign } from '@/hooks/web/useDesign'
import { useAppStore } from '@/store/modules/app'
import { setCssVar } from '@/utils'

const { variables } = useDesign()

const appStore = useAppStore()

const props = defineProps({
  size: {
    type: String as PropType<ComponentSize>,
    default: 'default',
    validator: function (value: string) {
      return ['default', 'small', 'large'].includes(value)
    }
  }
})

provide('configGlobal', props)

// 初始化所有主题色
onMounted(() => {
  appStore.setCssVarTheme()
})

const { width } = useWindowSize()

// 监听窗口变化
watch(
  () => width.value,
  (width: number) => {
    if (width < 768) {
      !appStore.getMobile ? appStore.setMobile(true) : undefined
      setCssVar('--left-menu-min-width', '0')
      appStore.setCollapse(true)
      appStore.getLayout !== 'classic' ? appStore.setLayout('classic') : undefined
    } else {
      appStore.getMobile ? appStore.setMobile(false) : undefined
      setCssVar('--left-menu-min-width', '64px')
    }
  },
  {
    immediate: true
  }
)
</script>

<template>
  <ElConfigProvider :namespace="variables.elNamespace" :message="{ max: 1 }" :size="size">
    <slot></slot>
  </ElConfigProvider>
</template>
