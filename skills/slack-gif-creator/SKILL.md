---
name: slack-gif-creator
description: 用于创建针对 Slack 优化的动态 GIF 的知识和工具。提供约束条件、验证工具和动画概念。当用户请求为 Slack 制作动态 GIF 时使用，例如"为我制作一个 X 做 Y 的 Slack GIF"。
license: Complete terms in LICENSE.txt
---

# Slack GIF 创建器

提供工具和知识用于创建针对 Slack 优化的动态 GIF 的工具包。

## Slack 要求

**尺寸：**
- 表情包 GIF：128x128（推荐）
- 消息 GIF：480x480

**参数：**
- FPS：10-30（越低文件越小）
- 颜色：48-128（越少文件越小）
- 持续时间：表情包 GIF 保持在 3 秒以内

## 核心工作流程

```python
from core.gif_builder import GIFBuilder
from PIL import Image, ImageDraw

# 1. 创建构建器
builder = GIFBuilder(width=128, height=128, fps=10)

# 2. 生成帧
for i in range(12):
    frame = Image.new('RGB', (128, 128), (240, 248, 255))
    draw = ImageDraw.Draw(frame)

    # 使用 PIL 基元绘制动画
    # （圆形、多边形、线条等）

    builder.add_frame(frame)

# 3. 保存并优化
builder.save('output.gif', num_colors=48, optimize_for_emoji=True)
```

## 绘制图形

### 处理用户上传的图片
如果用户上传了图片，考虑他们是否想要：
- **直接使用**（例如"将它做成动画"、"把它分成多帧"）
- **作为灵感**（例如"做一个类似这样的"）

使用 PIL 加载和处理图片：
```python
from PIL import Image

uploaded = Image.open('file.png')
# 直接使用，或仅作为颜色/样式参考
```

### 从零开始绘制
从零开始绘制图形时，使用 PIL ImageDraw 基元：

```python
from PIL import ImageDraw

draw = ImageDraw.Draw(frame)

# 圆形/椭圆形
draw.ellipse([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b), width=3)

# 星形、三角形、任意多边形
points = [(x1, y1), (x2, y2), (x3, y3), ...]
draw.polygon(points, fill=(r, g, b), outline=(r, g, b), width=3)

# 线条
draw.line([(x1, y1), (x2, y2)], fill=(r, g, b), width=5)

# 矩形
draw.rectangle([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b), width=3)
```

**不要使用：** 表情符号字体（跨平台不可靠）或假设此技能中存在预打包图形。

### 让图形看起来精美

图形应该看起来精致有创意，而不是基础平庸。以下是方法：

**使用较粗的线条** - 始终将轮廓和线条的 `width` 设置为 2 或更高。细线（width=1）看起来粗糙且业余。

**添加视觉深度**：
- 为背景使用渐变（`create_gradient_background`）
- 叠加多个形状增加复杂度（例如，星形内嵌较小星形）

**让形状更有趣**：
- 不要只画一个普通圆形——添加高光、光环或图案
- 星形可以有光晕效果（在后面绘制更大的半透明版本）
- 组合多个形状（星形 + 闪光、圆形 + 光环）

**注重颜色**：
- 使用鲜艳、互补的颜色
- 增加对比度（深色形状配浅色轮廓，浅色形状配深色轮廓）
- 考虑整体构图

**对于复杂形状**（心形、雪花等）：
- 使用多边形和椭圆的组合
- 仔细计算点坐标以保证对称
- 添加细节（心形可以有高光曲线，雪花有复杂的分支）

要有创意，注重细节！好的 Slack GIF 应该看起来精致，而不是占位符图形。

## 可用工具

### GIFBuilder（`core.gif_builder`）
组装帧并针对 Slack 优化：
```python
builder = GIFBuilder(width=128, height=128, fps=10)
builder.add_frame(frame)  # 添加 PIL 图像
builder.add_frames(frames)  # 添加帧列表
builder.save('out.gif', num_colors=48, optimize_for_emoji=True, remove_duplicates=True)
```

### 验证器（`core.validators`）
检查 GIF 是否符合 Slack 要求：
```python
from core.validators import validate_gif, is_slack_ready

# 详细验证
passes, info = validate_gif('my.gif', is_emoji=True, verbose=True)

# 快速检查
if is_slack_ready('my.gif'):
    print("准备好了！")
```

