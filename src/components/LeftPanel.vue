<template>
    <!-- 左侧轨道面板 -->
    <div class="sidebar track-panel">
        <h2 class="track-title">声轨区</h2>

        <div class="track-controls">
            <button class="control-btn" title="静音" @click="mute">M</button>
            <button class="control-btn" title="独奏" @click="solo">S</button>
            <button class="control-btn" title="录音" @click="record">🎤</button>
            <button class="control-btn" title="钢琴卷轴" @click="openPianoRoll">🎹</button>
        </div>

        <button class="add-track-btn" @click="addTrack">+ 添加声轨</button>

        <div class="volume-section">
            <label for="volume-control" class="volume-label">主声轨音量</label>
            <input type="range" id="volume-control" min="0" max="100" v-model="volume" class="volume-range" />
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            volume: 80
        }
    },
    methods: {
        addTrack() {
            fetch('/add_track', { method: 'POST' })
                .then(res => res.json())
                .then(data => {
                    alert('添加声轨成功')
                    console.log(data.note_blocks) // 可替代 renderNoteBlocks(data.note_blocks)
                    // 调用子组件/事件或 Vue 状态更新逻辑
                })
                .catch(err => {
                    console.error(err)
                    alert('调用失败')
                })
        },
        mute() {
            console.log("静音")
        },
        solo() {
            console.log("独奏")
        },
        record() {
            console.log("录音")
        },
        openPianoRoll() {
            console.log("打开钢琴卷轴")
        }
    }
}
</script>

<style scoped>
.sidebar,
.track-panel {
    background-color: #1e1e1e;
    padding: 20px 14px 0 14px;
    height: 100vh;
    overflow-y: auto;
    box-sizing: border-box;
    width: 220px;
    display: flex;
    flex-direction: column;
    align-items: stretch;
}

.track-title {
    font-size: 1.1rem;
    font-weight: bold;
    margin-bottom: 16px;
    color: #fff;
    letter-spacing: 0.8px;
}

.track-controls {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 16px;
}

.control-btn {
    background: #232323;
    border: none;
    color: #bbb;
    font-size: 1rem;
    border-radius: 5px;
    padding: 6px 10px;
    cursor: pointer;
    transition: background 0.2s, color 0.2s, transform 0.1s;
}

.control-btn:hover {
    background: #0d6efd;
    color: #fff;
    transform: translateY(-1.5px) scale(1.05);
}

.add-track-btn {
    width: 100%;
    padding: 10px 0;
    background: linear-gradient(90deg, #0d6efd 60%, #38b6ff 100%);
    border: none;
    color: #fff;
    border-radius: 6px;
    font-size: 1rem;
    font-weight: bold;
    cursor: pointer;
    margin: 14px 0 24px 0;
    box-shadow: 0 1px 6px #0002;
    transition: background 0.2s, transform 0.1s;
    letter-spacing: 0.8px;
}

.add-track-btn:hover {
    background: linear-gradient(90deg, #38b6ff 0%, #0d6efd 100%);
    transform: translateY(-1.5px) scale(1.02);
    box-shadow: 0 3px 12px #0d6efd33;
}

.volume-section {
    margin-top: 8px;
}

.volume-label {
    font-size: 0.95rem;
    font-weight: 500;
    margin-bottom: 6px;
    display: block;
    color: #eee;
}

.volume-range {
    width: 100%;
    accent-color: #0d6efd;
    margin-bottom: 8px;
}

.left-panel,
.right-panel {
    width: 220px;
    background: #1e1e1e;
}
</style>
