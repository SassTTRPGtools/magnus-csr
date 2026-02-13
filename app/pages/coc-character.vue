<template>
  <div class="min-h-screen bg-magnus-bg p-4">
    <div class="max-w-7xl mx-auto">
      <!-- 複製成功提示 -->
      <div v-if="showCopyNotification" 
           class="fixed top-4 right-4 bg-green-600 text-white px-4 py-2 rounded-lg shadow-lg z-50 font-typewriter text-sm animate-bounce">
        {{ copyNotificationText }}
      </div>
      
      <!-- 一欄式 + 分頁 (TAB) -->
      <div class="flex flex-col gap-4">
        <div class="flex flex-wrap gap-2">
          <button
            @click="showKeysModal = true"
            :class="[
              'px-3 py-2 rounded border text-xs font-typewriter transition-colors',
              'text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100'
            ]"
          >
            📖 密鑰&能力
          </button>

        </div>

        <!-- 角色頁（含技能） -->
        <div v-show="activeTab === 'character'">
          <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            <div class="space-y-6 lg:col-span-1">
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

              <!-- 傷害軌與壓力兩欄整合區塊 -->
              <div class="character-section mb-6">
                <div class="border-2 border-black bg-white p-3">
                  <div class="grid grid-cols-1 lg:grid-cols-2 gap-3">
                    
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
                          <span class="font-bold text-red-800" title="只能移動鄰近距離（通常是爬行）；如果速度為0則無法移動">重創</span>
                        </label>
                        <label class="flex items-center track-item p-1">
                          <input type="radio" v-model="character.damageTrack" value="dead" class="mr-1 scale-75">
                          <span class="font-bold text-black">死亡</span>
                        </label>
                      </div>
                      
                      <!-- 複製狀態值按鈕 -->
                      <div class="mt-3 pt-2 border-t border-gray-300">
                        <button @click="copyStatusToClipboard" 
                                class="w-full text-xs px-2 py-1 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100">
                          📋 複製狀態值
                        </button>
                      </div>
                    </div>

                    <!-- 理智值（中欄） -->
                    <div class="border-r border-gray-300 pr-3 relative">
                      <!-- 理智值區域 -->
                      <div class="mb-3">
                        <div class="text-center mb-2">
                          <div class="text-xs font-bold uppercase tracking-wide mb-1">理智值 (SAN)</div>
                          <div class="flex items-center justify-center gap-2">
                            <input type="text" 
                                   :value="character.currentSanity || 0"
                                   @input="e => { character.currentSanity = Number(e.target.value) || 0; saveCharacter(); }"
                                   class="w-12 h-9 text-center font-typewriter font-bold text-base border-2 border-black rounded py-1.5">
                            <span class="font-bold text-lg">/</span>
                            <div class="w-12 h-9 flex items-center justify-center font-typewriter font-bold text-base border-2 border-gray-400 bg-gray-100 rounded">
                              {{ character.maxSanity || 0 }}
                            </div>
                          </div>
                        </div>

                        <!-- 瘋狂閾值 -->
                        <div class="text-center mb-2">
                          <div class="text-xs font-bold uppercase tracking-wide mb-1">瘋狂閾值</div>
                          <div class="flex items-center justify-center gap-2">
                            <div class="w-16 h-9 flex items-center justify-center text-base font-typewriter font-bold border-2 border-red-600 bg-red-50 rounded">{{ insanityThreshold }}</div>
                            <button @click="resetInsanityThreshold" 
                                    class="h-9 flex items-center justify-center text-xs font-medium px-3 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100 whitespace-nowrap">
                              重設
                            </button>
                          </div>
                        </div>

                        <!-- 克蘇魯神話 -->
                        <div class="text-center">
                          <div class="text-xs font-bold uppercase tracking-wide mb-1">克蘇魯神話</div>
                          <input type="text" 
                                 :value="character.cthulhuMythos || 0"
                                 @input="e => { character.cthulhuMythos = Number(e.target.value) || 0; updateMaxSanity(); }"
                                 class="w-16 h-9 text-center font-typewriter font-bold text-base border-2 border-purple-600 rounded py-1.5">
                          <div class="text-[10px] text-gray-600 mt-0.5">減少理智上限</div>
                        </div>
                      </div>

                    </div>

                  </div>
                </div>
              </div>

              <!-- 攻擊 -->
              <div class="character-section mb-6">
                <div class="border-2 border-black bg-white p-4">
                  <div class="text-sm font-bold uppercase tracking-wide mb-3">攻擊</div>
                  <textarea v-model="attacksText" 
                            @input="syncAttacksFromText"
                            class="w-full h-24 bg-transparent font-typewriter text-sm border border-gray-300 rounded p-2 resize-none focus:outline-none focus:border-black"
                            placeholder="記錄攻擊方式..."></textarea>
                </div>
              </div>

              <!-- 進階 -->
              <div class="character-section">
                <div class="border-2 border-black bg-white p-4">
                  <div class="text-xs font-bold uppercase tracking-wide mb-3">晉升位階（完成任意四次）</div>
                  <div class="text-xs space-y-1">
                    <label class="flex items-center">
                      <input type="checkbox" v-model="character.advancementChecks.perfection" class="mr-2">
                      <span>邁向完美：選擇氣力、速度或智力一項節省值 +1。</span>
                    </label>
                    <label class="flex items-center">
                      <input type="checkbox" v-model="character.advancementChecks.effort" class="mr-2">
                      <span>額外努力：將努力值 +1。</span>
                    </label>
                    <label class="flex items-center">
                      <input type="checkbox" v-model="character.advancementChecks.skill" class="mr-2">
                      <span>技能：將一項技能提升一階，但不超過職業。</span>
                    </label>
                    <label class="flex items-center">
                      <input type="checkbox" v-model="character.advancementChecks.ability" class="mr-2">
                      <span>新能力：從你的當前或更低階層中選擇一項基於類型的新能力。</span>
                    </label>
                    <label class="flex items-center">
                      <input type="checkbox" v-model="character.advancementChecks.recovery" class="mr-2">
                      <span>提升恢復：你的恢復額外 +2。</span>
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
                            class="px-3 py-2 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100">
                      匯出 JSON
                    </button>
                    <label class="px-3 py-2 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100 cursor-pointer text-center">
                      匯入 JSON
                      <input type="file" @change="importFromJSON" accept=".json" class="hidden">
                    </label>
                    <!-- 第二行 -->
                    <button @click="exportToText" 
                            class="px-3 py-2 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100">
                      複製純文字版本
                    </button>
                    <button @click="clearForm" 
                            class="px-3 py-2 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100">
                      清空資料
                    </button>
                  </div>
                </div>
              </div>
            </div>

            <div class="space-y-6 lg:col-span-2">
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
                        <span class="block mb-1">上限值</span>
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
                        <span class="block mb-1">上限值</span>
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
                        <span class="block mb-1">上限值</span>
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

              <!-- 技能 -->
              <div class="character-section mb-6">
                <div class="border-2 border-black bg-white p-4">
                  <div class="flex items-center justify-between mb-2">
                    <div class="text-sm font-bold uppercase tracking-wide">技能</div>
                    <div class="flex gap-2">
                      <button @click="showGrowthChartModal = true" 
                              class="text-xs px-3 py-2 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100">
                        📊 成長對照
                      </button>
                      <button @click="showSkillsModal = true" 
                              class="text-xs px-3 py-2 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100">
                        管理
                      </button>
                    </div>
                  </div>
                  <div class="text-xs text-gray-600 font-typewriter mb-4">
                    <span>大師🎖️：可獲得免費1級努力</span>
                  </div>
                  <div class="grid grid-cols-1 lg:grid-cols-3 gap-3">
                    <div v-for="category in skillCategories" :key="category.id" class="border border-gray-200 rounded p-3 bg-gray-50">
                      <div class="flex items-center justify-between mb-2">
                        <div class="text-sm font-bold">{{ category.icon }} {{ category.label }}</div>
                        <label class="flex items-center cursor-pointer group">
                          <input type="checkbox" v-model="character.skillCategoryGrowth[category.id]" class="sr-only peer">
                          <div class="relative w-10 h-6 bg-gray-300 peer-checked:bg-green-600 rounded-full transition-colors border border-gray-400 peer-checked:border-green-700"></div>
                          <div class="absolute left-1 top-1 w-4 h-4 bg-white rounded-full peer-checked:translate-x-4 transition-transform pointer-events-none"></div>
                          <span class="ml-2 text-xs font-typewriter text-gray-600 group-hover:text-gray-800">{{ character.skillCategoryGrowth[category.id] ? '可成長' : '已鎖定' }}</span>
                        </label>
                      </div>
                      <div class="space-y-2">
                        <div v-for="skill in (visibleGroupedSkills[category.id] || [])" :key="skill.id" class="text-sm border-b border-gray-200 pb-2">
                          <div class="flex items-start justify-between">
                            <div class="pr-2">
                              <div class="font-medium">{{ skill && skill.name }}</div>
                              <div v-if="skill && skill.note" class="text-gray-600">{{ skill.note }}</div>
                            </div>
                            <span v-if="skill && getSkillDisplayLevel(skill)" :class="getSkillBadgeClass(skill)" class="px-2 py-0.5 rounded border text-xs whitespace-nowrap">
                              {{ getSkillDisplayLevel(skill) }}
                            </span>
                          </div>
                          <div v-if="skill && getVisibleSpecialties(skill).length" class="mt-2 space-y-1">
                            <div v-for="(spec, specIndex) in getVisibleSpecialties(skill)" :key="specIndex" class="flex items-start justify-between text-xs">
                              <div class="text-gray-700">- {{ spec && spec.name }}</div>
                              <span v-if="spec" :class="getSkillLevelBadgeClass(spec.level)" class="px-2 py-0.5 rounded border text-xs whitespace-nowrap">
                                {{ getSkillLevelLabel(spec.level) }}
                              </span>
                            </div>
                          </div>
                        </div>
                        <div v-if="(visibleGroupedSkills[category.id] || []).length === 0" class="text-xs text-gray-500">無技能</div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>

              <!-- 裝備 -->
              <div class="character-section">
                <div class="border-2 border-black bg-white p-4">
                  <div class="text-center text-sm font-bold uppercase tracking-wide mb-4">裝備</div>
                  <textarea v-model="character.equipment" 
                            class="w-full h-80 bg-transparent font-typewriter text-sm border-none resize-none focus:outline-none"
                            placeholder="記錄裝備..."></textarea>
                </div>
              </div>
            </div>
          </div>
        </div>


      </div>
    </div>
  </div>

  <!-- 密鑰&能力浮動侧邊欄 -->
  <transition name="slide-right">
    <div v-if="showKeysModal" class="fixed left-0 top-0 h-screen z-40 bg-white border-r-2 border-black flex flex-col" :style="{ width: keysModalWidth + 'px' }">
    <!-- 標題欄 -->
    <div class="flex items-center justify-between p-4 border-b-2 border-black flex-shrink-0">
      <div class="text-sm font-bold uppercase tracking-wide">密鑰&能力</div>
      <button @click="showKeysModal = false" class="text-lg font-bold text-gray-700 hover:text-black transition-colors">✕</button>
    </div>
    <!-- 內容區 -->
    <div class="p-4 overflow-y-auto flex-1 space-y-6">
        <!-- 密鑰部分 -->
        <div class="character-section">
          <div class="border-2 border-black bg-white p-4">
            <div class="flex items-center justify-between mb-4">
              <div class="text-sm font-bold uppercase tracking-wide">密鑰</div>
              <div class="flex items-center space-x-2">
                <span class="text-xs px-2 py-1 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50">密鑰上限</span>
                <input type="number" v-model.number="character.cypherLimit" min="0" class="w-14 px-2 py-1 border-b border-black bg-transparent text-center font-typewriter text-xs focus:outline-none" placeholder="上限">
                <button @click="generateRandomCyphers" 
                        :disabled="character.cypherLimit <= 0"
                        :class="[
                          'text-xs px-2 py-1 rounded border font-typewriter transition-colors',
                          character.cypherLimit <= 0 
                            ? 'bg-gray-400 text-gray-600 cursor-not-allowed' 
                            : 'text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100'
                        ]">
                  隨機獲得
                </button>
                <button @click="addNewCypher" 
                        :disabled="character.cypherLimit > 0 && character.cyphers.length >= character.cypherLimit"
                        :class="[
                          'text-xs px-2 py-1 rounded border font-typewriter transition-colors',
                          character.cypherLimit > 0 && character.cyphers.length >= character.cypherLimit 
                            ? 'bg-gray-400 text-gray-600 cursor-not-allowed' 
                            : 'text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100'
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

        <!-- 能力部分 -->
        <div class="character-section">
          <div class="border-2 border-black bg-white p-4">
            <div class="flex items-center justify-between mb-4">
              <div class="text-sm font-bold uppercase tracking-wide">能力</div>
              <button @click="addNewAbility" 
                      class="text-xs px-2 py-1 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100">
                + 添加能力
              </button>
            </div>
            
            <!-- 能力列表 -->
            <div class="space-y-2 max-h-96 overflow-y-auto">
              <div v-for="(ability, index) in character.abilities" :key="index" class="border border-gray-300 rounded p-2 bg-gray-50">
                <div class="flex items-center justify-between mb-2">
                  <button @click="ability.collapsed = !ability.collapsed" 
                          class="flex items-center text-sm font-medium text-gray-700 hover:text-black flex-1 text-left">
                    <span class="mr-2">{{ ability.collapsed ? '▶' : '▼' }}</span>
                    <span>{{ getAbilityTitle(ability.content) || `能力 ${index + 1}` }}</span>
                  </button>
                  <div class="flex items-center space-x-1">
                    <button @click="copyAbilityToClipboard(ability)" class="text-blue-600 hover:text-blue-800 text-xs px-1 py-1 rounded border border-blue-300 hover:bg-blue-50" title="複製能力詳細內容">
                      📋
                    </button>
                    <button @click="removeAbility(index)" class="text-red-600 hover:text-red-800 text-xs">
                      ✕
                    </button>
                  </div>
                </div>
                
                <div v-if="!ability.collapsed">
                  <textarea v-model="ability.content" 
                           placeholder="描述能力的效果、成本和限制..." 
                           class="w-full h-24 text-xs bg-transparent border border-gray-300 rounded p-2 resize-none focus:outline-none focus:border-black font-typewriter"
                           rows="4"></textarea>
                </div>
                
                <!-- 摺疊時顯示預覽 -->
                <div v-else-if="ability.content" class="text-xs text-gray-600 italic truncate">
                  {{ getAbilityPreview(ability.content) }}
                </div>
              </div>
              
              <!-- 無能力時的提示 -->
              <div v-if="character.abilities.length === 0" class="text-center text-gray-500 text-sm py-4">
                尚未添加任何能力
              </div>
            </div>
          </div>
        </div>
      </div>
    <!-- 缩放手柄 -->
    <div @mousedown="startResizeKeysModal" class="fixed" :style="{ left: (keysModalWidth - 4) + 'px', top: 0, width: '8px', height: '100vh', cursor: 'col-resize', zIndex: 41 }"></div>
  </div>
  </transition>

  <!-- 技能管理 Modal -->
  <div v-if="showSkillsModal" class="fixed inset-0 z-50 flex items-center justify-center bg-black/60 p-4">
    <div class="bg-white border-2 border-black w-full max-w-6xl max-h-[90vh] overflow-hidden flex flex-col">
      <div class="flex items-center justify-between p-4 border-b border-black">
        <div class="text-sm font-bold uppercase tracking-wide">技能管理</div>
        <button @click="showSkillsModal = false" class="text-xs px-3 py-2 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100">關閉</button>
      </div>
      <div class="p-4 overflow-y-auto">
        <div class="flex items-center justify-between mb-4">
          <div class="text-xs font-bold">技能管理</div>
        </div>
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-4">
          <div v-for="category in skillCategories" :key="category.id" class="border border-gray-200 rounded p-3">
            <div class="text-xs font-bold mb-2">{{ category.icon }} {{ category.label }}</div>
            <div class="space-y-2">
              <div v-for="skill in (visibleGroupedSkills[category.id] || [])" :key="skill.id" class="border-b border-gray-200 pb-3">
                <div class="text-xs font-medium mb-1">{{ skill && skill.name }}</div>
                <div v-if="skill && skill.note" class="text-[11px] text-gray-600 mb-2">{{ skill.note }}</div>
                <select
                  v-if="skill"
                  v-model="skill.level"
                  :disabled="skill.allowSpecialties"
                  :title="skill.allowSpecialties ? '此技能有專精項目，主技能定位已鎖定不可修改' : ''"
                  :class="[
                    'w-full text-xs border rounded px-2 py-1 font-typewriter focus:outline-none mb-2',
                    skill.allowSpecialties
                      ? 'border-gray-200 bg-gray-100 text-gray-500 cursor-not-allowed'
                      : 'border-gray-300 focus:border-black bg-gray-50'
                  ]">
                  <option v-for="level in getLevelOptionsForSkill(skill)" :key="level.value" :value="level.value">
                    {{ level.mod ? `${level.label} (${level.mod})` : level.label }}
                  </option>
                </select>
                <div v-if="skill && isCreditRatingSkill(skill)" class="mb-2 space-y-1">
                  <div class="text-[11px] text-gray-600">自訂顯示</div>
                  <input
                    v-model.trim="skill.creditDisplay"
                    type="text"
                    placeholder="例如：42% 或 中產階級"
                    class="w-full text-xs border border-gray-300 rounded px-2 py-1 font-typewriter focus:outline-none focus:border-black bg-white">
                </div>
                <div v-if="skill && skill.allowSpecialties" class="text-[11px] text-gray-500 -mt-1 mb-2">主技能定位已鎖定，請調整下方專精項目。</div>
                <div v-if="skill && skill.allowSpecialties" class="space-y-2">
                  <div class="text-[11px] text-gray-600">專精</div>
                  <div class="flex flex-wrap items-center gap-2">
                    <select
                      v-model="specialtyPickers[skill.id]"
                      @change="addSpecialtyFromOption(skill, specialtyPickers[skill.id]); specialtyPickers[skill.id] = ''"
                      class="text-xs border border-gray-300 rounded px-2 py-1 font-typewriter focus:outline-none focus:border-black bg-gray-50">
                      <option value="">新增專精...</option>
                      <option v-for="option in getAvailableSpecialtyOptions(skill)" :key="option" :value="option">
                        {{ option }}
                      </option>
                    </select>
                  </div>
                  <div v-for="(spec, specIndex) in (Array.isArray(skill.specialties) ? skill.specialties.filter(s => s && s.name !== undefined) : [])" :key="specIndex" class="flex flex-wrap items-center gap-2">
                    <input
                      v-model="spec.name"
                      :placeholder="skill.specialtyPlaceholder || '專精名稱'"
                      class="flex-1 min-w-[140px] text-xs bg-transparent border border-gray-300 rounded px-2 py-1 focus:outline-none focus:border-black font-typewriter">
                    <select v-model="spec.level" class="text-xs border border-gray-300 rounded px-2 py-1 font-typewriter focus:outline-none focus:border-black bg-gray-50">
                      <option v-for="level in skillLevelOptions" :key="level.value" :value="level.value">
                        {{ level.label }} ({{ level.mod }})
                      </option>
                    </select>
                    <button @click="removeSpecialty(skill, specIndex)" class="text-xs text-red-700 border border-red-300 rounded px-2 py-1 hover:bg-red-50">
                      移除
                    </button>
                  </div>
                </div>
              </div>
              <div v-if="(visibleGroupedSkills[category.id] || []).length === 0" class="text-xs text-gray-500">無技能</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- 技能成長對照 Modal -->
  <div v-if="showGrowthChartModal" class="fixed inset-0 z-50 flex items-center justify-center bg-black/60 p-4">
    <div class="bg-white border-2 border-black w-full max-w-md overflow-hidden flex flex-col">
      <div class="flex items-center justify-between p-4 border-b border-black">
        <div class="text-sm font-bold uppercase tracking-wide">技能成長對照表</div>
        <button @click="showGrowthChartModal = false" class="text-xs px-3 py-2 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100">關閉</button>
      </div>
      <div class="p-6 overflow-y-auto">
        <div class="space-y-3 font-typewriter text-sm">
          <div class="border border-gray-300 rounded p-3 bg-gray-50">
            <div class="font-bold text-center mb-4 text-base">位階成長目標值</div>
            <div class="space-y-2">
              <div class="flex justify-between">
                <span class="font-bold">外行</span>
                <span class="text-red-600 font-bold">≧ 12</span>
              </div>
              <div class="bg-white h-px"></div>
              <div class="flex justify-between">
                <span class="font-bold">新手</span>
                <span class="text-orange-600 font-bold">≧ 14</span>
              </div>
              <div class="bg-white h-px"></div>
              <div class="flex justify-between">
                <span class="font-bold">業餘</span>
                <span class="text-gray-600 font-bold">≧ 16</span>
              </div>
              <div class="bg-white h-px"></div>
              <div class="flex justify-between">
                <span class="font-bold">職業</span>
                <span class="text-green-600 font-bold">≧ 18</span>
              </div>
              <div class="bg-white h-px"></div>
              <div class="flex justify-between">
                <span class="font-bold">專家</span>
                <span class="text-blue-600 font-bold">≧ 19</span>
              </div>
              <div class="bg-white h-px"></div>
              <div class="flex justify-between">
                <span class="font-bold">大師</span>
                <span class="text-purple-600 font-bold">= 20</span>
              </div>
            </div>
          </div>
          <div class="text-xs text-gray-600 italic text-center mt-4">
            達到目標值後，該技能可進行位階晉升。
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, nextTick, computed } from 'vue'

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
  currentSanity: 99,
  maxSanity: 99,
  baseSanity: 99,
  cthulhuMythos: 0,
  insanityThreshold: 80,
  supernaturalStressMarks: Array(10).fill(false),
  equipment: '',
  attacks: Array(4).fill(''),
  skills: buildDefaultSkills(),
  cyphers: [],
  cypherLimit: 0,
  abilities: [],
  xp: 0,
  background: '',
  recoveryBonus: 0,
  advancementChecks: {
    perfection: false,
    effort: false,
    skill: false,
    ability: false,
    recovery: false
  },
  skillCategoryGrowth: {
    combat: false,
    social: false,
    investigation: false,
    technical: false,
    survival: false,
    knowledge: false
  }
})