### 缓动函数（`core.easing`）
流畅的运动而非线性运动：
```python
from core.easing import interpolate

# 进度从 0.0 到 1.0
t = i / (num_frames - 1)

# 应用缓动
y = interpolate(start=0, end=400, t=t, easing='ease_out')

# 可用：linear、ease_in、ease_out、ease_in_out、
#      bounce_out、elastic_out、back_out
```

### 帧辅助函数（`core.frame_composer`）
常见需求的便捷函数：
```python
from core.frame_composer import (
    create_blank_frame,         # 纯色背景
    create_gradient_background,  # 垂直渐变
    draw_circle,                # 圆形辅助函数
    draw_text,                  # 简单文字渲染
    draw_star                   # 五角星
)
```

## 动画概念

### 摇晃/振动
通过振荡偏移对象位置：
- 使用带有帧索引的 `math.sin()` 或 `math.cos()`
- 添加小的随机变化以获得自然感
- 应用于 x 和/或 y 位置

### 脉冲/心跳
节律性地缩放对象大小：
- 使用 `math.sin(t * frequency * 2 * math.pi)` 实现平滑脉冲
- 心跳效果：两次快速脉冲后停顿（调整正弦波）
- 在基础大小的 0.8 到 1.2 之间缩放

### 弹跳
对象落下并弹跳：
- 使用 `interpolate()` 和 `easing='bounce_out'` 实现落地效果
- 使用 `easing='ease_in'` 实现下落（加速）
- 每帧增加 y 速度模拟重力

### 旋转
绕中心旋转对象：
- PIL：`image.rotate(angle, resample=Image.BICUBIC)`
- 摇摆效果：使用正弦波代替线性角度

### 淡入/淡出
逐渐出现或消失：
- 创建 RGBA 图像，调整 alpha 通道
- 或使用 `Image.blend(image1, image2, alpha)`
- 淡入：alpha 从 0 到 1
- 淡出：alpha 从 1 到 0

### 滑动
对象从屏幕外移动到位置：
- 起始位置：在帧边界之外
- 结束位置：目标位置
- 使用 `interpolate()` 和 `easing='ease_out'` 实现平滑停止
- 超出效果：使用 `easing='back_out'`

### 缩放
缩放和定位实现缩放效果：
- 放大：从 0.1 缩放到 2.0，裁剪中心
- 缩小：从 2.0 缩放到 1.0
- 可以添加运动模糊增加戏剧感（PIL 滤镜）

### 爆炸/粒子爆发
创建向外辐射的粒子：
- 以随机角度和速度生成粒子
- 每帧更新每个粒子：`x += vx`，`y += vy`
- 添加重力：`vy += gravity_constant`
- 随时间淡出粒子（减少 alpha）

## 优化策略

仅在被要求缩小文件大小时，实施以下几种方法：

1. **减少帧数** - 降低 FPS（10 而非 20）或缩短持续时间
2. **减少颜色** - `num_colors=48` 而非 128
3. **缩小尺寸** - 128x128 而非 480x480
4. **删除重复帧** - 在 save() 中使用 `remove_duplicates=True`
5. **表情包模式** - `optimize_for_emoji=True` 自动优化

```python
# 表情包最大优化
builder.save(
    'emoji.gif',
    num_colors=48,
    optimize_for_emoji=True,
    remove_duplicates=True
)
```

## 理念

此技能提供：
- **知识**：Slack 的要求和动画概念
- **工具**：GIFBuilder、验证器、缓动函数
- **灵活性**：使用 PIL 基元创建动画逻辑

它不提供：
- 固化的动画模板或预制函数
- 表情符号字体渲染（跨平台不可靠）
- 内置预打包图形库

**关于用户上传的说明**：此技能不包含预建图形，但如果用户上传了图片，使用 PIL 加载并处理——根据他们的请求判断是直接使用还是仅作为灵感。

要有创意！组合多种概念（弹跳 + 旋转、脉冲 + 滑动等），充分利用 PIL 的全部功能。

## 依赖项

```bash
pip install pillow imageio numpy
```
