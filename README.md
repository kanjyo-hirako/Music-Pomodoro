# 音乐番茄钟 - 音频波浪可视化

一个精美的番茄钟计时器，配合实时音频波浪可视化，采用复古文学风格的奶油象牙纸张纹理设计。

## 功能特性

### 🎵 音频波浪可视化
- 从浏览器标签页/窗口实时捕获音频
- 200条动画线条组成圆形波浪图案
- 平滑的呼吸动画效果
- 线条随音频频率数据实时响应

### ⏱️ 番茄钟计时器
- 25分钟专注计时器显示在波浪环中央
- 开始、暂停、重置控制按钮
- 计时完成时的视觉反馈（颜色闪烁）
- 3D粗体数字显示，优雅排版

### 🎨 视觉设计
- **字体**：Cormorant Garamond 和 Playfair Display（优雅的过渡衬线斜体）
- **配色方案**：皇家蓝墨水 (#1a3a5c) 配奶油象牙纸 (#f5f0e8)
- **纹理**：细腻的纸张纹理覆盖层，使用 multiply 混合模式
- **按钮**：3D圆柱设计，带真实的悬停/按下动画

### ✨ 动画与过渡效果
- **按钮悬停**：缓慢弹起效果（0.6秒），阴影扩展
- **按钮按下**：平滑按下，深度减小
- **波浪展开**：线条从中心依次展开，间隔15毫秒
- **计时器淡入**：波浪动画完成后显示
- **呼吸效果**：微妙的透明度脉动（4秒周期）

## 安装方法

无需安装！只需在现代浏览器中打开 `circle.html` 即可（推荐 Chrome、Edge 或 Firefox）。

```bash
# 克隆仓库
git clone https://github.com/yourusername/music-clock.git

# 在浏览器中打开
open circle.html
# 或 Windows 系统
start circle.html
```

## 使用方法

1. **开始音频捕获**
   - 点击中央的"开始"按钮
   - 选择一个正在播放音频的浏览器标签页或窗口
   - 在浏览器提示中启用"共享音频"

2. **使用番茄钟计时器**
   - 波浪动画完成后，计时器会显示在中央
   - 点击"Start"开始25分钟倒计时
   - 点击"Pause"暂停计时
   - 点击"Reset"重置为25分钟

3. **停止音频**
   - 在浏览器的共享控制中停止共享
   - 波浪环会收缩，按钮会重新出现

## 核心实现

### 音频捕获与可视化

```javascript
// 音频上下文和分析器设置
this.audio_ctx = new AudioContext();
const source = this.audio_ctx.createMediaStreamSource(
    new MediaStream([audio_track])
);

this.analyser = this.audio_ctx.createAnalyser();
this.analyser.fftSize = 512;
this.analyser.smoothingTimeConstant = 0.8;
source.connect(this.analyser);

// 实时可视化循环
run() {
    const loop = () => {
        this.analyser.getByteTimeDomainData(this.data_array);
        const bin_count = this.data_array.length;

        for (let i = 0; i < this.lines_total; i++) {
            const bin_index = Math.floor(i / this.lines_total * bin_count);
            const value = Math.abs(this.data_array[bin_index] - 128) / 128 * 3;
            this.lines[i].style.setProperty("--s", value);
        }

        this.anim_id = requestAnimationFrame(loop);
    };
    loop();
}
```

### 波浪展开动画

```javascript
animate_lines_in() {
    this.lines.forEach((line, i) => {
        const angle = parseFloat(line.dataset.angle);
        // 依次展开动画，每条线延迟15毫秒
        setTimeout(() => {
            line.classList.add("visible");
            line.style.transform = `translate(-50%, -50%) rotate(${angle}deg) scale(1)`;
        }, i * 15);
    });

    // 动画完成后显示计时器
    setTimeout(() => {
        document.getElementById('timer-container').classList.add('visible');
        document.getElementById('timer-controls').classList.add('visible');
    }, this.lines_total * 15 + 500);
}
```

### 3D按钮效果

```css
#start-btn {
    background: linear-gradient(180deg, #faf6ef 0%, #f0ead8 100%);
    box-shadow:
        0 0.8rem 0 #8a7e6e,
        0 1rem 0 #7a6e5e,
        0 1.2rem 0 #6a5e4e,
        0 1.4rem 0.6rem rgba(100,80,60,0.15),
        0 2rem 2rem rgba(100,80,60,0.1),
        inset 0 1rem 1.5rem rgba(255,255,250,0.6),
        inset 0 -0.8rem 1.2rem rgba(100,80,60,0.08);
    transition: transform 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
}

#start-btn:hover {
    transform: translateY(-1.5rem) scale(1.06);
}
```

### 番茄钟计时器逻辑

```javascript
const pomodoro = {
    duration: 25 * 60, // 25分钟
    remaining: 25 * 60,
    
    start() {
        this.interval = setInterval(() => {
            this.remaining--;
            this.updateDisplay();
            if (this.remaining <= 0) this.complete();
        }, 1000);
    },
    
    complete() {
        this.pause();
        // 视觉反馈
        this.display.style.color = '#8b4513';
        setTimeout(() => {
            this.display.style.color = '#3a3028';
        }, 2000);
    },
    
    updateDisplay() {
        const minutes = Math.floor(this.remaining / 60);
        const seconds = this.remaining % 60;
        this.display.textContent = 
            `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
    }
};
```

### 纸张纹理覆盖

```css
body::before {
    content: "";
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: url("data:image/svg+xml,..."); /* SVG噪点滤镜 */
    pointer-events: none;
    z-index: 100;
    mix-blend-mode: multiply;
}
```

## 浏览器兼容性

| 浏览器  | 音频捕获 | 动画效果 | 状态 |
|---------|----------|----------|------|
| Chrome  | ✅ 完全  | ✅ 完全  | 推荐 |
| Edge    | ✅ 完全  | ✅ 完全  | 推荐 |
| Firefox | ✅ 完全  | ✅ 完全  | 支持 |
| Safari  | ⚠️ 有限  | ✅ 完全  | 部分 |

## 技术细节

- **无依赖**：纯 HTML、CSS 和 JavaScript
- **响应式设计**：使用 `vmin` 单位自动缩放
- **性能优化**：使用 `requestAnimationFrame` 实现 60fps 动画
- **内存管理**：停止时正确清理音频资源

## 文件结构

```
music-clock/
├── circle.html      # 主应用文件
└── README.md        # 本文档
```

## 未来增强功能

- [ ] 可自定义计时时长（5、15、25、45分钟）
- [ ] 番茄钟之间的休息计时器
- [ ] 音频可视化主题（不同的波浪图案）
- [ ] 统计数据追踪
- [ ] 键盘快捷键支持
- [ ] 深色模式支持

## 许可证

MIT 许可证 - 随意使用和修改！

## 致谢

- 字体：[Cormorant Garamond](https://fonts.google.com/specimen/Cormorant+Garamond) 和 [Playfair Display](https://fonts.google.com/specimen/Playfair+Display)
- 音频 API：Web Audio API
- 设计：自定义复古文学风格

---

用 ❤️ 为专注工作而生
