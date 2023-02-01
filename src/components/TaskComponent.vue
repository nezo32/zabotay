<template>
  <div class="task has-text-dark">
    <div class="task__image" @click="workspaceActive = !workspaceActive">
      <img :src="urlPlaceholder" alt="task" />
      <span class="is-size-6">Открыть рабочее пространство</span>
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
            <button
              class="has-text-dark is-primary button"
              ref="paletteContainer"
            >
              <PaletteIcon @click="paletteActive = !paletteActive" />
              <div class="palette" v-if="paletteActive">
                <span
                  class="is-size-6"
                  v-for="(v, i) of colors"
                  :key="i"
                  @click="
                    color = v;
                    paletteActive = false;
                  "
                  :class="{ active: v == color }"
                >
                  {{ v }}
                </span>
              </div>
            </button>
            <button
              class="has-text-dark is-primary button"
              ref="brushContainer"
            >
              <BrushIcon
                @click="
                  choose = 'brush';
                  brushActive = !brushActive;
                "
              />
              <div class="brush" v-if="brushActive">
                <span
                  class="is-size-6"
                  v-for="(v, i) of widths"
                  :key="i"
                  @click="
                    widthBrush = v;
                    brushActive = false;
                  "
                  :class="{ active: v == widthBrush }"
                >
                  <p>{{ v }}</p>
                </span>
              </div>
            </button>
            <button class="has-text-dark is-primary button" ref="textContainer">
              <TextIcon @click="choose = 'text'" />
            </button>
            <button class="has-text-dark is-primary button">
              <DeleteIcon @click="deleteActive = true" />
            </button>
            <button class="has-text-dark is-primary button" ref="lineContainer">
              <ArrowIcon
                @click="
                  choose = 'line';
                  lineActive = !lineActive;
                "
              />
              <div class="arrow" v-if="lineActive">
                <span
                  class="is-size-6"
                  v-for="(v, i) of widths"
                  :key="i"
                  @click="
                    widthBrush = v;
                    lineActive = false;
                  "
                  :class="{ active: v == widthBrush }"
                >
                  <p>{{ v }}</p>
                </span>
              </div>
            </button>
            <button class="has-text-dark is-primary button">
              <CursorIcon @click="choose = 'cursor'" />
            </button>
            <button
              class="has-text-dark is-primary button"
              ref="eraserContainer"
            >
              <EraserIcon
                @click="
                  choose = 'eraser';
                  eraserActive = !eraserActive;
                "
              />
              <div class="eraser" v-if="eraserActive">
                <span
                  class="is-size-6"
                  v-for="(v, i) of widths"
                  :key="i"
                  @click="
                    widthEraser = v;
                    eraserActive = false;
                  "
                  :class="{ active: v == widthEraser }"
                >
                  {{ v }}
                </span>
              </div>
            </button>
          </div>
        </div>
        <DrawComponent
          :choose="choose"
          :color="color"
          :img-url="imgUrl"
          :width="props.width"
          :height="props.height"
          :brush-width="widthBrush"
          :eraser-width="widthEraser"
          v-model:delete="deleteActive"
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
import { clickOutsideElement } from "../clickOutsideElement";

const paletteContainer = ref();
const brushContainer = ref();
const textContainer = ref();
const lineContainer = ref();
const eraserContainer = ref();

const choose = ref<"brush" | "eraser" | "line" | "text" | "cursor" | "cursor">(
  "brush"
);

const props = defineProps<{ imgUrl?: string; width: number; height: number }>();

const paletteActive = ref(false);
const brushActive = ref(false);
const textActive = ref(false);
const deleteActive = ref(false);
const lineActive = ref(false);
const cursorActive = ref(false);
const eraserActive = ref(false);

const colors = ref(["black", "green", "red", "blue"] as const);
type Colors = (typeof colors.value)[number];

const widths = ref([2, 4, 6, 8, 10, 12, 20, 48] as const);
type Widths = (typeof widths.value)[number];

const color = ref<Colors>("black");
const widthBrush = ref<Widths>(2);
const widthEraser = ref<Widths>(2);

const urlPlaceholder = ref(
  props.imgUrl ||
    "https://otkrit-ka.ru/uploads/posts/2021-11/krasivye-foto-kartinki-znak-voprosa-39.jpg"
);
const workspaceActive = ref(false);

clickOutsideElement(paletteContainer, () => {
  paletteActive.value = false;
});
clickOutsideElement(brushContainer, () => {
  brushActive.value = false;
});
clickOutsideElement(textContainer, () => {
  textActive.value = false;
});
clickOutsideElement(lineContainer, () => {
  lineActive.value = false;
});
clickOutsideElement(eraserContainer, () => {
  eraserActive.value = false;
});
</script>

<style scoped lang="scss">
.task {
  position: relative;

  &__image {
    padding: 20px;
    background: white;
    border-radius: 20px;
    position: relative;
    > img {
      width: 300px;
    }
    > span {
      position: absolute;
      bottom: -10px;
      right: 2px;
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
        cursor: pointer;
        position: absolute;
        z-index: 6;
        bottom: 10px;
        right: 10px;
      }

      &__toolbar {
        z-index: 6;
        width: fit-content;
        &__wrapper {
          display: flex;
          flex-direction: row;
          width: fit-content;
          gap: 5px;

          > button {
            cursor: pointer;
            position: relative;
            height: fit-content;
            padding: 0;

            > div {
              border-radius: 10px;
              background: #f5f6fa;
              padding: 10px;

              z-index: 98;
              position: absolute;
              bottom: 65px;
              right: 0;

              display: flex;
              flex-direction: column;
              gap: 5px;
              > span.active {
                color: rgb(80, 80, 80);
                border-radius: 5px;
                background: hsl(44deg, 100%, 77%) !important;
              }

              > span {
                &:hover {
                  color: white;
                  background: hsl(171deg, 100%, 41%);
                  border-radius: 5px;
                }
                padding: 2px 5px;
                text-align: start;
                > p {
                  width: 100%;
                  text-align: start;
                }
                width: 100%;
                min-width: 35px;
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
