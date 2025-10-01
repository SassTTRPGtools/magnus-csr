<template>
  <div class="min-h-screen bg-black text-green-400 font-mono p-4 relative overflow-hidden">
    <!-- Terminal scanlines effect -->
    <div class="absolute inset-0 pointer-events-none opacity-10">
      <div class="h-full w-full" style="background: repeating-linear-gradient(0deg, transparent, transparent 2px, #00ff00 2px, #00ff00 4px);"></div>
    </div>
    
    <div class="max-w-7xl mx-auto relative z-10">
      <!-- 複製成功提示 -->
      <div v-if="showCopyNotification" 
           class="fixed top-4 right-4 bg-green-900 text-green-100 px-4 py-2 border border-green-400 shadow-2xl z-50 font-mono text-sm animate-pulse">
        <span class="text-green-300">></span> {{ copyNotificationText }}
      </div>
      
      <!-- 三欄式佈局 - 標題夾在中間 -->
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        
        <!-- 左欄 - 基本資訊與屬性 -->
        <div class="character-sheet-column">
          <!-- 角色基本資訊 -->
          <div class="mb-6">
            <div class="border border-green-400 bg-black bg-opacity-80 p-4 shadow-lg shadow-green-400/20">
              <div class="text-center mb-4">
                <div class="text-green-300 text-xs uppercase tracking-wider mb-2 flex items-center justify-center">
                  <span class="text-green-500">></span> <span class="ml-1">OPERATIVE.NAME</span>
                </div>
                <input type="text" v-model="character.name" 
                       class="w-full border-b border-green-400 bg-transparent text-center font-mono text-lg text-green-100 focus:outline-none focus:border-yellow-400 focus:text-yellow-100 transition-colors placeholder-green-600" placeholder="[CLASSIFIED]">
              </div>            

              <div class="text-center">
                <input type="text" v-model="character.focus" 
                       class="w-full border-b border-green-400 bg-transparent text-center font-mono text-green-100 focus:outline-none focus:border-yellow-400 focus:text-yellow-100 transition-colors placeholder-green-600" placeholder="[ASSIGNMENT]">
                <div class="text-green-300 text-xs uppercase tracking-wider mt-2 flex items-center justify-center">
                  <span class="text-green-500">></span> <span class="ml-1">ROLE.TEMPLATE</span>
                </div>
              </div>
            </div>
          </div>

          <!-- 屬性表格 -->
          <div class="mb-6">
            <div class="border border-green-400 bg-black bg-opacity-80">
              <div class="grid grid-cols-2 border-b border-green-400 text-center text-xs font-bold uppercase bg-green-900 bg-opacity-30 text-green-300">
                <div class="p-2 border-r border-green-400 flex items-center justify-center">
                  <span class="text-green-500">></span> <span class="ml-1">位階</span>
                </div>
                <div class="p-2 flex items-center justify-center">
                  <span class="text-green-500">></span> <span class="ml-1">努力值</span>
                </div>
              </div>
              <div class="grid grid-cols-2 text-center">
                <input type="number" v-model.number="character.tier" 
                       class="p-3 border-r border-green-400 bg-transparent text-center font-mono text-green-100 focus:outline-none focus:bg-green-900 focus:bg-opacity-20 transition-colors">
                <input type="number" v-model.number="character.effort" 
                       class="p-3 bg-transparent text-center font-mono text-green-100 focus:outline-none focus:bg-green-900 focus:bg-opacity-20 transition-colors">
              </div>
            </div>
          </div>

          <!-- 屬性值 -->
          <div class="mb-6">
            <div class="border border-green-400 bg-black bg-opacity-80">
              <div class="grid grid-cols-3 border-b border-green-400 text-center text-xs font-bold uppercase bg-green-900 bg-opacity-30 text-green-300">
                <div class="p-2 border-r border-green-400 flex items-center justify-center">
                  <span class="text-green-500">></span> <span class="ml-1">氣力</span>
                </div>
                <div class="p-2 border-r border-green-400 flex items-center justify-center">
                  <span class="text-green-500">></span> <span class="ml-1">速度</span>
                </div>
                <div class="p-2 flex items-center justify-center">
                  <span class="text-green-500">></span> <span class="ml-1">智力</span>
                </div>
              </div>
              
              <!-- Current Values Row -->
              <div class="grid grid-cols-3 text-center border-b border-green-400">
                <input type="number" v-model.number="character.might.current" 
                       class="p-4 border-r border-green-400 bg-transparent text-center font-mono text-2xl text-green-100 focus:outline-none focus:bg-green-900 focus:bg-opacity-20 focus:text-yellow-400 transition-all">
                <input type="number" v-model.number="character.speed.current" 
                       class="p-4 border-r border-green-400 bg-transparent text-center font-mono text-2xl text-green-100 focus:outline-none focus:bg-green-900 focus:bg-opacity-20 focus:text-yellow-400 transition-all">
                <input type="number" v-model.number="character.intellect.current" 
                       class="p-4 bg-transparent text-center font-mono text-2xl text-green-100 focus:outline-none focus:bg-green-900 focus:bg-opacity-20 focus:text-yellow-400 transition-all">
              </div>
              
              <!-- Edge 標籤 -->
              <div class="grid grid-cols-3 text-center text-xs border-t border-mothership-accent">
                <!-- 氣力 -->
                <div class="grid grid-cols-2 gap-1 p-1 border-r border-black">
                  <div>
                    <span class="block mb-1">上限</span>
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
                    <span class="block mb-1">上限</span>
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
                    <span class="block mb-1">上限</span>
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
          <div class="mb-6">
            <div class="border border-green-400 bg-black bg-opacity-80 p-4">
              <div class="text-green-300 text-sm uppercase tracking-wider mb-4 text-center flex items-center justify-center">
                <span class="text-green-500">></span> <span class="ml-1">恢復動作</span>
              </div>
              
              <!-- 兩欄佈局 -->
              <div class="grid grid-cols-2 gap-4">
                <!-- 左欄：1D6 + 輸入框 -->
                <div class="flex flex-col items-center justify-center">
                  <div class="border-2 border-green-400 bg-green-900 bg-opacity-20 p-4 w-20 h-20 flex flex-col items-center justify-center hover:bg-green-900 hover:bg-opacity-40 transition-all duration-200">
                    <div class="text-lg font-bold font-mono mb-1 text-green-100">1d6+</div>
                    <input type="number" v-model.number="character.recoveryBonus" 
                           class="w-12 bg-transparent text-center font-mono font-bold text-xl border-b-2 border-green-400 text-green-100 focus:outline-none focus:border-yellow-400 mt-1">
                  </div>
                </div>
                
                <!-- 右欄：恢復動作格子 -->
                <div class="space-y-2">
                  <div class="text-xs font-mono text-green-400 mb-2 text-center">恢復動作</div>
                  
                  <!-- 第一個格子：動作 -->
                  <div class="mb-2">
                    <div class="flex items-center justify-between mb-1">
                      <span class="text-xs font-semibold text-green-400 font-mono">動作</span>
                    </div>
                    <div class="grid grid-cols-3 gap-1">
                      <div v-for="(roll, index) in getActionRecoveryRolls()" :key="`action-${index}`" 
                           class="w-6 h-6 border-2 border-green-600 bg-green-50 flex items-center justify-center cursor-pointer hover:bg-green-100"
                           @click="roll.used = !roll.used">
                        <span v-if="roll.used" class="text-green-800 font-bold text-xs">✓</span>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- 壓力與傷勢區塊 -->
          <div class="mb-6">
            <div class="border border-green-400 bg-black bg-opacity-80 p-4">
              <!-- 複製狀態值按鈕（置中） -->
              <div class="text-center mb-4">
                <button @click="copyStatusToClipboard" 
                        class="text-xs px-3 py-2 bg-blue-900 text-blue-100 hover:bg-blue-800 border border-blue-400 font-mono transition-all duration-200 hover:shadow-lg hover:shadow-blue-400/30">
                  [COPY_STATUS]
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
                      <div class="absolute inset-0 bg-red-900 bg-opacity-60 rounded-full transform rotate-12 border border-red-600" style="clip-path: polygon(20% 0%, 100% 30%, 90% 90%, 10% 100%, 0% 60%)"></div>
                      <div class="absolute inset-1 bg-red-800 bg-opacity-40 rounded-full transform -rotate-6 border border-red-500" style="clip-path: polygon(30% 10%, 95% 25%, 85% 85%, 15% 95%, 5% 65%)"></div>
                      <!-- 中心方框 -->
                      <div class="absolute inset-4 bg-black bg-opacity-80 border-2 border-red-400 flex items-center justify-center">
                        <input type="number" v-model.number="character.currentStress" 
                               class="w-full text-center font-bold text-lg bg-transparent border-none focus:outline-none text-red-400 font-mono">
                      </div>
                      <!-- STRESS 標籤 -->
                      <div class="absolute -top-1 -left-1 bg-red-900 text-red-100 text-xs font-bold px-2 py-1 transform -rotate-12 rounded border border-red-400 font-mono">
                        壓力
                      </div>
                    </div>
                  </div>
                  
                  <!-- 壓力下限輸入框 -->
                  <div class="text-center">
                    <label class="block text-xs font-semibold text-red-400 mb-1 font-mono">壓力下限</label>
                    <input type="number" v-model.number="character.stressMin" min="0" 
                           class="w-16 px-2 py-1 border-2 border-red-400 bg-black bg-opacity-80 text-center font-mono text-sm focus:outline-none focus:border-yellow-400 text-red-400 focus:bg-red-900 focus:bg-opacity-20">
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- 進階 -->
          <div class="character-section">
            <div class="border-2 border-mothership-accent bg-mothership-panel mothership-panel-texture p-4">
              <div class="text-xs font-bold uppercase tracking-wide mb-3 text-mothership-text">晉升位階（完成任意四項）</div>
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
          <div>
            <div class="border border-green-400 bg-black bg-opacity-80 p-4">
              <div class="text-green-300 text-xs uppercase tracking-wider mb-4 flex items-center">
                <span class="text-green-500">></span> <span class="ml-1">DATA.MANAGEMENT</span>
              </div>
              <div class="grid grid-cols-2 gap-2 text-xs">
                <!-- 第一行 -->
                <button @click="exportToJSON" 
                        class="px-3 py-2 bg-purple-900 text-purple-100 hover:bg-purple-800 border border-purple-400 font-mono transition-all duration-200 hover:shadow-lg hover:shadow-purple-400/30">
                  [EXPORT_JSON]
                </button>
                <label class="px-3 py-2 bg-orange-900 text-orange-100 hover:bg-orange-800 border border-orange-400 font-mono cursor-pointer text-center transition-all duration-200 hover:shadow-lg hover:shadow-orange-400/30">
                  [IMPORT_JSON]
                  <input type="file" @change="importFromJSON" accept=".json" class="hidden">
                </label>
                <!-- 第二行 -->
                <button @click="exportToText" 
                        class="px-3 py-2 bg-gray-800 text-gray-100 hover:bg-gray-700 border border-gray-400 font-mono transition-all duration-200 hover:shadow-lg hover:shadow-gray-400/30">
                  [COPY_TEXT]
                </button>
                <button @click="clearForm" 
                        class="px-3 py-2 bg-red-900 text-red-100 hover:bg-red-800 border border-red-400 font-mono transition-all duration-200 hover:shadow-lg hover:shadow-red-400/30">
                  [CLEAR_ALL]
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- 中欄 - 標題、技能與攻擊 -->
        <div class="character-sheet-column">
          <!-- 標題 -->
          <div class="text-center mb-8 p-6 border border-green-400 bg-black bg-opacity-90">
            <div class="text-green-300 text-xs mb-2 tracking-widest">SYSTEM INTERFACE V2.1</div>
            <h1 class="text-4xl font-bold text-green-400 font-mono tracking-wider mb-2 animate-pulse">
              MOTHERSHIP
            </h1>
            <div class="flex items-center justify-center space-x-2 text-xl text-green-300 font-mono tracking-widest">
              <span class="text-green-500">></span>
              <span>SCI-FI HORROR RPG</span>
              <span class="text-green-500 animate-ping">_</span>
            </div>
            <div class="mt-2 text-xs text-green-600 tracking-wider">SECURITY CLEARANCE: AUTHORIZED</div>
          </div>

          <!-- 已學習技能 -->
          <div>
            <div class="border border-green-400 bg-black bg-opacity-80 p-4">
              <div class="flex items-center justify-between mb-4">
                <div class="text-green-300 text-sm uppercase tracking-wider flex items-center">
                  <span class="text-green-500">></span> <span class="ml-1">LEARNED.SKILLS</span>
                </div>
                <div class="flex items-center space-x-2">
                  <button @click="showSkillTreeModal = true" 
                          class="text-xs px-3 py-1 bg-green-900 text-green-100 hover:bg-green-800 border border-green-400 font-mono transition-all duration-200 hover:shadow-lg hover:shadow-green-400/30">
                    [MANAGE]
                  </button>
                  <button @click="clearAllSkills" 
                          :disabled="character.selectedSkills.length === 0"
                          :class="[
                            'text-xs px-3 py-1 font-mono transition-all duration-200',
                            character.selectedSkills.length === 0 
                              ? 'bg-gray-800 text-gray-500 cursor-not-allowed border border-gray-600' 
                              : 'bg-red-900 text-red-100 hover:bg-red-800 border border-red-400 hover:shadow-lg hover:shadow-red-400/30'
                          ]">
                    [PURGE]
                  </button>
                </div>
              </div>
              
              <!-- 已學習技能網格 -->
              <div class="grid grid-cols-2 gap-2 min-h-[200px]">
                <div v-for="skillId in character.selectedSkills" :key="skillId"
                     class="border border-green-600 bg-green-900 bg-opacity-20 p-2 flex items-center space-x-2 hover:bg-green-900 hover:bg-opacity-40 transition-all duration-200">
                  <div class="w-2 h-2 rounded-full" :class="{
                    'bg-green-400': getSkillTierClass(skillId) === 'tier-basic',
                    'bg-yellow-400': getSkillTierClass(skillId) === 'tier-intermediate', 
                    'bg-red-400': getSkillTierClass(skillId) === 'tier-advanced'
                  }"></div>
                  <span class="text-green-100 text-sm font-mono">{{ getSkillName(skillId) }}</span>
                </div>
                
                <!-- 空狀態提示 -->
                <div v-if="character.selectedSkills.length === 0" 
                     class="col-span-2 flex flex-col items-center justify-center py-8 text-green-600 border border-green-700 bg-green-900 bg-opacity-10">
                  <div class="text-sm font-mono mb-1 flex items-center">
                    <span class="text-green-500">></span> <span class="ml-1">NO SKILLS ACQUIRED</span>
                  </div>
                  <div class="text-xs font-mono text-green-700">[USE INTERFACE TO SELECT]</div>
                </div>
              </div>
             
            </div>
          </div>

        </div>

        <!-- 右欄 - 密鑰 -->
        <div class="character-sheet-column">
          <!-- 密鑰 -->
          <div class="mb-6">
            <div class="border border-green-400 bg-black bg-opacity-80 p-4">
              <div class="flex items-center justify-between mb-4">
                <div class="text-green-300 text-sm uppercase tracking-wider flex items-center">
                  <span class="text-green-500">></span> <span class="ml-1">密鑰</span>
                </div>
                <div class="flex items-center space-x-2">
                  <div class="flex items-center space-x-1">
                    <span class="text-xs text-red-300 font-mono">[LIMIT]</span>
                    <input type="number" v-model.number="character.cypherLimit" min="0" class="w-12 px-1 py-1 border border-green-400 bg-transparent text-center font-mono text-xs text-green-100 focus:outline-none focus:border-yellow-400 focus:bg-green-900 focus:bg-opacity-20" placeholder="0">
                  </div>
                  <button @click="generateRandomCyphers" 
                          :disabled="character.cypherLimit <= 0"
                          :class="[
                            'text-xs px-2 py-1 border font-mono transition-all duration-200',
                            character.cypherLimit <= 0 
                              ? 'bg-gray-800 text-gray-500 border-gray-600 cursor-not-allowed' 
                              : 'bg-yellow-900 text-yellow-100 border-yellow-400 hover:bg-yellow-800 hover:shadow-lg hover:shadow-yellow-400/30'
                          ]">
                    [RNG]
                  </button>
                  <button @click="addNewCypher" 
                          :disabled="character.cypherLimit > 0 && character.cyphers.length >= character.cypherLimit"
                          :class="[
                            'text-xs px-2 py-1 border font-mono transition-all duration-200',
                            character.cypherLimit > 0 && character.cyphers.length >= character.cypherLimit 
                              ? 'bg-gray-800 text-gray-500 border-gray-600 cursor-not-allowed' 
                              : 'bg-green-900 text-green-100 border-green-400 hover:bg-green-800 hover:shadow-lg hover:shadow-green-400/30'
                          ]">
                    [+ADD]
                  </button>
                </div>
              </div>
              
              <!-- 密鑰列表 -->
              <div class="space-y-2 max-h-96 overflow-y-auto">
                <div v-for="(cypher, index) in character.cyphers" :key="index" class="border border-green-600 bg-green-900 bg-opacity-20 p-3 hover:bg-green-900 hover:bg-opacity-30 transition-all duration-200">
                  <div class="flex items-center justify-between mb-2">
                    <button @click="cypher.collapsed = !cypher.collapsed" 
                            class="flex items-center text-sm font-mono text-green-100 hover:text-green-300 flex-1 text-left transition-colors">
                      <span class="mr-2 text-green-500">{{ cypher.collapsed ? '>' : 'v' }}</span>
                      <span>{{ getCypherTitle(cypher.content) || `CYPHER_${String(index + 1).padStart(2, '0')}` }}</span>
                    </button>
                    <div class="flex items-center space-x-2">
                      <button @click="copyCypherToClipboard(cypher)" class="text-blue-400 hover:text-blue-300 text-xs px-1 py-1 border border-blue-500 hover:bg-blue-900 hover:bg-opacity-30 font-mono transition-all" title="Copy cypher data">
                        [CPY]
                      </button>
                      <button @click="removeCypher(index)" class="text-red-400 hover:text-red-300 text-xs px-1 py-1 border border-red-500 hover:bg-red-900 hover:bg-opacity-30 font-mono transition-all">
                        [DEL]
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

          <!-- 物品欄 -->
          <div class="mb-6">
            <div class="border border-green-400 bg-black bg-opacity-80 p-4">
              <div class="flex items-center justify-between mb-4">
                <div class="text-green-300 text-sm uppercase tracking-wider flex items-center">
                  <span class="text-green-500">></span> <span class="ml-1">物品欄</span>
                </div>
                <div class="flex items-center space-x-2">
                  <div class="flex items-center space-x-1">
                    <span class="text-xs text-red-300 font-mono">[LIMIT]</span>
                    <input type="number" v-model.number="character.inventoryLimit" min="0" 
                           class="w-12 px-1 py-1 border border-green-400 bg-transparent text-center font-mono text-xs text-green-100 focus:outline-none focus:border-yellow-400 focus:bg-green-900 focus:bg-opacity-20" 
                           placeholder="0">
                  </div>
                  <button @click="toggleInventoryEditMode" 
                          :class="[
                            'text-xs px-2 py-1 border font-mono transition-all duration-200',
                            'bg-blue-900 text-blue-100 border-blue-400 hover:bg-blue-800 hover:shadow-lg hover:shadow-blue-400/30'
                          ]">
                    {{ inventoryEditMode ? '[VIEW]' : '[EDIT]' }}
                  </button>
                  <button @click="addNewItem" 
                          v-if="inventoryEditMode"
                          :disabled="character.inventoryLimit > 0 && character.inventory && character.inventory.length >= character.inventoryLimit"
                          :class="[
                            'text-xs px-2 py-1 border font-mono transition-all duration-200',
                            character.inventoryLimit > 0 && character.inventory && character.inventory.length >= character.inventoryLimit 
                              ? 'bg-gray-800 text-gray-500 border-gray-600 cursor-not-allowed' 
                              : 'bg-green-900 text-green-100 border-green-400 hover:bg-green-800 hover:shadow-lg hover:shadow-green-400/30'
                          ]">
                    [+ADD]
                  </button>
                </div>
              </div>
              
              <!-- 編輯模式 -->
              <div v-if="inventoryEditMode" class="space-y-2 max-h-96 overflow-y-auto">
                <div v-for="(item, index) in (character.inventory || [])" :key="index" 
                     class="border border-green-600 bg-green-900 bg-opacity-20 p-3 hover:bg-green-900 hover:bg-opacity-30 transition-all duration-200">
                  <div class="flex items-center justify-between mb-2">
                    <button @click="item.collapsed = !item.collapsed" 
                            class="flex items-center text-sm font-mono text-green-100 hover:text-green-300 flex-1 text-left transition-colors">
                      <span class="mr-2 text-green-500">{{ item.collapsed ? '>' : 'v' }}</span>
                      <span>{{ getItemTitle(item.content) || `ITEM_${String(index + 1).padStart(2, '0')}` }}</span>
                    </button>
                    <div class="flex items-center space-x-2">
                      <button @click="copyItemToClipboard(item)" 
                              class="text-blue-400 hover:text-blue-300 text-xs px-1 py-1 border border-blue-500 hover:bg-blue-900 hover:bg-opacity-30 font-mono transition-all" 
                              title="Copy item data">
                        [CPY]
                      </button>
                      <button @click="removeItem(index)" 
                              class="text-red-400 hover:text-red-300 text-xs px-1 py-1 border border-red-500 hover:bg-red-900 hover:bg-opacity-30 font-mono transition-all">
                        [DEL]
                      </button>
                    </div>
                  </div>
                  
                  <div v-if="!item.collapsed">
                    <textarea v-model="item.content" 
                             placeholder="物品名稱 | 價格&#10;物品描述..." 
                             class="w-full h-24 text-xs bg-transparent border border-green-600 text-green-100 placeholder-green-400 font-mono p-2 resize-none focus:outline-none focus:border-yellow-400 focus:bg-green-900 focus:bg-opacity-20"
                             rows="4"></textarea>
                  </div>
                  
                  <!-- 摺疊時顯示預覽 -->
                  <div v-else-if="item.content" class="text-xs text-green-400 italic truncate font-mono">
                    {{ getItemPreview(item.content) }}
                  </div>
                </div>
                
                <!-- 無物品時的提示 -->
                <div v-if="!character.inventory || character.inventory.length === 0" class="text-center text-green-500 font-mono text-xs p-4 border border-green-600 bg-black bg-opacity-50">
                  > NO.ITEMS.LOADED
                </div>
                
                <!-- 物品上限提示 -->
                <div v-if="character.inventoryLimit > 0 && character.inventory && character.inventory.length >= character.inventoryLimit" 
                     class="text-center text-yellow-600 text-xs py-2 border border-yellow-400 bg-yellow-900 bg-opacity-20 font-mono">
                  已達物品上限 ({{ character.inventory ? character.inventory.length : 0 }}/{{ character.inventoryLimit }})
                </div>
              </div>
              
              <!-- 查看模式 -->
              <div v-else class="space-y-4 max-h-96 overflow-y-auto">
                <div v-for="(category, categoryName) in categorizedItems" :key="categoryName" class="mb-4">
                  <h3 class="text-green-400 font-mono text-sm font-bold mb-2 border-b border-green-600 pb-1">
                    {{ categoryName }}
                  </h3>
                  <div class="grid grid-cols-1 gap-2">
                    <div v-for="(item, index) in category" :key="index" 
                         class="bg-green-900 bg-opacity-10 border border-green-600 p-2 hover:bg-green-900 hover:bg-opacity-20 transition-all duration-200 cursor-help"
                         :title="getItemTooltip(item)">
                      <div class="flex items-center justify-between">
                        <span class="font-mono text-green-100 text-sm">{{ item.name }}</span>
                        <span class="font-mono text-yellow-400 text-xs">{{ item.price }}</span>
                      </div>
                      <div class="text-green-400 text-xs font-mono mt-1 opacity-80">
                        {{ item.description.length > 60 ? item.description.substring(0, 60) + '...' : item.description }}
                      </div>
                    </div>
                  </div>
                </div>
                
                <!-- 無物品時的提示 -->
                <div v-if="Object.keys(categorizedItems).length === 0" class="text-center text-green-500 font-mono text-xs p-4 border border-green-600 bg-black bg-opacity-50">
                  > NO.ITEMS.LOADED
                </div>
              </div>
            </div>
          </div>

        </div>
      </div>
    </div>
    
    <!-- Terminal風格確認對話框 -->
    <div v-if="showTerminalConfirm" class="fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center z-50" @click.self="closeTerminalConfirm">
      <div class="bg-black border-2 border-red-400 p-6 max-w-md w-full mx-4 font-mono">
        <div class="text-red-400 text-sm whitespace-pre-line mb-6 leading-relaxed">
          {{ terminalConfirmMessage }}
        </div>
        <div class="flex space-x-4 justify-center">
          <button @click="confirmTerminalAction" 
                  class="px-4 py-2 bg-red-900 text-red-100 border border-red-400 hover:bg-red-800 transition-all duration-200 font-mono text-sm">
            [Y] YES
          </button>
          <button @click="closeTerminalConfirm" 
                  class="px-4 py-2 bg-gray-900 text-gray-100 border border-gray-400 hover:bg-gray-800 transition-all duration-200 font-mono text-sm">
            [N] NO
          </button>
        </div>
      </div>
    </div>
    
    <!-- 技能樹 Modal -->
    <div v-if="showSkillTreeModal" 
         class="fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center z-50 backdrop-blur-sm"
         @click="closeSkillTreeModal">
      <div class="border border-green-400 bg-black bg-opacity-95 max-w-6xl w-full max-h-[90vh] flex flex-col shadow-2xl shadow-green-400/20" @click.stop>
        <!-- Modal header -->
        <div class="border-b border-green-400 p-4 bg-green-900 bg-opacity-30">
          <div class="flex items-center justify-between">
            <div class="text-green-300 text-lg uppercase tracking-wider flex items-center font-mono">
              <span class="text-green-500">></span> <span class="ml-1">SKILL.TREE.INTERFACE</span>
            </div>
            <button @click="closeSkillTreeModal" class="text-red-400 hover:text-red-300 border border-red-500 px-2 py-1 font-mono text-sm hover:bg-red-900 hover:bg-opacity-30 transition-all">[X]</button>
          </div>
        </div>
        
        <!-- Modal body with scrolling -->
        <div class="flex-1 overflow-y-auto p-6">
          <div class="space-y-8">
              
            <!-- 初階技能 -->
            <div class="skill-tier">
              <div class="text-green-300 font-mono text-sm mb-4 flex items-center">
                <span class="text-green-500">></span> <span class="ml-1">TIER_01.BASIC</span>
              </div>
              <div class="grid grid-cols-4 gap-3">
              <div v-for="(skill, index) in basicSkills.filter(s => s.id !== '')" :key="skill.id" 
                   class="skill-node cursor-pointer" 
                   :class="{ 
                     'skill-selected': isSkillSelected(skill.id)
                   }"
                   @click="toggleSkill(skill.id)">
                <div class="skill-circle">
                  <span class="skill-dot"></span>
                </div>
                <span class="skill-label">{{ skill.name }}</span>
              </div>
            </div>
            </div>
              
            <!-- 中階技能 -->
            <div class="skill-tier">
              <div class="text-yellow-300 font-mono text-sm mb-4 flex items-center">
                <span class="text-yellow-500">></span> <span class="ml-1">TIER_02.INTERMEDIATE</span>
              </div>
              <div class="grid grid-cols-4 gap-3">
                <div v-for="(skill, index) in intermediateSkills.filter(s => s.id !== '')" :key="skill.id" 
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
            </div>
              
            <!-- 高階技能 -->
            <div class="skill-tier">
              <div class="text-red-300 font-mono text-sm mb-4 flex items-center">
                <span class="text-red-500">></span> <span class="ml-1">TIER_03.ADVANCED</span>
              </div>
              <div class="grid grid-cols-4 gap-3">
                <div v-for="(skill, index) in advancedSkills.filter(s => s.id !== '')" :key="skill.id" 
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
            </div>
          </div>
        </div>
        
        <!-- Modal footer -->
        <div class="border-t border-green-400 p-4 bg-green-900 bg-opacity-20 flex items-center justify-between">
          <div class="text-green-300 font-mono text-sm flex items-center">
            <span class="text-green-500">></span> 
            <span class="ml-1">SKILLS_SELECTED: {{ character.selectedSkills.length }}</span>
          </div>
          <button @click="closeSkillTreeModal" 
                  class="px-4 py-2 bg-green-900 text-green-100 hover:bg-green-800 border border-green-400 font-mono transition-all duration-200 hover:shadow-lg hover:shadow-green-400/30">
            [CLOSE_INTERFACE]
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
    pool: 10,
    edge: 0,
    current: 10
  },
  speed: {
    pool: 10,
    edge: 0,
    current: 10
  },
  intellect: {
    pool: 10,
    edge: 0,
    current: 10
  },
  recoveryRolls: {
    action: [
      { used: false },
      { used: false },
      { used: false }
    ],
    tenMin: [
      { used: false },
      { used: false },
      { used: false }
    ],
    oneHour: [
      { used: false },
      { used: false },
      { used: false }
    ]
  },
  currentStress: 2,
  stressMin: 2,
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
  inventory: [],
  inventoryLimit: 0,
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

