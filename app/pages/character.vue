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

          <!-- 恢復骰獨立區塊 -->
          <div class="character-section mb-6">
            <div class="border-2 border-black bg-white p-4">
              <div class="text-sm font-bold uppercase tracking-wide mb-3 text-center">恢復骰</div>
              
              <!-- 兩欄佈局 -->
              <div class="grid grid-cols-2 gap-4">
                <!-- 左欄：1D6 + 輸入框 -->
                <div class="flex flex-col items-center justify-center">
                  <div class="border-2 border-black bg-gray-50 p-4 w-20 h-20 flex flex-col items-center justify-center">
                    <div class="text-lg font-bold font-typewriter mb-1">1d6+</div>
                    <input type="number" v-model.number="character.recoveryBonus" 
                           class="w-12 bg-transparent text-center font-typewriter font-bold text-xl border-b-2 border-black focus:outline-none mt-1">
                  </div>
                </div>
                
                <!-- 右欄：2x2 checkbox grid -->
                <div class="grid grid-cols-2 gap-2 text-sm">
                  <label class="flex items-center track-item p-2 border border-gray-300 rounded">
                    <input type="checkbox" v-model="character.recoveryRolls.action" class="mr-2 scale-90">
                    <span class="font-medium">動作</span>
                  </label>
                  <label class="flex items-center track-item p-2 border border-gray-300 rounded">
                    <input type="checkbox" v-model="character.recoveryRolls.tenMin" class="mr-2 scale-90">
                    <span class="font-medium">10分鐘</span>
                  </label>
                  <label class="flex items-center track-item p-2 border border-gray-300 rounded">
                    <input type="checkbox" v-model="character.recoveryRolls.oneHour" class="mr-2 scale-90">
                    <span class="font-medium">1小時</span>
                  </label>
                  <label class="flex items-center track-item p-2 border border-gray-300 rounded">
                    <input type="checkbox" v-model="character.recoveryRolls.tenHours" class="mr-2 scale-90">
                    <span class="font-medium">10小時</span>
                  </label>
                </div>
              </div>
            </div>
          </div>

          <!-- 傷害軌、壓力與理智軌三欄整合區塊 -->
          <div class="character-section mb-6">
            <div class="border-2 border-black bg-white p-3">
              <div class="grid grid-cols-1 lg:grid-cols-3 gap-3">
                
                <!-- 傷害軌（左欄） -->
                <div class="border-r border-gray-300 pr-3">
                  <div class="text-xs font-bold uppercase tracking-wide mb-3">傷害軌</div>
                  <div class="space-y-1 text-xs">
                    <label class="flex items-center track-item p-1">
                      <input type="radio" v-model="character.damageTrack" value="hale" class="mr-1 scale-75">
                      <span class="font-bold">強健</span>
                    </label>
                    <!-- 連結線與輕傷 -->
                    <div class="flex items-center my-1">
                      <div class="w-4 h-0.5 bg-gray-400 mx-1"></div>
                      <label class="flex items-center track-item p-1">
                        <input type="radio" v-model="character.damageTrack" value="hurt" class="mr-1 scale-75">
                        <span class="text-xs font-bold text-red-700" title="僅部分角色可用">輕傷</span>
                      </label>
                      <div class="w-4 h-0.5 bg-gray-400 mx-1"></div>
                    </div>
                    <label class="flex items-center track-item p-1">
                      <input type="radio" v-model="character.damageTrack" value="impaired" class="mr-1 scale-75">
                      <span class="font-bold text-red-600" title="應用努力的成本 +1；弱效/強效影響無效；戰鬥時特殊骰面只會 +1">帶傷</span>
                    </label>
                    <label class="flex items-center track-item p-1">
                      <input type="radio" v-model="character.damageTrack" value="debilitated" class="mr-1 scale-75">
                      <span class="font-bold text-red-800" title="只能移動鄰近距離（通常是爬行）；如果速度池為0則無法移動">重創</span>
                    </label>
                    <label class="flex items-center track-item p-1">
                      <input type="radio" v-model="character.damageTrack" value="dead" class="mr-1 scale-75">
                      <span class="font-bold text-black">死亡</span>
                    </label>
                  </div>
                </div>

                <!-- 壓力（中欄） -->
                <div class="border-r border-gray-300 pr-3 relative">
                  <!-- 上方壓力標題區 -->
                  <div class="mb-3">
                    <!-- 壓力圓形血跡背景 -->
                    <div class="relative mb-2">
                      <div class="w-16 h-16 mx-auto relative">
                        <!-- 血跡背景 -->
                        <div class="absolute inset-0 bg-red-900 rounded-full transform rotate-12" style="clip-path: polygon(20% 0%, 100% 30%, 90% 90%, 10% 100%, 0% 60%)"></div>
                        <div class="absolute inset-1 bg-red-800 rounded-full transform -rotate-6" style="clip-path: polygon(30% 10%, 95% 25%, 85% 85%, 15% 95%, 5% 65%)"></div>
                        <!-- 中心白色方框 -->
                        <div class="absolute inset-3 bg-white border-2 border-black flex items-center justify-center">
                          <input type="number" v-model.number="character.currentStress" 
                                 class="w-full text-center font-bold text-sm bg-transparent border-none focus:outline-none">
                        </div>
                        <!-- STRESS 標籤 -->
                        <div class="absolute -top-1 -left-1 bg-red-900 text-white text-xs font-bold px-1 py-0.5 transform -rotate-12 rounded">
                          壓力
                        </div>
                      </div>
                    </div>

                    <!-- 壓力等級圓形血跡背景 -->
                    <div class="relative">
                      <div class="w-16 h-16 mx-auto relative">
                        <!-- 血跡背景 -->
                        <div class="absolute inset-0 bg-red-900 rounded-full transform -rotate-12" style="clip-path: polygon(15% 5%, 95% 20%, 100% 80%, 20% 95%, 0% 50%)"></div>
                        <div class="absolute inset-1 bg-red-800 rounded-full transform rotate-8" style="clip-path: polygon(25% 15%, 90% 30%, 95% 75%, 25% 90%, 10% 55%)"></div>
                        <!-- 中心白色方框 -->
                        <div class="absolute inset-3 bg-white border-2 border-black flex items-center justify-center">
                          <input type="number" v-model.number="character.stressLevel" 
                                 class="w-full text-center font-bold text-sm bg-transparent border-none focus:outline-none">
                        </div>
                        <!-- STRESS LEVEL 標籤 -->
                        <div class="absolute -top-1 -right-1 bg-red-900 text-white text-xs font-bold px-1 py-0.5 transform rotate-12 rounded text-center leading-tight">
                          壓力量級
                        </div>
                      </div>
                    </div>
                  </div>

                  <!-- 黑色背景的方格區域 -->
                  <div class="bg-black p-2 rounded">
                    <!-- 白色標題 -->
                    <div class="text-white text-xs font-bold text-center mb-1 uppercase tracking-wide leading-tight">
                      源自超自然來源<br>的壓力值
                    </div>
                    
                    <!-- 5x2 白色方格 -->
                    <div class="grid grid-cols-5 gap-1">
                      <div v-for="level in 10" :key="level" 
                        class="w-5 h-5 border border-gray-300 relative"
                        :style="character.supernaturalStressMarks[level-1] ? 'background-color:#2d5a2d;' : 'background-color:#fff;'">
                        <input type="checkbox" v-model="character.supernaturalStressMarks[level-1]" 
                               class="w-full h-full opacity-0 absolute left-0 top-0 cursor-pointer">
                        <span v-if="character.supernaturalStressMarks[level-1]" class="absolute left-0 top-0 w-full h-full flex items-center justify-center pointer-events-none">
                          <svg width="14" height="14" viewBox="0 0 18 18"><polyline points="4,10 8,14 14,6" stroke="white" stroke-width="2.5" fill="none"/></svg>
                        </span>
                      </div>
                    </div>
                  </div>
                </div>

                <!-- 理智軌（右欄） -->
                <div>
                  <div class="text-xs font-bold uppercase tracking-wide mb-3 sanity-title" title="崩潰發作時擲骰 1D20，心理崩潰骰到 19-20 或完全崩潰骰到 20 將發生勇氣爆發。勇氣爆發：你忽略崩潰結果與壓力量級導致的任務受阻，但仍舊會承受重傷，到緊迫的危機解除。">理智軌</div>
                  <div class="space-y-1 text-xs">
                    <label class="flex items-center track-item p-1">
                      <input type="radio" v-model="character.sanityTrack" value="calm" class="mr-1 scale-75">
                      <span class="font-bold" title="正常狀態">平靜</span>
                    </label>
                    
                    <label class="flex items-center track-item p-1">
                      <input type="radio" v-model="character.sanityTrack" value="uneasy" class="mr-1 scale-75">
                      <span class="font-bold text-yellow-600" title="表現在扮演上">不安</span>
                    </label>
                    
                    <label class="flex items-center track-item p-1">
                      <input type="radio" v-model="character.sanityTrack" value="shaken" class="mr-1 scale-75">
                      <span class="font-bold text-orange-600" title="表現在扮演上">動搖</span>
                    </label>
                    
                    <label class="flex items-center track-item p-1">
                      <input type="radio" v-model="character.sanityTrack" value="neurotic" class="mr-1 scale-75">
                      <span class="font-bold text-red-600" title="心理崩潰發作，擲骰隨機表">神經質</span>
                    </label>
                    
                    <label class="flex items-center track-item p-1">
                      <input type="radio" v-model="character.sanityTrack" value="irrational" class="mr-1 scale-75">
                      <span class="font-bold text-red-700" title="表現在扮演上">不理性</span>
                    </label>
                    
                    <label class="flex items-center track-item p-1">
                      <input type="radio" v-model="character.sanityTrack" value="insane" class="mr-1 scale-75">
                      <span class="font-bold text-red-800" title="心理崩潰發作，擲骰隨機表">精神錯亂</span>
                    </label>
                    
                    <label class="flex items-center track-item p-1">
                      <input type="radio" v-model="character.sanityTrack" value="breakdown" class="mr-1 scale-75">
                      <span class="font-bold text-black" title="完全崩潰發作，你陷入不可控制的狀態，當狀態結束時回到神經質階段，且永久留下一個症狀">完全崩潰</span>
                    </label>
                  </div>
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

        <!-- 中欄 - 標題、技能與攻擊 -->
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

          <!-- 攻擊 -->
          <div class="character-section">
            <div class="border-2 border-black bg-white p-4">
              <div class="text-sm font-bold uppercase tracking-wide mb-4">攻擊</div>
                <div v-for="n in 4" :key="n" class="flex items-center border-b border-gray-300 pb-1">
                  <input type="text" class="flex-1 bg-transparent font-typewriter text-sm focus:outline-none mr-2">
                </div>
            </div>
          </div>
          <!-- 技能 -->
          <div class="character-section mb-6">
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

          <!-- 裝備 -->
          <div class="character-section">
            <div class="border-2 border-black bg-white p-4">
              <div class="text-center text-sm font-bold uppercase tracking-wide mb-4">裝備</div>
              <textarea v-model="character.abilities" 
                        class="w-full h-80 bg-transparent font-typewriter text-sm border-none resize-none focus:outline-none"
                        placeholder="記錄裝備..."></textarea>
            </div>
          </div>

        </div>

        <!-- 右欄 - 密鑰與能力 -->
        <div class="character-sheet-column">
          <!-- 密鑰 -->
          <div class="character-section mb-6">
            <div class="border-2 border-black bg-white p-4">
              <div class="flex items-center justify-between mb-4">
                <div class="text-sm font-bold uppercase tracking-wide">密鑰</div>
                <div class="flex items-center space-x-2">
                  <span class="text-xs bg-red-800 text-white px-2 py-1">密鑰上限</span>
                  <input type="number" v-model.number="character.cypherLimit" min="0" class="w-14 px-2 py-1 border-b border-black bg-transparent text-center font-typewriter text-xs focus:outline-none" placeholder="上限">
                  <button @click="generateRandomCyphers" 
                          :disabled="character.cypherLimit <= 0"
                          :class="[
                            'text-xs px-2 py-1 rounded font-typewriter',
                            character.cypherLimit <= 0 
                              ? 'bg-gray-400 text-gray-600 cursor-not-allowed' 
                              : 'bg-green-700 text-white hover:bg-green-800'
                          ]">
                    隨機獲得
                  </button>
                </div>
              </div>
              
              <!-- 密鑰列表 -->
              <div class="space-y-2 max-h-96 overflow-y-auto">
                <div v-for="(cypher, index) in character.cyphers" :key="index" class="border border-gray-300 rounded p-2 bg-gray-50">
                  <div class="flex items-center justify-between mb-2">
                    <input type="text" v-model="cypher.title" placeholder="密鑰名稱" 
                           class="flex-1 font-bold text-sm bg-transparent border-b border-gray-400 focus:outline-none focus:border-black mr-2">
                    <div class="flex items-center space-x-1">
                      <button @click="copyCypherToClipboard(cypher)" class="text-blue-600 hover:text-blue-800 text-xs px-1 py-1 rounded border border-blue-300 hover:bg-blue-50" title="複製密鑰詳細內容">
                        📋
                      </button>
                      <button @click="removeCypher(index)" class="text-red-600 hover:text-red-800 text-xs">
                        ✕
                      </button>
                    </div>
                  </div>
                  <div class="flex items-center mb-2">
                    <span class="text-xs text-gray-600 mr-2">等級：</span>
                    <input type="text" v-model="cypher.level" placeholder="1d6" 
                           class="w-16 text-xs bg-transparent border-b border-gray-400 focus:outline-none focus:border-black">
                  </div>
                  <textarea v-model="cypher.content" placeholder="密鑰效果描述..." 
                           class="w-full h-16 text-xs bg-transparent border border-gray-300 rounded p-2 resize-none focus:outline-none focus:border-black"
                           rows="3"></textarea>
                </div>
                
                <!-- 添加新密鑰按鈕 -->
                <button @click="addNewCypher" 
                        :disabled="character.cypherLimit > 0 && character.cyphers.length >= character.cypherLimit"
                        :class="[
                          'w-full py-2 border-2 border-dashed rounded text-sm',
                          character.cypherLimit > 0 && character.cyphers.length >= character.cypherLimit 
                            ? 'border-gray-300 text-gray-400 cursor-not-allowed' 
                            : 'border-gray-400 text-gray-600 hover:border-gray-600 hover:text-gray-800'
                        ]">
                  {{ character.cypherLimit > 0 && character.cyphers.length >= character.cypherLimit 
                     ? `已達上限 (${character.cyphers.length}/${character.cypherLimit})` 
                     : '+ 添加新密鑰' }}
                </button>
              </div>
            </div>
          </div>

          <!-- 能力 -->
          <div class="character-section h-full">
            <div class="border-2 border-black bg-white p-4 h-full">
              <div class="text-center text-sm font-bold uppercase tracking-wide mb-4">能力</div>
              <textarea v-model="character.abilities" 
                        class="w-full h-full bg-transparent font-typewriter text-sm border-none resize-none focus:outline-none"
                        placeholder="記錄角色的特殊能力..."></textarea>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

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
  sanityTrack: 'calm',
  currentStress: 0,
  stressLevel: 0,
  supernaturalStressMarks: Array(10).fill(false),
  equipment: '',
  cyphers: [],
  cypherLimit: 0,
  abilities: '',
  xp: 0,
  background: '',
  recoveryBonus: 0
})

