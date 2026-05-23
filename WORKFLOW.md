# 文章生产工作流

## 流程概述

1. **提问对话**：就某个技术主题向 Claude 提问，进行深度问答对话
2. **生成文章**：对话结束后，让 Claude 根据对话内容总结提炼，生成 HTML 格式的技术博客文章
3. **发布**：将 HTML 文件保存到本仓库，更新主页文章列表，推送上线

---

## 触发生成文章

对话结束后，向 Claude 发送：

> 根据我们刚才的对话，帮我生成一篇技术博客文章，HTML 格式，风格与仓库现有文章保持一致。

Claude 会参考 `NVIDIA GPU_CUDA_Software_Compatibility.html` 的结构和样式生成新文章。

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
<head>          内联 CSS（深色主题，与主页风格一致）
.doc-header     文章标题、副标题、标签、发布日期
.sidebar/nav    左侧章节导航（scroll spy 高亮）
.chapter        正文章节（支持代码块、表格、提示框）
footer          返回首页链接
<script>        scroll spy 逻辑
```

参考：`nvidia-cuda-compatibility.html`

---

## index.html 文章卡片模板

```html
<a class="card" href="<filename>.html">
  <div class="card-header">
    <span class="card-title"><文章标题></span>
    <span class="card-arrow">→</span>
  </div>
  <p class="card-desc"><一句话摘要></p>
  <div class="card-meta">
    <span class="chip cuda"><span class="chip-dot"></span>CUDA</span>
    <!-- 按需添加标签 -->
    <span class="card-date">2026</span>
  </div>
</a>
```

可用的 chip 样式类：`cuda` `gpu` `docker` `ml` `linux`
