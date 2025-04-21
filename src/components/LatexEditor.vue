<template>
  <div class="latex-editor">
    <LatexToolbar @insert="insertSymbol" />
    <div class="editor-preview-container">
      <div class="editor-section">
        <div class="container-title">编辑区</div>
        <div class="textarea-wrapper">
          <textarea
            v-model="latexInput"
            placeholder="输入LaTeX公式，例如: x^2 + y^2 = z^2"
            @input="updatePreview"
            ref="textareaRef"
          ></textarea>
        </div>
      </div>
      <div class="preview-section">
        <div class="container-title">预览</div>
        <div class="preview-content" ref="previewEl"></div>
        <div v-if="errorMessage" class="error-message">{{ errorMessage }}</div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, nextTick } from 'vue'
import katex from 'katex'
import 'katex/dist/katex.min.css'
import LatexToolbar from './LatexToolbar.vue'

const latexInput = ref('x^2 + y^2 = z^2')
const previewEl = ref<HTMLElement | null>(null)
const textareaRef = ref<HTMLTextAreaElement | null>(null)
const errorMessage = ref('')

const updatePreview = () => {
  if (previewEl.value) {
    try {
      katex.render(latexInput.value, previewEl.value, {
        displayMode: true,
        throwOnError: false
      })
      errorMessage.value = '' // 清除错误信息
    } catch (error) {
      console.error('LaTeX渲染错误:', error)
      errorMessage.value = error instanceof Error ? error.message : '渲染错误'
    }
  }
}

const insertSymbol = (symbol: string) => {
  if (!textareaRef.value) return

  const textarea = textareaRef.value
  const start = textarea.selectionStart
  const end = textarea.selectionEnd
  
  // 在光标位置插入符号
  latexInput.value = 
    latexInput.value.substring(0, start) + 
    symbol + 
    latexInput.value.substring(end)

  // 更新预览
  updatePreview()

  // 设置光标位置
  nextTick(() => {
    const newCursorPos = start + symbol.length
    textarea.focus()
    textarea.setSelectionRange(newCursorPos, newCursorPos)
  })
}

onMounted(() => {
  updatePreview()
})
</script>

<style scoped>
.latex-editor {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.editor-preview-container {
  display: flex;
  flex-direction: column;
  gap: 20px;
  margin-top: 20px;
}

.editor-section, .preview-section {
  width: 100%;
  border: 1px solid #ddd;
  border-radius: 4px;
  padding: 16px;
  background-color: white;
  box-sizing: border-box;
}

.container-title {
  font-size: 16px;
  font-weight: bold;
  margin-bottom: 12px;
  color: #333;
  padding-left: 4px;
  border-left: 4px solid #4CAF50;
}

.textarea-wrapper {
  width: 100%;
  box-sizing: border-box;
}

textarea {
  display: block;
  width: 100%;
  height: 120px;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-family: monospace;
  resize: vertical;
  line-height: 1.5;
  background-color: #f8f9fa;
  box-sizing: border-box;
  margin: 0;
}

textarea:focus {
  outline: none;
  border-color: #4CAF50;
  box-shadow: 0 0 0 2px rgba(76, 175, 80, 0.2);
}

.preview-content {
  min-height: 120px;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #f8f9fa;
  display: flex;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
}

.error-message {
  margin-top: 8px;
  color: #dc3545;
  font-size: 14px;
  padding: 8px;
  background-color: #f8d7da;
  border: 1px solid #f5c6cb;
  border-radius: 4px;
}

:deep(.katex-display) {
  margin: 0;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .latex-editor {
    padding: 10px;
  }

  .editor-preview-container {
    gap: 10px;
  }

  .editor-section, .preview-section {
    padding: 12px;
  }

  textarea, .preview-content {
    height: 100px;
    min-height: 100px;
  }
}
</style>