// 密鑰管理
const addNewCypher = () => {
  // 檢查是否達到上限
  if (character.value.cypherLimit > 0 && character.value.cyphers.length >= character.value.cypherLimit) {
    alert(`已達密鑰上限 (${character.value.cypherLimit} 個)`)
    return
  }
  
  character.value.cyphers.push({
    title: '',
    level: '',
    content: ''
  })
}

const removeCypher = (index) => {
  character.value.cyphers.splice(index, 1)
}

const copyCypherToClipboard = async (cypher) => {
  const cypherText = `${cypher.title}
等級：${cypher.level}

${cypher.content}`
  
  try {
    await navigator.clipboard.writeText(cypherText)
    alert('密鑰內容已複製到剪貼簿！')
  } catch (error) {
    console.error('複製失敗:', error)
    // 備用方案：創建臨時文字區域
    const textArea = document.createElement('textarea')
    textArea.value = cypherText
    document.body.appendChild(textArea)
    textArea.select()
    try {
      document.execCommand('copy')
      alert('密鑰內容已複製到剪貼簿！')
    } catch (fallbackError) {
      alert('複製失敗，請手動複製內容')
    }
    document.body.removeChild(textArea)
  }
}

const generateRandomCyphers = async () => {
  const limit = character.value.cypherLimit || 0
  if (limit <= 0) {
    alert('請先設定密鑰上限')
    return
  }
  
  try {
    const response = await fetch('/data/cypher.json')
    const cypherData = await response.json()
    
    // 清空現有密鑰
    character.value.cyphers = []
    
    // 隨機選擇密鑰，數量等於密鑰上限
    for (let i = 0; i < limit; i++) {
      const randomIndex = Math.floor(Math.random() * cypherData.length)
      const randomCypher = cypherData[randomIndex]
      
      // 處理擲骰表格
      let content = randomCypher.content
      if (randomCypher.roll_table) {
        content += '\n\n' + randomCypher.roll_table.map(item => 
          `${item.range}：${item.result}`
        ).join('\n')
      }
      
      character.value.cyphers.push({
        title: randomCypher.title,
        level: randomCypher.level,
        content: content
      })
    }
    
    alert(`成功生成 ${limit} 個隨機密鑰！`)
  } catch (error) {
    console.error('載入密鑰資料失敗:', error)
    alert('載入密鑰資料失敗，請檢查檔案是否存在')
  }
}

