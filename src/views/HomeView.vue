<template>
  <div class="home has-text-dark">
    <div class="home__header">
      <div class="home__header__inner box">
        <h2 class="is-size-2">Задача</h2>
        <span class="is-size-4">Дополнительная информация</span>
      </div>
    </div>
    <div class="home__body">
      <div class="home__body__content">
        <span v-for="i in 12" class="is-size-5" :key="i"
          >Lorem ipsum dolor sit, amet consectetur adipisicing elit. Placeat sit
          architecto molestiae perferendis temporibus quis, maxime sint cumque
          quidem libero asperiores quibusdam delectus similique eius voluptate
          praesentium illum quas porro?</span
        >
      </div>
      <div class="home__body__content" style="flex-shrink: 0">
        <TaskComponent
          img-url="https://ru-static.z-dn.net/files/d70/b2d7e6a8f30660b99d6d09f2520841d9.jpg"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import TaskComponent from "@/components/TaskComponent.vue";
</script>

<style scoped lang="scss">
.home {
  display: flex;
  flex-direction: column;
  gap: 30px;

  &__body {
    height: max-content;
    display: flex;
    flex-direction: row;
    gap: 10px;
    &__content {
      > img {
        width: 300px;
      }
    }
  }
}
</style>
