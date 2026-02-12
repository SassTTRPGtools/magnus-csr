<template>
  <div class="flex flex-col min-h-screen bg-magnus-bg">
    <header class="w-full bg-magnus-paper shadow-lg py-4 px-8 flex items-center justify-between magnus-header flex-shrink-0 border-b-2 border-magnus-accent">
      <div class="text-2xl font-bold text-magnus-text tracking-widest magnus-title drop-shadow font-typewriter">CSR角色表</div>
      <nav class="space-x-4">        
        <NuxtLink to="/character" class="text-magnus-text hover:text-magnus-accent font-semibold transition duration-300">馬格努斯檔案角色表</NuxtLink>
        <NuxtLink to="/coc-character" class="text-magnus-text hover:text-magnus-accent font-semibold transition duration-300">克蘇魯風角色表</NuxtLink>
        <NuxtLink to="/mothership-character" class="text-magnus-text hover:text-magnus-accent font-semibold transition duration-300">OSR太空恐怖角色表</NuxtLink>        
        <NuxtLink to="/" class="text-magnus-text hover:text-magnus-accent font-semibold transition duration-300">首頁</NuxtLink>
      </nav>
    </header>
    <main class="flex-1 flex flex-col bg-magnus-bg">
      <slot />
    </main>
  </div>
</template>

<style scoped>
.magnus-header {
  box-shadow: 0 4px 16px rgba(161, 60, 60, 0.3);
  background: linear-gradient(135deg, #232a25 0%, #181c1b 100%);
}
.magnus-title {
  font-family: 'Special Elite', 'Courier New', monospace;
  letter-spacing: 0.08em;
  text-shadow: 0 2px 8px rgba(161, 60, 60, 0.6), 0 1px 0 rgba(229, 225, 216, 0.1);
}
.font-typewriter {
  font-family: 'Special Elite', 'Courier New', monospace !important;
}
</style>
