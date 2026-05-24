# 项目指令

这是 Frank 的技术博客项目，托管于 GitHub Pages（https://eefengwei.github.io/ntodo/）。博客以问答对话形式生产文章：Frank 就技术主题提问，对话结束后由 Claude 提炼总结，生成 HTML 文章并发布。

---

## 生成新文章

收到"帮我生成一篇技术博客文章"的请求时：

1. 参考 `nvidia-cuda-compatibility.html` 的结构和样式
2. 文件名：英文小写 + 连字符，不含空格（如 `cuda-deep-dive.html`）
3. 所有文章必须**中英双语**，默认英文，右上角按钮切换（见下方双语规范）
4. 生成后更新 `index.html`，在对应 section 的 `.cards` 里新增卡片，将"已发布"计数加一

---

## 发布流程

```bash
git add <new-article>.html index.html
git commit -m "Add: <文章标题>"
git push
```

提交信息不加 Co-Authored-By 行。Author 统一使用 `Frank <wfeng6@gmail.com>`（已通过项目级 git config 设置，无需手动指定）。

---

## 文章 HTML 结构

```
<html>          class="lang-en"，lang="en"（默认英文）
<head>          内联 CSS；含双语 display 规则；含 lang-btn 样式
.doc-header     标题、副标题、标签、日期；右上角 .lang-btn 切换按钮
.sidebar/nav    左侧章节导航（scroll spy 高亮）；导航文字双语
.chapter        正文章节（代码块、表格、提示框）；正文文字双语
footer          返回首页链接（双语）
<script>        scroll spy + toggleLang() + localStorage 初始化
```

---

## 双语实现规范

**CSS**：
```css
html.lang-en .zh { display: none; }
html.lang-zh .en { display: none; }
```

**行内文字**：`<span class="en">...</span><span class="zh">...</span>`

**块级内容**：`<div class="en">...</div><div class="zh">...</div>`

**SVG 文字**：`<tspan class="en">` / `<tspan class="zh">`，两者共享相同 x/y 坐标

**切换按钮**（放在 `.doc-header` 内）：
```html
<button class="lang-btn" id="langBtn" onclick="toggleLang()">中文</button>
```

**JS**（追加到 `<script>` 末尾，替换 `<英文标题>` / `<中文标题>`）：
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
    document.title = '<英文标题>';
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

## 标题排版规范

- 主标题（`h1` / `.doc-title`）**不使用 `<br>` 强制换行**
- `index.html` 的 `.hero h1` 设置 `white-space: nowrap; font-size: 24px`
- 若标题过长导致换行，优先缩小 font-size，而非拆行

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
    <span class="en"><one-line summary></span>
    <span class="zh"><一句话摘要></span>
  </p>
  <div class="card-meta">
    <span class="chip cuda"><span class="chip-dot"></span>CUDA</span>
    <span class="card-date">2026</span>
  </div>
</a>
```

可用 chip 类：`cuda` `gpu` `docker` `ml` `linux`
