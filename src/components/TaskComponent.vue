<template>
  <div class="task has-text-dark">
    <div class="task__image" @click="workspaceActive = !workspaceActive">
      <img :src="urlPlaceholder" alt="task" />
    </div>
    <BaseModal v-model:modal-active="workspaceActive" v-if="workspaceActive">
      <div class="container">
        <canvas
          @mousedown="onMouseDown"
          @mouseup="onMouseUp"
          @mousemove="onMouseMove"
          @mouseout="onMouseUp"
          ref="workspace"
          class="task__workspace"
          width="1200"
          height="800"
        ></canvas>
      </div>
    </BaseModal>
  </div>
</template>

<script setup lang="ts">
import BaseModal from "./BaseModal.vue";
import { ref, computed, onMounted } from "vue";

const props = defineProps<{ imgUrl?: string }>();

const urlPlaceholder = ref(
  props.imgUrl ||
    "https://otkrit-ka.ru/uploads/posts/2021-11/krasivye-foto-kartinki-znak-voprosa-39.jpg"
);
const workspaceActive = ref(false);

const workspace = ref<HTMLCanvasElement>();
let workspaceCTX = computed(() => workspace.value?.getContext("2d"));
const drawing = ref(false);
const lineWidth = ref(5);
const x = ref(0);
const y = ref(0);
const offsetX = computed(() => workspace.value?.offsetLeft);
const offsetY = computed(() => workspace.value?.offsetTop);

function onMouseDown(ev: MouseEvent) {
  console.log("drawning");
  drawing.value = true;
  x.value = ev.clientX;
  y.value = ev.clientY;
}
function onMouseUp() {
  const workspaceCTX = workspace.value?.getContext("2d");
  console.log("stop");
  drawing.value = false;
  workspaceCTX?.stroke();
  workspaceCTX?.beginPath();
}
function onMouseMove(ev: MouseEvent) {
  const workspaceCTX = workspace.value?.getContext("2d");
  if (!drawing.value) return;
  if (!workspaceCTX) return;
  workspaceCTX.lineWidth = lineWidth.value;
  workspaceCTX.lineCap = "round";
  workspaceCTX.lineTo(ev.clientX - (offsetX.value || 0), ev.clientY);
  workspaceCTX.stroke();
  console.log("aboba");
}

onMounted(() => {
  if (!workspace.value) return;
  workspace.value.width = window.innerWidth - (offsetX.value || 0);
  workspace.value.height = window.innerHeight - (offsetY.value || 0);
});
</script>

<style scoped lang="scss">
.task {
  &__image {
    > img {
      width: 300px;
    }
  }
}
</style>
