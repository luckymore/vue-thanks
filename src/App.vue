<script setup lang="ts">
import { onMounted, ref } from 'vue'

function getImageUrl(name: string) {
  return new URL(`./assets/${name}.svg`, import.meta.url).href
}
const names = ['vue', 'vite', 'cypress', 'pinia', 'vue-awesome']
const logos = names.map(getImageUrl)

// π、ρ、β、δ 诸星代表蝎子的头部
// σ、α、τ 代表蝎子的胸部
// ε、μ、ζ、η、θ、ι、κ、λ、υ 等代表蝎子的尾巴
const starGroups = [
  [
    { name: '房宿一', p: [630, 415], r: 12, symbol: 'π' },
    { name: '房宿二', p: [684, 393], r: 12, symbol: 'ρ' },
    { name: '房宿四', p: [499, 635], r: 12, westernName: 'Acrab, Graffias', symbol: 'β1' },
    { name: '房宿三', p: [585, 462], r: 24, westernName: 'Dschubba', symbol: 'δ' }
  ],
  { name: '心宿一', p: [503, 634], r: 12, westernName: 'Alniyat', symbol: 'σ' },
  { name: '心宿二', p: [492, 737], r: 12, westernName: 'Antares', symbol: 'α' },
  { name: '心宿三', p: [479, 862], r: 12, westernName: 'Alniyat', symbol: 'τ' },
  { name: '尾宿二', p: [387, 892], r: 12, symbol: 'ε' },
  { name: '尾宿一', p: [249, 894], r: 12, symbol: 'μ' },
  { name: '尾宿三', p: [188, 823], r: 12, westernName: 'Grafias', symbol: 'ζ' },
  { name: '尾宿四', p: [178, 823], r: 12, westernName: '', symbol: 'η' },
  { name: '尾宿五', p: [158, 823], r: 12, westernName: 'Sargas', symbol: 'θ' },
  { name: '尾宿六', p: [138, 823], r: 12, westernName: '', symbol: 'ι' },
  { name: '尾宿七', p: [128, 823], r: 12, westernName: 'Girtab', symbol: 'κ' },
  { name: '尾宿八', p: [108, 823], r: 12, westernName: 'Shaula', symbol: 'λ' },
  { name: '尾宿九', p: [88, 823], r: 12, westernName: 'Lesath', symbol: 'υ' }
]

const lines = ref<any[]>([])

onMounted(() => {
  // 根据 startGroups 中每个元素的 p 属性生成 lines
  // 如果 starGroups 的元素是数组，那么数组的每个元素都指向该数组的下一个兄弟元素
  // 如果 starGroups 的元素不是数组，那么该元素指向 starGroups 的下一个兄弟元素
  for (let i = 0; i < starGroups.length; i++) {
    const group = starGroups[i]
    if (Array.isArray(group)) {
      for (let j = 0; j < group.length; j++) {
        const star = group[j]
        if (j < group.length - 1) {
          lines.value.push({
            start: star.p,
            end: group[j + 1].p
          })
        }
      }
    } else {
      if (i < starGroups.length - 1) {
        lines.value.push({
          start: group.p,
          end: starGroups[i + 1].p
        })
      }
    }
  }
})

</script>

<template>
  <div class="bg">
    <svg
      width="100%"
      height="100%"
      scale="1.5"
    >
      <filter
        id="outset-shadow"
        height="150%"
        width="150%"
        x="-25%"
        y="-25%"
        filterUnits="userSpaceOnUse"
      >
        <feMorphology
          operator="dilate"
          radius="3"
          in="SourceAlpha"
          result="thicken"
        />
        <feGaussianBlur
          in="thicken"
          stdDeviation="3"
          result="blurred"
        />
        <feFlood
          flood-color="#1f33a2"
          result="glowColor"
        />
        <feComposite
          in="glowColor"
          in2="blurred"
          operator="in"
          result="softGlowColored"
        />
        <feComposite
          in="softGlowColored"
          in2="SourceGraphic"
          operator="out"
          result="outSoftGlowColored"
        />
        <feMerge>
          <feMergeNode in="outSoftGlowColored" />
          <feMergeNode in="SourceGraphic" />
        </feMerge>
      </filter>

      <circle
        cx="100"
        cy="100"
        r="100"
        filter="url(#outset-shadow)"
        fill="#f0debb"
        fill-opacity="0.7">

      </circle>
      <!-- 绘制节点 -->
      <circle
        v-for="star in starGroups.flat()"
        :key="star.name"
        :cx="star.p[0]"
        :cy="star.p[1]"
        :r="star.r / Math.cos(45 * (Math.PI / 180))"
        filter="url(#outset-shadow)"
        fill="#f0debb"
        fill-opacity="0.7"
      />
      <image
        v-for="(star, index) in starGroups.flat()"
        :key="star.name"
        class="logo"
        :href="logos[index > 5 ? 3 : index]"
        :x="star.p[0] - star.r"
        :y="star.p[1] - star.r"
        :width="star.r * 2"
        :height="star.r * 2"
        :scale="0.5"
        stroke="red"
      />

      <!-- 使用直线连接节点 -->
      <line
        v-for="line in lines"
        :key="line.name"
        :x1="line.start[0]"
        :y1="line.start[1]"
        :x2="line.end[0]"
        :y2="line.end[1]"
        stroke="white"
      />
    </svg>
  </div>
</template>

<style scoped>
.bg {
  background-image: url(https://upload.wikimedia.org/wikipedia/commons/thumb/a/a0/Antares_overlooking_an_Auxiliary_Telescope.jpg/800px-Antares_overlooking_an_Auxiliary_Telescope.jpg);
  background-size: cover;
  background-position: center;
  height: 100vh;
  width: 100vw;
}

.logo {
  margin: 0 2rem 55px 0;
  position: relative;
  top: -0.5rem;
  margin-right: 1.5rem;
  width: 3rem;
  height: 3rem;
  border-radius: 50%;
  border: 1px solid #ccc;
  padding: 0.5rem;

  display: flex;
  justify-content: center;
  align-items: center;

  background-color: #fff;

  box-shadow: 0 0 10px #ccc;

  transition: all 0.3s;
}
</style>
