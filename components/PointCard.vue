<template>
  <div class="customer container" ref="el" @click="handleClick">
    <img src="/assets/card.png" />
    <div
      class="box"
      :style="{
        top: y - stampSize / 2 + 'px',
        left: x - stampSize / 2 + 'px',
        width: `${stampSize}px`,
        height: `${stampSize}px`,
      }"
    ></div>
    <div
      v-for="(element, index) in elements"
      :key="index"
      class="created-element"
      :style="{
        top: `${element.y - stampSize / 2}px`,
        left: `${element.x - stampSize / 2}px`,
        width: `${stampSize}px`,
        height: `${stampSize}px`,
      }"
    >
      <img src="/assets/stamp.png" />
    </div>
  </div>
</template>

<script  setup>
const el = ref(null);
const { width, height } = useElementSize(el);
const stampSize = computed(() => width.value * 0.18);
const { isOutside, elementX, elementY, elementHeight, elementWidth } =
  useMouseInElement(el);
const x = computed(() => {
  return Math.min(
    Math.max(elementX.value, stampSize.value / 2),
    elementWidth.value - stampSize.value / 2
  );
});

const y = computed(() => {
  return Math.min(
    Math.max(elementY.value, stampSize.value / 2),
    elementHeight.value - stampSize.value / 2
  );
});
const elements = reactive([]);

const handleClick = (event) => {
  const containerRect = el.value.getBoundingClientRect();
  let mouseX = event.clientX - containerRect.left;
  let mouseY = event.clientY - containerRect.top;

  const elementWidth = 50; // Assuming the new element's width is 50px
  const elementHeight = 50; // Assuming the new element's height is 50px

  // Constrain the initial position within the container
  mouseX = Math.min(
    Math.max(mouseX, stampSize.value / 2),
    containerRect.width - elementWidth
  );
  mouseY = Math.min(
    Math.max(mouseY, stampSize.value / 2),
    containerRect.height - elementHeight
  );

  const collidingIndexes = [];
  elements.forEach((el, index) => {
    const dx = mouseX - el.x;
    const dy = mouseY - el.y;
    const distance = Math.sqrt(dx * dx + dy * dy);
    if (distance < stampSize.value) {
      collidingIndexes.push(index);
    }
  });

  // Remove colliding elements
  for (let i = collidingIndexes.length - 1; i >= 0; i--) {
    elements.splice(collidingIndexes[i], 1);
  }

  // Add the new element to the elements array
  elements.push({ x: mouseX, y: mouseY });
};
</script>

<style>
div.customer {
  border: 10px solid greenyellow;
}
.created-element {
  position: absolute;
  border-radius: 50%;
  /* background-color: blue; */
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
}
.container {
  position: relative;
}
.box {
  border-radius: 50%;

  border: 3px solid green;
  position: absolute;
  top: 5px;
}
</style>