// Terminal風格確認對話框狀態
const showTerminalConfirm = ref(false)
const terminalConfirmMessage = ref('')
const terminalConfirmCallback = ref(null)

// 物品欄編輯模式
const inventoryEditMode = ref(false)

// 技能樹資料
const basicSkills = ref([
  { id: 'linguistics', name: '語言學' },
  { id: 'biology', name: '動物學' },
  { id: 'botany', name: '植物學' },
  { id: 'geology', name: '地質學' },
  { id: 'industrialEquipment', name: '工業裝備' },
  { id: 'firstAid', name: '應急裝配' },
  { id: 'chemistry', name: '化學' },
  { id: 'computers', name: '終端機' },
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
  { id: 'psychology', name: '心理學', prereq: ['linguistics','biology','botany'] },
  { id: 'pathology', name: '病理學', prereq: ['biology','botany'] },
  { id: 'fieldMedicine', name: '戰地醫學', prereq: ['botany','biology'] },
  { id: 'ecology', name: '生態學', prereq: ['botany','geology'] },
  { id: 'asteroid', name: '小行星採礦', prereq: ['geology','industrialEquipment'] },
  { id: 'mechanics', name: '機械修理', prereq: ['industrialEquipment','firstAid'] },
  { id: 'explosives', name: '炸藥', prereq: ['firstAid', 'chemistry','militaryTraining'] },
  { id: 'pharmacology', name: '藥理學', prereq: ['chemistry'] },
  { id: 'hacking', name: '駭客', prereq: ['computers'] },
  { id: 'piloting', name: '駕駛', prereq: ['zerogy'] },
  { id: 'physics', name: '物理學', prereq: ['mathematics'] },
  { id: 'mysticism', name: '神祕學', prereq: ['art', 'archaeology', 'theology'] },
  { id: 'survival', name: '野外求生', prereq: ['botany','militaryTraining'] },
  { id: 'gunsmithing', name: '槍械', prereq: ['extravehicular','militaryTraining'] },
  { id: 'closeCombat', name: '格鬥', prereq: ['extravehicular','athletics','militaryTraining'] }
])

