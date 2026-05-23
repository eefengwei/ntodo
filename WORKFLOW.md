# 文章生产工作流

## 流程概述

1. **提问对话**：就某个技术主题向 Claude 提问，进行深度问答对话
2. **生成文章**：对话结束后，让 Claude 根据对话内容总结提炼，生成 HTML 格式的技术博客文章
3. **发布**：将 HTML 文件保存到本仓库，更新主页文章列表，推送上线

---

## 触发生成文章

对话结束后，向 Claude 发送：

> 根据我们刚才的对话，帮我生成一篇技术博客文章，HTML 格式，风格与仓库现有文章保持一致。

Claude 会参考 `nvidia-cuda-compatibility.html` 的结构和样式生成新文章。

**双语要求**：所有文章必须同时提供英文和中文内容，默认展示英文，通过页面右上角按钮切换。

---

## 发布步骤

```bash
# 1. 将生成的 HTML 文件保存到仓库根目录（文件名用英文连字符，不含空格）
#    示例：cuda-compat-deep-dive.html

# 2. 更新 index.html：
#    - 在对应 section 的 .cards 里新增一个 .card 块（复制现有已发布卡片）
#    - 修改标题、描述、标签、href 链接
#    - 将"已发布"计数加一

# 3. 提交推送
git add <new-article>.html index.html
git commit -m "Add: <文章标题>"
git push
```

GitHub Pages 约 1～2 分钟后自动部署。

---

## 文章文件命名规范

- 英文小写 + 连字符，**不含空格**
- 示例：`cuda-container-driver-compatibility.html`（✅）
- 反例：`NVIDIA GPU_CUDA.html`（❌ 含空格，URL 需转义）

---

## 文章 HTML 结构

每篇文章是独立的自包含 HTML 文件，标准结构：

```
<head>          内联 CSS（深色主题，与主页风格一致；含双语 display 规则）
<html>          class="lang-en"，lang="en"（默认英文）
.doc-header     文章标题、副标题、标签、发布日期；含右上角 .lang-btn 切换按钮
.sidebar/nav    左侧章节导航（scroll spy 高亮）；导航文字双语
.chapter        正文章节（支持代码块、表格、提示框）；正文文字双语
footer          返回首页链接（双语）
<script>        scroll spy 逻辑 + toggleLang() + localStorage 初始化
```

参考：`nvidia-cuda-compatibility.html`

---

## 双语实现规范

**CSS**（在 `<style>` 中添加）：
```css
html.lang-en .zh { display: none; }
html.lang-zh .en { display: none; }
```

**HTML 元素**：行内文字用 `<span class="en">` / `<span class="zh">` 包裹；块级内容用 `<div class="en">` / `<div class="zh">`。

**SVG 文字**：用 `<tspan class="en">` / `<tspan class="zh">`，两个 tspan 共享相同 x/y 坐标。

**切换按钮**（放在 `.doc-header` 内）：
```html
<button class="lang-btn" id="langBtn" onclick="toggleLang()">中文</button>
```

**JS**（追加到 `<script>` 块末尾）：
```javascript
function toggleLang() {
  const html = document.documentElement;
  const btn = document.getElementById('langBtn');
  const isEn = html.classList.contains('lang-en');
  if (isEn) {
    html.classList.replace('lang-en', 'lang-zh');
    html.lang = 'zh-CN';
    btn.textContent = 'EN';
    localStorage.setItem('lang', 'zh');
    document.title = '<中文标题>';
  } else {
    html.classList.replace('lang-zh', 'lang-en');
    html.lang = 'en';
    btn.textContent = '中文';
    localStorage.setItem('lang', 'en');
    document.title = '<English Title>';
  }
}
(function () {
  const saved = localStorage.getItem('lang');
  if (saved === 'zh') {
    const html = document.documentElement;
    html.classList.replace('lang-en', 'lang-zh');
    html.lang = 'zh-CN';
    document.getElementById('langBtn').textContent = 'EN';
    document.title = '<中文标题>';
  }
})();
```

---

## index.html 文章卡片模板

```html
<a class="card" href="<filename>.html">
  <div class="card-header">
    <span class="card-title">
      <span class="en"><English Title></span>
      <span class="zh"><中文标题></span>
    </span>
    <span class="card-arrow">→</span>
  </div>
  <p class="card-desc">
    <span class="en"><one-line summary in English></span>
    <span class="zh"><一句话中文摘要></span>
  </p>
  <div class="card-meta">
    <span class="chip cuda"><span class="chip-dot"></span>CUDA</span>
    <!-- 按需添加标签 -->
    <span class="card-date">2026</span>
  </div>
</a>
```

可用的 chip 样式类：`cuda` `gpu` `docker` `ml` `linux`
