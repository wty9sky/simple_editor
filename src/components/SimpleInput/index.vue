<template>
  <div>
    <div
      ref="editorRef"
      class="rich-input"
      contenteditable
      :spellcheck="false"
      :placeholder="placeholder"
      :max-length="400"
      @focus="onFocus"
      @blur="onBlur"
      @input="onInput"
      @paste="pasteFn"
      v-html="text"
    ></div>
    <span>字数：{{ repalceHtmlToText(modelValue).length }}</span>
    <button class="h-10 px-6 font-semibold rounded-md border border-slate-200 text-slate-900" @click="addEmoji">添加表情</button>
    <button class="h-10 px-6 font-semibold rounded-md bg-black text-white" @click="submit">发布沸点</button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps({
  placeholder: {
    type: String,
    default: '分享今日新鲜事'
  },
  modelValue: String,
  maxStickTextCount: {
    type: Number,
    default: 400
  }
});


const range = ref();
const editorRef = ref();
const text = ref('');

const replaceUrl = (content) => {
  const reg =
    /((?!http[s]?:\/\/[A-Za-z0-9]+\.(?:png|jpg|gif|jpeg))(?!.*\.(?:png|jpg|gif|jpeg))http[s]?:\/\/[-A-Za-z0-9+&@#/%?=~_|!:,.;]*(?![?&].*\.(?:png|jpg|gif|jpeg))[-A-Za-z0-9+&@#/%=~_|])/g;

  const textReplace = content.replace(
    reg,
    `<a href='$1' target='_blank' style='color: #eb7340;display: inline;line-height:24px;'><svg t="1711694310637" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="4290" width="15" height="15"><path d="M607.934444 417.856853c-6.179746-6.1777-12.766768-11.746532-19.554358-16.910135l-0.01228 0.011256c-6.986111-6.719028-16.47216-10.857279-26.930349-10.857279-21.464871 0-38.864146 17.400299-38.864146 38.864146 0 9.497305 3.411703 18.196431 9.071609 24.947182l-0.001023 0c0.001023 0.001023 0.00307 0.00307 0.005117 0.004093 2.718925 3.242857 5.953595 6.03853 9.585309 8.251941 3.664459 3.021823 7.261381 5.997598 10.624988 9.361205l3.203972 3.204995c40.279379 40.229237 28.254507 109.539812-12.024871 149.820214L371.157763 796.383956c-40.278355 40.229237-105.761766 40.229237-146.042167 0l-3.229554-3.231601c-40.281425-40.278355-40.281425-105.809861 0-145.991002l75.93546-75.909877c9.742898-7.733125 15.997346-19.668968 15.997346-33.072233 0-23.312962-18.898419-42.211381-42.211381-42.211381-8.797363 0-16.963347 2.693342-23.725354 7.297197-0.021489-0.045025-0.044002-0.088004-0.066515-0.134053l-0.809435 0.757247c-2.989077 2.148943-5.691629 4.669346-8.025791 7.510044l-78.913281 73.841775c-74.178443 74.229608-74.178443 195.632609 0 269.758863l3.203972 3.202948c74.178443 74.127278 195.529255 74.127278 269.707698 0l171.829484-171.880649c74.076112-74.17435 80.357166-191.184297 6.282077-265.311575L607.934444 417.856853z" fill="#5D5D5D" p-id="4291"></path><path d="M855.61957 165.804257l-3.203972-3.203972c-74.17742-74.178443-195.528232-74.178443-269.706675 0L410.87944 334.479911c-74.178443 74.178443-78.263481 181.296089-4.085038 255.522628l3.152806 3.104711c3.368724 3.367701 6.865361 6.54302 10.434653 9.588379 2.583848 2.885723 5.618974 5.355985 8.992815 7.309476 0.025583 0.020466 0.052189 0.041956 0.077771 0.062422l0.011256-0.010233c5.377474 3.092431 11.608386 4.870938 18.257829 4.870938 20.263509 0 36.68962-16.428158 36.68962-36.68962 0-5.719258-1.309832-11.132548-3.645017-15.95846l0 0c-4.850471-10.891048-13.930267-17.521049-20.210297-23.802102l-3.15383-3.102664c-40.278355-40.278355-24.982998-98.79612 15.295358-139.074476l171.930791-171.830507c40.179095-40.280402 105.685018-40.280402 145.965419 0l3.206018 3.152806c40.279379 40.281425 40.279379 105.838513 0 146.06775l-75.686796 75.737962c-10.296507 7.628748-16.97358 19.865443-16.97358 33.662681 0 23.12365 18.745946 41.87062 41.87062 41.87062 8.048303 0 15.563464-2.275833 21.944801-6.211469 0.048095 0.081864 0.093121 0.157589 0.141216 0.240477l1.173732-1.083681c3.616364-2.421142 6.828522-5.393847 9.529027-8.792247l79.766718-73.603345C929.798013 361.334535 929.798013 239.981676 855.61957 165.804257z" fill="#5D5D5D" p-id="4292"></path></svg>网页链接</a>`,
  );
  return textReplace;
};


const emit = defineEmits(['submit', 'update:modelValue', 'input', 'focus', 'blur']);
const placeholder = ref('请输入内容');


const repalceHtmlToText = (content) => {
  let str = content;
  str = str.replace(/<\/?.+?>/g, '');
  str = str.replace(/&nbsp;/g, ' ');
  return str;
};


const onInput = (event) => {
  try {
    range.value = window.getSelection()?.getRangeAt(0);
  } catch (error) {
    console.log(error);
  }
  const { innerHTML } = event.target;
  emit('update:modelValue', innerHTML);
  emit('input', event);
};

const onFocus = (event) => {
  emit('focus', event);
};

const onBlur = (event) => {
  try {
    range.value = window.getSelection()?.getRangeAt(0);
  } catch (error) {
    console.log(error);
  }
  emit('blur', event);
};

function pasteFn(event) {
  const { clipboardData } = event;
  if (clipboardData) {
    const texts = clipboardData.getData('text/plain');
    const file = clipboardData.items.length > 0 ? clipboardData.items[0].getAsFile() : null;
    if (text) {
      event.preventDefault(); // 阻止默认的粘贴行为
      document.execCommand('insertText', false, texts); // 插入纯文本
    } else if (file) {
      event.preventDefault(); // 阻止默认的粘贴行为
      // 处理粘贴的文件，例如上传到服务器
      emit('pasteImg', event, file);
    }
  }
}

const clear = () => {
  if (editorRef.value) {
    editorRef.value.innerHTML = '';
    emit('update:modelValue', editorRef.value.innerHTML);
  }
};

const submit = () => {
  const replaceText = replaceUrl(editorRef.value.innerHTML);
  emit('update:modelValue', replaceText);
  emit('submit');
};


const addEmoji = () => {
  const emoji = "😁";
  addText(emoji);
};

function addText(val, isPop) {
  const selection = window.getSelection();
  if (selection) {
    selection.removeAllRanges();
    // 为空初始化光标
    if (!range.value) {
      editorRef.value?.focus();
      range.value = selection.getRangeAt(0);
    }
    // 删除选中内容
    range.value.deleteContents();
    // 添加内容
    range.value.insertNode(range.value.createContextualFragment(val));
    range.value.collapse(false);
    selection.addRange(range.value);

    // const result = repalceHtmlToText(editorRef.value?.innerHTML);
    // if (result.length !== 0) {
    //   if (result.length > props.maxStickTextCount) {
    //     submitStickDisabled.value = true;
    //   } else {
    //     submitStickDisabled.value = false;
    //   }
    // } else {
    //   submitStickDisabled.value = true;
    // }
    emit('update:modelValue', editorRef.value?.innerHTML || '');
    const event = editorRef.value;
    emit('input', event);
  }
}

defineExpose({ clear });

</script>

<style>
.rich-input {
  position: relative;
  font-size: 14px;
  line-height: 24px;
  color: var(--juejin-font-1);

  outline: none;
  padding: 5px 12px;
  box-sizing: border-box;
  white-space: pre-wrap;
  border: #888 solid 1px;
  border-radius: 5px;
  width: 500px;
  height: 100px;
  overflow: scroll;
  word-break: break-all;
}
.rich-input:empty::after {
  cursor: text;
  content: attr(placeholder);
  color: #333;
  position: absolute;
  top: 5px;
  pointer-events: none;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
</style>