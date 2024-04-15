<template>
  <main class="w-full h-full flex p-4 flex-col justify-center items-center gap-x-4">
    <ul v-if="editor" class="w-1/2 border border-black">
      <li v-for="item in items" :key="item.id" class="flex justify-center items-center w-full">
        <span class="mr-auto">{{ item.value }}</span>
        <button class="border rounded-md p-2" @click="handleItemClick(item)">插入文献</button>
      </li>
    </ul>
    <div> 目前光标位置在：{{ position }}</div>
    <div class="w-1/2 border border-black">
      <editor-content :editor="editor" />
    </div>
  </main>
</template>

<script setup>
import { useEditor, EditorContent } from '@tiptap/vue-3'
import Superscript from '@tiptap/extension-superscript'
import StarterKit from '@tiptap/starter-kit'
import { computed, ref } from 'vue';
const items = ref(
[
  {
    id:1,
    value:'这是第一篇参考文献'
  },
  {
    id:2,
    value:'这是第二篇参考文献'
  }
])
const position = computed(()=>editor.value.reactiveState.value.selection.$anchor.pos)
const editor = useEditor({
  extensions: [StarterKit,Superscript],
  editorProps: {
    attributes: {
      class: 'prose dark:prose-invert prose-sm sm:prose-base lg:prose-lg xl:prose-2xl m-5 focus:outline-none',
    },
  },
  content: `
    <h2>
      这是一个测试标题
    </h2>
    <p>
      这是一段测试文字
    </p>
    <h3>参考文献</h3>
    <ul>
    </ul>
  `,
})

function handleItemClick(item) {
  if (!editor) {
    return;
  }

// 1、获取光标位置
const position = editor.value.reactiveState.value.selection.$anchor.pos

// 2、在当前光标位置后面加上一个上标
const supContent = `<sup>[${item.id}]</sup>`
editor.value.commands.insertContentAt(position,supContent)


// 3、参考文献后面加入item.name
const listContent = `<li>${item.value}</li>`

const bulletList = editor.value.$node('bulletList')

const listItems = bulletList.children

const lastListItem = listItems[0]

editor.value.commands.insertContentAt(lastListItem.lastChild, listContent)
}
</script>