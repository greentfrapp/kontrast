<template>
  <div class="p-10 flex flex-col gap-10">
    <div class="flex flex-col gap-2">
      <div v-for="bg, i in backgrounds" :key="i" class="flex gap-2">
        {{ 'Background ' + i }}
        <div v-for="fg, j in foregrounds" :key="j" class="w-16">
          <div :style="{ background: grayToRgb(bg), color: grayToRgb(fg)}" class="w-10 h-10">
            Test
          </div>
          <div class="truncate">{{ getContrastRatio(fg, bg) }}</div>
        </div>
      </div>
    </div>
    <div>
      <div class="flex gap-4 mb-2">
        <h1>Foregrounds</h1>
        <div @click="addForeground" class="text-neutral-700 text-sm px-2 bg-neutral-100 rounded shadow w-max cursor-pointer">Add foreground</div>
      </div>
      <div class="flex">
        <div v-for="fg, i in foregrounds" :key="i" class="w-36 border rounded p-2">
          <div :style="{ background: grayToRgb(fg)}" class="w-10 h-10"></div>
          <input type="range" min="0" max="255" :modelValue="fg" @input="$event => foregrounds[i] = parseInt($event.target.value)" class="w-full" />
          <div class="flex">
            <div class="text-neutral-700 text-sm px-2 bg-neutral-100 rounded shadow w-max cursor-pointer" @click="foregrounds[i] -= 1">-</div>
            <div class="text-neutral-700 text-sm px-2 bg-neutral-100 rounded shadow w-max cursor-pointer" @click="foregrounds[i] += 1">+</div>
          </div>
          <div>{{ grayToRgb(fg) }}</div>
        </div>
      </div>
    </div>
    <div>
      <div class="flex gap-4 mb-2">
        <h1>Backgrounds</h1>
        <div @click="addBackground" class="text-neutral-700 text-sm px-2 bg-neutral-100 rounded shadow w-max cursor-pointer">Add background</div>
      </div>
      <div class="flex">
        <div v-for="bg, i in backgrounds" :key="i" class="w-36 border rounded p-2">
          <div :style="{ background: grayToRgb(bg)}" class="w-10 h-10"></div>
          <input type="range" min="0" max="255" :modelValue="bg" @input="$event => backgrounds[i] = parseInt($event.target.value)" class="w-full" />
          <div class="flex">
            <div class="text-neutral-700 text-sm px-2 bg-neutral-100 rounded shadow w-max cursor-pointer" @click="backgrounds[i] -= 1">-</div>
            <div class="text-neutral-700 text-sm px-2 bg-neutral-100 rounded shadow w-max cursor-pointer" @click="backgrounds[i] += 1">+</div>
          </div>
          <div>{{ grayToRgb(bg) }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import luminance from 'relative-luminance'

const backgrounds = ref([])
const foregrounds = ref([])

function addForeground () {
  foregrounds.value.push(100)
}
function addBackground () {
  backgrounds.value.push(100)
}

function grayToRgb (gray:number) {
  return rgbToHex(gray, gray, gray)
}

function componentToHex(c) {
  var hex = c.toString(16);
  return hex.length == 1 ? "0" + hex : hex;
}

function rgbToHex(r, g, b) {
  return "#" + componentToHex(r) + componentToHex(g) + componentToHex(b);
}

function hexToRgb (hex:string) {
  var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex)
  return result ? [
    parseInt(result[1], 16),
    parseInt(result[2], 16),
    parseInt(result[3], 16)
  ] : null
}

function getContrastRatio (c1, c2) {
  const ratio = (luminance([c1, c1, c1]) + 0.05) / (luminance([c2, c2, c2]) + 0.05)
  if (ratio < 1) return 1 / ratio
  return ratio
}
</script>
