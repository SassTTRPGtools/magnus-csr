<template>
  <div class="min-h-screen bg-magnus-bg p-4">
    <div class="max-w-7xl mx-auto">
      <!-- 三欄式佈局 - 標題夾在中間 -->
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        
        <!-- 左欄 - 基本資訊與屬性 -->
        <div class="character-sheet-column">
          <!-- 角色基本資訊 -->
          <div class="character-section mb-6">
            <div class="border-2 border-black bg-white p-4">
              <div class="text-center mb-4">
                <label class="block text-xs font-bold uppercase tracking-wide mb-1">姓名</label>
                <input type="text" v-model="character.name" 
                       class="w-full border-b border-black bg-transparent text-center font-typewriter text-lg focus:outline-none">
              </div>            

              <div class="text-center">
                <input type="text" v-model="character.focus" 
                       class="w-full border-b border-black bg-transparent text-center font-typewriter focus:outline-none">
                <label class="block text-xs font-bold uppercase tracking-wide mt-1">角色句子</label>
              </div>
            </div>
          </div>

          <!-- 屬性表格 -->
          <div class="character-section mb-6">
            <div class="border-2 border-black bg-white">
              <div class="grid grid-cols-3 border-b border-black text-center text-xs font-bold uppercase bg-gray-100">
                <div class="p-2 border-r border-black">位階</div>
                <div class="p-2 border-r border-black">努力</div>
                <div class="p-2">XP</div>
              </div>
              <div class="grid grid-cols-3 text-center">
                <input type="number" v-model.number="character.tier" 
                       class="p-3 border-r border-black bg-transparent text-center font-typewriter focus:outline-none">
                <input type="number" v-model.number="character.effort" 
                       class="p-3 border-r border-black bg-transparent text-center font-typewriter focus:outline-none">
                <input type="number" v-model.number="character.xp" 
                       class="p-3 bg-transparent text-center font-typewriter focus:outline-none">
              </div>
            </div>
          </div>

          <!-- 屬性值 -->
          <div class="character-section mb-6">
            <div class="border-2 border-black bg-white">
              <div class="grid grid-cols-3 border-b border-black text-center text-xs font-bold uppercase">
                <div class="p-2 border-r border-black">氣力</div>
                <div class="p-2 border-r border-black">速度</div>
                <div class="p-2">智力</div>
              </div>
              
              <!-- Pool 行 -->
              <div class="grid grid-cols-3 text-center border-b border-black">
                <input type="number" v-model.number="character.might.current" 
                       class="p-4 border-r border-black bg-transparent text-center font-typewriter text-xl focus:outline-none">
                <input type="number" v-model.number="character.speed.current" 
                       class="p-4 border-r border-black bg-transparent text-center font-typewriter text-xl focus:outline-none">
                <input type="number" v-model.number="character.intellect.current" 
                       class="p-4 bg-transparent text-center font-typewriter text-xl focus:outline-none">
              </div>
              
              <!-- Edge 標籤 -->
              <div class="grid grid-cols-3 text-center text-xs border-t border-black">
                <!-- 氣力 -->
                <div class="grid grid-cols-2 gap-1 p-1 border-r border-black">
                  <div>
                    <span class="block mb-1">池</span>
                    <input type="number" v-model.number="character.might.pool" 
                           class="w-full bg-transparent text-center font-typewriter border-b border-black focus:outline-none">
                  </div>
                  <div>
                    <span class="block mb-1">節省值</span>
                    <input type="number" v-model.number="character.might.edge" 
                           class="w-full bg-transparent text-center font-typewriter border-b border-black focus:outline-none">
                  </div>
                </div>
                <!-- 速度 -->
                <div class="grid grid-cols-2 gap-1 p-1 border-r border-black">
                  <div>
                    <span class="block mb-1">池</span>
                    <input type="number" v-model.number="character.speed.pool" 
                           class="w-full bg-transparent text-center font-typewriter border-b border-black focus:outline-none">
                  </div>
                  <div>
                    <span class="block mb-1">節省值</span>
                    <input type="number" v-model.number="character.speed.edge" 
                           class="w-full bg-transparent text-center font-typewriter border-b border-black focus:outline-none">
                  </div>
                </div>
                <!-- 智力 -->
                <div class="grid grid-cols-2 gap-1 p-1">
                  <div>
                    <span class="block mb-1">池</span>
                    <input type="number" v-model.number="character.intellect.pool" 
                           class="w-full bg-transparent text-center font-typewriter border-b border-black focus:outline-none">
                  </div>
                  <div>
                    <span class="block mb-1">節省值</span>
                    <input type="number" v-model.number="character.intellect.edge" 
                           class="w-full bg-transparent text-center font-typewriter border-b border-black focus:outline-none">
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- 恢復骰與傷害軌合併區塊 -->
          <div class="character-section mb-6">
            <div class="border-2 border-black bg-white p-4 grid grid-cols-1 md:grid-cols-2 gap-4 items-start">
              <!-- 恢復骰（左側） -->
              <div>
                <div class="text-xs font-bold uppercase tracking-wide mb-3">恢復骰</div>
                <div class="space-y-2 text-sm mb-2">
                  <label class="flex items-center">
                    <span class="mr-2">1D6 +</span>
                    <input type="number" v-model.number="character.recoveryBonus" class="w-16 border-b border-black bg-transparent text-center font-typewriter focus:outline-none mr-2">
                  </label>
                </div>
                <div class="space-y-2 text-sm">
                  <label class="flex items-center">
                    <input type="checkbox" v-model="character.recoveryRolls.action" class="mr-2">
                    <span>動作</span>
                  </label>
                  <label class="flex items-center">
                    <input type="checkbox" v-model="character.recoveryRolls.tenMin" class="mr-2">
                    <span>10 分鐘</span>
                  </label>
                  <label class="flex items-center">
                    <input type="checkbox" v-model="character.recoveryRolls.oneHour" class="mr-2">
                    <span>1 小時</span>
                  </label>
                  <label class="flex items-center">
                    <input type="checkbox" v-model="character.recoveryRolls.tenHours" class="mr-2">
                    <span>10 小時</span>
                  </label>
                </div>
              </div>
              <!-- 傷害軌（右側） -->
              <div>
                <div class="text-xs font-bold uppercase tracking-wide mb-3">傷害軌</div>
                <div class="space-y-2 text-sm">
                  <label class="flex items-center">
                    <input type="radio" v-model="character.damageTrack" value="hale" class="mr-2">
                    <span class="font-bold">強健</span>
                  </label>
                  <!-- 連結線與輕傷 -->
                  <div class="flex items-center my-2">
                    <div class="w-8 h-0.5 bg-gray-400 mx-2"></div>
                    <label class="flex items-center">
                      <input type="radio" v-model="character.damageTrack" value="hurt" class="mr-1">
                      <span class="text-xs font-bold text-red-700">輕傷</span>
                    </label>
                    <div class="w-8 h-0.5 bg-gray-400 mx-2"></div>
                  </div>
                  <div class="text-xs text-gray-500 ml-8 mb-2">僅部分角色可用</div>
                  <label class="flex items-center">
                    <input type="radio" v-model="character.damageTrack" value="impaired" class="mr-2">
                    <span class="font-bold text-red-600">帶傷</span>
                  </label>
                  <div class="ml-6 text-xs text-gray-600">
                    <div>• 應用努力的成本 +1</div>
                    <div>• 弱效/強效影響無效</div>
                    <div>• 戰鬥時特殊骰面只會 +1</div>
                  </div>
                  <label class="flex items-center">
                    <input type="radio" v-model="character.damageTrack" value="debilitated" class="mr-2">
                    <span class="font-bold text-red-800">重創</span>
                  </label>
                  <div class="ml-6 text-xs text-gray-600">
                    <div>• 只能移動鄰近距離（通常是爬行）</div>
                    <div>• 如果速度池為0則無法移動</div>
                  </div>
                  <label class="flex items-center">
                    <input type="radio" v-model="character.damageTrack" value="dead" class="mr-2">
                    <span class="font-bold text-black">死亡</span>
                  </label>
                </div>
              </div>
            </div>
          </div>

          <!-- 進階 -->
          <div class="character-section">
            <div class="border-2 border-black bg-white p-4">
              <div class="text-xs font-bold uppercase tracking-wide mb-3">晉升位階（完成任意四項）</div>
              <div class="text-xs space-y-1">
                <label class="flex items-center">
                  <input type="checkbox" class="mr-2">
                  <span>提升數值：獲得 4 點數值，能自由分配到任意池中。</span>
                </label>
                <label class="flex items-center">
                  <input type="checkbox" class="mr-2">
                  <span>邁向完美：將選擇氣力、速度或智力一項 +1。</span>
                </label>
                <label class="flex items-center">
                  <input type="checkbox" class="mr-2">
                  <span>額外努力：將努力值 +1。</span>
                </label>
                <label class="flex items-center">
                  <input type="checkbox" class="mr-2">
                  <span>技能：選擇一項新技能（攻擊與防禦除外）受訓。 或者將受訓技能改為專精。</span>
                </label>
                <label class="flex items-center">
                  <input type="checkbox" class="mr-2">
                  <span>其他選項：見規則書。</span>
                </label>
              </div>
            </div>
          </div>
        </div>

        <!-- 中欄 - 標題與特殊能力 -->
        <div class="character-sheet-column">
          <!-- 標題 -->
          <div class="text-center mb-8">
            <h1 class="text-4xl font-bold text-green-600 font-typewriter tracking-wider mb-2">
              THE MAGNUS
            </h1>
            <h2 class="text-2xl font-bold text-green-600 font-typewriter tracking-widest mb-1">
              ARCHIVES
            </h2>
            <p class="text-sm text-magnus-text font-typewriter tracking-wide">
              ROLEPLAYING GAME
            </p>
          </div>
          
          <!-- 特殊能力 -->
          <div class="character-section h-full">
            <div class="border-2 border-black bg-white p-4 h-96">
              <div class="text-center text-sm font-bold uppercase tracking-wide mb-4">能力</div>
              <textarea v-model="character.abilities" 
                        class="w-full h-80 bg-transparent font-typewriter text-sm border-none resize-none focus:outline-none"
                        placeholder="記錄角色的特殊能力..."></textarea>
            </div>
          </div>
        </div>

        <!-- 右欄 - 密鑰與技能 -->
        <div class="character-sheet-column">
          <!-- 密鑰 -->
          <div class="character-section mb-6">
            <div class="border-2 border-black bg-white p-4">
              <div class="flex items-center justify-between mb-4">
                <div class="text-sm font-bold uppercase tracking-wide">密鑰</div>
                <div class="text-xs bg-red-800 text-white px-2 py-1">
                  密鑰上限
                </div>
              </div>
              <textarea v-model="character.cyphers" 
                        class="w-full h-32 bg-transparent font-typewriter text-sm border-none resize-none focus:outline-none"
                        placeholder="記錄密鑰道具..."></textarea>
            </div>
          </div>

          <!-- 壓力區塊 -->
          <div class="character-section mb-6">
            <div class="border-2 border-black bg-white p-4 relative">
              <div class="flex items-center justify-between mb-4">
                <div class="text-sm font-bold uppercase tracking-wide">壓力</div>
                <div class="text-xs bg-red-800 text-white px-2 py-1 transform -rotate-3">
                  壓力等級
                </div>
              </div>
              <!-- 壓力等級方格 -->
              <div class="grid grid-cols-3 gap-2 mb-4">
                <div v-for="level in 9" :key="level" class="aspect-square border-2 border-black bg-white">
                  <input type="checkbox" v-model="character.stressLevels[level-1]" 
                         class="w-full h-full">
                </div>
              </div>
              <div class="text-xs uppercase font-bold tracking-wide mb-2">
                壓力等級來源於超自然事件
              </div>
              <!-- 血跡裝飾效果 -->
              <div class="absolute top-1 right-1 w-12 h-12 opacity-40">
                <div class="w-full h-full bg-red-700 rounded-full transform rotate-45 relative">
                  <div class="absolute top-2 left-2 w-3 h-3 bg-red-900 rounded-full"></div>
                  <div class="absolute bottom-1 right-1 w-2 h-2 bg-red-900 rounded-full"></div>
                </div>
              </div>
            </div>
          </div>

          <!-- 技能 -->
          <div class="character-section">
            <div class="border-2 border-black bg-white p-4">
              <div class="text-sm font-bold uppercase tracking-wide mb-4">
                技能
              </div>
              <div class="space-y-1">
                <div v-for="n in 15" :key="n" class="flex items-center border-b border-gray-300 pb-1">
                  <input type="text" class="flex-1 bg-transparent font-typewriter text-sm focus:outline-none mr-2">
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 底部攻擊區域 -->
      <div class="mt-6">
        <div class="border-2 border-black bg-white p-4">
          <div class="text-sm font-bold uppercase tracking-wide mb-4">攻擊</div>
          <div v-for="n in 4" :key="n" class="grid grid-cols-12 gap-2 mb-2">
            <input type="text" class="col-span-6 bg-transparent border-b border-gray-300 font-typewriter text-sm focus:outline-none">
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const character = ref({
  name: '',
  type: '',
  descriptor: '',
  focus: '',
  tier: 1,
  effort: 1,
  might: {
    pool: 0,
    edge: 0,
    current: 0
  },
  speed: {
    pool: 0,
    edge: 0,
    current: 0
  },
  intellect: {
    pool: 0,
    edge: 0,
    current: 0
  },
  recoveryRolls: {
    action: false,
    tenMin: false,
    oneHour: false,
    tenHours: false
  },
  damageTrack: 'hale',
  stressLevels: Array(9).fill(false),
  equipment: '',
  cyphers: '',
  abilities: '',
  xp: 0,
  background: '',
  recoveryBonus: 0
})

