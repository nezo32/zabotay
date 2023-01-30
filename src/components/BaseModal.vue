<template>
  <Teleport to="body">
    <div class="modal has-text-dark" v-if="modalActive">
      <div class="modal__inner">
        <slot />
        <div
          class="modal__inner__exit"
          @click="$emit('update:modalActive', false)"
        >
          <img src="@/assets/img/backspaceicon.png" alt="back" />
          Вернуться к заданию
        </div>
      </div>
    </div>
  </Teleport>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted } from "vue";

defineProps<{ modalActive?: boolean }>();
defineEmits(["update:modalActive"]);

onMounted(() => {
  const app = document.getElementById("app");
  if (app) app.style.overflow = "hidden";
});
onUnmounted(() => {
  const app = document.getElementById("app");
  if (app) app.style.overflow = "auto";
});
</script>

<style scoped lang="scss">
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  min-height: 100vh;
  overflow-y: auto;

  z-index: 999;

  background: rgba(255, 255, 255, 0.48);

  display: flex;
  align-items: center;
  justify-content: center;

  &__inner {
    min-width: 300px;

    position: relative;

    background: white;
    box-shadow: 2px 2px 6px rgba(11, 20, 64, 0.2);
    border-radius: 24px;
    padding: 30px;

    &__exit {
      display: flex;
      flex-direction: row;
      align-items: center;
      gap: 5px;

      > img {
        width: 25px;
      }

      position: absolute;
      top: 7px;
      left: 10px;
    }
  }
}
</style>
