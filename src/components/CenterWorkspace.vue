<template>
    <div class="main-workspace d-flex flex-column">
        <div class="playback-bar">
            <div>当前时间：<span>{{ currentTime }}</span></div>
            <div>节拍速度：<strong>{{ bpm }} bpm</strong></div>
            <div>拍号：<strong>4/4</strong></div>
            <button @click="play" class="btn btn-sm btn-success">播放</button>
            <button @click="pause" class="btn btn-sm btn-secondary">暂停</button>
            <button @click="stop" class="btn btn-sm btn-danger">停止</button>
        </div>

        <div class="edit-area-scroll">
            <div class="edit-area-content" :style="{ width: editAreaWidth + 'px' }">
                <div class="timeline-scroll-wrapper">
                    <div class="timeline">
                        <div v-for="i in barCount" :key="i">第{{ i }}小节</div>
                    </div>
                </div>

                <div class="timeline-control">
                    <label for="bar-count-range" style="margin-right:8px;">小节数：</label>
                    <input type="range" id="bar-count-range" min="1" max="32" v-model.number="barCount" />
                    <span id="bar-count-value">{{ barCount }}</span>
                </div>

                <div id="note-block-container">
                    <div v-if="noteBlocks.length === 0" class="empty-segment-placeholder">
                        在这里添加循环乐段或双击创建片段
                    </div>
                    <div v-else class="note-block-list">
                        <div v-for="(block, idx) in noteBlocks" :key="block.index" class="note-block-item"
                            @click="openNoteBlockEditor(idx)">
                            乐段 #{{ block.index }} - 音色: {{ block.timbre }}
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div id="note-block-modal" class="note-block-modal" v-if="showModal" @click.self="closeModal">
            <div class="note-block-modal-content">
                <div class="modal-header">
                    <span id="modal-title">乐段 #{{ editingBlockIndex + 1 }} 编辑</span>
                    <button id="close-modal-btn" class="close-btn" @click="closeModal">&times;</button>
                </div>
                <div class="modal-body">
                    <div class="modal-bpm">
                        <label for="modal-bpm-input">BPM：</label>
                        <input type="number" id="modal-bpm-input" min="40" max="240" v-model.number="bpm"
                            style="width:60px;" />
                    </div>
                    <div id="piano-roll-container" ref="pianoRollContainer"></div>
                </div>
                <div class="modal-footer">
                    <button id="save-note-block-btn" class="btn btn-success" @click="saveNoteBlock">保存</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            barCount: 9,
            bpm: 120,
            currentTime: '0:00:00.00',
            noteBlocks: [],
            showModal: false,
            editingBlockIndex: null,
            cellWidth: 70,
            pitches: ['C', 'B', 'A', 'G', 'F', 'E', 'D', 'C'],
        };
    },
    computed: {
        editAreaWidth() {
            return this.barCount * this.cellWidth + (this.barCount - 1) * 12 + 32;
        },
    },
    methods: {
        fetchNoteBlocks() {
            fetch('/get_note_blocks')
                .then(res => res.json())
                .then(data => {
                    this.noteBlocks = data.note_blocks || [];
                })
                .catch(err => {
                    console.error('加载乐段失败', err);
                });
        },
        play() {
            fetch('/play', { method: 'POST' })
                .then(res => res.json())
                .then(data => alert(data.message))
                .catch(() => alert('播放失败'));
        },
        pause() {
            alert('暂停功能未实现'); // 你可以扩展
        },
        stop() {
            alert('停止功能未实现'); // 你可以扩展
        },
        openNoteBlockEditor(index) {
            this.editingBlockIndex = index;
            this.showModal = true;
            this.renderPianoRoll();
        },
        closeModal() {
            this.showModal = false;
            this.editingBlockIndex = null;
        },
        renderPianoRoll() {
            const container = this.$refs.pianoRollContainer;
            container.innerHTML = '';

            let html = `<div style="overflow-x:auto;">
        <table class="piano-roll-table" style="min-width:${this.barCount * this.cellWidth + 40}px;">
        <tbody>`;

            for (let p = 0; p < this.pitches.length; p++) {
                html += '<tr>';
                html += `<td class="pitch-label">${this.pitches[p]}</td>`;
                for (let b = 0; b < this.barCount; b++) {
                    html += `<td class="piano-cell" data-pitch="${this.pitches[p]}" data-bar="${b}"></td>`;
                }
                html += '</tr>';
            }
            html += '</tbody></table></div>';

            container.innerHTML = html;

            container.querySelectorAll('.piano-cell').forEach(cell => {
                cell.addEventListener('click', function () {
                    this.classList.toggle('active');
                });
            });
        },
        saveNoteBlock() {
            alert('保存功能未实现');
            this.closeModal();
        },
    },
    mounted() {
        this.fetchNoteBlocks();
    },
};
</script>

