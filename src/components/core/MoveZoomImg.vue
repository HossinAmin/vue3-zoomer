<template>
  <div
    class="cursor-zoom-in overflow-clip border-none"
    ref="containerRef"
    :style="{ cursor: zoomDir === 'OUT' ? 'zoom-out' : 'zoom-in' }"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
    @mousemove="handleMouseMove"
    @click="handleClick"
  >
    <img
      class="h-full w-full object-fill"
      alt="zoom-image"
      :src="src"
      :style="{
        transform: `translate(${zoomedImgOffset.left}px, ${zoomedImgOffset.top}px) scale(${currentScale})`,
        transformOrigin: '0 0',
        transition: isTransition ? 'transform 100ms ease-in-out' : 'none',
      }"
    />
  </div>
</template>

<script setup lang="ts">
import { PropType, computed, useTemplateRef } from "vue";
import { calZoomedImgOffset } from "~/utils/zoom";
import { getRelCursorPosition } from "~/utils/cursorPosition";
import { useTransition } from "~/composables/useTransition";
import useMultiZoom from "~/composables/useMultiZoom";

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
  step: {
    type: Number,
  },
  presist: {
    type: Boolean,
  },
});

const currentScale = defineModel("currentScale", {
  default: 1,
});
const zoomedImgOffset = defineModel("zoomedImgOffset", {
  default: {
    left: 0,
    top: 0,
  },
});

const containerRef = useTemplateRef("containerRef");

const isZoomed = computed(() => currentScale.value > 1);

const { isTransition, startTransition } = useTransition();
const { zoomDir, multiStepZoomIn } = useMultiZoom(
  currentScale,
  zoomedImgOffset,
  containerRef,
  props.zoomScale,
);

const cursorStyle = computed(() => (isZoomed.value ? "zoom-out" : "zoom-in"));

const calculateZoomPosition = (x: number, y: number) => {
  const elementWidth = containerRef.value?.clientWidth ?? 1;
  const elementHeight = containerRef.value?.clientHeight ?? 1;

  // Calculate the ratio of the mouse position relative to the element's dimensions
  const xRatio = x / elementWidth;
  const yRatio = y / elementHeight;

  // Calculate the dimensions of the zoomed image
  const zoomedWidth = elementWidth * props.zoomScale;
  const zoomedHeight = elementHeight * props.zoomScale;

  let newLeft = -(zoomedWidth - elementWidth) * xRatio;
  let newTop = -(zoomedHeight - elementHeight) * yRatio;

  // Ensure left position does not move the image out of bounds
  newLeft = Math.max(Math.min(newLeft, 0), elementWidth - zoomedWidth);

  // Ensure top position does not move the image out of bounds
  newTop = Math.max(Math.min(newTop, 0), elementHeight - zoomedHeight);

  return { newLeft, newTop };
};

const handleMouseEnter = (event: MouseEvent) => {
  if (props.trigger === "hover") {
<<<<<<< HEAD
    currentScale.value = props.zoomScale;
    startTransition(250);

    const { pos: currentPos } = getRelCursorPosition(event, containerRef.value);

    // Calculate the new position for the zoomed image based on the current mouse coordinates
<<<<<<< HEAD
    const { newLeft, newTop } = calculateZoomPosition(
      cursorPosition.relativeX,
      cursorPosition.relativeY,
=======
    zoomedImgOffset.value = calZoomedImgOffset(
      currentPos,
      containerRef.value,
      props.zoomScale,
>>>>>>> ab76424 (utils name updates)
    );
=======
    const { pos: relPos } = getRelCursorPosition(event, containerRef.value);
    startTransition(150);
    multiStepZoomIn(currentScale.value, relPos, props.step ?? props.zoomScale);
>>>>>>> abe41e7 (optimize multi zoom functionality)
  }
};

const handleMouseLeave = () => {
  if (props.presist) return;
  resetPosition();
};

<<<<<<< HEAD
const handleClick = () => {
  // single click
  if (!props.step && props.trigger === "click") {
    if (!isZoomed.value) {
      currentScale.value = props.zoomScale;
<<<<<<< HEAD
      startTransition(250);

      zoomedImgOffset.value = calZoomedImgOffset(
        elementX.value,
        elementY.value,
        elementWidth.value,
        elementHeight.value,
=======
      startTransition(150);

      const { pos: cursorPosition } = getRelCursorPosition(
        event,
        containerRef.value,
      );

      zoomedImgOffset.value = calZoomedImgOffset(
        cursorPosition,
        containerRef.value,
>>>>>>> ab76424 (utils name updates)
        props.zoomScale,
      );
    } else {
      resetPosition();
    }
  }
  // multi click
  else if (props.step) {
<<<<<<< HEAD
    console.log("click");
=======
    const scale = zoomInOut(currentScale.value, props.zoomScale, props.step);
    const { pos: cursorPosition } = getRelCursorPosition(
      event,
      containerRef.value,
    );
>>>>>>> ab76424 (utils name updates)

    const scale = zoomInOut(currentScale.value, props.zoomScale, props.step);
    startTransition();
    currentScale.value = scale;
    zoomedImgOffset.value = calZoomedImgOffset(
<<<<<<< HEAD
      elementX.value,
      elementY.value,
      elementWidth.value,
      elementHeight.value,
=======
      cursorPosition,
      containerRef.value,
>>>>>>> ab76424 (utils name updates)
      scale,
    );
  }
=======
const handleClick = (event: MouseEvent) => {
  const { pos: relPos } = getRelCursorPosition(event, containerRef.value);
  startTransition(150);
  multiStepZoomIn(currentScale.value, relPos, props.step ?? props.zoomScale);
>>>>>>> abe41e7 (optimize multi zoom functionality)
};

const handleMouseMove = (event: MouseEvent) => {
  const { pos: cursorPosition, isOutside } = getRelCursorPosition(
    event,
    containerRef.value,
  );

<<<<<<< HEAD
  if (!cursorPosition.isOutside && isZoomed.value && !isTransition.value) {
    const { newLeft, newTop } = calculateZoomPosition(
      cursorPosition.relativeX,
      cursorPosition.relativeY,
=======
  if (!isOutside && isZoomed.value && !isTransition.value) {
    zoomedImgOffset.value = calZoomedImgOffset(
      cursorPosition,
      containerRef.value,
      currentScale.value,
>>>>>>> ab76424 (utils name updates)
    );
  }
};

const resetPosition = () => {
  isTransition.value = true;
  currentScale.value = 1;
  zoomedImgOffset.value = {
    left: 0,
    top: 0,
  };
};

const startTransition = () => {
  isTransition.value = true;
  setTimeout(() => (isTransition.value = false), 150);
};
</script>
