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
            @click="activeTab = 'character'"
            :class="[
              'px-3 py-2 rounded border text-xs font-typewriter transition-colors',
              activeTab === 'character'
                ? 'bg-green-700 text-white border-green-800'
                : 'bg-white text-gray-700 border-gray-300 hover:bg-gray-50'
            ]"
          >
            角色+技能
          </button>
          <button
            @click="activeTab = 'skills'"
            :class="[
              'px-3 py-2 rounded border text-xs font-typewriter transition-colors',
              activeTab === 'skills'
                ? 'bg-green-700 text-white border-green-800'
                : 'bg-white text-gray-700 border-gray-300 hover:bg-gray-50'
            ]"
            disabled
          >
            （停用）
          </button>
          <button
            @click="activeTab = 'keys'"
            :class="[
              'px-3 py-2 rounded border text-xs font-typewriter transition-colors',
              activeTab === 'keys'
                ? 'bg-green-700 text-white border-green-800'
                : 'bg-white text-gray-700 border-gray-300 hover:bg-gray-50'
            ]"
          >
            密鑰&能力
          </button>
        </div>

        <!-- 角色頁（含技能） -->
        <div v-show="activeTab === 'character'">
          <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
            <div class="space-y-6">
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

              <!-- 攻擊 -->
              <div class="character-section">
                <div class="border-2 border-black bg-white p-4">
                  <div class="text-sm font-bold uppercase tracking-wide mb-4">攻擊</div>
                  <div v-for="n in 4" :key="n" class="flex items-center border-b border-gray-300 pb-1">
                    <input type="text" v-model="character.attacks[n-1]" class="flex-1 bg-transparent font-typewriter text-sm focus:outline-none mr-2">
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
                          <span class="font-bold text-red-800" title="只能移動鄰近距離（通常是爬行）；如果速度池為0則無法移動">重創</span>
                        </label>
                        <label class="flex items-center track-item p-1">
                          <input type="radio" v-model="character.damageTrack" value="dead" class="mr-1 scale-75">
                          <span class="font-bold text-black">死亡</span>
                        </label>
                      </div>
                      
                      <!-- 複製狀態值按鈕 -->
                      <div class="mt-3 pt-2 border-t border-gray-300">
                        <button @click="copyStatusToClipboard" 
                                class="w-full text-xs px-2 py-1 bg-blue-600 text-white hover:bg-blue-700 rounded font-typewriter">
                          📋 複製狀態值
                        </button>
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

            <div class="space-y-6">
              <!-- 技能 -->
              <div class="character-section mb-6">
                <div class="border-2 border-black bg-white p-4">
                  <div class="flex items-center justify-between mb-4">
                    <div class="text-sm font-bold uppercase tracking-wide">技能</div>
                    <div class="flex items-center space-x-2">
                      <label class="flex items-center text-xs">
                        <input type="checkbox" v-model="hideOutsiderSkills" class="mr-1 scale-90">
                        <span>隱藏外行</span>
                      </label>
                      <button @click="showSkillsModal = true" 
                              class="text-xs px-3 py-2 rounded border font-typewriter transition-colors text-blue-600 border-blue-300 bg-blue-50 hover:bg-blue-100">
                        管理
                      </button>
                    </div>
                  </div>
                  <div class="text-xs text-gray-700 mb-3 leading-relaxed">
                    <div>外行🤡：額外努力 1 級</div>
                    <div>大師🎖️：免費努力 1 級</div>
                  </div>
                  <div class="grid grid-cols-1 gap-3">
                    <div v-for="category in skillCategories" :key="category.id" class="border border-gray-200 rounded p-2 bg-gray-50">
                      <div class="text-xs font-bold mb-2">{{ category.icon }} {{ category.label }}</div>
                      <div class="space-y-1">
                        <div v-for="skill in (visibleGroupedSkills[category.id] || [])" :key="skill.id" class="text-xs border-b border-gray-200 pb-2">
                          <div class="flex items-start justify-between">
                            <div class="pr-2">
                              <div class="font-medium">{{ skill && skill.name }}</div>
                              <div v-if="skill && skill.note" class="text-gray-600">{{ skill.note }}</div>
                            </div>
                            <span v-if="skill && getSkillDisplayLevel(skill)" :class="getSkillLevelBadgeClass(skill.level)" class="px-2 py-0.5 rounded border text-[10px] whitespace-nowrap">
                              {{ getSkillDisplayLevel(skill) }}
                            </span>
                          </div>
                          <div v-if="skill && getVisibleSpecialties(skill).length" class="mt-1 space-y-1">
                            <div v-for="(spec, specIndex) in getVisibleSpecialties(skill)" :key="specIndex" class="flex items-start justify-between text-[11px]">
                              <div class="text-gray-700">- {{ spec && spec.name }}</div>
                              <span v-if="spec" :class="getSkillLevelBadgeClass(spec.level)" class="px-2 py-0.5 rounded border text-[10px] whitespace-nowrap">
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

        <div v-show="activeTab === 'keys'" class="space-y-6">
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

          <!-- 能力 -->
          <div class="character-section">
            <div class="border-2 border-black bg-white p-4 flex flex-col">
              <div class="flex items-center justify-between mb-4 flex-shrink-0">
                <div class="text-center text-sm font-bold uppercase tracking-wide">能力</div>
                <button @click="addNewAbility" 
                        class="text-xs px-2 py-1 bg-green-700 text-white hover:bg-green-800 rounded font-typewriter">
                  + 添加能力
                </button>
              </div>
              
              <!-- 能力列表 -->
              <div class="space-y-2 flex-1 overflow-y-auto min-h-0">
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
      </div>
    </div>
  </div>

  <!-- 技能管理 Modal -->
  <div v-if="showSkillsModal" class="fixed inset-0 z-50 flex items-center justify-center bg-black/60 p-4">
    <div class="bg-white border-2 border-black w-full max-w-6xl max-h-[90vh] overflow-hidden flex flex-col">
      <div class="flex items-center justify-between p-4 border-b border-black">
        <div class="text-sm font-bold uppercase tracking-wide">技能管理</div>
        <button @click="showSkillsModal = false" class="text-xs px-3 py-2 rounded border font-typewriter text-red-700 border-red-300 bg-red-50 hover:bg-red-100">關閉</button>
      </div>
      <div class="p-4 overflow-y-auto">
        <div class="flex items-center justify-between mb-4">
          <div class="text-xs text-gray-700 leading-relaxed">
            <div>外行🤡：額外努力 1 級</div>
            <div>大師🎖️：免費努力 1 級</div>
          </div>
          <label class="flex items-center text-xs">
            <input type="checkbox" v-model="hideOutsiderSkills" class="mr-1 scale-90">
            <span>隱藏外行</span>
          </label>
        </div>
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-4">
          <div v-for="category in skillCategories" :key="category.id" class="border border-gray-200 rounded p-3">
            <div class="text-xs font-bold mb-2">{{ category.icon }} {{ category.label }}</div>
            <div class="space-y-2">
              <div v-for="skill in (visibleGroupedSkills[category.id] || [])" :key="skill.id" class="border-b border-gray-200 pb-3">
                <div class="text-xs font-medium mb-1">{{ skill && skill.name }}</div>
                <div v-if="skill && skill.note" class="text-[11px] text-gray-600 mb-2">{{ skill.note }}</div>
                <select v-if="skill" v-model="skill.level" class="w-full text-xs border border-gray-300 rounded px-2 py-1 font-typewriter focus:outline-none focus:border-black bg-gray-50 mb-2">
                  <option v-for="level in skillLevelOptions" :key="level.value" :value="level.value">
                    {{ level.label }} ({{ level.mod }})
                  </option>
                </select>
                <div v-if="skill && skill.allowSpecialties" class="space-y-2">
                  <div class="text-[11px] text-gray-600">專精</div>
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
                  <button @click="addSpecialty(skill)" class="text-xs text-blue-700 border border-blue-300 rounded px-2 py-1 hover:bg-blue-50">
                    + 新增專精
                  </button>
                </div>
              </div>
              <div v-if="(visibleGroupedSkills[category.id] || []).length === 0" class="text-xs text-gray-500">無技能</div>
            </div>
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
  currentStress: 0,
  stressLevel: 0,
  supernaturalStressMarks: Array(10).fill(false),
  equipment: '',
  attacks: Array(4).fill(''),
  skills: buildDefaultSkills(),
  cyphers: [],
  cypherLimit: 0,
  abilities: [],
  xp: 0,
  background: '',
  recoveryBonus: 0
})