// 複製提示狀態
const showCopyNotification = ref(false)
const copyNotificationText = ref('')

const showSkillsModal = ref(false)
const showGrowthChartModal = ref(false)
const showKeysModal = ref(false)
const keysModalWidth = ref(400)
const isResizingKeysModal = ref(false)
const resizeStartX = ref(0)
const resizeStartWidth = ref(0)
const specialtyPickers = ref({})
const activeTab = ref('character')

// 密鑰侧邊欄缩放功能
const startResizeKeysModal = (e) => {
  isResizingKeysModal.value = true
  resizeStartX.value = e.clientX
  resizeStartWidth.value = keysModalWidth.value
  document.addEventListener('mousemove', doResizeKeysModal)
  document.addEventListener('mouseup', stopResizeKeysModal)
}

const doResizeKeysModal = (e) => {
  if (!isResizingKeysModal.value) return
  const diff = e.clientX - resizeStartX.value
  const newWidth = Math.max(300, Math.min(800, resizeStartWidth.value + diff))
  keysModalWidth.value = newWidth
}

const stopResizeKeysModal = () => {
  isResizingKeysModal.value = false
  document.removeEventListener('mousemove', doResizeKeysModal)
  document.removeEventListener('mouseup', stopResizeKeysModal)
}

// 攻击文本同步
const attacksText = computed({
  get: () => character.value.attacks.filter(a => a).join('\n'),
  set: (value) => {
    const lines = value.split('\n')
    character.value.attacks = Array(4).fill('').map((_, i) => lines[i] || '')
  }
})

