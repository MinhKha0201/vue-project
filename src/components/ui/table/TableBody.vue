<script setup lang="ts">
import type { HTMLAttributes } from 'vue'
import { cn } from '@/lib/utils'

const props = defineProps<{
    class?: HTMLAttributes['class']
}>()
</script>

<template>
    <tbody
        :class="cn('[&_tr:last-child]:border-0 overflow-x-auto', props.class)"
    >
        <slot />
    </tbody>
</template>