const advancedSkills = ref([
  { id: 'xenopsychology', name: '智慧種族學', prereq: ['psychology'] },
  { id: 'xenobiology', name: '外星生物學', prereq: ['pathology'] },
  { id: 'surgery', name: '手術', prereq: ['fieldMedicine','pathology'] },
  { id: 'planetology', name: '行星學', prereq: ['ecology', 'asteroid'] },
  { id: 'robotics', name: '機器人學', prereq: ['mechanics'] },
  { id: 'engineering', name: '工程學', prereq: ['mechanics'] },
  { id: 'hyperspace', name: '模控學', prereq: ['mechanics'] },
  { id: 'ai', name: '人工智慧', prereq: ['hacking'] },
  { id: 'hyperspace2', name: '超空間', prereq: ['physics', 'piloting','mysticism'] },
  { id: 'alienology', name: '異種秘教', prereq: ['mysticism'] },
  { id: 'command', name: '指揮', prereq: ['gunsmithing', 'piloting'] }
])



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
    return '/data/mothership-cypher.json'
  } else {
    // 取得 base 路徑（假設部署在 /magnus-csr）
    const base = window.location.pathname.split('/').filter(Boolean)[0] || ''
    return `/${base}/data/mothership-cypher.json`
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
  
  // 檢查是否有任一先決條件已選擇 (OR 邏輯)
  return skill.prereq.some(prereqId => isSkillSelected(prereqId))
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
      tooltip += '\n需要任一: ' + skill.prereq.join(' 或 ')
    } else {
      tooltip += '\n需要: ' + skill.prereq
    }
  }
  
  return tooltip
}