const syncAttacksFromText = (e) => {
  const lines = e.target.value.split('\n')
  character.value.attacks = Array(4).fill('').map((_, i) => lines[i] || '')
  saveCharacter()
}

const insanityThreshold = computed(() => {
  return character.value.insanityThreshold || 0
})

const resetInsanityThreshold = () => {
  character.value.insanityThreshold = Math.ceil(character.value.currentSanity * 4 / 5)
  saveCharacter()
}

const updateMaxSanity = () => {
  const mythos = Number(character.value.cthulhuMythos) || 0
  character.value.maxSanity = 99 - mythos
  if (character.value.maxSanity < 0) character.value.maxSanity = 0
  if (character.value.currentSanity > character.value.maxSanity) {
    character.value.currentSanity = character.value.maxSanity
  }
  saveCharacter()
}


const skillLevelOptions = [
  { value: 'outsider', label: '外行', mod: '-2' },
  { value: 'novice', label: '新手', mod: '-1' },
  { value: 'amateur', label: '業餘', mod: '0' },
  { value: 'pro', label: '職業', mod: '+1' },
  { value: 'expert', label: '專家', mod: '+2' },
  { value: 'master', label: '大師🎖️', mod: '+2' }
]

const creditRatingLevelOptions = [
  { value: 'outsider', label: '01–05%', mod: '' },
  { value: 'novice', label: '06–19%', mod: '' },
  { value: 'amateur', label: '20–49%', mod: '' },
  { value: 'pro', label: '50–74%', mod: '' },
  { value: 'expert', label: '75–89%', mod: '' },
  { value: 'master', label: '90%+', mod: '' }
]

