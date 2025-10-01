<template>
  <div class="min-h-screen bg-magnus-bg p-4">
    <div class="max-w-7xl mx-auto">
      <!-- 複製成功提示 -->
      <div v-if="showCopyNotification" 
           class="fixed top-4 right-4 bg-green-600 text-white px-4 py-2 rounded-lg shadow-lg z-50 font-typewriter text-sm animate-bounce">
        {{ copyNotificationText }}
      </div>
      
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
                <label class="block text-xs font-bold uppercase tracking-wide mt-1">角色模板</label>
              </div>
            </div>
          </div>

          <!-- 屬性表格 -->
          <div class="character-section mb-6">
            <div class="border-2 border-black bg-white">
              <div class="grid grid-cols-3 border-b border-black text-center text-xs font-bold uppercase bg-gray-100">
                <div class="p-2 border-r border-black">位階</div>
                <div class="p-2 border-r border-black">努力</div>
              </div>
              <div class="grid grid-cols-3 text-center">
                <input type="number" v-model.number="character.tier" 
                       class="p-3 border-r border-black bg-transparent text-center font-typewriter focus:outline-none">
                <input type="number" v-model.number="character.effort" 
                       class="p-3 border-r border-black bg-transparent text-center font-typewriter focus:outline-none">
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

          <!-- 壓力與傷勢區塊 -->
          <div class="character-section mb-6">
            <div class="border-2 border-black bg-white p-3">
              <!-- 複製狀態值按鈕（置中） -->
              <div class="text-center mb-4">
                <button @click="copyStatusToClipboard" 
                        class="text-xs px-3 py-2 bg-blue-600 text-white hover:bg-blue-700 rounded font-typewriter">
                  📋 複製狀態值
                </button>
              </div>
              
              <div class="grid grid-cols-1 lg:grid-cols-2 gap-4">
                
                <!-- 傷勢格（左欄） -->
                <div class="pr-3">
                  <div class="text-sm font-bold uppercase tracking-wide mb-3 text-center">傷勢</div>
                  
                  <!-- 輕度傷勢 -->
                  <div class="mb-4">
                    <div class="flex items-center justify-between mb-2">
                      <span class="text-xs font-semibold text-green-700">輕度</span>
                      <div class="flex space-x-1">
                        <button @click="addWoundBox('light')" 
                                class="text-xs px-1 py-0.5 bg-green-600 text-white hover:bg-green-700 rounded">
                          +
                        </button>
                        <button @click="removeLastWoundBox('light')" 
                                class="text-xs px-1 py-0.5 bg-red-600 text-white hover:bg-red-700 rounded"
                                :disabled="!hasWoundsOfType('light')">
                          -
                        </button>
                      </div>
                    </div>
                    <div class="grid grid-cols-3 gap-2">
                      <div v-for="(wound, index) in getLightWounds()" :key="`light-${index}`" 
                           class="w-8 h-8 border-2 border-green-600 bg-green-50 flex items-center justify-center cursor-pointer hover:bg-green-100"
                           @click="wound.checked = !wound.checked">
                        <span v-if="wound.checked" class="text-green-800 font-bold">✓</span>
                      </div>
                    </div>
                  </div>
                  
                  <!-- 重度傷勢 -->
                  <div class="mb-4">
                    <div class="flex items-center justify-between mb-2">
                      <span class="text-xs font-semibold text-yellow-700">重度</span>
                      <div class="flex space-x-1">
                        <button @click="addWoundBox('serious')" 
                                class="text-xs px-1 py-0.5 bg-yellow-600 text-white hover:bg-yellow-700 rounded">
                          +
                        </button>
                        <button @click="removeLastWoundBox('serious')" 
                                class="text-xs px-1 py-0.5 bg-red-600 text-white hover:bg-red-700 rounded"
                                :disabled="!hasWoundsOfType('serious')">
                          -
                        </button>
                      </div>
                    </div>
                    <div class="grid grid-cols-3 gap-2">
                      <div v-for="(wound, index) in getSeriousWounds()" :key="`serious-${index}`" 
                           class="w-8 h-8 border-2 border-yellow-600 bg-yellow-50 flex items-center justify-center cursor-pointer hover:bg-yellow-100"
                           @click="wound.checked = !wound.checked">
                        <span v-if="wound.checked" class="text-yellow-800 font-bold">✓</span>
                      </div>
                    </div>
                  </div>
                  
                  <!-- 嚴重傷勢 -->
                  <div class="mb-4">
                    <div class="flex items-center justify-between mb-2">
                      <span class="text-xs font-semibold text-red-700">嚴重</span>
                      <div class="flex space-x-1">
                        <button @click="addWoundBox('critical')" 
                                class="text-xs px-1 py-0.5 bg-red-600 text-white hover:bg-red-700 rounded">
                          +
                        </button>
                        <button @click="removeLastWoundBox('critical')" 
                                class="text-xs px-1 py-0.5 bg-red-600 text-white hover:bg-red-700 rounded"
                                :disabled="!hasWoundsOfType('critical')">
                          -
                        </button>
                      </div>
                    </div>
                    <div class="grid grid-cols-3 gap-2">
                      <div v-for="(wound, index) in getCriticalWounds()" :key="`critical-${index}`" 
                           class="w-8 h-8 border-2 border-red-600 bg-red-50 flex items-center justify-center cursor-pointer hover:bg-red-100"
                           @click="wound.checked = !wound.checked">
                        <span v-if="wound.checked" class="text-red-800 font-bold">✓</span>
                      </div>
                    </div>
                  </div>
                </div>

                <!-- 壓力（右欄） -->
                <div class="pl-3">
                  <!-- 壓力圓形血跡背景 -->
                  <div class="relative mb-4">
                    <div class="w-20 h-20 mx-auto relative">
                      <!-- 血跡背景 -->
                      <div class="absolute inset-0 bg-red-900 rounded-full transform rotate-12" style="clip-path: polygon(20% 0%, 100% 30%, 90% 90%, 10% 100%, 0% 60%)"></div>
                      <div class="absolute inset-1 bg-red-800 rounded-full transform -rotate-6" style="clip-path: polygon(30% 10%, 95% 25%, 85% 85%, 15% 95%, 5% 65%)"></div>
                      <!-- 中心白色方框 -->
                      <div class="absolute inset-4 bg-white border-2 border-black flex items-center justify-center">
                        <input type="number" v-model.number="character.currentStress" 
                               class="w-full text-center font-bold text-lg bg-transparent border-none focus:outline-none">
                      </div>
                      <!-- STRESS 標籤 -->
                      <div class="absolute -top-1 -left-1 bg-red-900 text-white text-xs font-bold px-2 py-1 transform -rotate-12 rounded">
                        壓力
                      </div>
                    </div>
                  </div>
                  
                  <!-- 壓力下限輸入框 -->
                  <div class="text-center">
                    <label class="block text-xs font-semibold text-gray-700 mb-1">壓力下限</label>
                    <input type="number" v-model.number="character.stressMin" min="0" 
                           class="w-16 px-2 py-1 border-2 border-gray-400 bg-white text-center font-typewriter text-sm focus:outline-none focus:border-red-500 rounded">
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

          <!-- 角色資料管理 -->
          <div class="character-section">
            <div class="border-2 border-black bg-white p-4">
              <div class="text-xs font-bold uppercase tracking-wide mb-3">角色資料管理</div>
              <div class="grid grid-cols-2 gap-2 text-xs">
                <!-- 第一行 -->
                <button @click="exportToJSON" 
                        class="px-3 py-2 bg-purple-600 text-white hover:bg-purple-700 rounded font-typewriter">
                  匯出 JSON
                </button>
                <label class="px-3 py-2 bg-orange-600 text-white hover:bg-orange-700 rounded font-typewriter cursor-pointer text-center">
                  匯入 JSON
                  <input type="file" @change="importFromJSON" accept=".json" class="hidden">
                </label>
                <!-- 第二行 -->
                <button @click="exportToText" 
                        class="px-3 py-2 bg-gray-600 text-white hover:bg-gray-700 rounded font-typewriter">
                  複製純文字版本
                </button>
                <button @click="clearForm" 
                        class="px-3 py-2 bg-red-600 text-white hover:bg-red-700 rounded font-typewriter">
                  清空資料
                </button>
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

          <!-- 已學習技能 -->
          <div class="character-section">
            <div class="border-2 border-black bg-white p-4">
              <div class="flex items-center justify-between mb-4">
                <div class="text-sm font-bold uppercase tracking-wide">已學習技能</div>
                <button @click="showSkillTreeModal = true" 
                        class="text-xs px-3 py-2 bg-blue-600 text-white hover:bg-blue-700 rounded font-typewriter transition-colors">
                  🌳 管理技能樹
                </button>
              </div>
              
              <!-- 已學習技能網格 -->
              <div class="grid grid-cols-2 gap-2 min-h-[200px]">
                <div v-for="skillId in character.selectedSkills" :key="skillId"
                     class="skill-badge">
                  <div class="skill-tier-indicator" :class="getSkillTierClass(skillId)"></div>
                  <span class="skill-name">{{ getSkillName(skillId) }}</span>
                  <button @click="removeSkillDirect(skillId)" 
                          class="skill-remove-btn">
                    ✕
                  </button>
                </div>
                
                <!-- 空狀態提示 -->
                <div v-if="character.selectedSkills.length === 0" 
                     class="col-span-2 flex flex-col items-center justify-center py-8 text-gray-500">
                  <div class="text-4xl mb-2">🌱</div>
                  <div class="text-sm font-medium mb-1">尚未學習任何技能</div>
                  <div class="text-xs">點擊上方按鈕開始選擇技能</div>
                </div>
              </div>
              
              <!-- 技能統計 -->
              <div class="mt-4 pt-3 border-t border-gray-300 text-xs text-gray-600">
                <div class="flex justify-between">
                  <span>已學習：{{ character.selectedSkills.length }} 項技能</span>
                  <span>進度：{{ getSkillProgress() }}</span>
                </div>
              </div>
            </div>
          </div>

        </div>

        <!-- 右欄 - 密鑰 -->
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
                  <button @click="addNewCypher" 
                          :disabled="character.cypherLimit > 0 && character.cyphers.length >= character.cypherLimit"
                          :class="[
                            'text-xs px-2 py-1 rounded font-typewriter',
                            character.cypherLimit > 0 && character.cyphers.length >= character.cypherLimit 
                              ? 'bg-gray-400 text-gray-600 cursor-not-allowed' 
                              : 'bg-green-700 text-white hover:bg-green-800'
                          ]">
                    + 添加密鑰
                  </button>
                </div>
              </div>
              
              <!-- 密鑰列表 -->
              <div class="space-y-2 max-h-96 overflow-y-auto">
                <div v-for="(cypher, index) in character.cyphers" :key="index" class="border border-gray-300 rounded p-2 bg-gray-50">
                  <div class="flex items-center justify-between mb-2">
                    <button @click="cypher.collapsed = !cypher.collapsed" 
                            class="flex items-center text-sm font-medium text-gray-700 hover:text-black flex-1 text-left">
                      <span class="mr-2">{{ cypher.collapsed ? '▶' : '▼' }}</span>
                      <span>{{ getCypherTitle(cypher.content) || `密鑰 ${index + 1}` }}</span>
                    </button>
                    <div class="flex items-center space-x-1">
                      <button @click="copyCypherToClipboard(cypher)" class="text-blue-600 hover:text-blue-800 text-xs px-1 py-1 rounded border border-blue-300 hover:bg-blue-50" title="複製密鑰詳細內容">
                        📋
                      </button>
                      <button @click="removeCypher(index)" class="text-red-600 hover:text-red-800 text-xs">
                        ✕
                      </button>
                    </div>
                  </div>
                  
                  <div v-if="!cypher.collapsed">
                    <textarea v-model="cypher.content" 
                             placeholder="貼上完整密鑰內容，包含標題、等級和效果描述..." 
                             class="w-full h-24 text-xs bg-transparent border border-gray-300 rounded p-2 resize-none focus:outline-none focus:border-black font-typewriter"
                             rows="4"></textarea>
                  </div>
                  
                  <!-- 摺疊時顯示預覽 -->
                  <div v-else-if="cypher.content" class="text-xs text-gray-600 italic truncate">
                    {{ getCypherPreview(cypher.content) }}
                  </div>
                </div>
                
                <!-- 無密鑰時的提示 -->
                <div v-if="character.cyphers.length === 0" class="text-center text-gray-500 text-sm py-4">
                  尚未添加任何密鑰
                </div>
                
                <!-- 密鑰上限提示 -->
                <div v-if="character.cypherLimit > 0 && character.cyphers.length >= character.cypherLimit" 
                     class="text-center text-orange-600 text-xs py-2">
                  已達密鑰上限 ({{ character.cyphers.length }}/{{ character.cypherLimit }})
                </div>
              </div>
            </div>
          </div>


        </div>
      </div>
    </div>
    
    <!-- 技能樹 Modal -->
    <div v-if="showSkillTreeModal" 
         class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50"
         @click="closeSkillTreeModal">
      <div class="skill-tree-modal" @click.stop>
        <div class="modal-header">
          <h3 class="modal-title">技能樹管理</h3>
          <button @click="closeSkillTreeModal" class="modal-close-btn">✕</button>
        </div>
        
        <div class="modal-body">
          <div class="skill-tree-container">
            <div class="skill-tree relative p-6">
              
              <!-- 初階技能 -->
              <div class="skill-tier absolute left-0 top-0">
                <div>初階</div>
                <div v-for="skill in basicSkills" :key="skill.id" 
                     class="skill-node cursor-pointer" 
                     :class="{ 
                       'skill-selected': isSkillSelected(skill.id),
                       'has-connection': hasConnection(skill.id),
                       'connection-active': hasActiveConnection(skill.id)
                     }"
                     @click="toggleSkill(skill.id)">
                  <div class="skill-circle">
                    <span class="skill-dot"></span>
                  </div>
                  <span class="skill-label">{{ skill.name }}</span>
                </div>
              </div>
              
              <!-- 中階技能 -->
              <div class="skill-tier absolute left-80 top-0">
                <div>中階</div>
                <div v-for="skill in intermediateSkills" :key="skill.id" 
                     class="skill-node cursor-pointer" 
                     :class="{ 
                       'skill-selected': isSkillSelected(skill.id),
                       'skill-disabled': !canSelectSkill(skill.id),
                       'has-connection': hasConnection(skill.id),
                       'connection-active': hasActiveConnection(skill.id)
                     }"
                     :title="getSkillTooltip(skill)"
                     @click="toggleSkill(skill.id)">
                  <div class="skill-circle">
                    <span class="skill-dot"></span>
                  </div>
                  <span class="skill-label">{{ skill.name }}</span>
                </div>
              </div>
              
              <!-- 高階技能 -->
              <div class="skill-tier absolute left-[640px] top-0">
                <div>高階</div>
                <div v-for="skill in advancedSkills" :key="skill.id" 
                     class="skill-node cursor-pointer" 
                     :class="{ 
                       'skill-selected': isSkillSelected(skill.id),
                       'skill-disabled': !canSelectSkill(skill.id)
                     }"
                     :title="getSkillTooltip(skill)"
                     @click="toggleSkill(skill.id)">
                  <div class="skill-circle">
                    <span class="skill-dot"></span>
                  </div>
                  <span class="skill-label">{{ skill.name }}</span>
                </div>
              </div>
              
              <!-- 連接線 -->
              <svg class="skill-connections" width="1000" height="700" style="position: absolute; top: 0; left: 0; pointer-events: none; z-index: 1;">
                <defs>
                  <marker id="arrowhead" markerWidth="10" markerHeight="7" 
                          refX="9" refY="3.5" orient="auto">
                    <polygon points="0 0, 10 3.5, 0 7" fill="#28a745" />
                  </marker>
                  <marker id="arrowhead-inactive" markerWidth="10" markerHeight="7" 
                          refX="9" refY="3.5" orient="auto">
                    <polygon points="0 0, 10 3.5, 0 7" fill="#dee2e6" />
                  </marker>
                </defs>
                <g v-for="connection in skillConnections" :key="connection.from + '-' + connection.to">
                  <line :x1="getSkillPosition(connection.from).x" 
                        :y1="getSkillPosition(connection.from).y"
                        :x2="getSkillPosition(connection.to).x" 
                        :y2="getSkillPosition(connection.to).y"
                        :stroke="isConnectionActive(connection.from, connection.to) ? '#28a745' : '#dee2e6'"
                        :stroke-width="isConnectionActive(connection.from, connection.to) ? 3 : 2"
                        :opacity="isConnectionActive(connection.from, connection.to) ? 0.8 : 0.4"
                        :stroke-dasharray="isConnectionActive(connection.from, connection.to) ? 'none' : '5,5'"
                        :marker-end="isConnectionActive(connection.from, connection.to) ? 'url(#arrowhead)' : 'url(#arrowhead-inactive)'" />
                </g>
              </svg>
            </div>
          </div>
        </div>
        
        <div class="modal-footer">
          <div class="skill-summary">
            <span>已選擇 {{ character.selectedSkills.length }} 項技能</span>
          </div>
          <button @click="closeSkillTreeModal" 
                  class="px-4 py-2 bg-green-600 text-white hover:bg-green-700 rounded font-typewriter">
            完成
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch, nextTick } from 'vue'

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
  currentStress: 0,
  stressMin: 0,
  wounds: [
    // 預設 3 個輕度傷勢格
    { severity: 'light', checked: false },
    { severity: 'light', checked: false },
    { severity: 'light', checked: false },
    // 預設 2 個重度傷勢格
    { severity: 'serious', checked: false },
    { severity: 'serious', checked: false },
    // 預設 2 個嚴重傷勢格
    { severity: 'critical', checked: false },
    { severity: 'critical', checked: false }
  ],
  cyphers: [],
  cypherLimit: 0,
  selectedSkills: [],
  xp: 0,
  background: '',
  recoveryBonus: 0,
  abilities: [] // 保留以防錯誤
})

