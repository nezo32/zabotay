<template>
  <div
    class="draw"
    :class="{
      draw: props.choose == 'brush',
      cursor: props.choose == 'cursor',
      text: props.choose == 'text',
      line: props.choose == 'line',
      eraser: props.choose == 'eraser',
    }"
  >
    <canvas
      ref="workspaceBack"
      class="task__workspace__back"
      :width="width"
      :height="height"
    ></canvas>
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
  </div>
</template>

<script setup lang="ts">
import { ref, watch, computed, onMounted, onUnmounted } from "vue";

const props = defineProps<{
  imgUrl?: string;
  width: number;
  height: number;
  color?: string;
  brushWidth?: number;
  eraserWidth?: number;
  delete?: boolean;
  choose: "brush" | "eraser" | "line" | "text" | "cursor";
}>();
const emits = defineEmits(["update:delete"]);

const deleteValue = computed({
  get() {
    return props.delete;
  },
  set(value) {
    emits("update:delete", value);
  },
});

const w = computed(() => `${props.width}px`);
const h = computed(() => `${props.height}px`);

const workspace = ref<HTMLCanvasElement>();
const workspaceBack = ref<HTMLCanvasElement>();
const ctx = ref<CanvasRenderingContext2D | null>(null);
const ctxBack = ref<CanvasRenderingContext2D | null>(null);

const offsetX = ref(0);
const offsetY = ref(0);

const color = computed(() => props.color || "black");
const thiccBrush = computed(() => props.brushWidth || 2);
const thiccEraser = computed(() => props.eraserWidth || 2);

const drawing = ref(false);
const line = ref(false);

const startX = ref(0);
const startY = ref(0);

function onMouseDown(e: MouseEvent) {
  if (props.choose == "brush" || props.choose == "eraser") drawing.value = true;
  if (props.choose == "line") {
    ctx.value?.beginPath();
    line.value = true;
    startX.value = e.clientX - offsetX.value;
    startY.value = e.clientY - offsetY.value;
  }
  if (!ctx.value) return;
}

function onMouseUp(e: MouseEvent) {
  drawing.value = false;
  if (!ctx.value) return;

  if (props.choose == "line" && line.value) {
    line.value = false;
    ctx.value.strokeStyle = color.value;
    ctx.value.lineWidth = thiccBrush.value;
    ctx.value.lineCap = "round";
    ctx.value.moveTo(startX.value, startY.value);
    ctx.value.lineTo(e.clientX - offsetX.value, e.clientY - offsetY.value);
    ctx.value.stroke();
  } else {
    ctx.value.stroke();
    ctx.value.beginPath();
  }
}

function onMouseMove(e: MouseEvent) {
  if (!drawing.value) return;
  if (!ctx.value) return;
  if (!workspace.value) return;
  if (props.choose == "brush") {
    ctx.value.globalCompositeOperation = "source-over";
    ctx.value.strokeStyle = color.value;
    ctx.value.lineWidth = thiccBrush.value;
  }
  if (props.choose == "eraser") {
    ctx.value.globalCompositeOperation = "destination-out";
    ctx.value.lineWidth = thiccEraser.value;
    ctx.value.strokeStyle = "rgba(0,0,0,1)";
  }
  ctx.value.lineCap = "round";
  ctx.value.lineTo(e.clientX - offsetX.value, e.clientY - offsetY.value);
  if (props.choose != "line") ctx.value.stroke();
}

watch(deleteValue, (n) => {
  if (n && ctx.value && workspace.value) {
    deleteValue.value = false;
    ctx.value.clearRect(0, 0, workspace.value.width, workspace.value.height);
  }
});

onMounted(() => {
  document.getElementsByTagName("html")[0].style.overflow = "hidden";
  window.scrollTo(0, 0);

  if (!workspace.value || !workspaceBack.value) return;
  ctx.value = workspace.value.getContext("2d");
  ctxBack.value = workspaceBack.value.getContext("2d");
  if (ctx.value == null || ctxBack.value == null) return;

  const task = new Image();
  task.src = props.imgUrl!;
  const ratio = task.width / task.height;
  ctxBack.value.drawImage(task, 0, 0, 250, 250 / ratio);

  const bounding = workspace.value.getBoundingClientRect();
  offsetX.value = bounding.left;
  offsetY.value = bounding.top;
});
onUnmounted(() => {
  document.getElementsByTagName("html")[0].style.overflow = "auto";
});
</script>

<style scoped lang="scss">
.draw {
  &.draw {
    cursor: cell;
  }
  &.cursor {
    cursor: default;
  }
  &.text {
    cursor: text;
  }
  &.line {
    cursor: crosshair;
  }
  &.eraser {
    cursor: cell;
  }

  width: v-bind(w);
  height: v-bind(h);
  position: relative;
  .task__workspace__back {
    z-index: 1;
    border: 1px solid transparent;
    position: absolute;
    top: 0;
    left: 0;
  }

  .task__workspace {
    z-index: 3;
    position: absolute;
    top: 0;
    left: 0;
    border: 1px solid black;
  }
}
</style>