const skillCategories = [
  { id: 'combat', label: '戰鬥', icon: '⚔️' },
  { id: 'social', label: '社交', icon: '💬' },
  { id: 'investigation', label: '調查', icon: '🔍' },
  { id: 'technical', label: '技術', icon: '🔧' },
  { id: 'survival', label: '生存', icon: '🏕️' },
  { id: 'knowledge', label: '知識', icon: '📚' }
]

function buildDefaultSkills () {
  return [
  // 戰鬥 (Combat)
  { id: 'combat', name: '戰鬥', category: 'combat', level: 'amateur', allowSpecialties: true, specialties: [], specialtyOptions: ['斧頭', '鬥毆', '鏈鋸', '連枷', '絞索', '斧', '劍', '鞭子'] },
  { id: 'firearms', name: '火器', category: 'combat', level: 'outsider', allowSpecialties: true, specialties: [], specialtyOptions: ['弓', '手槍', '重武器', '火焰發射器', '機關槍', '步槍／霰彈槍', '衝鋒槍'] },
  { id: 'dodge', name: '閃避', category: 'combat', level: 'outsider' },
  { id: 'throwing', name: '投擲', category: 'combat', level: 'amateur' },
  { id: 'first-aid', name: '急救', category: 'combat', level: 'amateur' },

  // 社交 (Social)
  { id: 'charm', name: '取悅', category: 'social', level: 'outsider' },
  { id: 'fast-talk', name: '話術', category: 'social', level: 'outsider' },
  { id: 'intimidate', name: '威脅', category: 'social', level: 'outsider' },
  { id: 'persuade', name: '說服', category: 'social', level: 'outsider' },  
  { id: 'art', name: '藝術／工藝', category: 'social', level: 'outsider', allowSpecialties: true, specialties: [], specialtyOptions: ['表演', '美術', '偽造文書', '攝影'] },
  { id: 'credit', name: '信用評級', category: 'social', level: 'outsider', creditDisplay: '' },

  // 調查 (Investigation)
  { id: 'library', name: '圖書館使用', category: 'investigation', level: 'amateur' },
  { id: 'listen', name: '聆聽', category: 'investigation', level: 'amateur' },
  { id: 'observe', name: '偵查', category: 'investigation', level: 'amateur' },
  { id: 'navigate', name: '導航', category: 'investigation', level: 'outsider' },
  { id: 'track', name: '追蹤', category: 'investigation', level: 'outsider' },
  { id: 'appraise', name: '鑑定', category: 'investigation', level: 'outsider' },
  { id: 'psychology', name: '心理學', category: 'investigation', level: 'outsider' },
  { id: 'psychoanalysis', name: '精神分析', category: 'investigation', level: 'outsider' },

  // 技術 (Technical)
  { id: 'drive', name: '駕駛', category: 'technical', level: 'outsider', allowSpecialties: true, specialties: [], specialtyOptions: ['民用螺旋飛機', '熱氣球', '小艇', '輪船', '船舶'], specialtyPlaceholder: '駕駛類型' },
  { id: 'car', name: '汽車駕駛', category: 'technical', level: 'novice' },
  { id: 'ride', name: '騎術', category: 'technical', level: 'outsider' },
  { id: 'electrical-repair', name: '電器維修', category: 'technical', level: 'outsider' },
  { id: 'mechanical-repair', name: '機械維修', category: 'technical', level: 'outsider' },
  { id: 'stealth', name: '潛匿', category: 'technical', level: 'amateur' },
  { id: 'disguise', name: '喬裝', category: 'technical', level: 'outsider' },
  { id: 'sleight-of-hand', name: '巧手', category: 'technical', level: 'outsider' },
  { id: 'locksmith', name: '鎖匠', category: 'technical', level: 'outsider' },
  { id: 'heavy-machinery', name: '操作重機', category: 'technical', level: 'outsider' },

  // 生存 (Survival)
  { id: 'survival', name: '生存', category: 'survival', level: 'outsider', allowSpecialties: true, specialties: [], specialtyOptions: ['高原', '沙漠', '叢林', '高山', '極地'], specialtyPlaceholder: '生存環境' },
  { id: 'climb', name: '攀爬', category: 'survival', level: 'novice' },
  { id: 'jump', name: '跳躍', category: 'survival', level: 'amateur' },
  { id: 'swim', name: '游泳', category: 'survival', level: 'amateur' },
  { id: 'nature', name: '自然世界', category: 'survival', level: 'outsider' },
  { id: 'language', name: '語言', category: 'survival', level: 'outsider', allowSpecialties: true, specialties: [], specialtyOptions: ['母語', '外語（自填）'], specialtyPlaceholder: '語言名稱' },

  // 知識 (Knowledge)
  { id: 'history', name: '歷史', category: 'knowledge', level: 'outsider' },
  { id: 'archaeology', name: '考古學', category: 'knowledge', level: 'outsider' },
  { id: 'anthropology', name: '人類學', category: 'knowledge', level: 'outsider' },
  { id: 'law', name: '法律', category: 'knowledge', level: 'outsider' },
  { id: 'medicine', name: '醫藥', category: 'knowledge', level: 'outsider' },
  { id: 'science', name: '科學', category: 'knowledge', level: 'outsider', allowSpecialties: true, specialties: [], specialtyOptions: ['天文學', '生物學', '植物學', '化學', '密碼學', '工程學', '司法科學', '地質學', '數學', '氣象學', '藥學', '物理學', '動物學'] },
  { id: 'occult', name: '神祕學', category: 'knowledge', level: 'outsider' },  
  { id: 'accounting', name: '會計', category: 'knowledge', level: 'outsider' },  
  
  ]
}

