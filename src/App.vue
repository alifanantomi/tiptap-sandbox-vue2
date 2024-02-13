<template>
  <div class="container-editor">
    <div v-if="editor">
      <button @click="editor.chain().focus().toggleBold().run()" :disabled="!editor.can().chain().focus().toggleBold().run()" :class="{ 'is-active': editor.isActive('bold') }">
        <i class="ri-bold"></i>
      </button>
      <button @click="editor.chain().focus().toggleItalic().run()" :disabled="!editor.can().chain().focus().toggleItalic().run()" :class="{ 'is-active': editor.isActive('italic') }">
        <i class="ri-italic"></i>
      </button>
      <button @click="editor.chain().focus().toggleStrike().run()" :disabled="!editor.can().chain().focus().toggleStrike().run()" :class="{ 'is-active': editor.isActive('strike') }">
        <i class="ri-strikethrough"></i>
      </button>
      <button @click="editor.chain().focus().toggleCode().run()" :disabled="!editor.can().chain().focus().toggleCode().run()" :class="{ 'is-active': editor.isActive('code') }">
        <i class="ri-code-s-slash-line"></i>
      </button>
      <button @click="editor.chain().focus().unsetAllMarks().run()">
        <i class="ri-format-clear"></i>
      </button>
      <button @click="editor.chain().focus().clearNodes().run()">
        clear nodes
      </button>
      <button @click="editor.chain().focus().setParagraph().run()" :class="{ 'is-active': editor.isActive('paragraph') }">
        <i class="ri-paragraph"></i>
      </button>
      <button @click="editor.chain().focus().toggleHeading({ level: 1 }).run()" :class="{ 'is-active': editor.isActive('heading', { level: 1 }) }">
        <i class="ri-h-1"></i>
      </button>
      <button @click="editor.chain().focus().toggleHeading({ level: 2 }).run()" :class="{ 'is-active': editor.isActive('heading', { level: 2 }) }">
        <i class="ri-h-2"></i>
      </button>
      <button @click="editor.chain().focus().toggleHeading({ level: 3 }).run()" :class="{ 'is-active': editor.isActive('heading', { level: 3 }) }">
        <i class="ri-h-3"></i>
      </button>
      <button @click="editor.chain().focus().toggleHeading({ level: 4 }).run()" :class="{ 'is-active': editor.isActive('heading', { level: 4 }) }">
        <i class="ri-h-4"></i>
      </button>
      <button @click="editor.chain().focus().toggleHeading({ level: 5 }).run()" :class="{ 'is-active': editor.isActive('heading', { level: 5 }) }">
        <i class="ri-h-5"></i>
      </button>
      <button @click="editor.chain().focus().toggleHeading({ level: 6 }).run()" :class="{ 'is-active': editor.isActive('heading', { level: 6 }) }">
        <i class="ri-h-6"></i>
      </button>
      <button @click="editor.chain().focus().toggleBulletList().run()" :class="{ 'is-active': editor.isActive('bulletList') }">
        <i class="ri-list-unordered"></i>
      </button>
      <button @click="editor.chain().focus().toggleOrderedList().run()" :class="{ 'is-active': editor.isActive('orderedList') }">
        <i class="ri-list-ordered-2"></i>
      </button>
      <button @click="editor.chain().focus().toggleCodeBlock().run()" :class="{ 'is-active': editor.isActive('codeBlock') }">
        <i class="ri-code-block"></i>
      </button>
      <button @click="editor.chain().focus().toggleBlockquote().run()" :class="{ 'is-active': editor.isActive('blockquote') }">
        <i class="ri-quote-text"></i>
      </button>
      <button @click="editor.chain().focus().setHorizontalRule().run()">
        <i class="ri-separator"></i>
      </button>
      <button @click="editor.chain().focus().setHardBreak().run()">
        <i class="ri-space"></i>
      </button>
      <button @click="editor.chain().focus().undo().run()" :disabled="!editor.can().chain().focus().undo().run()">
        <i class="ri-arrow-go-back-line"></i>
      </button>
      <button @click="editor.chain().focus().redo().run()" :disabled="!editor.can().chain().focus().redo().run()">
        <i class="ri-arrow-go-forward-line"></i>
      </button>
    </div>
    <editor-content :editor="editor" />
    <div class="character-count" v-if="editor">
      {{ editor.storage.characterCount.characters() }}/{{ limit }} characters
      <br>
      {{ editor.storage.characterCount.words() }} words
    </div>
  </div>
</template>

<script>
import CharacterCount from '@tiptap/extension-character-count'

import Document from '@tiptap/extension-document'
import Placeholder from '@tiptap/extension-placeholder'
import StarterKit from '@tiptap/starter-kit'
import { Editor, EditorContent } from '@tiptap/vue-2'
import 'remixicon/fonts/remixicon.css'

const CustomDocument = Document.extend({
  content: 'heading block*',
})

export default {
  components: {
    EditorContent,
  },

  data() {
    return {
      editor: null,
      limit: 280
    }
  },

  mounted() {
    this.editor = new Editor({
      extensions: [
        CustomDocument,
        StarterKit.configure({
          document: false,
        }),
        Placeholder.configure({
          placeholder: ({ node }) => {
            if (node.type.name === 'heading') {
              return 'What’s the title?'
            }

            return 'Can you add some further context?'
          },
        }),
        CharacterCount.configure({
          limit: this.limit,
        }),
      ],
      content: `
        <h1 id='title-heading'>
          It’ll always have a heading …
        </h1>
        <p>
          … if you pass a custom document. That’s the beauty of having full control over the schema.
        </p>
      `,
    })
  },

  beforeUnmount() {
    this.editor.destroy()
  },
}
</script>

<style lang="scss">
.container-editor {
  border: 3px solid black;
  border-radius: 0.75em;
  padding: 16px;
}

/* Basic editor styles */
.tiptap {
  > * + * {
    margin-top: 0.75em;
  }
}

.tiptap:focus-visible {
  outline: none;
}

/* Placeholder (at the top) */
/*.tiptap p.is-editor-empty:first-child::before {
  content: attr(data-placeholder);
  float: left;
  color: #ced4da;
  pointer-events: none;
  height: 0;
}*/

/* Placeholder (on every new line) */
.tiptap .is-empty::before {
  content: attr(data-placeholder);
  float: left;
  color: #ced4da;
  pointer-events: none;
  height: 0;
}

.character-count {
  margin-top: 1rem;
  color: #868e96;
}
</style>