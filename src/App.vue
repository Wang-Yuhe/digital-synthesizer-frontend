<!-- src/App.vue -->
<template>
  <div class="main-layout">
    <!-- 左侧面板 -->
    <div class="left-panel">
      <LeftPanel />
    </div>

    <!-- 中央工作区 -->
    <div class="center-workspace">
      <CenterWorkspace />
    </div>

    <!-- 右侧面板 -->
    <div class="right-panel">
      <RightPanel />
    </div>
  </div>

  <!-- 底部链 -->
  <div class="bottom-chain">
    <BottomChain />
  </div>
</template>

<script>
import LeftPanel from './components/LeftPanel.vue'
import CenterWorkspace from './components/CenterWorkspace.vue'
import RightPanel from './components/RightPanel.vue'
import BottomChain from './components/BottomChain.vue'

export default {
  components: {
    LeftPanel,
    CenterWorkspace,
    RightPanel,
    BottomChain
  }
}
</script>

<style>
/* 迁移 HTML 中 <style> 的通用样式 */
body {
  margin: 0;
  background: #181818;
  color: #eee;
  font-family: "Microsoft YaHei", Arial, sans-serif;
}

.main-layout {
  display: flex;
  height: 100vh;
}

.left-panel,
.right-panel {
  width: 250px;
  background: #1e1e1e;
}

.center-workspace {
  flex: 1;
  background: #232323;
  padding: 20px;
  overflow-y: auto;
}

.bottom-chain {
  width: 100%;
  background: #222;
  height: 6vh;
}
</style>
