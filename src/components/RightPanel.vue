<template>
    <div class="sidebar material-library">
        <h5>音频素材库</h5>
        <input type="text" class="search-input" placeholder="搜索素材..." v-model="searchQuery" />
        <div>
            <div v-for="(category, index) in filteredCategories" :key="category.name">
                <div class="folder" @click="toggleFolder(index)">
                    {{ category.name }}
                </div>
                <div class="folder-content" v-show="category.open">
                    <div v-for="(file, fIndex) in category.files" :key="fIndex" class="material-item">
                        {{ file }}
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            searchQuery: '',
            categories: [
                // 这里初始写死，后面可以用接口替代
                { name: '钢琴', files: ['示例素材1.wav', '示例素材2.wav'], open: false },
                { name: '打击乐', files: ['鼓1.wav', '鼓2.wav'], open: false },
                { name: '人声', files: ['voice_1.wav', 'voice_2.wav'], open: false },
            ],
        };
    },
    computed: {
        filteredCategories() {
            const q = this.searchQuery.trim().toLowerCase();
            if (!q) return this.categories;
            return this.categories
                .map((cat) => {
                    const filteredFiles = cat.files.filter(file =>
                        file.toLowerCase().includes(q)
                    );
                    if (filteredFiles.length > 0) {
                        return { ...cat, files: filteredFiles };
                    }
                    return null;
                })
                .filter(Boolean);
        },
    },
    methods: {
        toggleFolder(index) {
            this.categories[index].open = !this.categories[index].open;
        },
    },
    mounted() {
        // 这里可以改成从后端拉数据，示范写法：
        /*
        fetch('/api/material_categories')
          .then(res => res.json())
          .then(data => {
            this.categories = data.map(cat => ({
              name: cat.name,
              files: cat.files,
              open: false,
            }));
          });
        */
    },
};
</script>

<style scoped>
.material-library {
    background-color: #1e1e1e;
    padding: 15px;
    height: 100vh;
    overflow-y: hidden;
}

.search-input {
    margin-bottom: 10px;
    width: 100%;
    padding: 5px;
    border-radius: 4px;
    border: none;
}

.folder {
    cursor: pointer;
    margin-bottom: 5px;
    font-weight: bold;
}

.folder:hover {
    color: #0d6efd;
}

.folder-content {
    margin-left: 15px;
    margin-bottom: 15px;
}

.material-item {
    display: flex;
    align-items: center;
    padding: 5px;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.2s ease;
}
</style>
