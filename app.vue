<template>
  <div class="flex flex-row bg-transparent">
    <UContainer class="basis-1/5 flex flex-col py-3 gap-3">
      <ClientOnly>
        <UButton :icon="isDark ? 'i-heroicons-moon-20-solid' : 'i-heroicons-sun-20-solid'
          " color="gray" variant="ghost" aria-label="Theme" @click="isDark = !isDark" />
        <template #fallback>
          <div class="w-8 h-8" />
        </template>
      </ClientOnly>

      <UTabs :items="tabItems" v-model="tabSelect" orientation="vertical" v-if="tabItems.length > 0">
      </UTabs>

      <UButtonGroup orientation="horizontal">
        <UInput class="grow" v-model="CardName" />
        <UButton icon="i-heroicons-plus-circle-16-solid" @click="
          tabItems.push({
            label: CardName,
            stamp: [],
          })
          " />
      </UButtonGroup>

      <UBadge size="lg">集點卡</UBadge>
      <FileUpload v-model:target="Img.cardImg" />


      <UBadge size="lg">印章</UBadge>

      <FileUpload v-model:target="Img.stampImg" />
      <URange v-model="Img.stampSize" max="50" />

      <UButton v-if="tabItems.length > 0" icon="i-heroicons-document-duplicate" @click="copy(jsonToCsv(tabItems))">Copy
        Data
      </UButton>
      <UBadge class="text-center" v-if="tabItems.length > 0">
        {{ copied ? "Copied" : "Copy" }}
      </UBadge>
    </UContainer>
    <UContainer class="basis-4/5">
      <div v-for="(item, index) in tabItems">
        <PointCard @deleteCard="deleteCard(index)" v-if="index == tabSelect" :key="index" v-model:stampData="item.stamp"
          :Img="Img">
        </PointCard>
      </div>
    </UContainer>
  </div>

</template>

<script setup>
import { useStorage } from "@vueuse/core";
const Img = useStorage('Img', { cardImg: '', stampImg: '', stampSize: 0 })



const colorMode = useColorMode();
const isDark = computed({
  get() {
    return colorMode.value === "dark";
  },
  set() {
    colorMode.preference = colorMode.value === "dark" ? "light" : "dark";
  },
});

const CardName = ref(null);
const tabItems = useStorage("card-data", []);

const tabSelect = ref(-1);
const { text, copy, copied, isSupported } = useClipboard();
const deleteCard = (index) => {
  tabItems.value.splice(index, 1);

  tabSelect.value = -1;
};
const jsonToCsv = (json) => {
  // Define the CSV headers
  const headers = ["username", "stamp_count"];
  const csvRows = [headers.join(",")];

  // Process each entry in the JSON
  json.forEach((entry) => {
    const label = entry.label;
    const stampCount = entry.stamp.length > 5 ? 5 : entry.stamp.length;
    const row = [label, stampCount];
    csvRows.push(row.join(","));
  });

  // Join the rows with newlines
  return csvRows.join("\n");
};
</script>
<style></style>