const saveCharacter = () => {
  console.log('角色資料:', character.value)
  alert('角色已儲存！（目前只儲存在瀏覽器 console）')
}

const clearForm = () => {
  if (confirm('確定要清除所有資料嗎？')) {
    character.value = {
      name: '',
      type: '',
      descriptor: '',
      focus: '',
      tier: 1,
      effort: 1,
      might: { pool: 0, edge: 0, current: 0 },
      speed: { pool: 0, edge: 0, current: 0 },
      intellect: { pool: 0, edge: 0, current: 0 },
      recoveryRolls: {
        action: false,
        tenMin: false,
        oneHour: false,
        tenHours: false
      },
      damageTrack: 'hale',
      stressLevels: Array(9).fill(false),
      equipment: '',
      cyphers: '',
      abilities: '',
      xp: 0,
      background: '',
      recoveryBonus: 0
    }
  }
}
</script>

<style scoped>
.character-sheet-column {
  display: flex;
  flex-direction: column;
}

.character-section {
  margin-bottom: 1rem;
}

/* 紙張質感 */
.character-section .border-black {
  box-shadow: 
    inset 0 0 20px rgba(0, 0, 0, 0.1),
    0 2px 10px rgba(0, 0, 0, 0.3);
}

/* 輸入框樣式 */
input[type="text"], 
input[type="number"], 
textarea {
  font-family: 'Special Elite', 'Courier New', monospace;
}