const clearAllSkills = () => {
  if (character.value.selectedSkills.length === 0) return
  
  showTerminalConfirm.value = true
  terminalConfirmMessage.value = `> CONFIRM_DELETE_ALL_SKILLS
> SKILLS_COUNT: ${character.value.selectedSkills.length}
> WARNING: OPERATION_IRREVERSIBLE
> PROCEED? [Y/N]`
  terminalConfirmCallback.value = () => {
    character.value.selectedSkills = []
    showCopySuccess('> ALL_SKILLS_CLEARED')
    closeTerminalConfirm()
  }
}

// Terminal風格確認對話框相關函數
const closeTerminalConfirm = () => {
  showTerminalConfirm.value = false
  terminalConfirmMessage.value = ''
  terminalConfirmCallback.value = null
}

const confirmTerminalAction = () => {
  if (terminalConfirmCallback.value) {
    terminalConfirmCallback.value()
  }
}

// 物品欄管理函數
const toggleInventoryEditMode = () => {
  inventoryEditMode.value = !inventoryEditMode.value
}

const addNewItem = () => {
  // 確保inventory存在
  if (!character.value.inventory) {
    character.value.inventory = []
  }
  
  if (character.value.inventoryLimit > 0 && character.value.inventory.length >= character.value.inventoryLimit) {
    showTerminalConfirm.value = true
    terminalConfirmMessage.value = `> INVENTORY_LIMIT_REACHED\n> CURRENT_ITEMS: ${character.value.inventory.length}\n> MAX_LIMIT: ${character.value.inventoryLimit}\n> CANNOT_ADD_MORE_ITEMS`
    terminalConfirmCallback.value = () => {
      closeTerminalConfirm()
    }
    return
  }
  
  character.value.inventory.push({
    content: '',
    collapsed: false
  })
}

