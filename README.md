# 点彩画派 · 通用图片点彩工具（Pointillism Studio）

把任意图片「用色点重新画一遍」的网页工具：**自然散布函数随机落点，每个色点取源图像同一坐标的真实像素色**，零杜撰颜色、零缩放，横竖方图皆可。

## 功能

- **任意图片输入**：JPG / PNG / WebP / GIF / AVIF / BMP / SVG；支持点击选择、拖拽到画布、Ctrl+V 粘贴、`?img=图片名` 直载同目录文件
- **6 种自然散布方式**：泊松盘蓝噪声（Bridson）、R2 低差异序列、黄金比例错行、噪波密度（手工感）、逐格随机、完全随机散点
- **参数可调**：平均点距 2–8px、点径 0.5–2.0、点径自然抖动、画布底色取色器
- **按上传原图尺寸处理**，不做任何缩放
- **GPU 渲染**：全部色点一次性上传 WebGL，单次 `drawArrays(POINTS)` 绘制完成（自动圆形软边抗锯齿）；WebGL 不可用时自动回退 Canvas2D
- **双格式导出**：
  - SVG（矢量，每个色点一个 `<circle>`，点击下载时才懒生成）
  - PNG（位图，按像素原样导出）

## 使用

```bash
# 直接双击打开 pointillism-studio.html 即可，无需服务器

# 或本地起个静态服务
python3 -m http.server 8000
# 打开 http://localhost:8000/pointillism-studio.html
```

拖入一张图片 → 点击「重新撒点」换随机布局 → 调整点距/点径 → 下载 SVG 或 PNG。

## 技术要点

- 逐点取色：`sampleCoord(x, y, W, H)` 严格按图像尺寸钳制坐标（历史上竖图 bug 的根因，现已单测覆盖）
- 散布函数与取色钳制为纯函数，Node 单测覆盖 6 模式 × 横/竖/方三种画布
- 状态栏实时显示：点数、生成/取色/绘制耗时、渲染后端（WebGL GPU / Canvas2D 回退）

## 文件

| 文件 | 说明 |
| --- | --- |
| `pointillism-studio.html` | 工具本体（单文件，含全部逻辑） |
| `image-data.js` | 内置示例图（base64 data URI，可选删） |

> 提示：HEIC（iPhone 照片）/ TIFF / RAW 等浏览器无法解码的格式，请先转换后再使用（macOS：「预览」→ 文件 → 导出，或 `sips -s format jpeg 照片.heic --out 照片.jpg`）。
