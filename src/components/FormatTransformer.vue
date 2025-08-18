<script setup lang="ts">
import _ from 'lodash';
import type { UseValidationRule } from '@/composable/validation';
import CInputText from '@/ui/c-input-text/c-input-text.vue';

const props = withDefaults(
  defineProps<{
    transformer?: (v: string) => string
    inputValidationRules?: UseValidationRule<string>[]
    inputLabel?: string
    inputPlaceholder?: string
    inputDefault?: string
    outputLabel?: string
    outputLanguage?: string
  }>(),
  {
    transformer: _.identity,
    inputValidationRules: () => [],
    inputLabel: 'Input',
    inputDefault: '',
    inputPlaceholder: 'Input...',
    outputLabel: 'Output',
    outputLanguage: '',
  },
);

const { transformer, inputValidationRules, inputLabel, outputLabel, outputLanguage, inputPlaceholder, inputDefault }
  = toRefs(props);

const inputElement = ref<typeof CInputText>();

const input = ref(inputDefault.value);
const output = computed(() => transformer.value(input.value));
</script>

<template>
  <div class="container">
    <div class="input-container">
      <CInputText
        ref="inputElement"
        v-model:value="input"
        :placeholder="inputPlaceholder"
        :label="inputLabel"
        rows="20"
        autosize
        raw-text
        multiline
        test-id="input"
        :validation-rules="inputValidationRules"
        monospace
      />
    </div>
    <div class="output-container">
      <div class="output-label">
        {{ outputLabel }}
      </div>
      <textarea-copyable
        :value="output"
        :language="outputLanguage"
        :follow-height-of="inputElement?.inputWrapperRef"
      />
    </div>
  </div>
</template>

<style scoped>
/* 确保容器占满整个视口 */
.container {
  display: flex;
  height: 100%;
  width: 100%;
}

.input-container, .output-container {
  width: 50%; /* 每个容器占据一半宽度 */
  box-sizing: border-box; /* 确保 padding 和 border 不会增加容器的总宽度 */
  padding: 10px; /* 添加内边距 */
  height: 100%; /* 确保容器高度占满父容器 */
  overflow: auto; /* 防止内容超出容器时出现滚动条 */
}

.output-label {
  margin-bottom: 5px;
}
</style>