// 複製提示狀態
const showCopyNotification = ref(false)
const copyNotificationText = ref('')

// 技能樹Modal狀態
const showSkillTreeModal = ref(false)

// 技能樹資料
const basicSkills = ref([
  { id: 'linguistics', name: '語言學' },
  { id: 'biology', name: '動物學' },
  { id: 'botany', name: '植物學' },
  { id: 'geology', name: '地質學' },
  { id: 'industrialEquipment', name: '工業裝備' },
  { id: 'firstAid', name: '應急裝配' },
  { id: 'chemistry', name: '化學' },
  { id: 'computers', name: '電腦' },
  { id: 'zerogy', name: '零重力' },
  { id: 'mathematics', name: '數學' },
  { id: 'art', name: '藝術' },
  { id: 'archaeology', name: '考古學' },
  { id: 'theology', name: '神學' },
  { id: 'militaryTraining', name: '軍事訓練' },
  { id: 'extravehicular', name: '外環門路' },
  { id: 'athletics', name: '運動' }
])

const intermediateSkills = ref([
  { id: 'psychology', name: '心理學', prereq: ['linguistics'] },
  { id: 'pathology', name: '病理學', prereq: ['biology'] },
  { id: 'fieldMedicine', name: '戰地醫學', prereq: ['botany'] },
  { id: 'ecology', name: '生態學', prereq: ['botany'] },
  { id: 'asteroid', name: '小行星礦', prereq: ['geology'] },
  { id: 'mechanics', name: '機械修理', prereq: ['industrialEquipment'] },
  { id: 'explosives', name: '炸藥', prereq: ['firstAid', 'chemistry'] },
  { id: 'pharmacology', name: '藥理學', prereq: ['chemistry'] },
  { id: 'hacking', name: '駭客', prereq: ['computers'] },
  { id: 'piloting', name: '駕駛', prereq: ['zerogy'] },
  { id: 'physics', name: '物理學', prereq: ['mathematics'] },
  { id: 'mysticism', name: '神祕學', prereq: ['art', 'archaeology', 'theology'] },
  { id: 'survival', name: '野外求生', prereq: ['archaeology', 'theology'] },
  { id: 'gunsmithing', name: '槍械', prereq: ['militaryTraining'] },
  { id: 'closeCombat', name: '格鬥', prereq: ['extravehicular'] }
])

