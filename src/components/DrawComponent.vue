<template>
  <canvas
    @mousedown="onMouseDown"
    @mouseup="onMouseUp"
    @mousemove="onMouseMove"
    @mouseout="onMouseUp"
    ref="workspace"
    class="task__workspace"
    :width="width"
    :height="height"
  ></canvas>
</template>

<script setup lang="ts">
import { ref, watch, computed, onMounted, onUnmounted } from "vue";

const props = defineProps<{
  imgUrl?: string;
  width: number;
  height: number;
  color?: string;
}>();

const workspace = ref<HTMLCanvasElement>();
const ctx = ref<CanvasRenderingContext2D | null>(null);

const offsetX = ref(0);
const offsetY = ref(0);

const color = computed(() => props.color || "black");
const thicc = ref(2);

const drawing = ref(false);

function onMouseDown() {
  drawing.value = true;
}

function onMouseUp() {
  drawing.value = false;
  if (!ctx.value) return;
  ctx.value.stroke();
  ctx.value.beginPath();
}

function onMouseMove(e: MouseEvent) {
  if (!drawing.value) return;
  if (!ctx.value) return;
  console.log(color.value);
  ctx.value.strokeStyle = color.value;
  ctx.value.lineWidth = thicc.value;
  ctx.value.lineCap = "round";
  ctx.value.lineTo(e.clientX - offsetX.value, e.clientY - offsetY.value);
  ctx.value.stroke();
}

onMounted(() => {
  document.getElementsByTagName("html")[0].style.overflow = "hidden";
  window.scrollTo(0, 0);

  if (!workspace.value) return;
  ctx.value = workspace.value.getContext("2d");
  if (ctx.value == null) return;

  const task = new Image();
  task.src = props.imgUrl!;
  const ratio = task.width / task.height;
  ctx.value.drawImage(task, 0, 0, 250, 250 / ratio);

  const bounding = workspace.value.getBoundingClientRect();
  offsetX.value = bounding.left;
  offsetY.value = bounding.top;
});
onUnmounted(() => {
  document.getElementsByTagName("html")[0].style.overflow = "auto";
});
</script>

<style scoped lang="scss">
.task__workspace {
  border: 1px solid black;
}
</style>