const normalizeSpecialties = (value, fallbackLevel) => {
  if (!value) return []
  if (Array.isArray(value)) {
    return value
      .map(item => {
        if (!item) return null
        if (typeof item === 'string') return { name: item.trim(), level: fallbackLevel }
        if (typeof item === 'object') {
          return { name: item.name || '', level: item.level || fallbackLevel }
        }
        return null
      })
      .filter(item => item && item.name)
  }
  if (typeof value === 'string') {
    return value
      .split(/[、,]/)
      .map(text => text.trim())
      .filter(Boolean)
      .map(name => ({ name, level: fallbackLevel }))
  }
  return []
}

const normalizeSkills = (rawSkills) => {
  const defaults = buildDefaultSkills().map(skill => ({ ...skill }))
  if (!Array.isArray(rawSkills)) return defaults

  const byId = new Map(defaults.map(skill => [skill.id, skill]))
  const byName = new Map(defaults.map(skill => [skill.name, skill]))

  rawSkills.forEach((raw) => {
    if (!raw) return
    if (typeof raw === 'string') {
      const target = byName.get(raw)
      if (target) {
        target.level = target.level || 'amateur'
      }
      return
    }
    if (typeof raw !== 'object') return

    const name = raw.name || raw.text
    const target = (raw.id && byId.get(raw.id)) || (name && byName.get(name))
    if (!target) return

    if (raw.level) target.level = raw.level
    if (raw.creditDisplay !== undefined) {
      target.creditDisplay = String(raw.creditDisplay || '').trim()
    }
    if (raw.specialties !== undefined) {
      target.specialties = normalizeSpecialties(raw.specialties, target.level)
    }
    if (raw.specialtyOptions && Array.isArray(raw.specialtyOptions)) {
      target.specialtyOptions = raw.specialtyOptions
    }
    if (raw.note) target.note = raw.note
    if (raw.allowSpecialties !== undefined) target.allowSpecialties = raw.allowSpecialties
    if (raw.specialtyPlaceholder) target.specialtyPlaceholder = raw.specialtyPlaceholder
  })

  defaults.forEach((skill) => {
    if (skill.allowSpecialties && !Array.isArray(skill.specialties)) {
      skill.specialties = []
    }
    if (skill.allowSpecialties && !Array.isArray(skill.specialtyOptions)) {
      skill.specialtyOptions = []
    }
  })

  return defaults
}

const groupedSkills = computed(() => {
  const groups = { combat: [], social: [], investigation: [], technical: [], survival: [], knowledge: [] }
  character.value.skills.forEach(skill => {
    if (!skill || !skill.name) return
    if (groups[skill.category]) {
      groups[skill.category].push(skill)
    }
  })
  return groups
})

const getVisibleSpecialties = (skill) => {
  const specialties = Array.isArray(skill?.specialties)
    ? skill.specialties.filter(spec => spec && spec.name)
    : []
  return specialties
}

const isSkillVisible = (skill) => {
  return true
}

const visibleGroupedSkills = computed(() => {
  const groups = { combat: [], social: [], investigation: [], technical: [], survival: [], knowledge: [] }
  Object.keys(groupedSkills.value).forEach((key) => {
    groups[key] = groupedSkills.value[key].filter(skill => isSkillVisible(skill))
  })
  return groups
})

const isCreditRatingSkill = (skill) => {
  return skill?.id === 'credit' || skill?.name === '信用評級'
}

const getLevelOptionsForSkill = (skill) => {
  return isCreditRatingSkill(skill) ? creditRatingLevelOptions : skillLevelOptions
}

const getSkillLevelLabel = (level) => {
  const found = skillLevelOptions.find(option => option.value === level)
  return found ? `${found.label} ${found.mod}` : level
}

