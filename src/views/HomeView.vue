<template>
  <div class="home has-text-dark">
    <div class="home__header">
      <div class="home__header__inner box">
        <h2 class="is-size-2">Задача</h2>
        <span class="is-size-4">Дополнительная информация</span>
      </div>
    </div>
    <div class="home__body" ref="bodyContent">
      <div class="home__body__content">
        <span v-for="i in 20" class="is-size-5" :key="i"
          >Lorem ipsum dolor sit, amet consectetur adipisicing elit. Placeat sit
          architecto molestiae perferendis temporibus quis, maxime sint cumque
          quidem libero asperiores quibusdam delectus similique eius voluptate
          praesentium illum quas porro?<br
        /></span>
      </div>
      <div class="home__body__content" style="flex-shrink: 0">
        <TaskComponent
          :width="width"
          :height="height"
          img-url="https://ru-static.z-dn.net/files/d70/b2d7e6a8f30660b99d6d09f2520841d9.jpg"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import TaskComponent from "@/components/TaskComponent.vue";
import { ref, onMounted } from "vue";

const bodyContent = ref<HTMLElement>();

const width = ref(0);
const height = ref(0);

onMounted(() => {
  if (!bodyContent.value) return;
  width.value = bodyContent.value.clientWidth - 40;
  height.value = 720;
});
</script>

<style scoped lang="scss">
.home {
  display: flex;
  flex-direction: column;
  gap: 30px;

  &__header {
    &__inner {
      border-radius: 20px;
    }
  }

  &__body {
    height: max-content;
    display: flex;
    flex-direction: row;
    gap: 10px;
    &__content {
      padding: 20px;
      background: white;
      border-radius: 20px;
      box-shadow: 0 0.5em 1em -0.125em rgb(10 10 10 / 10%),
        0 0px 0 1px rgb(10 10 10 / 2%);
      height: fit-content;
      > img {
        width: 300px;
      }
    }
  }
}
</style>
