<script lang="ts" setup>
import { VModal, VEntity, VEntityField } from "@halo-dev/components";
import { ref, watch } from "vue";

const props = withDefaults(
  defineProps<{
    visible: boolean;
  }>(),
  {
    visible: false,
  }
);

const emit = defineEmits<{
  (e: "update:visible", visible: boolean): void;
}>();

const globalSearchInput = ref<HTMLInputElement | null>(null);
const keyword = ref("");

const selectedIndex = ref(0);
const searchResults = ref([]);

const handleKeydown = (e: KeyboardEvent) => {
  if (!props.visible) {
    return;
  }

  const { key, ctrlKey } = e;

  if (key === "ArrowUp" || (key === "k" && ctrlKey)) {
    selectedIndex.value = Math.max(0, selectedIndex.value - 1);
    e.preventDefault();
  }

  if (key === "ArrowDown" || (key === "j" && ctrlKey)) {
    selectedIndex.value = Math.min(
      searchResults.value.length - 1,
      selectedIndex.value + 1
    );
    e.preventDefault();
  }

  if (key === "Enter") {
    const searchResult = searchResults.value[selectedIndex.value];
    console.log(searchResult);
  }

  if (key === "Escape") {
    onVisibleChange(false);
    e.preventDefault();
  }
};

watch(
  () => selectedIndex.value,
  (index) => {
    if (index > 0) {
      document.getElementById(`search-item-${index}`)?.scrollIntoView();
      return;
    }
    document.getElementById("search-input")?.scrollIntoView();
  }
);

watch(
  () => keyword.value,
  () => {
    selectedIndex.value = 0;
  }
);

watch(
  () => props.visible,
  (visible) => {
    if (visible) {
      setTimeout(() => {
        globalSearchInput.value?.focus();
      }, 100);

      document.addEventListener("keydown", handleKeydown);
    } else {
      document.removeEventListener("keydown", handleKeydown);
      keyword.value = "";
      selectedIndex.value = 0;
    }
  }
);

const onVisibleChange = (visible: boolean) => {
  emit("update:visible", visible);
};
</script>

<template>
  <VModal
    :visible="visible"
    :body-class="['!p-0']"
    :mount-to-body="true"
    :width="650"
    :centered="false"
    @update:visible="onVisibleChange"
  >
    <div id="search-input" class="border-b border-gray-100 px-4 py-2.5">
      <input
        ref="globalSearchInput"
        v-model="keyword"
        placeholder="输入关键词以搜索"
        class="w-full py-1 text-base outline-none"
        autocomplete="off"
        autocorrect="off"
        spellcheck="false"
      />
    </div>
    <div class="px-2 py-2.5">
      <div
        v-if="!searchResults.length"
        class="flex items-center justify-center text-sm text-gray-500"
      >
        <span>没有搜索结果</span>
      </div>
      <ul
        v-if="searchResults.length > 0"
        class="box-border flex h-full w-full flex-col gap-0.5"
        role="list"
      >
        <li
          v-for="(item, itemIndex) in searchResults"
          :id="`search-item-${itemIndex}`"
          :key="itemIndex"
        >
          <VEntity
            class="rounded-md px-2 py-2.5 hover:bg-gray-100"
            :class="{ 'bg-gray-100': selectedIndex === itemIndex }"
          >
            <template #start>
              <VEntityField :title="item"></VEntityField>
            </template>
            <template #end>
              <VEntityField :description="item"></VEntityField>
            </template>
          </VEntity>
        </li>
      </ul>
    </div>
    <div class="border-t border-gray-100 px-4 py-2.5">
      <div class="flex items-center justify-end">
        <span class="mr-1 text-xs text-gray-600">选择</span>
        <kbd
          class="mr-1 w-5 rounded border p-0.5 text-center text-[10px] text-gray-600 shadow-sm"
        >
          ↑
        </kbd>
        <kbd
          class="mr-5 w-5 rounded border p-0.5 text-center text-[10px] text-gray-600 shadow-sm"
        >
          ↓
        </kbd>
        <span class="mr-1 text-xs text-gray-600">确认</span>
        <kbd
          class="mr-5 rounded border p-0.5 text-[10px] text-gray-600 shadow-sm"
        >
          Enter
        </kbd>
        <span class="mr-1 text-xs text-gray-600">关闭</span>
        <kbd class="rounded border p-0.5 text-[10px] text-gray-600 shadow-sm">
          Esc
        </kbd>
      </div>
    </div>
  </VModal>
</template>
