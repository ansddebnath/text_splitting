<template>
  <footer v-if="$nav.currentPage > 2 && $nav.currentLayout !== 'cover'" class="absolute bottom-0 left-0 right-0 px-4 py-2 text-right text-sm opacity-50 justify-between flex items-center">
    <span>Page {{ $nav.currentPage-2 }} of {{ $nav.total-2 }}</span>
    <img src="./images/Ansys-black.png" class="h-40px dark:hidden"/>
    <img src="./images/Ansys-white.png" class="h-40px hidden dark:block"/>
  </footer>
  <footer v-if="$nav.currentPage === 2 && $nav.currentLayout !== 'cover'" class="absolute bottom-0 left-0 right-0 px-4 py-2 justify-end flex text-sm opacity-50">
    <img src="./images/Ansys-black.png" class="h-40px dark:hidden"/>
    <img src="./images/Ansys-white.png" class="h-40px hidden dark:block"/>
  </footer>
</template>

