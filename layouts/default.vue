<template>
  <div class="bg-[#F2F2F2] space-y-4">
    <nav class="container mx-auto h-auto flex items-center justify-center">
      <NuxtLink to="/">
        <img src="/images/logo.png" class="h-32 items-center" />
      </NuxtLink>
    </nav>
    <div class="container mx-auto px-4 bg-[#82CBC4] p-4 rounded-lg shadow-lg">
      <slot />
    </div>
  </div>
</template>
