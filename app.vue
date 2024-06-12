<template>
  <div class="customer container" ref="el" @click="handleClick">
    <div class="box" :style="{ top: y - 25 + 'px', left: x - 25 + 'px' }"></div>
    <div
      v-for="(element, index) in elements"
      :key="index"
      class="created-element"
      :style="{ top: `${element.y-25}px`, left: `${element.x-25}px` }"
    >
      New Element
    </div>
  </div>
  {{ elementY }}
  {{ elementX }}
  {{ isOutside }}
</template>

<script setup>
const el = ref(null);

const { isOutside, elementX, elementY, elementHeight, elementWidth } =
  useMouseInElement(el);
const x = computed(() => {
  return Math.min(Math.max(elementX.value, 25), elementWidth.value - 25);
});

const y = computed(() => {
  return Math.min(Math.max(elementY.value, 25), elementHeight.value - 25);
});
const elements = reactive([]);

const handleClick = (event) => {
      const containerRect = el.value.getBoundingClientRect();
      let mouseX = event.clientX - containerRect.left;
      let mouseY = event.clientY - containerRect.top;

      const elementWidth = 50;  // Assuming the new element's width is 50px
      const elementHeight = 50; // Assuming the new element's height is 50px

      // Constrain the initial position within the container
      mouseX = Math.min(Math.max(mouseX, 25), containerRect.width - elementWidth);
      mouseY = Math.min(Math.max(mouseY, 25), containerRect.height - elementHeight);

      // Check for collisions and remove colliding elements
      const collidingIndexes = [];
      elements.forEach((el, index) => {
        const isColliding = !(mouseX + elementWidth < el.x || mouseX > el.x + elementWidth ||
                              mouseY + elementHeight < el.y || mouseY > el.y + elementHeight);
        if (isColliding) {
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
  width: 100%;
  height: 30vh;
  border: 3px solid;
}
.created-element {
  position: absolute;
  width: 50px;
  height: 50px;
  background-color: blue;
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
}
.container {
  position: relative;
  top: 250px;
}
.box {
  width: 50px;
  height: 50px;
  border: 3px solid green;
  position: absolute;
  top: 5px;
}
</style>