// 隱藏原生 title 工具提示
onMounted(() => {
  const elementsWithTitle = document.querySelectorAll('[title]')
  elementsWithTitle.forEach(element => {
    const originalTitle = element.getAttribute('title')
    element.setAttribute('data-tooltip', originalTitle)
    element.removeAttribute('title')
    
    let timeout
    
    element.addEventListener('mouseenter', () => {
      timeout = setTimeout(() => {
        element.setAttribute('title', originalTitle)
      }, 1000) // 延遲1秒再顯示原生提示（實際上我們的自定義提示會先顯示）
    })
    
    element.addEventListener('mouseleave', () => {
      clearTimeout(timeout)
      element.removeAttribute('title')
    })
  })
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
      sanityTrack: 'calm',
      currentStress: 0,
      stressLevel: 0,
  supernaturalStressMarks: Array(10).fill(false),
      equipment: '',
      cyphers: [],
      cypherLimit: 0,
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

/* Magnus Archives 風格的 Tooltip */
[data-tooltip] {
  position: relative;
  cursor: help;
}

[data-tooltip]:hover::before {
  content: attr(data-tooltip);
  position: absolute;
  bottom: calc(100% + 10px);
  left: 50%;
  transform: translateX(-50%);
  background: linear-gradient(135deg, #1a1a1a 0%, #2d2d2d 50%, #1a1a1a 100%);
  color: #e5e1d8;
  padding: 12px 16px;
  border-radius: 6px;
  z-index: 9999;
  font-size: 12px;
  font-weight: normal;
  border: 2px solid #a13c3c;
  box-shadow: 
    0 8px 24px rgba(0, 0, 0, 0.9),
    inset 0 1px 0 rgba(255, 255, 255, 0.15),
    0 0 0 1px rgba(161, 60, 60, 0.4),
    0 0 20px rgba(161, 60, 60, 0.3);
  animation: fadeInTooltip 0.3s ease-out;
  max-width: 280px;
  min-width: 120px;
  white-space: normal;
  line-height: 1.4;
  font-family: 'Special Elite', 'Courier New', monospace;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
  pointer-events: none;
  /* 智能邊界檢測 */
  right: auto;
}

/* 針對螢幕右側的元素調整工具提示位置 */
.grid > div:last-child [data-tooltip]:hover::before {
  left: auto;
  right: 0;
  transform: translateX(0);
}

/* 針對螢幕左側的元素調整工具提示位置 */
.grid > div:first-child [data-tooltip]:hover::before {
  left: 0;
  transform: translateX(0);
}

[data-tooltip]:hover::after {
  content: '';
  position: absolute;
  bottom: calc(100% + 4px);
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 0;
  border-left: 8px solid transparent;
  border-right: 8px solid transparent;
  border-top: 8px solid #a13c3c;
  z-index: 9998;
  animation: fadeInTooltip 0.3s ease-out;
  pointer-events: none;
}

/* 箭頭位置調整 */
.grid > div:last-child [data-tooltip]:hover::after {
  left: auto;
  right: 20px;
  transform: translateX(0);
}

.grid > div:first-child [data-tooltip]:hover::after {
  left: 20px;
  transform: translateX(0);
}

@keyframes fadeInTooltip {
  from {
    opacity: 0;
    transform: translateY(5px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 理智軌標題特殊效果 */
.sanity-title:hover {
  color: #a13c3c;
  text-shadow: 0 0 8px rgba(161, 60, 60, 0.6);
  transition: all 0.3s ease;
}

/* 傷害軌和理智軌項目懸浮效果 */
.track-item:hover {
  background-color: rgba(161, 60, 60, 0.1);
  border-radius: 2px;
  transition: all 0.2s ease;
}

/* 壓力方格特殊樣式 */
.bg-black input[type="checkbox"] {
  appearance: none;
  -webkit-appearance: none;
  cursor: pointer;
}

.bg-black input[type="checkbox"]:checked {
  background-color: #000000;
  border: 2px solid #ffffff;
}

.bg-black input[type="checkbox"]:checked::after {
  content: '✓';
  color: white;
  font-weight: bold;
  font-size: 14px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