const removeItem = (index) => {
  if (character.value.inventory && Array.isArray(character.value.inventory)) {
    character.value.inventory.splice(index, 1)
  }
}

const copyItemToClipboard = async (item) => {
  const itemText = item.content || '(空白物品)'
  
  try {
    await navigator.clipboard.writeText(itemText)
    showCopySuccess('物品內容已複製！')
  } catch (error) {
    console.error('複製失敗:', error)
    const textArea = document.createElement('textarea')
    textArea.value = itemText
    document.body.appendChild(textArea)
    textArea.select()
    try {
      document.execCommand('copy')
      showCopySuccess('物品內容已複製！')
    } catch (fallbackError) {
      alert('複製失敗，請手動複製內容')
    }
    document.body.removeChild(textArea)
  }
}

const getItemTitle = (content) => {
  if (!content) return ''
  const lines = content.split('\n')
  const firstLine = lines[0]
  const titleMatch = firstLine.match(/^([^|]+)/) 
  return titleMatch ? titleMatch[1].trim() : firstLine.trim()
}

const getItemPreview = (content) => {
  if (!content) return ''
  const lines = content.split('\n')
  const preview = lines.slice(0, 2).join(' ').replace(/\s+/g, ' ').trim()
  return preview.length > 80 ? preview.substring(0, 80) + '...' : preview
}