<style scoped>
/* 你的样式照搬之前的CSS，scoped防止污染 */
.main-workspace {
    flex-grow: 1;
    padding: 12px 8px 0 8px;
    background: #232323;
    min-height: unset;
    box-sizing: border-box;
    font-size: 0.92rem;
}

.timeline-scroll-wrapper {
    overflow-x: auto;
    margin-bottom: 8px;
    border-bottom: 1px solid #333;
    height: 38px;
}

.timeline {
    display: flex;
    gap: 12px;
    padding-bottom: 4px;
    font-size: 1rem;
    color: #bbb;
    font-weight: 600;
    user-select: none;
    align-items: center;
}

.timeline>div {
    min-width: 70px;
    max-width: 70px;
    text-align: center;
    padding: 4px 0;
    background: #232323;
    border-radius: 6px;
    box-sizing: border-box;
    flex-shrink: 0;
    transition: background 0.2s;
}

.timeline>div:hover {
    background: #2d2d2d;
}

.timeline-control {
    display: flex;
    align-items: center;
    margin-bottom: 10px;
    font-size: 0.98rem;
}

#bar-count-range {
    width: 160px;
    margin: 0 8px;
    accent-color: #0d6efd;
}

#bar-count-value {
    min-width: 24px;
    display: inline-block;
    text-align: left;
    color: #0d6efd;
    font-weight: bold;
}

.playback-bar {
    display: flex;
    align-items: center;
    gap: 20px;
    margin-bottom: 16px;
    font-size: 1.05rem;
}

.playback-bar>div {
    color: #eee;
}

.playback-bar strong {
    color: #0d6efd;
    font-weight: bold;
    font-size: 1.08rem;
}

.playback-bar button {
    padding: 6px 18px;
    border: none;
    border-radius: 7px;
    font-size: 0.95rem;
    font-weight: 600;
    margin-left: 6px;
    cursor: pointer;
    transition: background 0.2s, transform 0.1s;
    box-shadow: 0 2px 8px #0002;
}

.note-block-list {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-bottom: 14px;
}

.note-block-item {
    padding: 10px 14px;
    background-color: #232323;
    border-radius: 8px;
    color: #eee;
    cursor: pointer;
    user-select: none;
    transition: background-color 0.2s, color 0.2s, border-color 0.2s;
    border: 2px solid #555;
    font-size: 1.05rem;
    box-shadow: 0 2px 8px #0001;
    margin-bottom: 8px;
}

.note-block-item:hover,
.note-block-item.selected {
    background-color: #0d6efd;
    color: #fff;
    border-color: #0d6efd;
}

.empty-segment-placeholder {
    flex-grow: 1;
    border: 2px dashed #444;
    border-radius: 8px;
    color: #666;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.05rem;
    user-select: none;
    min-height: 40px;
    background: #232323;
    margin-top: 10px;
}

.note-block-modal {
    position: fixed;
    left: 0;
    top: 0;
    width: 100vw;
    height: 100vh;
    background: rgba(0, 0, 0, 0.5);
    z-index: 9999;
    display: flex;
    align-items: center;
    justify-content: center;
}

.note-block-modal-content {
    background: #232323;
    border-radius: 10px;
    padding: 24px 28px;
    min-width: 900px;
    min-height: 500px;
    box-shadow: 0 8px 32px #0008;
    position: relative;
}

.modal-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 18px;
}

.close-btn {
    background: none;
    border: none;
    color: #fff;
    font-size: 2rem;
    cursor: pointer;
}

.modal-bpm {
    margin-bottom: 16px;
}

#piano-roll-container {
    background: #444;
    border-radius: 6px;
    overflow-x: auto;
}

.piano-roll-table {
    border-collapse: collapse;
    width: auto;
}

.piano-roll-table td {
    border: 1px solid #888;
    width: 70px;
    height: 28px;
    background: #666;
}

.piano-roll-table .pitch-label {
    background: #333;
    color: #fff;
    width: 32px;
    text-align: center;
}

.piano-roll-table .piano-cell.active {
    background: #4fc3f7;
}
</style>
