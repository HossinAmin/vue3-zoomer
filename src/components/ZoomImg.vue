<template>
  <div class="relative">
    <DragZoomImg
      v-if="isDrag"
      v-model:current-scale="currentScale"
      v-model:zoomed-img-offset="zoomedImgOffset"
      v-model:current-scale="scale"
      v-bind="props"
      ref="zoomComponent"
      class="h-full w-full"
    />

    <MoveZoomImg
      v-else
      v-model:zoomed-img-offset="zoomedImgOffset"
      v-model:current-scale="scale"
      v-bind="props"
      ref="zoomComponent"
      class="h-full w-full"
    />

    <ZoomButtons
      v-if="showZoomBtns"
      v-model:zoomed-img-offset="zoomedImgOffset"
      v-model:current-scale="currentScale"
      v-bind="props"
      :max-zoom="currentScale === zoomScale"
      :min-zoom="currentScale === 1"
      @zoomIn="handleZoomIn"
      @zoomOut="handleZoomOut"
    />

    <ZoomMap
      v-if="showImgMap"
      v-show="windowPosition"
      class="absolute bottom-[28%] left-0 box-content h-[25%] w-[25%] border-8 border-transparent outline outline-2 outline-offset-[-8px] outline-white"
      :src="src"
      :zoom-scale="scale"
      :position="windowPosition"
      @update:position="updateOffset"
    />
  </div>
</template>

<script setup lang="ts">
import { type PropType, computed, ref } from "vue";
import type { PositionType } from "~/types";
import { offset2pos, pos2offset } from "~/utils/zoom";
import DragZoomImg from "~/components/core/DragZoomImg.vue";
import MoveZoomImg from "~/components/core/MoveZoomImg.vue";
import ZoomMap from "~/components/core/ZoomMap.vue";

const props = defineProps({
  src: {
    type: String,
    required: true,
  },
  zoomScale: {
    type: Number,
    default: 2,
  },
  trigger: {
    type: String as PropType<"click" | "hover">,
    default: "click",
  },
  zoomType: {
    type: String as PropType<"move" | "drag">,
    default: "move",
  },
  step: {
    type: Number,
  },
  persist: {
    type: Boolean,
  },
  showZoomBtns: {
    type: Boolean,
  },
  showImgMap: {
    type: Boolean,
  },
});

const currentScale = ref(1);
const zoomedImgOffset = ref({ left: 0, top: 0 });
const scale = ref(1);

const isDrag = computed(
  () => props.zoomType === "drag" || window.innerWidth < 768,
);

const windowPosition = computed(() => {
  if (scale.value !== 1) {
    //Multiply scale by 4 because the map window is quarter the map container
    return offset2pos(zoomedImgOffset.value, scale.value * 4);
  }
});

const zoomComponent = useTemplateRef("zoomComponent");

const handleZoomIn = () => {
  if (zoomComponent.value) {
    zoomComponent.value.zoomDir = "IN";
    zoomComponent.value.multiZoom();
  }
};

const handleZoomOut = () => {
  if (zoomComponent.value) {
    zoomComponent.value.zoomDir = "OUT";
    zoomComponent.value.multiZoom();
  }
};

const updateOffset = (newPosition?: PositionType) => {
  //Multiply scale by 4 because the map window is quarter the map container
  if (newPosition)
    zoomedImgOffset.value = pos2offset(newPosition, scale.value * 4);
};
</script>
