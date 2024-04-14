<template>
  <footer class="absolute right-0 bottom-0 p-2" v-if="$slidev.nav.currentPage !== 1 && $slidev.nav.currentPage !== 11"><SlideCurrentNo />/<SlidesTotal /></footer>
</template>