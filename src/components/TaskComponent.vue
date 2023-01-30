<template>
  <div class="task has-text-dark">
    <div class="task__image" @click="workspaceActive = !workspaceActive">
      <img :src="urlPlaceholder" alt="task" />
    </div>
    <BaseModal v-model:modal-active="workspaceActive">
      <canvas class="task__workspace" height="800" width="1200"></canvas>
    </BaseModal>
  </div>
</template>

<script setup lang="ts">
import BaseModal from "./BaseModal.vue";
import { ref } from "vue";

const props = defineProps<{ imgUrl?: string }>();

const urlPlaceholder = ref(
  props.imgUrl ||
    "https://otkrit-ka.ru/uploads/posts/2021-11/krasivye-foto-kartinki-znak-voprosa-39.jpg"
);

const workspaceActive = ref(false);
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
