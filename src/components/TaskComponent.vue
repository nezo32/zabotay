<template>
  <div class="task has-text-dark">
    <div class="task__image" @click="workspaceActive = !workspaceActive">
      <img :src="urlPlaceholder" alt="task" />
    </div>
    <div class="task__container" v-if="workspaceActive">
      <div class="task__container__inner">
        <button
          class="button is-warning is-size-5"
          @click="workspaceActive = false"
        >
          Назад
        </button>
        <div class="task__container__inner__toolbar">
          <div class="task__container__inner__toolbar__wrapper">
            <button class="has-text-dark is-primary button">
              <PaletteIcon />
              <div class="palette">
                <!--eslint-disable-next-line prettier/prettier-->
                <span
                  v-for="i in 4"
                  :key="i"
                  :class="{
                    black: i == 1,
                    blue: i == 2,
                    red: i == 3,
                    green: i == 4,
                  }"
                ></span>
              </div>
            </button>
            <button class="has-text-dark is-primary button">
              <BrushIcon />
            </button>
            <button class="has-text-dark is-primary button">
              <TextIcon />
            </button>
            <button class="has-text-dark is-primary button">
              <DeleteIcon />
            </button>
            <button class="has-text-dark is-primary button">
              <ArrowIcon />
            </button>
            <button class="has-text-dark is-primary button">
              <CursorIcon />
            </button>
            <button class="has-text-dark is-primary button">
              <EraserIcon />
            </button>
          </div>
        </div>
        <DrawComponent
          :color="color"
          :img-url="imgUrl"
          :width="props.width"
          :height="props.height"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import DrawComponent from "./DrawComponent.vue";
import BrushIcon from "./icons/BrushIcon.vue";
import PaletteIcon from "./icons/PaletteIcon.vue";
import TextIcon from "./icons/TextIcon.vue";
import DeleteIcon from "./icons/DeleteIcon.vue";
import ArrowIcon from "./icons/ArrowIcon.vue";
import CursorIcon from "./icons/CursorIcon.vue";
import EraserIcon from "./icons/EraserIcon.vue";
import { ref } from "vue";

const props = defineProps<{ imgUrl?: string; width: number; height: number }>();

const color = ref<"black" | "green" | "red" | "blue">("black");

const urlPlaceholder = ref(
  props.imgUrl ||
    "https://otkrit-ka.ru/uploads/posts/2021-11/krasivye-foto-kartinki-znak-voprosa-39.jpg"
);
const workspaceActive = ref(false);
</script>

<style scoped lang="scss">
.task {
  position: relative;

  &__image {
    > img {
      width: 300px;
    }
  }

  &__container {
    font-size: 0;
    background-color: white;
    position: absolute;
    top: 0;
    right: 0;

    &__inner {
      position: relative;

      > button {
        position: absolute;
        bottom: 10px;
        right: 10px;
      }

      &__toolbar {
        width: fit-content;
        &__wrapper {
          display: flex;
          flex-direction: row;
          width: fit-content;
          gap: 5px;

          > button {
            position: relative;
            height: fit-content;
            padding: 0;

            .palette {
              z-index: 98;
              position: absolute;
              top: 15px;
              left: 0;

              > span {
                width: 10px;
                height: 10px;

                &.black {
                  background-color: black;
                }
              }
            }
            /* outline: none;
            border: none;
            border-radius: 50%;
            padding: 5px 6px; */
          }
        }

        position: absolute;
        bottom: 10px;
        left: 0;
        right: 0;
        text-align: center;
        margin: auto;
      }
    }
  }
}
</style>
