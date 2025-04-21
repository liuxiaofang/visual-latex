<template>
  <div class="latex-toolbar">
    <div v-for="(category, index) in toolbarItems" :key="index" class="toolbar-category">
      <div class="category-title">{{ category.title }}</div>
      <div class="category-items">
        <button
          v-for="item in category.items"
          :key="item.symbol"
          class="toolbar-item"
          @click="insertSymbol(item)"
          :title="item.description"
        >
          <span v-if="item.preview" v-html="item.preview"></span>
          <span v-else>{{ item.symbol }}</span>
        </button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import katex from 'katex'

interface ToolbarItem {
  symbol: string
  insert: string
  description: string
  preview?: string
}

interface ToolbarCategory {
  title: string
  items: ToolbarItem[]
}

const emit = defineEmits<{
  (e: 'insert', value: string): void
}>()

// 预渲染KaTeX公式
const renderKatex = (tex: string): string => {
  try {
    return katex.renderToString(tex, {
      throwOnError: false,
      displayMode: false
    })
  } catch (error) {
    console.error('预览渲染错误:', error)
    return tex
  }
}

const toolbarItems = ref<ToolbarCategory[]>([
  {
    title: '基本运算',
    items: [
      { symbol: '+', insert: '+', description: '加号' },
      { symbol: '−', insert: '-', description: '减号' },
      { symbol: '×', insert: '\\times', description: '乘号', preview: renderKatex('\\times') },
      { symbol: '÷', insert: '\\div', description: '除号', preview: renderKatex('\\div') },
      { symbol: '=', insert: '=', description: '等号' },
      { symbol: '≠', insert: '\\neq', description: '不等号', preview: renderKatex('\\neq') },
    ]
  },
  {
    title: '上下标',
    items: [
      { symbol: 'x²', insert: '^2', description: '平方', preview: renderKatex('x^2') },
      { symbol: 'x³', insert: '^3', description: '立方', preview: renderKatex('x^3') },
      { symbol: 'xⁿ', insert: '^{n}', description: 'n次方', preview: renderKatex('x^{n}') },
      { symbol: 'x₁', insert: '_1', description: '下标1', preview: renderKatex('x_1') },
      { symbol: 'x₍ᵢ₎', insert: '_{i}', description: '下标i', preview: renderKatex('x_{i}') },
    ]
  },
  {
    title: '分数',
    items: [
      { 
        symbol: 'a/b', 
        insert: '\\frac{}{}', 
        description: '分数',
        preview: renderKatex('\\frac{a}{b}')
      },
      { 
        symbol: '½',
        insert: '\\frac{1}{2}',
        description: '二分之一',
        preview: renderKatex('\\frac{1}{2}')
      },
    ]
  },
  {
    title: '根号',
    items: [
      { 
        symbol: '√',
        insert: '\\sqrt{}',
        description: '平方根',
        preview: renderKatex('\\sqrt{x}')
      },
      { 
        symbol: '∛',
        insert: '\\sqrt[3]{}',
        description: '立方根',
        preview: renderKatex('\\sqrt[3]{x}')
      },
      { 
        symbol: '∜',
        insert: '\\sqrt[4]{}',
        description: '四次方根',
        preview: renderKatex('\\sqrt[4]{x}')
      },
    ]
  },
  {
    title: '希腊字母',
    items: [
      { symbol: 'α', insert: '\\alpha', description: 'alpha', preview: renderKatex('\\alpha') },
      { symbol: 'β', insert: '\\beta', description: 'beta', preview: renderKatex('\\beta') },
      { symbol: 'γ', insert: '\\gamma', description: 'gamma', preview: renderKatex('\\gamma') },
      { symbol: 'θ', insert: '\\theta', description: 'theta', preview: renderKatex('\\theta') },
      { symbol: 'π', insert: '\\pi', description: 'pi', preview: renderKatex('\\pi') },
      { symbol: 'Σ', insert: '\\Sigma', description: 'Sigma', preview: renderKatex('\\Sigma') },
    ]
  },
  {
    title: '数学符号',
    items: [
      { symbol: '∑', insert: '\\sum', description: '求和', preview: renderKatex('\\sum') },
      { symbol: '∏', insert: '\\prod', description: '求积', preview: renderKatex('\\prod') },
      { symbol: '∫', insert: '\\int', description: '积分', preview: renderKatex('\\int') },
      { symbol: '∞', insert: '\\infty', description: '无穷', preview: renderKatex('\\infty') },
      { symbol: '∈', insert: '\\in', description: '属于', preview: renderKatex('\\in') },
      { symbol: '⊆', insert: '\\subseteq', description: '子集', preview: renderKatex('\\subseteq') },
    ]
  },
])

const insertSymbol = (item: ToolbarItem) => {
  emit('insert', item.insert)
}
</script>

<style scoped>
.latex-toolbar {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
  padding: 16px;
  background-color: #f5f5f5;
  border: 1px solid #ddd;
  border-radius: 4px;
  margin-bottom: 16px;
}

.toolbar-category {
  min-width: 150px;
}

.category-title {
  font-size: 14px;
  font-weight: bold;
  margin-bottom: 8px;
  color: #666;
}

.category-items {
  display: flex;
  flex-wrap: wrap;
  gap: 4px;
}

.toolbar-item {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 32px;
  height: 32px;
  padding: 4px 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: white;
  cursor: pointer;
  font-size: 16px;
  transition: all 0.2s;
}

.toolbar-item:hover {
  background-color: #e9ecef;
  border-color: #adb5bd;
}

.toolbar-item:active {
  background-color: #dee2e6;
}

:deep(.katex) {
  font-size: 1em;
}
</style>