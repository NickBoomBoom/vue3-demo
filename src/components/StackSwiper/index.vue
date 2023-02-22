<script setup lang="ts">
const SCALE = 1;
const SCALE_STEP = 0.04;
const props = withDefaults(
  defineProps<{
    current: number;
    spaceWidth: number;
  }>(),
  {
    spaceWidth: 20,
    current: 0,
  }
);
const swiperRef = ref();
let swiperWidth: number;
const ITEM_CLASS_NAME = ".swiper-item";
const _current = ref(props.current);
watch(_current, (val: number, oldVal: number) => {
  nextTick(() => transition(val));
});
function getItems() {
  return swiperRef.value.querySelectorAll(ITEM_CLASS_NAME);
}
function transition(val: number) {
  const items = getItems();
  let xArr = [];
  for (let i = 0; i < items.length; i++) {
    const t = items[i];
    const s = getComputedStyle(t);

    /matrix\((.+)\)/.test(s.transform);
    const arr = RegExp.$1.split(",");
    const scale = +arr[0];
    const x = +arr[4];
    const { width } = t.getBoundingClientRect();
    xArr.push(x);
    let _s = scale;
    let _x = x;
    let _z;
    const d = Math.abs(val - i);
    if (i < val) {
      _z = i;
      _s = SCALE - d * SCALE_STEP;
      _x = -d * props.spaceWidth - (width - width * _s) / 2;
    } else if (i === val) {
      _s = 1;
      _z = items.length;
      _x = 0;
    } else if (i > val) {
      _z = items.length - i;
      _s = scale + (val - i) * SCALE_STEP;
      _x = (i - val) * props.spaceWidth + (width - width * _s) / 2;
    }
    console.log(i, d, _x, _s);

    t.style.cssText = `
    z-index: ${_z};
    left: calc(50% + ${_x}px);
    transform: translate(-50%) scale(${_s});
    `;
  }
}

function getCenterDistance(el: HTMLElement, percent = 1): number {
  const { width } = el.getBoundingClientRect();
  return el.offsetLeft - swiperWidth / 2 + width / 2;
}
function prev() {
  _current.value -= 1;
}
function next() {
  _current.value += 1;
}
function init() {
  const { width } = swiperRef.value.getBoundingClientRect();
  swiperWidth = width;
  const items = getItems();

  let percent = 1;
  for (let i = 0; i < items.length; i++) {
    const el = items[i];
    const cx = -getCenterDistance(el);
    const dx = cx + props.spaceWidth * i;
    const zIndex = items.length - i;
    console.log(i, props.spaceWidth * i);
    el.style.cssText = `
      left: calc(50% + ${props.spaceWidth * i}px);
      transform: translate(-50%) scale(${percent});
      z-index: ${zIndex};
    `;
    percent -= SCALE_STEP;
  }
}
// function getTranslateX() {
//   const items = getItems()
//   const { width } = items[current.value].getBoundingClientRect()
// }
onMounted(() => {
  nextTick(init);
});
</script>

<template>
  <div
    ref="swiperRef"
    class="w-full flex flex-nowrap overflow-hidden stack-swiper relative"
  >
    <slot />
    <button class="absolute left-0 top-1/2 bg-red-300 w-10" @click="prev">
      -
    </button>
    <button class="absolute right-0 top-1/2 bg-red-300 w-10" @click="next">
      +
    </button>

    <div class=""></div>
  </div>
</template>

<style lang="less">
.stack-swiper {
  height: 300px;

  &::after {
    content: "";
    position: absolute;
    left: 50%;
    top: 0;
    bottom: 0;
    width: 1px;
    height: 100%;
    background-color: yellow;
    z-index: 333;
  }

  .swiper-item {
    flex-shrink: 0;
    transition: all 0.3s;
    position: absolute;
    left: 50%;
    transform: translate(-50%);

    &::after {
      content: "";
      position: absolute;
      left: 50%;
      top: 0;
      bottom: 0;
      width: 1px;
      height: 100%;
      background-color: skyblue;
    }

    // &.prev {
    //   transform: scale(.8);
    // }

    // &.current {}

    // &.next {
    //   transform: scale(.8);

    // }
  }
}
</style>