const parseItemContent = (content) => {
  if (!content) return { name: '', price: '', description: '', category: '其他' }
  
  const lines = content.split('\n')
  const firstLine = lines[0] || ''
  const parts = firstLine.split('|')
  
  const name = parts[0] ? parts[0].trim() : ''
  const price = parts[1] ? parts[1].trim() : ''
  const description = lines.slice(1).join(' ').trim()
  
  // 根據物品名稱或描述判斷類別
  let category = '其他'
  const nameAndDesc = (name + ' ' + description).toLowerCase()
  
  if (nameAndDesc.includes('武器') || nameAndDesc.includes('槍') || nameAndDesc.includes('刀') || 
      nameAndDesc.includes('彈藥') || nameAndDesc.includes('手榴彈') || nameAndDesc.includes('爆炸')) {
    category = '武器'
  } else if (nameAndDesc.includes('護甲') || nameAndDesc.includes('制服') || nameAndDesc.includes('防具')) {
    category = '護甲'
  } else if (nameAndDesc.includes('醫療') || nameAndDesc.includes('藥') || nameAndDesc.includes('治療') || 
             nameAndDesc.includes('急救') || nameAndDesc.includes('掃描')) {
    category = '醫療裝備'
  } else if (nameAndDesc.includes('工具') || nameAndDesc.includes('電子') || nameAndDesc.includes('維修')) {
    category = '工具裝備'
  } else if (nameAndDesc.includes('通訊') || nameAndDesc.includes('電子') || nameAndDesc.includes('無線電')) {
    category = '通訊裝備'
  }
  
  return { name, price, description, category }
}