const advancedSkills = ref([
  { id: 'xenopsychology', name: '智慧種族學', prereq: ['psychology'] },
  { id: 'xenobiology', name: '外星生物學', prereq: ['pathology'] },
  { id: 'surgery', name: '手術', prereq: ['fieldMedicine'] },
  { id: 'planetology', name: '行星學', prereq: ['ecology', 'asteroid'] },
  { id: 'robotics', name: '機器人學', prereq: ['asteroid'] },
  { id: 'engineering', name: '工程學', prereq: ['mechanics'] },
  { id: 'hyperspace', name: '橫控學', prereq: ['explosives'] },
  { id: 'ai', name: '人工智慧', prereq: ['hacking'] },
  { id: 'hyperspace2', name: '超空間', prereq: ['physics', 'piloting'] },
  { id: 'alienology', name: '異種妙教', prereq: ['mysticism'] },
  { id: 'command', name: '指揮', prereq: ['gunsmithing', 'survival'] }
])

// 技能連接關係 - 自動生成基於先決條件
const skillConnections = computed(() => {
  const connections = []
  
  // 為中階技能生成連線
  intermediateSkills.value.forEach(skill => {
    if (skill.prereq && Array.isArray(skill.prereq)) {
      skill.prereq.forEach(prereqId => {
        connections.push({ from: prereqId, to: skill.id })
      })
    } else if (skill.prereq) {
      connections.push({ from: skill.prereq, to: skill.id })
    }
  })
  
  // 為高階技能生成連線
  advancedSkills.value.forEach(skill => {
    if (skill.prereq && Array.isArray(skill.prereq)) {
      skill.prereq.forEach(prereqId => {
        connections.push({ from: prereqId, to: skill.id })
      })
    } else if (skill.prereq) {
      connections.push({ from: skill.prereq, to: skill.id })
    }
  })
  
  return connections
})

