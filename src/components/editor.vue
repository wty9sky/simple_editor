<template>
  <div>
    <div class="flex_row">
      <div class="flex_col">
        <span>简单的富文本输入框</span>
        <SimpleInput
          ref="editorRef"
          v-model="content"
          @submit="submit"
        ></SimpleInput>
      </div>
      <div class="flex_col">
        <span>预览结果：</span>
        <span v-html="editorResult"></span>
      </div>
    </div>
  </div>
</template>

<script setup>
import SimpleInput from '../components/SimpleInput/index.vue'
import { ref } from 'vue'

const content = ref('');
const editorRef = ref(null);
const textAreaResult = ref('');
const textArea = ref('');

const editorResult = ref('');

const submit = () => {
  console.log(content.value);
  editorResult.value = content.value;
  editorRef.value.clear();
}

const submitTextArea = () => {
  textAreaResult.value = textArea.value;
}

</script>

<style scoped>
.flex_row {
  display: flex;
  flex-flow: row nowrap;
  justify-content: space-between;
  align-items: start;
}
.flex_col {
  width: 100%;
  display: flex;
  flex-flow: column;
  justify-content: center;
  align-self: start;
}
</style>