// 複製提示狀態
const showCopyNotification = ref(false)
const copyNotificationText = ref('')

const showSkillsModal = ref(false)
const activeTab = ref('character')
const hideOutsiderSkills = ref(false)

const skillLevelOptions = [
  { value: 'outsider', label: '外行🤡', mod: '-1' },
  { value: 'novice', label: '新手', mod: '-1' },
  { value: 'amateur', label: '業餘', mod: '0' },
  { value: 'pro', label: '職業', mod: '+1' },
  { value: 'expert', label: '專家', mod: '+2' },
  { value: 'master', label: '大師🎖️', mod: '+2' }
]

const skillCategories = [
  { id: 'might', label: '氣力 (Might)', icon: '🔴' },
  { id: 'speed', label: '速度 (Speed)', icon: '🔵' },
  { id: 'intellect', label: '智力 (Intellect)', icon: '🟣' }
]

function createSpecialties (names, level) {
  if (!Array.isArray(names)) return []
  return names.filter(Boolean).map(name => ({ name, level }))
}

function buildDefaultSkills () {
  return [
  { id: 'combat', name: '戰鬥', category: 'might', level: 'amateur', allowSpecialties: true, specialties: createSpecialties(['斧頭', '鬥毆', '鏈鋸', '連枷', '絞索', '斧', '劍', '鞭子'], 'amateur') },
  { id: 'throwing', name: '投擲', category: 'might', level: 'amateur' },
  { id: 'climb', name: '攀爬', category: 'might', level: 'amateur' },
  { id: 'jump', name: '跳躍', category: 'might', level: 'amateur' },
  { id: 'swim', name: '游泳', category: 'might', level: 'amateur' },
  { id: 'track', name: '追蹤', category: 'might', level: 'outsider' },
  { id: 'heavy-machinery', name: '操作重機', category: 'might', level: 'outsider' },
  { id: 'first-aid', name: '急救', category: 'might', level: 'amateur' },
  { id: 'survival', name: '生存', category: 'might', level: 'outsider' },
  { id: 'nature', name: '自然世界', category: 'might', level: 'outsider' },
  { id: 'firearms', name: '火器', category: 'speed', level: 'outsider', allowSpecialties: true, specialties: createSpecialties(['弓', '手槍', '重武器', '火焰發射器', '機關槍', '步槍／霰彈槍', '衝鋒槍'], 'outsider') },
  { id: 'dodge', name: '閃避', category: 'speed', level: 'outsider' },
  { id: 'stealth', name: '隱匿', category: 'speed', level: 'amateur' },
  { id: 'disguise', name: '偽裝', category: 'speed', level: 'outsider' },
  { id: 'sleight-of-hand', name: '巧手', category: 'speed', level: 'outsider' },
  { id: 'locksmith', name: '鎖匠', category: 'speed', level: 'outsider' },
  { id: 'drive', name: '駕駛', category: 'speed', level: 'outsider' },
  { id: 'car', name: '開車', category: 'speed', level: 'amateur' },
  { id: 'electrical-repair', name: '電器維修', category: 'speed', level: 'outsider' },
  { id: 'mechanical-repair', name: '機械維修', category: 'speed', level: 'outsider' },
  { id: 'accounting', name: '會計', category: 'intellect', level: 'outsider' },
  { id: 'anthropology', name: '人類學', category: 'intellect', level: 'outsider' },
  { id: 'appraise', name: '鑑定', category: 'intellect', level: 'outsider' },
  { id: 'archaeology', name: '考古學', category: 'intellect', level: 'outsider' },
  { id: 'art', name: '藝術／工藝', category: 'intellect', level: 'outsider', allowSpecialties: true, specialties: createSpecialties(['表演', '美術', '偽造', '攝影'], 'outsider') },
  { id: 'charm', name: '魅力', category: 'intellect', level: 'outsider' },
  { id: 'credit', name: '信用評級', category: 'intellect', level: 'outsider' },
  { id: 'fast-talk', name: '話術', category: 'intellect', level: 'outsider' },
  { id: 'history', name: '歷史', category: 'intellect', level: 'outsider' },
  { id: 'law', name: '法律', category: 'intellect', level: 'outsider' },
  { id: 'library', name: '圖書館使用', category: 'intellect', level: 'amateur' },
  { id: 'listen', name: '聆聽', category: 'intellect', level: 'amateur' },
  { id: 'medicine', name: '醫藥', category: 'intellect', level: 'outsider' },
  { id: 'navigate', name: '導航', category: 'intellect', level: 'outsider' },
  { id: 'occult', name: '神祕學', category: 'intellect', level: 'outsider' },
  { id: 'persuade', name: '說服', category: 'intellect', level: 'outsider' },
  { id: 'psychology', name: '心理學', category: 'intellect', level: 'outsider' },
  { id: 'psychoanalysis', name: '精神分析', category: 'intellect', level: 'outsider' },
  { id: 'science', name: '科學', category: 'intellect', level: 'outsider', allowSpecialties: true, specialties: createSpecialties(['天文學', '生物學', '植物學', '化學', '經濟學', '地質學', '數學', '氣象學', '藥學', '物理學', '動物學'], 'outsider') },
  { id: 'language', name: '語言', category: 'intellect', level: 'outsider', allowSpecialties: true, specialties: [], specialtyPlaceholder: '外語自填' },
  { id: 'native-language', name: '母語', category: 'intellect', level: 'pro', note: '熟練' },
  { id: 'intimidate', name: '威脅', category: 'intellect', level: 'outsider' },
  { id: 'cthulhu-mythos', name: '克蘇魯神話*', category: 'intellect', level: 'outsider' }
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
    if (raw.specialties !== undefined) {
      target.specialties = normalizeSpecialties(raw.specialties, target.level)
    }
    if (raw.note) target.note = raw.note
    if (raw.allowSpecialties !== undefined) target.allowSpecialties = raw.allowSpecialties
    if (raw.specialtyPlaceholder) target.specialtyPlaceholder = raw.specialtyPlaceholder
  })

  defaults.forEach((skill) => {
    if (skill.allowSpecialties && !Array.isArray(skill.specialties)) {
      skill.specialties = []
    }
  })

  return defaults
}