const categorizedItems = computed(() => {
  const categories = {}
  
  // 安全檢查，確保inventory存在且為陣列
  if (character.value.inventory && Array.isArray(character.value.inventory)) {
    character.value.inventory.forEach(item => {
      if (!item.content) return
      
      const parsed = parseItemContent(item.content)
      if (!categories[parsed.category]) {
        categories[parsed.category] = []
      }
      categories[parsed.category].push(parsed)
    })
  }
  
  return categories
})

const getItemTooltip = (item) => {
  return `${item.name}\n價格: ${item.price}\n\n${item.description}`
}

// 恢復動作相關函數
const getActionRecoveryRolls = () => {
  if (!character.value.recoveryRolls.action || !Array.isArray(character.value.recoveryRolls.action)) {
    character.value.recoveryRolls.action = [
      { used: false },
      { used: false },
      { used: false }
    ]
  }
  return character.value.recoveryRolls.action
}

const getTenMinRecoveryRolls = () => {
  if (!character.value.recoveryRolls.tenMin || !Array.isArray(character.value.recoveryRolls.tenMin)) {
    character.value.recoveryRolls.tenMin = [
      { used: false },
      { used: false },
      { used: false }
    ]
  }
  return character.value.recoveryRolls.tenMin
}

const getOneHourRecoveryRolls = () => {
  if (!character.value.recoveryRolls.oneHour || !Array.isArray(character.value.recoveryRolls.oneHour)) {
    character.value.recoveryRolls.oneHour = [
      { used: false },
      { used: false },
      { used: false }
    ]
  }
  return character.value.recoveryRolls.oneHour
}

const getSkillName = (skillId) => {
  const allSkills = [...basicSkills.value, ...intermediateSkills.value, ...advancedSkills.value]
  const skill = allSkills.find(s => s.id === skillId)
  return skill ? skill.name : skillId
}



// 連線相關函數已移除

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

// 獲取技能階層樣式
const getSkillTierClass = (skillId) => {
  if (basicSkills.value.find(s => s.id === skillId)) return 'tier-basic'
  if (intermediateSkills.value.find(s => s.id === skillId)) return 'tier-intermediate'
  if (advancedSkills.value.find(s => s.id === skillId)) return 'tier-advanced'
  return ''
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
      might: { pool: 10, edge: 0, current: 10 },
      speed: { pool: 10, edge: 0, current: 10 },
      intellect: { pool: 10, edge: 0, current: 10 },
      recoveryRolls: {
        action: false,
        tenMin: false,
        oneHour: false,
        tenHours: false
      },
      currentStress: 2,
      stressMin: 2,
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
    localStorage.setItem('mothership-csr-character', characterData)
  } catch (error) {
    console.error('自動儲存失敗:', error)
  }
}