const getSkillLevelLabelForSkill = (skill, level) => {
  if (isCreditRatingSkill(skill)) {
    const custom = String(skill?.creditDisplay || '').trim()
    if (custom) return custom
  }
  const options = getLevelOptionsForSkill(skill)
  const found = options.find(option => option.value === level)
  if (!found) return level
  return found.mod ? `${found.label} ${found.mod}` : found.label
}

const getSkillDisplayLevel = (skill) => {
  return getSkillLevelLabelForSkill(skill, skill.level)
}

const getSkillTooltip = (skill) => {
  const specs = getVisibleSpecialties(skill)
  if (!specs.length) return ''
  return specs.map(spec => `${spec.name} ${getSkillLevelLabel(spec.level)}`).join('\n')
}

const getSkillLevelBadgeClass = (level) => {
  switch (level) {
    case 'outsider':
      return 'text-red-700 border-red-300 bg-red-50'
    case 'novice':
      return 'text-orange-700 border-orange-300 bg-orange-50'
    case 'amateur':
      return 'text-gray-600 border-gray-300 bg-gray-50'
    case 'pro':
      return 'text-green-700 border-green-300 bg-green-50'
    case 'expert':
      return 'text-blue-700 border-blue-300 bg-blue-50'
    case 'master':
      return 'text-purple-700 border-purple-300 bg-purple-50'
    default:
      return 'text-gray-600 border-gray-300 bg-gray-50'
  }
}

const getSkillBadgeClass = (skill) => {
  if (isCreditRatingSkill(skill)) {
    return 'text-indigo-700 border-indigo-300 bg-indigo-50'
  }
  return getSkillLevelBadgeClass(skill.level)
}

const addSpecialty = (skill) => {
  if (!skill.allowSpecialties) return
  if (!Array.isArray(skill.specialties)) skill.specialties = []
  const defaultLevel = skill.level || 'outsider'
  skill.specialties.push({ name: '', level: defaultLevel })
}

const hasSpecialtyName = (skill, name) => {
  if (!Array.isArray(skill.specialties)) return false
  return skill.specialties.some(spec => spec && spec.name === name)
}

const getAvailableSpecialtyOptions = (skill) => {
  const options = Array.isArray(skill.specialtyOptions) ? skill.specialtyOptions : []
  const hasEmpty = Array.isArray(skill.specialties)
    ? skill.specialties.some(spec => spec && !spec.name)
    : false
  return options.filter(option => !hasSpecialtyName(skill, option))
    .filter(option => !(option === '外語（自填）' && hasEmpty))
}

const addSpecialtyFromOption = (skill, option) => {
  if (!skill.allowSpecialties) return
  if (!option) return
  const levelOverride = option === '母語' ? 'amateur' : (skill.level || 'outsider')
  const name = option === '外語（自填）' ? '' : option
  if (name && hasSpecialtyName(skill, name)) return
  if (!Array.isArray(skill.specialties)) skill.specialties = []
  if (!name && skill.specialties.some(spec => spec && !spec.name)) return
  skill.specialties.push({ name, level: levelOverride })
}

const removeSpecialty = (skill, index) => {
  if (!Array.isArray(skill.specialties)) return
  skill.specialties.splice(index, 1)
}

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

// 能力管理
const addNewAbility = () => {
  character.value.abilities.push({
    content: '',
    collapsed: false
  })
}

const removeAbility = (index) => {
  character.value.abilities.splice(index, 1)
}