const groupedSkills = computed(() => {
  const groups = { might: [], speed: [], intellect: [] }
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
  if (!hideOutsiderSkills.value) return specialties
  return specialties.filter(spec => spec.level !== 'outsider')
}

const isSkillVisible = (skill) => {
  if (!hideOutsiderSkills.value) return true
  if (skill.level !== 'outsider') return true
  return getVisibleSpecialties(skill).length > 0
}

const visibleGroupedSkills = computed(() => {
  const groups = { might: [], speed: [], intellect: [] }
  Object.keys(groupedSkills.value).forEach((key) => {
    groups[key] = groupedSkills.value[key].filter(skill => isSkillVisible(skill))
  })
  return groups
})

const getSkillLevelLabel = (level) => {
  const found = skillLevelOptions.find(option => option.value === level)
  return found ? `${found.label} ${found.mod}` : level
}

const getSkillDisplayLevel = (skill) => {
  if (hideOutsiderSkills.value && skill.level === 'outsider') return ''
  return getSkillLevelLabel(skill.level)
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

const addSpecialty = (skill) => {
  if (!skill.allowSpecialties) return
  if (!Array.isArray(skill.specialties)) skill.specialties = []
  const defaultLevel = skill.level || 'outsider'
  skill.specialties.push({ name: '', level: defaultLevel })
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
      damageTrack: 'hale',
      currentStress: 0,
      stressLevel: 0,
      supernaturalStressMarks: Array(10).fill(false),
      equipment: '',
      attacks: Array(4).fill(''),
      skills: buildDefaultSkills(),
      cyphers: [],
      cypherLimit: 0,
      abilities: [],
      xp: 0,
      background: '',
      recoveryBonus: 0
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
    const savedData = localStorage.getItem('magnus-csr-character')
    if (savedData) {
      const parsedData = JSON.parse(savedData)
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
        currentStress: 0,
        stressLevel: 0,
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
        ...parsedData, // 覆蓋已儲存的資料
        skills: processedSkills
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
壓力：${character.value.currentStress}　壓力量級：${character.value.stressLevel}
超自然壓力：${character.value.supernaturalStressMarks.filter(Boolean).length}/10

【攻擊】
${nonEmptyAttacks.length > 0 ? 
  nonEmptyAttacks.map((attack, index) => `${index + 1}. ${attack}`).join('\n') : 
  '(無攻擊記錄)'}

【技能】
${character.value.skills.map((skill, index) => {
  const levelText = getSkillLevelLabel(skill.level)
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
壓力：${character.value.currentStress}　壓力量級：${character.value.stressLevel}
超自然壓力：${character.value.supernaturalStressMarks.filter(Boolean).length}/10`

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

/* 傷害軌項目懸浮效果 */
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