// 顯示複製成功提示
const showCopySuccess = (text) => {
  copyNotificationText.value = text
  showCopyNotification.value = true
  setTimeout(() => {
    showCopyNotification.value = false
  }, 2000) // 2秒後隱藏
}

// 密鑰管理
const addNewCypher = () => {
  // 檢查是否達到上限
  if (character.value.cypherLimit > 0 && character.value.cyphers.length >= character.value.cypherLimit) {
    alert(`已達密鑰上限 (${character.value.cypherLimit} 個)`)
    return
  }
  
  character.value.cyphers.push({
    content: '',
    collapsed: false
  })
}

const removeCypher = (index) => {
  character.value.cyphers.splice(index, 1)
}

const copyCypherToClipboard = async (cypher) => {
  const cypherText = cypher.content || '(空白密鑰)'
  
  try {
    await navigator.clipboard.writeText(cypherText)
    showCopySuccess('密鑰內容已複製！')
  } catch (error) {
    console.error('複製失敗:', error)
    // 備用方案：創建臨時文字區域
    const textArea = document.createElement('textarea')
    textArea.value = cypherText
    document.body.appendChild(textArea)
    textArea.select()
    try {
      document.execCommand('copy')
      showCopySuccess('密鑰內容已複製！')
    } catch (fallbackError) {
      alert('複製失敗，請手動複製內容')
    }
    document.body.removeChild(textArea)
  }
}

const getCypherJsonPath = () => {
  // Nuxt 3/4: use useRuntimeConfig().app.baseURL if可用
  // 但前端可用 window.location.pathname 判斷
  const isDev = process.dev || window.location.hostname === 'localhost'
  if (isDev) {
    return '/data/cypher.json'
  } else {
    // 取得 base 路徑（假設部署在 /magnus-csr）
    const base = window.location.pathname.split('/').filter(Boolean)[0] || ''
    return `/${base}/data/cypher.json`
  }
}