const loadFromLocalStorage = () => {
  try {
    const savedData = localStorage.getItem('mothership-csr-character')
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
        might: { pool: 10, edge: 0, current: 10 },
        speed: { pool: 10, edge: 0, current: 10},
        intellect: { pool: 10, edge: 0, current: 10 },
        recoveryRolls: {
          action: [
            { used: false },
            { used: false },
            { used: false }
          ],
          tenMin: [
            { used: false },
            { used: false },
            { used: false }
          ],
          oneHour: [
            { used: false },
            { used: false },
            { used: false }
          ]
        },
        currentStress: 2,
        stressMin: 2,
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
        inventory: [],
        inventoryLimit: 0,
        selectedSkills: [],
        xp: 0,
        background: '',
        recoveryBonus: 0,
        abilities: [], // 保留以防錯誤
        ...parsedData, // 覆蓋已儲存的資料
        selectedSkills: parsedData.selectedSkills || [], // 確保技能選擇格式正確
        inventory: parsedData.inventory || [], // 確保物品欄格式正確
        inventoryLimit: parsedData.inventoryLimit || 0 // 確保物品上限格式正確
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
/* Terminal UI CSS - Classic Green Screen Aesthetic */
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;500;700;900&display=swap');

/* Global terminal styling */
:root {
  --terminal-bg: #000000;
  --terminal-primary: #00ff00;
  --terminal-secondary: #00aa00;
  --terminal-accent: #ffff00;
  --terminal-danger: #ff4444;
  --terminal-text: #00ff00;
  --terminal-dim: #008800;
  --terminal-border: #00ff00;
  --font-terminal: 'Orbitron', 'Courier New', monospace;
}

body {
  font-family: var(--font-terminal);
  background: var(--terminal-bg);
  color: var(--terminal-text);
}

/* Terminal glow effects */
.terminal-glow {
  box-shadow: 0 0 10px currentColor;
}

.terminal-text-glow {
  text-shadow: 0 0 10px currentColor;
}

/* Scrollbar styling */
::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: #001100;
}

::-webkit-scrollbar-thumb {
  background: #00ff00;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #00aa00;
}

/* Animation for terminal cursor */
@keyframes blink {
  0%, 50% { opacity: 1; }
  51%, 100% { opacity: 0; }
}

.cursor {
  animation: blink 1s infinite;
}

/* Input focus effects */
input:focus {
  animation: none !important;
}

/* Button hover effects */
button:hover {
  transform: translateY(-1px);
}

/* Terminal scanlines animation */
@keyframes scanlines {
  0% { transform: translateY(-100%); }
  100% { transform: translateY(100vh); }
}

/* Custom terminal-style textarea */
textarea {
  font-family: var(--font-terminal);
  background: rgba(0, 255, 0, 0.05);
  border: 1px solid #00aa00;
  color: #00ff00;
  resize: vertical;
}

textarea:focus {
  outline: none;
  border-color: #ffff00;
  background: rgba(255, 255, 0, 0.05);
  color: #ffff00;
}

/* Skill Tree Styles - Terminal Color Scheme */
.skill-tier {
  margin-bottom: 2rem;
}

.skill-node {
  display: flex;
  align-items: center;
  padding: 6px 10px;
  margin: 6px 0;
  border-radius: 2px;
  background: 
    linear-gradient(135deg, rgba(0, 255, 0, 0.1) 0%, rgba(0, 255, 0, 0.05) 100%),
    repeating-linear-gradient(
      0deg,
      transparent,
      transparent 1px,
      rgba(0, 255, 0, 0.05) 1px,
      rgba(0, 255, 0, 0.05) 2px
    );
  border: 2px solid #00aa00;
  box-shadow: 
    0 0 8px rgba(0, 255, 0, 0.4),
    inset 0 2px 0 rgba(0, 255, 0, 0.15),
    inset 0 -1px 0 rgba(0, 0, 0, 0.1);
  transition: all 0.2s ease;
  position: relative;
  z-index: 2;
  color: #00ff00;
  font-family: 'Orbitron', monospace;
  text-shadow: 
    0 0 2px rgba(0, 255, 0, 0.5),
    1px 1px 1px rgba(0, 0, 0, 0.7);
}

.skill-node:hover {
  transform: translateY(-2px);
  box-shadow: 
    0 0 15px rgba(0, 255, 0, 0.6),
    0 4px 20px rgba(0, 255, 0, 0.3),
    inset 0 0 10px rgba(0, 255, 0, 0.1);
  border: 3px solid #00ff00;
  text-shadow: 
    0 0 3px rgba(0, 255, 0, 0.8),
    1px 1px 1px rgba(0, 0, 0, 0.9);
}

.skill-node.skill-selected {
  background: 
    linear-gradient(135deg, #00ff00 0%, #00aa00 100%),
    repeating-linear-gradient(
      45deg,
      rgba(0, 0, 0, 0.2),
      rgba(0, 0, 0, 0.2) 1px,
      transparent 1px,
      transparent 3px
    );
  border: 3px solid #00ff00;
  color: #000000;
  font-weight: bold;
  text-shadow: 
    0 0 3px rgba(0, 0, 0, 0.8),
    1px 1px 1px rgba(0, 0, 0, 0.9);
  box-shadow: 
    0 0 15px rgba(0, 255, 0, 0.8),
    0 0 25px rgba(0, 255, 0, 0.8),
    inset 0 2px 0 rgba(255, 255, 255, 0.3),
    inset 0 -2px 0 rgba(0, 0, 0, 0.2);
  animation: selected-pulse 2s ease-in-out infinite alternate;
}

@keyframes selected-pulse {
  from { 
    box-shadow: 
      0 0 15px rgba(0, 255, 0, 0.8),
      0 0 25px rgba(0, 255, 0, 0.8);
  }
  to { 
    box-shadow: 
      0 0 20px rgba(0, 255, 0, 1),
      0 0 35px rgba(0, 255, 0, 1);
  }
}

.skill-node.skill-disabled {
  background: rgba(100, 100, 100, 0.1);
  border-color: #666666;
  color: #666666;
  cursor: not-allowed;
  text-shadow: none;
}

.skill-node.skill-disabled:hover {
  transform: none;
  box-shadow: none;
  border-color: #666666;
}

.skill-circle {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: radial-gradient(circle at 30% 30%, rgba(0, 255, 0, 0.3) 0%, rgba(0, 255, 0, 0.1) 100%);
  border: 2px solid #00aa00;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 8px;
  position: relative;
}

.skill-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: #00ff00;
  box-shadow: 0 0 4px rgba(0, 255, 0, 0.8);
}

.skill-node.skill-selected .skill-circle {
  background: radial-gradient(circle at 30% 30%, #ffffff 0%, #00ff00 100%);
  border-color: #000000;
}

.skill-node.skill-selected .skill-dot {
  background: #000000;
  box-shadow: none;
}

.skill-node.skill-disabled .skill-circle {
  background: rgba(100, 100, 100, 0.2);
  border-color: #666666;
}

.skill-node.skill-disabled .skill-dot {
  background: #666666;
  box-shadow: none;
}

.skill-label {
  font-size: 12px;
  font-weight: 500;
}

/* Disable old CSS classes that might conflict */
.mothership-btn-glow,
.mothership-input-glow,
.character-section,
.panel-enhanced,
.text-enhanced,
.border-enhanced {
  all: unset;
}
</style>
