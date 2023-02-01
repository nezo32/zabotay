<template>
  <div
    ref="container"
    class="draw"
    :class="{
      draw: props.choose == 'brush',
      cursor: props.choose == 'cursor',
      text: props.choose == 'text',
      line: props.choose == 'line',
      eraser: props.choose == 'eraser',
    }"
    @mousedown="dragDown"
    @mousemove="dragMove"
    @mouseup="dragUp"
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

const container = ref<HTMLElement>();

const workspace = ref<HTMLCanvasElement>();
const workspaceBack = ref<HTMLCanvasElement>();
const ctx = ref<CanvasRenderingContext2D | null>(null);
const ctxBack = ref<CanvasRenderingContext2D | null>(null);

const offsetX = ref(0);
const offsetY = ref(0);

const color = computed(() => props.color || "black");
const thiccBrush = computed(() => props.brushWidth || 2);
const thiccEraser = computed(() => props.eraserWidth || 2);

const dragging = ref(false);

const drawing = ref(false);
const line = ref(false);
const text = ref(false);

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
  if (props.choose == "text") {
    if (!container.value) return;
    if ((e.target as HTMLElement).tagName == "INPUT") return;
    text.value = true;
    startX.value = e.clientX - offsetX.value;
    startY.value = e.clientY - offsetY.value;
    const tmpElement = document.createElement("input");
    tmpElement.className = "draggable__input";
    tmpElement.style.position = "absolute";
    tmpElement.style.top = `${startY.value}px`;
    tmpElement.style.left = `${startX.value}px`;
    tmpElement.placeholder = "Введите текст";
    container.value.appendChild(tmpElement);
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

const currentInput = ref<HTMLInputElement>();

function dragDown(e: MouseEvent) {
  if (
    (e.target as HTMLElement).tagName == "INPUT" &&
    props.choose == "cursor"
  ) {
    dragging.value = true;
    currentInput.value = e.target as HTMLInputElement;
    currentInput.value.disabled = true;
  }
}
function dragMove(e: MouseEvent) {
  if (!currentInput.value) return;
  if (dragging.value) {
    currentInput.value.style.top = `${e.clientY - offsetY.value}px`;
    currentInput.value.style.left = `${e.clientX - offsetX.value}px`;
  }
}
function dragUp() {
  if (!currentInput.value) return;
  dragging.value = false;
  currentInput.value.disabled = false;
}

document.addEventListener("keydown", (el: KeyboardEvent) => {
  if (
    currentInput.value &&
    (el.key == "Delete" || el.key == "Backspace") &&
    props.choose == "cursor"
  ) {
    currentInput.value.remove();
  }
});

watch(deleteValue, (n) => {
  if (n && ctx.value && workspace.value) {
    deleteValue.value = false;
    ctx.value.clearRect(0, 0, workspace.value.width, workspace.value.height);
    [...document.getElementsByClassName("draggable__input")].map(
      (el) => el && el.remove()
    );
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
  ctxBack.value.drawImage(task, 0, 0, 300, 300 / ratio);

  const bounding = workspace.value.getBoundingClientRect();
  offsetX.value = bounding.left;
  offsetY.value = bounding.top;
});
onUnmounted(() => {
  document.getElementsByTagName("html")[0].style.overflow = "auto";
});
</script>

<style lang="scss">
.draw {
  .draggable__input {
    z-index: 4;
    padding: 5px;
    outline: none;
    &:hover {
      border: 1px solid black;
    }
    border: 1px solid transparent;
    background: transparent;
    font-size: 18px;
    font-weight: 400;
    line-height: 120%;
  }

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