const generateRandomCyphers = async () => {
  const limit = character.value.cypherLimit || 0
  if (limit <= 0) {
    showCopySuccess('請先設定密鑰上限')
    return
  }
  
  // 計算需要補充的密鑰數量
  const currentCount = character.value.cyphers.length
  const needCount = limit - currentCount
  
  if (needCount <= 0) {
    showCopySuccess('密鑰已達上限，無需補充')
    return
  }
  
  try {
    const cypherPath = getCypherJsonPath()
    const response = await fetch(cypherPath)
    if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`)
    const cypherData = await response.json()
    
    // 只補充缺少的密鑰，不清空現有的
    for (let i = 0; i < needCount; i++) {
      const randomIndex = Math.floor(Math.random() * cypherData.length)
      const randomCypher = cypherData[randomIndex]
      
      // 組合完整內容：標題 + 等級 + 效果
      let content = randomCypher.title + '\n等級：' + randomCypher.level + '\n' + randomCypher.content
      
      // 處理擲骰表格
      if (randomCypher.roll_table) {
        content += '\n\n' + randomCypher.roll_table.map(item => 
          `${item.range}：${item.result}`
        ).join('\n')
      }
      
      character.value.cyphers.push({
        content: content,
        collapsed: false
      })
    }
    
    showCopySuccess(`成功補充 ${needCount} 個隨機密鑰！(${currentCount} → ${limit})`)
  } catch (error) {
    console.error('載入密鑰資料失敗:', error)
    showCopySuccess('載入密鑰資料失敗，請檢查檔案是否存在')
  }
}

// 技能樹管理
const isSkillSelected = (skillId) => {
  return character.value.selectedSkills.includes(skillId)
}

const canSelectSkill = (skillId) => {
  // 初階技能總是可以選擇
  const basicSkill = basicSkills.value.find(s => s.id === skillId)
  if (basicSkill) return true
  
  // 找到技能的先決條件
  const skill = [...intermediateSkills.value, ...advancedSkills.value].find(s => s.id === skillId)
  if (!skill || !skill.prereq || skill.prereq.length === 0) return true
  
  // 檢查所有先決條件是否都已選擇
  return skill.prereq.every(prereqId => isSkillSelected(prereqId))
}

const toggleSkill = (skillId) => {
  if (!canSelectSkill(skillId)) return
  
  const index = character.value.selectedSkills.indexOf(skillId)
  if (index > -1) {
    // 移除技能，同時移除所有依賴此技能的技能
    character.value.selectedSkills.splice(index, 1)
    removeDependentSkills(skillId)
  } else {
    // 添加技能
    character.value.selectedSkills.push(skillId)
  }
}

const removeDependentSkills = (skillId) => {
  // 找到所有依賴此技能的技能
  const dependentSkills = [...intermediateSkills.value, ...advancedSkills.value]
    .filter(s => s.prereq && s.prereq.includes(skillId))
    .map(s => s.id)
  
  dependentSkills.forEach(depSkillId => {
    const index = character.value.selectedSkills.indexOf(depSkillId)
    if (index > -1) {
      character.value.selectedSkills.splice(index, 1)
      // 遞歸移除更深層的依賴
      removeDependentSkills(depSkillId)
    }
  })
}

const getSkillTooltip = (skill) => {
  let tooltip = skill.name
  
  if (skill.description) {
    tooltip += ': ' + skill.description
  }
  
  if (skill.prereq) {
    if (Array.isArray(skill.prereq)) {
      tooltip += '\n需要: ' + skill.prereq.join(', ')
    } else {
      tooltip += '\n需要: ' + skill.prereq
    }
  }
  
  return tooltip
}

const getSkillName = (skillId) => {
  const allSkills = [...basicSkills.value, ...intermediateSkills.value, ...advancedSkills.value]
  const skill = allSkills.find(s => s.id === skillId)
  return skill ? skill.name : skillId
}

const getSkillPosition = (skillId) => {
  // 根據技能ID返回在技能樹中的位置
  const nodeHeight = 48 // 節點高度包含間距
  const startY = 60 // 起始Y位置（標題下方）
  
  const basicIndex = basicSkills.value.findIndex(s => s.id === skillId)
  if (basicIndex !== -1) {
    return { 
      x: 140, // 基礎技能列的中心X位置
      y: startY + basicIndex * nodeHeight 
    }
  }
  
  const intIndex = intermediateSkills.value.findIndex(s => s.id === skillId)
  if (intIndex !== -1) {
    return { 
      x: 420, // 中階技能列的中心X位置
      y: startY + intIndex * nodeHeight 
    }
  }
  
  const advIndex = advancedSkills.value.findIndex(s => s.id === skillId)
  if (advIndex !== -1) {
    return { 
      x: 700, // 高階技能列的中心X位置
      y: startY + advIndex * nodeHeight 
    }
  }
  
  return { x: 0, y: 0 }
}

const isConnectionActive = (fromId, toId) => {
  return isSkillSelected(fromId) && isSkillSelected(toId)
}

const hasConnection = (skillId) => {
  // 檢查此技能是否有連接到其他技能
  return skillConnections.value.some(conn => conn.from === skillId)
}

const hasActiveConnection = (skillId) => {
  // 檢查此技能的連接線是否應該高亮
  const connections = skillConnections.value.filter(conn => conn.from === skillId)
  return connections.some(conn => isSkillSelected(skillId) && isSkillSelected(conn.to))
}

// Modal 管理函數
const closeSkillTreeModal = () => {
  showSkillTreeModal.value = false
  document.body.style.overflow = 'auto'
}

// 監聽modal狀態變化
watch(showSkillTreeModal, (newValue) => {
  if (newValue) {
    document.body.style.overflow = 'hidden'
  } else {
    document.body.style.overflow = 'auto'
  }
})

// 直接移除技能（不檢查依賴）
const removeSkillDirect = (skillId) => {
  if (confirm(`確定要移除「${getSkillName(skillId)}」技能嗎？這會同時移除所有依賴此技能的進階技能。`)) {
    const index = character.value.selectedSkills.indexOf(skillId)
    if (index > -1) {
      character.value.selectedSkills.splice(index, 1)
      removeDependentSkills(skillId)
    }
  }
}

// 獲取技能階層樣式
const getSkillTierClass = (skillId) => {
  if (basicSkills.value.find(s => s.id === skillId)) return 'tier-basic'
  if (intermediateSkills.value.find(s => s.id === skillId)) return 'tier-intermediate'
  if (advancedSkills.value.find(s => s.id === skillId)) return 'tier-advanced'
  return ''
}

// 獲取技能進度
const getSkillProgress = () => {
  const basic = character.value.selectedSkills.filter(id => 
    basicSkills.value.find(s => s.id === id)
  ).length
  const intermediate = character.value.selectedSkills.filter(id => 
    intermediateSkills.value.find(s => s.id === id)
  ).length
  const advanced = character.value.selectedSkills.filter(id => 
    advancedSkills.value.find(s => s.id === id)
  ).length
  
  return `初階 ${basic} | 中階 ${intermediate} | 高階 ${advanced}`
}

// 傷勢格管理
const addWoundBox = (severity) => {
  character.value.wounds.push({
    severity: severity,
    checked: false
  })
}

const removeLastWoundBox = (severity) => {
  // 從後面開始找到第一個符合類型的傷勢格並移除
  for (let i = character.value.wounds.length - 1; i >= 0; i--) {
    if (character.value.wounds[i].severity === severity) {
      character.value.wounds.splice(i, 1)
      break
    }
  }
}

const hasWoundsOfType = (severity) => {
  return character.value.wounds.some(w => w.severity === severity)
}

const getLightWounds = () => {
  return character.value.wounds.filter(w => w.severity === 'light')
}

const getSeriousWounds = () => {
  return character.value.wounds.filter(w => w.severity === 'serious')
}

const getCriticalWounds = () => {
  return character.value.wounds.filter(w => w.severity === 'critical')
}



// 解析密鑰標題
const getCypherTitle = (content) => {
  if (!content) return null
  
  // 提取第一行作為標題
  const firstLine = content.split('\n')[0].trim()
  if (firstLine.length > 0 && firstLine.length <= 50) {
    return firstLine
  }
  
  return null
}

// 獲取密鑰預覽
const getCypherPreview = (content) => {
  if (!content) return ''
  
  // 跳過第一行（標題），顯示後面的內容
  const lines = content.split('\n')
  if (lines.length > 1) {
    const preview = lines.slice(1).join(' ').trim()
    return preview.length > 60 ? preview.substring(0, 60) + '...' : preview
  }
  
  // 如果只有一行，顯示前60個字符
  return content.length > 60 ? content.substring(0, 60) + '...' : content
}

// 能力管理函數（保留以防模板中仍有引用）
const addNewAbility = () => {
  // 不再實際添加能力，只是避免錯誤
  console.log('能力功能已移除')
}

const removeAbility = (index) => {
  // 不再實際移除能力，只是避免錯誤  
  console.log('能力功能已移除')
}

const copyAbilityToClipboard = async (ability) => {
  // 不再實際複製能力，只是避免錯誤
  console.log('能力功能已移除')
}

const getAbilityTitle = (content) => {
  // 返回空字符串避免錯誤
  return ''
}

const getAbilityPreview = (content) => {
  // 返回空字符串避免錯誤
  return ''
}

// ESC鍵關閉modal
const handleKeydown = (event) => {
  if (event.key === 'Escape' && showSkillTreeModal.value) {
    closeSkillTreeModal()
  }
}

// 隱藏原生 title 工具提示
onMounted(() => {
  // 頁面載入時自動載入儲存的資料
  loadFromLocalStorage()
  
  // 添加ESC鍵監聽
  document.addEventListener('keydown', handleKeydown)
  
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

onUnmounted(() => {
  // 清理事件監聽器
  document.removeEventListener('keydown', handleKeydown)
  // 確保滾動恢復
  document.body.style.overflow = 'auto'
})

// 監聽角色資料變化，自動儲存到 localStorage
watch(character, () => {
  nextTick(() => {
    saveToLocalStorage()
  })
}, { deep: true })

const saveCharacter = () => {
  console.log('角色資料:', character.value)
  alert('角色已儲存！（目前只儲存在瀏覽器 console）')
}

const clearForm = () => {
  if (confirm('確定要清除所有資料嗎？這將建立全新角色，無法復原。')) {
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
      currentStress: 0,
      stressMin: 0,
      wounds: [
        // 預設 3 個輕度傷勢格
        { severity: 'light', checked: false },
        { severity: 'light', checked: false },
        { severity: 'light', checked: false },
        // 預設 2 個重度傷勢格
        { severity: 'serious', checked: false },
        { severity: 'serious', checked: false },
        // 預設 2 個嚴重傷勢格
        { severity: 'critical', checked: false },
        { severity: 'critical', checked: false }
      ],
      cyphers: [],
      cypherLimit: 0,
      selectedSkills: [],
      xp: 0,
      background: '',
      recoveryBonus: 0,
      abilities: [] // 保留以防錯誤
    }
    showCopySuccess('所有資料已清空，可以建立新角色了！')
  }
}

// 角色資料管理功能
const saveToLocalStorage = () => {
  try {
    const characterData = JSON.stringify(character.value)
    localStorage.setItem('magnus-csr-character', characterData)
  } catch (error) {
    console.error('自動儲存失敗:', error)
  }
}

const loadFromLocalStorage = () => {
  try {
    const savedData = localStorage.getItem('magnus-csr-character')
    if (savedData) {
      const parsedData = JSON.parse(savedData)
      
      // 先處理技能資料，確保格式正確
      let processedSkills = Array(15).fill(null).map(() => ({ text: '', level: 'normal', editing: false }))
      if (parsedData.skills && Array.isArray(parsedData.skills)) {
        processedSkills = Array(15).fill(null).map((_, i) => {
          const existing = parsedData.skills[i]
          if (typeof existing === 'string') {
            return { text: existing, level: 'normal', editing: false }
          } else if (existing && typeof existing === 'object') {
            return { 
              text: existing.text || '', 
              level: existing.level || 'normal', 
              editing: false 
            }
          }
          return { text: '', level: 'normal', editing: false }
        })
      }
      
      // 確保所有必要的屬性都存在，並補充預設值
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
        currentStress: 0,
        stressMin: 0,
        wounds: [
          // 預設 3 個輕度傷勢格
          { severity: 'light', checked: false },
          { severity: 'light', checked: false },
          { severity: 'light', checked: false },
          // 預設 2 個重度傷勢格
          { severity: 'serious', checked: false },
          { severity: 'serious', checked: false },
          // 預設 2 個嚴重傷勢格
          { severity: 'critical', checked: false },
          { severity: 'critical', checked: false }
        ],
        cyphers: [],
        cypherLimit: 0,
        selectedSkills: [],
        xp: 0,
        background: '',
        recoveryBonus: 0,
        abilities: [], // 保留以防錯誤
        ...parsedData, // 覆蓋已儲存的資料
        selectedSkills: parsedData.selectedSkills || [] // 確保技能選擇格式正確
      }
      
      // 確保陣列長度正確
      if (!character.value.supernaturalStressMarks || character.value.supernaturalStressMarks.length !== 10) {
        character.value.supernaturalStressMarks = Array(10).fill(false).map((_, i) => character.value.supernaturalStressMarks?.[i] || false)
      }
    }
  } catch (error) {
    console.error('自動載入失敗:', error)
  }
}

const exportToJSON = () => {
  try {
    const characterData = JSON.stringify(character.value, null, 2)
    const blob = new Blob([characterData], { type: 'application/json' })
    const url = URL.createObjectURL(blob)
    
    const link = document.createElement('a')
    link.href = url
    link.download = `${character.value.name || 'magnus-character'}-${new Date().toISOString().split('T')[0]}.json`
    document.body.appendChild(link)
    link.click()
    document.body.removeChild(link)
    URL.revokeObjectURL(url)
    
    showCopySuccess('JSON 檔案已下載！')
  } catch (error) {
    console.error('匯出失敗:', error)
    showCopySuccess('匯出失敗，請重試')
  }
}

const importFromJSON = (event) => {
  const file = event.target.files[0]
  if (!file) return
  
  const reader = new FileReader()
  reader.onload = (e) => {
    try {
      const importedData = JSON.parse(e.target.result)
      if (confirm('確定要匯入這個角色資料嗎？這將覆蓋目前的資料。')) {
        // 先處理技能資料，確保格式正確
        let processedSkills = Array(15).fill(null).map(() => ({ text: '', level: 'normal', editing: false }))
        if (importedData.skills && Array.isArray(importedData.skills)) {
          processedSkills = Array(15).fill(null).map((_, i) => {
            const imported = importedData.skills[i]
            if (typeof imported === 'string') {
              return { text: imported, level: 'normal', editing: false }
            } else if (imported && typeof imported === 'object') {
              return { 
                text: imported.text || '', 
                level: imported.level || 'normal', 
                editing: false 
              }
            }
            return { text: '', level: 'normal', editing: false }
          })
        }
        
        character.value = {
          ...character.value,
          ...importedData,
          selectedSkills: importedData.selectedSkills || [] // 確保技能選擇格式正確
        }
        // 確保陣列長度正確
        if (!character.value.supernaturalStressMarks || character.value.supernaturalStressMarks.length !== 10) {
          character.value.supernaturalStressMarks = Array(10).fill(false).map((_, i) => importedData.supernaturalStressMarks?.[i] || false)
        }
        showCopySuccess('角色資料匯入成功！')
        // 自動儲存會由 watch 觸發
      }
    } catch (error) {
      console.error('匯入失敗:', error)
      showCopySuccess('匯入失敗，請檢查檔案格式')
    }
  }
  reader.readAsText(file)
  
  // 清除 input 值，允許重複選擇同一檔案
  event.target.value = ''
}

const exportToText = async () => {
  try {
    // 獲取已選擇的技能
    const selectedSkillNames = character.value.selectedSkills.map(skillId => {
      const skill = [...basicSkills, ...intermediateSkills, ...advancedSkills].find(s => s.id === skillId)
      return skill ? skill.name : skillId
    })
    
    let textContent = `MOTHERSHIP RPG - 角色卡
==========================================

【基本資訊】
姓名：${character.value.name || '(未設定)'}
角色句子：${character.value.focus || '(未設定)'}

【屬性數值】
位階：${character.value.tier}　努力：${character.value.effort}　XP：${character.value.xp}

氣力　池：${character.value.might.pool}　節省值：${character.value.might.edge}　目前：${character.value.might.current}
速度　池：${character.value.speed.pool}　節省值：${character.value.speed.edge}　目前：${character.value.speed.current}
智力　池：${character.value.intellect.pool}　節省值：${character.value.intellect.edge}　目前：${character.value.intellect.current}

【恢復骰】1d6+${character.value.recoveryBonus}
動作：${character.value.recoveryRolls.action ? '✓' : '○'}　10分鐘：${character.value.recoveryRolls.tenMin ? '✓' : '○'}　1小時：${character.value.recoveryRolls.oneHour ? '✓' : '○'}　10小時：${character.value.recoveryRolls.tenHours ? '✓' : '○'}

【狀態】
壓力：${character.value.currentStress}/${character.value.stressMin}

【傷勢】
輕度：${character.value.wounds.filter(w => w.severity === 'light').map(w => w.checked ? '☑' : '☐').join(' ')}
重度：${character.value.wounds.filter(w => w.severity === 'serious').map(w => w.checked ? '☑' : '☐').join(' ')}
嚴重：${character.value.wounds.filter(w => w.severity === 'critical').map(w => w.checked ? '☑' : '☐').join(' ')}

【已習得技能】
${selectedSkillNames.length > 0 ? 
  selectedSkillNames.map((skillName, index) => `${index + 1}. ${skillName}`).join('\n') : 
  '(尚未習得任何技能)'}

【密鑰】(上限：${character.value.cypherLimit})
${character.value.cyphers.length > 0 ? 
  character.value.cyphers.map((cypher, index) => 
    `${index + 1}. ${cypher.content.replace(/\n/g, '\n   ')}`
  ).join('\n\n') : '(無密鑰)'}

==========================================
匯出時間：${new Date().toLocaleString('zh-TW')}
`

    // 複製到剪貼簿
    try {
      await navigator.clipboard.writeText(textContent)
      showCopySuccess('角色卡純文字版本已複製到剪貼簿！')
    } catch (error) {
      console.error('複製失敗:', error)
      // 備用方案：創建臨時文字區域
      const textArea = document.createElement('textarea')
      textArea.value = textContent
      document.body.appendChild(textArea)
      textArea.select()
      try {
        document.execCommand('copy')
        showCopySuccess('角色卡純文字版本已複製到剪貼簿！')
      } catch (fallbackError) {
        showCopySuccess('複製失敗，請重試')
      }
      document.body.removeChild(textArea)
    }
  } catch (error) {
    console.error('匯出失敗:', error)
    showCopySuccess('匯出失敗，請重試')
  }
}

// 複製狀態值到剪貼簿
const copyStatusToClipboard = async () => {
  // 計算各類型傷勢數量
  const lightWounds = character.value.wounds.filter(w => w.severity === 'light' && w.checked).length
  const seriousWounds = character.value.wounds.filter(w => w.severity === 'serious' && w.checked).length
  const criticalWounds = character.value.wounds.filter(w => w.severity === 'critical' && w.checked).length
  const totalWounds = lightWounds + seriousWounds + criticalWounds
  
  const woundsSummary = totalWounds > 0 ? 
    `輕度:${lightWounds} 重度:${seriousWounds} 嚴重:${criticalWounds}` : 
    '無傷勢'
  
  const statusText = `${character.value.name || '未命名角色'}

【數值】
位階：${character.value.tier}　努力：${character.value.effort}　XP：${character.value.xp}

氣力　池：${character.value.might.pool}　節省值：${character.value.might.edge}　目前：${character.value.might.current}
速度　池：${character.value.speed.pool}　節省值：${character.value.speed.edge}　目前：${character.value.speed.current}
智力　池：${character.value.intellect.pool}　節省值：${character.value.intellect.edge}　目前：${character.value.intellect.current}

【恢復骰】1d6+${character.value.recoveryBonus}
動作：${character.value.recoveryRolls.action ? '✓' : '○'}　10分鐘：${character.value.recoveryRolls.tenMin ? '✓' : '○'}　1小時：${character.value.recoveryRolls.oneHour ? '✓' : '○'}　10小時：${character.value.recoveryRolls.tenHours ? '✓' : '○'}

【狀態】
壓力：${character.value.currentStress}/${character.value.stressMin}
傷勢：${woundsSummary}`

  try {
    await navigator.clipboard.writeText(statusText)
    showCopySuccess('狀態值已複製到剪貼簿！')
  } catch (error) {
    console.error('複製失敗:', error)
    // 降級處理
    const textArea = document.createElement('textarea')
    textArea.value = statusText
    document.body.appendChild(textArea)
    textArea.select()
    try {
      document.execCommand('copy')
      showCopySuccess('狀態值已複製到剪貼簿！')
    } catch (fallbackError) {
      showCopySuccess('複製失敗，請重試')
    }
    document.body.removeChild(textArea)
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

/* 下拉選單樣式 */
select {
  font-family: 'Special Elite', 'Courier New', monospace;
  background-color: #faf9f7;
  transition: all 0.2s ease;
}

select:focus {
  background-color: rgba(161, 60, 60, 0.05);
  border-color: #a13c3c;
}

select option {
  background-color: #faf9f7;
  color: inherit;
}

/* 技能顏色樣式 - 使用 !important 確保優先級 */
.text-red-600 {
  color: #dc2626 !important;
}

.text-green-700 {
  color: #15803d !important;
}

.text-blue-700 {
  color: #1d4ed8 !important;
}

.text-gray-800 {
  color: #1f2937 !important;
}

.text-gray-400 {
  color: #9ca3af !important;
}

/* 技能項目懸浮效果 */
.cursor-pointer:hover {
  background-color: rgba(0, 0, 0, 0.05) !important;
  transition: background-color 0.2s ease;
}

/* 編輯狀態樣式 */
.bg-yellow-50 {
  background-color: #fefce8;
}

.border-yellow-300 {
  border-color: #fde047;
}

/* 技能等級標籤樣式 */
.bg-red-50 {
  background-color: #fef2f2;
}

.border-red-300 {
  border-color: #fca5a5;
}

.bg-green-50 {
  background-color: #f0fdf4;
}

.border-green-300 {
  border-color: #86efac;
}

.bg-blue-50 {
  background-color: #eff6ff;
}

.border-blue-300 {
  border-color: #93c5fd;
}

.bg-gray-50 {
  background-color: #f9fafb;
}

.border-gray-300 {
  border-color: #d1d5db;
}

.text-gray-600 {
  color: #4b5563;
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

/* 技能樹樣式 */
.skill-tree {
  position: relative;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
  border-radius: 8px;
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.1);
}

.skill-tier {
  width: 280px;
  padding: 20px;
}

.skill-node {
  display: flex;
  align-items: center;
  padding: 8px 12px;
  margin: 8px 0;
  border-radius: 20px;
  background: linear-gradient(135deg, #ffffff 0%, #f8f9fa 100%);
  border: 2px solid #dee2e6;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
  position: relative;
  z-index: 2;
}

.skill-node:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.15);
  border-color: #6c757d;
}

.skill-node.skill-selected {
  background: linear-gradient(135deg, #28a745 0%, #20c997 100%);
  border-color: #1e7e34;
  color: white;
  font-weight: bold;
}

.skill-node.skill-selected:hover {
  background: linear-gradient(135deg, #218838 0%, #1c7c54 100%);
}

.skill-node.skill-disabled {
  background: linear-gradient(135deg, #e9ecef 0%, #dee2e6 100%);
  border-color: #adb5bd;
  color: #6c757d;
  cursor: not-allowed;
  opacity: 0.6;
}

.skill-node.skill-disabled:hover {
  transform: none;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.skill-circle {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: linear-gradient(135deg, #007bff 0%, #6f42c1 100%);
  border: 2px solid #0056b3;
  margin-right: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.skill-node.skill-selected .skill-circle {
  background: linear-gradient(135deg, #ffc107 0%, #fd7e14 100%);
  border-color: #e0a800;
}

.skill-node.skill-disabled .skill-circle {
  background: linear-gradient(135deg, #adb5bd 0%, #6c757d 100%);
  border-color: #868e96;
}

.skill-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: white;
}

.skill-label {
  font-size: 13px;
  font-weight: 500;
  line-height: 1.2;
}

/* 連接線樣式 */
.skill-connections {
  z-index: 1;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

.connection-active {
  stroke: #28a745;
  stroke-width: 3;
  opacity: 0.8;
  filter: drop-shadow(0 0 3px rgba(40, 167, 69, 0.5));
  animation: pulseConnection 2s infinite;
}

.connection-inactive {
  stroke: #dee2e6;
  stroke-width: 2;
  opacity: 0.4;
  stroke-dasharray: 5,5;
}

@keyframes pulseConnection {
  0%, 100% { opacity: 0.8; }
  50% { opacity: 1; }
}

/* CSS-based 連接線 (備用方案) */
.skill-node::before,
.skill-node::after {
  content: '';
  position: absolute;
  background: #dee2e6;
  z-index: -1;
}

/* 水平連接線 */
.skill-node.has-connection::after {
  width: 280px;
  height: 2px;
  right: -280px;
  top: 50%;
  transform: translateY(-50%);
  background: linear-gradient(to right, #dee2e6 0%, transparent 100%);
}

.skill-node.has-connection.connection-active::after {
  background: linear-gradient(to right, #28a745 0%, rgba(40, 167, 69, 0.3) 100%);
  height: 3px;
  animation: flowLine 2s infinite;
}

@keyframes flowLine {
  0% { transform: translateY(-50%) scaleX(0); transform-origin: left; }
  50% { transform: translateY(-50%) scaleX(1); }
  100% { transform: translateY(-50%) scaleX(1); }
}

/* 技能節點懸浮時的連接線高亮 */
.skill-node:hover.has-connection::after {
  background: linear-gradient(to right, #007bff 0%, rgba(0, 123, 255, 0.3) 100%);
  height: 3px;
}

/* 技能階層標題 */
.skill-tier > div:first-child {
  background: linear-gradient(135deg, #495057 0%, #343a40 100%);
  color: white;
  padding: 6px 12px;
  border-radius: 15px;
  font-size: 11px;
  font-weight: bold;
  letter-spacing: 1px;
  text-transform: uppercase;
  box-shadow: 0 2px 4px rgba(0,0,0,0.2);
  margin-bottom: 15px;
}

/* Modal 樣式 */
.skill-tree-modal {
  background: white;
  border-radius: 12px;
  box-shadow: 0 25px 50px rgba(0, 0, 0, 0.25);
  max-width: 1100px;
  width: 95vw;
  max-height: 90vh;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 24px;
  border-bottom: 2px solid #e5e7eb;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
}

.modal-title {
  font-size: 18px;
  font-weight: bold;
  color: #374151;
  font-family: 'Special Elite', 'Courier New', monospace;
}

.modal-close-btn {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background: #ef4444;
  color: white;
  border: none;
  font-size: 16px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.modal-close-btn:hover {
  background: #dc2626;
  transform: scale(1.1);
}

.modal-body {
  flex: 1;
  overflow: auto;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
}

.modal-body .skill-tree {
  min-width: 1000px;
  height: 700px;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
  border-radius: 8px;
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.1);
}

.modal-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 24px;
  border-top: 2px solid #e5e7eb;
  background: white;
}

.skill-summary {
  font-size: 14px;
  color: #6b7280;
  font-weight: 500;
}

/* 技能卡片樣式 */
.skill-badge {
  display: flex;
  align-items: center;
  background: linear-gradient(135deg, #ffffff 0%, #f8f9fa 100%);
  border: 2px solid #e5e7eb;
  border-radius: 8px;
  padding: 8px 12px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  transition: all 0.2s ease;
  position: relative;
}

.skill-badge:hover {
  transform: translateY(-1px);
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
  border-color: #d1d5db;
}

.skill-tier-indicator {
  width: 8px;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  border-radius: 8px 0 0 8px;
}

.tier-basic {
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
}

.tier-intermediate {
  background: linear-gradient(135deg, #3b82f6 0%, #2563eb 100%);
}

.tier-advanced {
  background: linear-gradient(135deg, #8b5cf6 0%, #7c3aed 100%);
}

.skill-name {
  flex: 1;
  font-size: 12px;
  font-weight: 500;
  margin-left: 12px;
  color: #374151;
}

.skill-remove-btn {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: #ef4444;
  color: white;
  border: none;
  font-size: 12px;
  cursor: pointer;
  transition: all 0.2s ease;
  margin-left: 8px;
}

.skill-remove-btn:hover {
  background: #dc2626;
  transform: scale(1.1);
}

/* Modal 動畫 */
@keyframes modalFadeIn {
  from {
    opacity: 0;
    transform: scale(0.9);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.skill-tree-modal {
  animation: modalFadeIn 0.3s ease-out;
}
</style>
