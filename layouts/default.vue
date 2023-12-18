<template>
  <div class="h-screen">
    <nav class="container mx-auto  h-24 flex items-center">
      <NuxtLink to="/">
        <h1 class="text-5xl">Pokedex</h1>
      </NuxtLink>
    </nav>
    <div class="container mx-auto px-3">
      <slot />
    </div>
  </div>
</template>