input[type="text"]:focus, 
input[type="number"]:focus, 
textarea:focus {
  background-color: rgba(161, 60, 60, 0.05);
}

/* 標題綠色 */
h1, h2 {
  color: #2d5a2d;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
}

/* 復古表格樣式 */
.grid input {
  transition: all 0.2s ease;
}

.grid input:hover {
  background-color: rgba(0, 0, 0, 0.02);
}

/* 紙張背景紋理 */
.bg-white {
  background-color: #faf9f7;
  background-image: 
    radial-gradient(circle at 20% 50%, rgba(120, 119, 109, 0.3) 0%, rgba(255,255,255,0) 50%),
    radial-gradient(circle at 80% 20%, rgba(120, 119, 109, 0.3) 0%, rgba(255,255,255,0) 50%),
    radial-gradient(circle at 40% 80%, rgba(120, 119, 109, 0.3) 0%, rgba(255,255,255,0) 50%);
}

/* 血跡效果 - 可選 */
.blood-stain::after {
  content: '';
  position: absolute;
  top: 20px;
  right: 30px;
  width: 50px;
  height: 50px;
  background: radial-gradient(circle, #8b0000 30%, #a52a2a  50%, transparent 70%);
  border-radius: 50% 30% 60% 40%;
  opacity: 0.3;
  transform: rotate(45deg);
}
</style>
