# Music Pomodoro - Audio Wave Visualization

A beautifully crafted Pomodoro timer with real-time audio wave visualization, featuring a vintage literary aesthetic with cream ivory paper texture.

## Features

### 🎵 Audio Wave Visualization
- Real-time audio capture from browser tab/window
- 200 animated lines forming a circular wave pattern
- Smooth breathing animation effect
- Lines respond to audio frequency data

### ⏱️ Pomodoro Timer
- 25-minute focus timer displayed in the center of the wave circle
- Start, Pause, and Reset controls
- Visual feedback when timer completes (color flash)
- Elegant typography with 3D bold numbers

### 🎨 Visual Design
- **Font**: Cormorant Garamond & Playfair Display (elegant transitional serif italic)
- **Color Scheme**: Royal blue ink (#1a3a5c) on cream ivory paper (#f5f0e8)
- **Texture**: Subtle paper grain overlay with multiply blend mode
- **Button**: 3D cylindrical design with realistic hover/press animations

### ✨ Animations & Transitions
- **Button hover**: Slow pop-up effect (0.6s) with shadow expansion
- **Button press**: Smooth press-down with depth reduction
- **Wave expansion**: Lines expand from center with staggered timing (15ms intervals)
- **Timer fade-in**: Appears after wave animation completes
- **Breathing effect**: Subtle opacity pulse (4s cycle)

## Installation

No installation required! Simply open `circle.html` in a modern browser (Chrome, Edge, or Firefox recommended).

```bash
# Clone the repository
git clone https://github.com/yourusername/music-clock.git

# Open in browser
open circle.html
# or
start circle.html  # Windows
```

## Usage

1. **Start Audio Capture**
   - Click the central "开始" (Start) button
   - Select a browser tab or window with audio
   - Enable "Share audio" in the browser prompt

2. **Use Pomodoro Timer**
   - Timer appears after wave animation completes
   - Click "Start" to begin 25-minute countdown
   - Click "Pause" to pause the timer
   - Click "Reset" to reset to 25 minutes

3. **Stop Audio**
   - Stop sharing in your browser's sharing controls
   - Wave circle will collapse and button will reappear

## Core Implementation

### Audio Capture & Visualization

```javascript
// Audio context and analyser setup
this.audio_ctx = new AudioContext();
const source = this.audio_ctx.createMediaStreamSource(
    new MediaStream([audio_track])
);

this.analyser = this.audio_ctx.createAnalyser();
this.analyser.fftSize = 512;
this.analyser.smoothingTimeConstant = 0.8;
source.connect(this.analyser);

// Real-time visualization loop
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

### Wave Expansion Animation

```javascript
animate_lines_in() {
    this.lines.forEach((line, i) => {
        const angle = parseFloat(line.dataset.angle);
        // Staggered animation with 15ms delay per line
        setTimeout(() => {
            line.classList.add("visible");
            line.style.transform = `translate(-50%, -50%) rotate(${angle}deg) scale(1)`;
        }, i * 15);
    });

    // Show timer after animation completes
    setTimeout(() => {
        document.getElementById('timer-container').classList.add('visible');
        document.getElementById('timer-controls').classList.add('visible');
    }, this.lines_total * 15 + 500);
}
```

### 3D Button Effect

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

### Pomodoro Timer Logic

```javascript
const pomodoro = {
    duration: 25 * 60, // 25 minutes
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
        // Visual feedback
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

### Paper Texture Overlay

```css
body::before {
    content: "";
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: url("data:image/svg+xml,..."); /* SVG noise filter */
    pointer-events: none;
    z-index: 100;
    mix-blend-mode: multiply;
}
```

## Browser Compatibility

| Browser | Audio Capture | Animations | Status |
|---------|--------------|------------|--------|
| Chrome  | ✅ Full      | ✅ Full    | Recommended |
| Edge    | ✅ Full      | ✅ Full    | Recommended |
| Firefox | ✅ Full      | ✅ Full    | Supported |
| Safari  | ⚠️ Limited   | ✅ Full    | Partial |

## Technical Details

- **No dependencies**: Pure HTML, CSS, and JavaScript
- **Responsive**: Uses `vmin` units for scaling
- **Performance**: 60fps animations with `requestAnimationFrame`
- **Memory**: Proper cleanup of audio resources on stop

## File Structure

```
music-clock/
├── circle.html      # Main application file
└── README.md        # This documentation
```

## Future Enhancements

- [ ] Customizable timer duration (5, 15, 25, 45 minutes)
- [ ] Break timer between Pomodoro sessions
- [ ] Audio visualization themes (different wave patterns)
- [ ] Statistics tracking
- [ ] Keyboard shortcuts
- [ ] Dark mode support

## License

MIT License - feel free to use and modify!

## Credits

- Fonts: [Cormorant Garamond](https://fonts.google.com/specimen/Cormorant+Garamond) & [Playfair Display](https://fonts.google.com/specimen/Playfair+Display)
- Audio API: Web Audio API
- Design: Custom vintage literary aesthetic

---

Made with ❤️ for focused work sessions