const copyAbilityToClipboard = async (ability) => {
  const abilityText = `${ability.content}`
  
  try {
    await navigator.clipboard.writeText(abilityText)
    showCopySuccess('能力內容已複製！')
  } catch (error) {
    console.error('複製失敗:', error)
    // 備用方案：創建臨時文字區域
    const textArea = document.createElement('textarea')
    textArea.value = abilityText
    document.body.appendChild(textArea)
    textArea.select()
    try {
      document.execCommand('copy')
      showCopySuccess('能力內容已複製！')
    } catch (fallbackError) {
      alert('複製失敗，請手動複製內容')
    }
    document.body.removeChild(textArea)
  }
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

// 解析能力標題
const getAbilityTitle = (content) => {
  if (!content) return null
  
  // 匹配冒號前的所有內容作為標題
  const match = content.match(/^([^：:]+)[：:]/)
  if (match) {
    return match[1].trim()
  }
  
  // 如果沒有匹配到冒號，嘗試提取第一行作為標題
  const firstLine = content.split('\n')[0].trim()
  if (firstLine.length > 0 && firstLine.length <= 50) {
    return firstLine
  }
  
  return null
}

// 獲取能力預覽
const getAbilityPreview = (content) => {
  if (!content) return ''
  
  // 如果有標題格式，顯示冒號後的內容
  const match = content.match(/^[^：:]*[：:]\s*(.*)/)
  if (match) {
    const preview = match[1].trim()
    return preview.length > 50 ? preview.substring(0, 50) + '...' : preview
  }
  
  // 否則顯示前50個字符
  return content.length > 50 ? content.substring(0, 50) + '...' : content
}

// 隱藏原生 title 工具提示
onMounted(() => {
  // 頁面載入時自動載入儲存的資料
  loadFromLocalStorage()
  
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

// 監聽角色資料變化，自動儲存到 localStorage
watch(character, () => {
  nextTick(() => {
    saveToLocalStorage()
  })
}, { deep: true })

const saveCharacter = () => {
  console.log('角色資料:', character.value)
  // alert('角色已儲存！（目前只儲存在瀏覽器 console）')
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
      damageTrack: 'hale',
      currentSanity: 99,
      maxSanity: 99,
      baseSanity: 99,
      cthulhuMythos: 0,
      insanityThreshold: 80,
      supernaturalStressMarks: Array(10).fill(false),
      equipment: '',
      attacks: Array(4).fill(''),
      skills: buildDefaultSkills(),
      cyphers: [],
      cypherLimit: 0,
      abilities: [],
      xp: 0,
      background: '',
      recoveryBonus: 0,
      advancementChecks: {
        perfection: false,
        effort: false,
        skill: false,
        ability: false,
        recovery: false
      },
      skillCategoryGrowth: {
        combat: false,
        social: false,
        investigation: false,
        technical: false,
        survival: false,
        knowledge: false
      }
    }
    showCopySuccess('所有資料已清空，可以建立新角色了！')
  }
}

// 角色資料管理功能
const saveToLocalStorage = () => {
  try {
    const characterData = JSON.stringify(character.value)
    localStorage.setItem('coc-csr-character', characterData)
  } catch (error) {
    console.error('自動儲存失敗:', error)
  }
}

const loadFromLocalStorage = () => {
  try {
    const savedData = localStorage.getItem('coc-csr-character')
    if (savedData) {
      let parsedData
      try {
        parsedData = JSON.parse(savedData)
      } catch (jsonError) {
        console.error('JSON 解析失敗:', jsonError)
        // 如果 JSON 解析失敗，清除損壞的數據
        localStorage.removeItem('coc-csr-character')
        console.warn('已清除損壞的 localStorage 數據')
        return
      }
      
      if (parsedData && Object.prototype.hasOwnProperty.call(parsedData, 'sanityTrack')) {
        delete parsedData.sanityTrack
      }
      
      const processedSkills = normalizeSkills(parsedData.skills)
      
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
        damageTrack: 'hale',
        currentSanity: 99,
        maxSanity: 99,
        baseSanity: 99,
        cthulhuMythos: 0,
        insanityThreshold: 80,
        supernaturalStressMarks: Array(10).fill(false),
        equipment: '',
        attacks: Array(4).fill(''),
        skills: processedSkills,
        cyphers: [],
        cypherLimit: 0,
        abilities: [],
        xp: 0,
        background: '',
        recoveryBonus: 0,
        advancementChecks: {
          perfection: false,
          effort: false,
          skill: false,
          ability: false,
          recovery: false
        },
        skillCategoryGrowth: {
          combat: false,
          social: false,
          investigation: false,
          technical: false,
          survival: false,
          knowledge: false
        },
        ...parsedData, // 覆蓋已儲存的資料
        skills: processedSkills
      }
      
      // 確保新字段有預設值
      if (character.value.currentSanity === undefined || character.value.currentSanity === null) {
        character.value.currentSanity = 99
      }
      if (character.value.baseSanity === undefined || character.value.baseSanity === null) {
        character.value.baseSanity = 99
      }
      if (character.value.cthulhuMythos === undefined || character.value.cthulhuMythos === null) {
        character.value.cthulhuMythos = 0
      }
      
      // 重新計算 maxSanity
      const mythos = Number(character.value.cthulhuMythos) || 0
      character.value.maxSanity = 99 - mythos
      if (character.value.maxSanity < 0) character.value.maxSanity = 0
      
      // 確保 currentSanity 不超過 maxSanity
      if (character.value.currentSanity > character.value.maxSanity) {
        character.value.currentSanity = character.value.maxSanity
      }
      
      // 重新計算瘋狂閾值
      if (character.value.insanityThreshold === undefined || character.value.insanityThreshold === null) {
        character.value.insanityThreshold = Math.ceil(character.value.currentSanity * 4 / 5)
      }
      
      // 確保陣列長度正確
      if (!character.value.attacks || character.value.attacks.length !== 4) {
        character.value.attacks = Array(4).fill('').map((_, i) => character.value.attacks?.[i] || '')
      }
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
    link.download = `${character.value.name || 'coc-character'}-${new Date().toISOString().split('T')[0]}.json`
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
      if (importedData && Object.prototype.hasOwnProperty.call(importedData, 'sanityTrack')) {
        delete importedData.sanityTrack
      }
      if (confirm('確定要匯入這個角色資料嗎？這將覆蓋目前的資料。')) {
        const processedSkills = normalizeSkills(importedData.skills)
        
        character.value = {
          ...character.value,
          ...importedData,
          skills: processedSkills
        }
        // 確保陣列長度正確
        if (!character.value.attacks || character.value.attacks.length !== 4) {
          character.value.attacks = Array(4).fill('').map((_, i) => importedData.attacks?.[i] || '')
        }
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
    // 過濾非空的攻擊
    const nonEmptyAttacks = character.value.attacks.filter(attack => attack.trim())
    
    let textContent = `THE MAGNUS ARCHIVES - 角色卡
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

【狀態軌】
傷害軌：${getTrackDisplayName(character.value.damageTrack, 'damage')}
理智值：${character.value.currentSanity}/${character.value.maxSanity}　瘋狂閾值：${character.value.insanityThreshold}
克蘇魯神話：${character.value.cthulhuMythos}

【攻擊】
${nonEmptyAttacks.length > 0 ? 
  nonEmptyAttacks.map((attack, index) => `${index + 1}. ${attack}`).join('\n') : 
  '(無攻擊記錄)'}

【技能】
${character.value.skills.map((skill, index) => {
  const levelText = getSkillDisplayLevel(skill)
  const noteText = skill.note ? `（${skill.note}）` : ''
  const lines = [`${index + 1}. ${skill.name} ${levelText}${noteText}`]
  if (Array.isArray(skill.specialties) && skill.specialties.length > 0) {
    skill.specialties.forEach((spec) => {
      const specLevel = getSkillLevelLabel(spec.level)
      lines.push(`   - ${spec.name} ${specLevel}`)
    })
  }
  return lines.join('\n')
}).join('\n')}

【密鑰】(上限：${character.value.cypherLimit})
${character.value.cyphers.length > 0 ? 
  character.value.cyphers.map((cypher, index) => 
    `${index + 1}. ${cypher.content.replace(/\n/g, '\n   ')}`
  ).join('\n\n') : '(無密鑰)'}

【能力】
${character.value.abilities.length > 0 ? 
  character.value.abilities.map((ability, index) => 
    `${index + 1}. ${ability.content.replace(/\n/g, '\n   ')}`
  ).join('\n\n') : '(無能力)'}

【裝備】
${character.value.equipment || '(無裝備記錄)'}

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
  const statusText = `${character.value.name || '未命名角色'}

【數值】
位階：${character.value.tier}　努力：${character.value.effort}　XP：${character.value.xp}

氣力　池：${character.value.might.pool}　節省值：${character.value.might.edge}　目前：${character.value.might.current}
速度　池：${character.value.speed.pool}　節省值：${character.value.speed.edge}　目前：${character.value.speed.current}
智力　池：${character.value.intellect.pool}　節省值：${character.value.intellect.edge}　目前：${character.value.intellect.current}

【恢復骰】1d6+${character.value.recoveryBonus}
動作：${character.value.recoveryRolls.action ? '✓' : '○'}　10分鐘：${character.value.recoveryRolls.tenMin ? '✓' : '○'}　1小時：${character.value.recoveryRolls.oneHour ? '✓' : '○'}　10小時：${character.value.recoveryRolls.tenHours ? '✓' : '○'}

【狀態軌】
傷害軌：${getTrackDisplayName(character.value.damageTrack, 'damage')}
理智值：${character.value.currentSanity}/${character.value.maxSanity}　瘋狂閾值：${character.value.insanityThreshold}
克蘇魯神話：${character.value.cthulhuMythos}　超自然壓力：${character.value.supernaturalStressMarks.filter(Boolean).length}/10`

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

// 輔助函數：獲取軌道狀態的顯示名稱
const getTrackDisplayName = (value, type) => {
  const tracks = {
    damage: {
      'hale': '強健',
      'hurt': '輕傷',
      'impaired': '帶傷',
      'debilitated': '重創',
      'dead': '死亡'
    },
  }
  return tracks[type][value] || value
}
</script>

<style scoped>
/* 克蘇魯朋友主題配色 */
:root {
  --cthulhu-parchment: #D4C5B0;  /* 古旧纸张色 */
  --cthulhu-brown: #6B4423;      /* 深棕色 */
  --cthulhu-light-brown: #8B5A3C; /* 棕色 */
  --cthulhu-dark: #2C1810;       /* 深褐色 */
  --cthulhu-red: #8B1C1C;        /* 血色红 */
}

/* 全局背景 */
:deep(.min-h-screen) {
  background-color: #8B7765 !important;
  background-image: 
    repeating-linear-gradient(
      0deg,
      rgba(0, 0, 0, 0.03) 0px,
      transparent 1px,
      transparent 2px,
      rgba(0, 0, 0, 0.03) 3px
    ),
    radial-gradient(
      ellipse at 20% 50%,
      rgba(40, 20, 10, 0.15) 0%,
      transparent 50%
    ),
    radial-gradient(
      ellipse at 80% 80%,
      rgba(40, 20, 10, 0.1) 0%,
      transparent 50%
    );
}

/* 按鈕樣式 - 克蘇魯風格 */
:deep(button) {
  font-family: 'Special Elite', 'Courier New', monospace;
  font-weight: bold;
  letter-spacing: 0.5px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
  transition: all 0.2s ease;
}

:deep(button:hover) {
  transform: translateY(-2px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.4);
}

:deep(button:active) {
  transform: translateY(0);
}

/* 標籤頁按鈕 */
:deep(.bg-green-700) {
  background-color: #2196F3 !important;
  border-color: #1565C0 !important;
  font-weight: bold;
  letter-spacing: 1px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.4) !important;
}

:deep(.bg-green-700:hover) {
  background-color: #42A5F5 !important;
  transform: translateY(-1px);
}

/* 按鈕統一使用淺藍邊框風格，與管理按鈕一致 */

/* 字體樣式 */
:deep(label),
:deep(.text-xs),
:deep(.text-sm),
:deep(.text-base) {
  font-family: 'Special Elite', 'Courier New', monospace;
  color: #1a0f08;
  text-shadow: 1px 1px 1px rgba(255, 255, 255, 0.4);
  font-weight: 600;
}

/* 輸入框樣式 */
:deep(input[type="text"]),
:deep(input[type="number"]),
:deep(textarea),
:deep(select) {
  font-family: 'Special Elite', 'Courier New', monospace;
  background-color: #F5EDE0 !important;
  color: #1a0f08 !important;
  border-color: #8B5A3C !important;
  font-weight: 600;
  letter-spacing: 0.3px;
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.1);
}

:deep(input[type="text"]:focus),
:deep(input[type="number"]:focus),
:deep(textarea:focus),
:deep(select:focus) {
  background-color: #FFFAF3 !important;
  border-color: #A0684C !important;
  box-shadow: 
    inset 0 2px 4px rgba(0, 0, 0, 0.1),
    0 0 8px rgba(160, 104, 76, 0.4);
  outline: none;
}

/* 邊框樣式 */
:deep(.border-black) {
  border-color: #6B4423 !important;
  box-shadow: 
    inset 0 0 15px rgba(0, 0, 0, 0.1),
    0 3px 8px rgba(0, 0, 0, 0.2);
}

:deep(.border-b,
      .border-r) {
  border-color: #6B4423 !important;
}

/* 紙質背景 */
:deep(.bg-white) {
  background-color: #E8DCC8 !important;
  background-image: 
    repeating-linear-gradient(
      0deg,
      rgba(0, 0, 0, 0.02) 0px,
      transparent 1px,
      transparent 2px,
      rgba(0, 0, 0, 0.02) 3px
    ),
    radial-gradient(
      ellipse at 15% 30%,
      rgba(40, 20, 10, 0.08) 0%,
      transparent 40%
    ),
    radial-gradient(
      ellipse at 85% 75%,
      rgba(40, 20, 10, 0.06) 0%,
      transparent 40%
    );
}

/* 灰色背景區塊 */
:deep(.bg-gray-50) {
  background-color: #F0E6D2 !important;
  border-color: #C8B8A0 !important;
}

:deep(.bg-gray-100) {
  background-color: #E8DCC8 !important;
}

/* Modal 樣式 */
:deep(.fixed.inset-0) {
  background-color: rgba(20, 10, 5, 0.8) !important;
}

:deep(.border-2.border-black) {
  border-color: #6B4423 !important;
  background-color: #DCC8B0 !important;
}

/* 複製成功提示 */
:deep(.bg-green-600) {
  background-color: #8B1C1C !important;
}

/* 文字顏色 */
:deep(.text-gray-700) {
  color: #1a0f08 !important;
}

:deep(.text-gray-600) {
  color: #3C2815 !important;
}

:deep(.text-gray-500) {
  color: #5C4830 !important;
}

/* Character Section */
.character-section {
  margin-bottom: 1rem;
}

.character-section .border-black {
  border-color: #6B4423 !important;
  box-shadow: 
    inset 0 0 15px rgba(0, 0, 0, 0.1),
    0 3px 8px rgba(0, 0, 0, 0.2);
}

/* 選擇框樣式 */
:deep(.border-gray-300) {
  border-color: #B8A890 !important;
  background-color: #E8DCC8 !important;
}

/* 紅色警告樣式 */
:deep(.bg-red-50) {
  background-color: #F0E6D2 !important;
}

:deep(.bg-red-800) {
  background-color: #8B1C1C !important;
}

:deep(.border-red-300) {
  border-color: #CD7F7F !important;
}

:deep(.text-red-600) {
  color: #8B1C1C !important;
}

:deep(.text-red-700) {
  color: #6B1414 !important;
}

/* 藍色樣式 */
:deep(.bg-blue-600) {
  background-color: #4A4E71 !important;
}

:deep(.bg-blue-50) {
  background-color: #E8E8F0 !important;
}

/* 過渡動畫 */
:deep(.transition-all) {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

:deep(.transition-colors) {
  transition: background-color 0.2s ease, border-color 0.2s ease, color 0.2s ease;
}

/* 陰影效果 */
:deep(.shadow-lg) {
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
}

/* 複選框樣式 */
:deep(input[type="checkbox"]) {
  accent-color: #8B5A3C;
}

/* Hover 效果 */
.track-item:hover {
  background-color: rgba(139, 90, 60, 0.1);
  transition: all 0.2s ease;
}

/* 工具提示 */
[data-tooltip]:hover::before {
  content: attr(data-tooltip);
  position: absolute;
  bottom: calc(100% + 10px);
  left: 50%;
  transform: translateX(-50%);
  background: #2C1810;
  color: #E8DCC8;
  padding: 8px 12px;
  border-radius: 4px;
  z-index: 9999;
  font-size: 11px;
  font-weight: normal;
  border: 1px solid #6B4423;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
  white-space: nowrap;
  font-family: 'Special Elite', 'Courier New', monospace;
  pointer-events: none;
}

[data-tooltip]:hover::after {
  content: '';
  position: absolute;
  bottom: calc(100% + 4px);
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 0;
  border-left: 6px solid transparent;
  border-right: 6px solid transparent;
  border-top: 6px solid #2C1810;
  z-index: 9998;
  pointer-events: none;
}

/* 動畫 */
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

@keyframes slideRight {
  from {
    transform: translateX(-100%);
  }
  to {
    transform: translateX(0);
  }
}

@keyframes fade {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.slide-right-enter-active,
.slide-right-leave-active {
  transition: transform 0.3s ease;
}

.slide-right-enter-from,
.slide-right-leave-to {
  transform: translateX(-100%);
}
</style>
