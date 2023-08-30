<template>
  <div class="px-2 py-4">
    <div class="mb-1 px-2 text-stone-500">Tags</div>
    <div class="mb-2 flex flex-wrap gap-2 px-2">
      <DTag v-for="tag in tags" :color="tag.color" @remove="toggleTag(tag)" :key="tag.id" removable>
        {{ tag.name }}
      </DTag>
    </div>
    <button type="button" @click="addTag" class="flex w-full items-center gap-2 rounded-lg px-2 py-1.5 text-stone-500">
      <Plus :size="18" />
      <div>Tag hinzufügen</div>
    </button>
    <template v-if="sheetOpen">
      <MSheet @close="sheetOpen = false">
        <div class="p-2 text-sm">
          <div class="mt-3 flex max-h-[calc(100vh-125px)] flex-col gap-2 overflow-scroll pb-4">
            <div
              v-for="tag in data?.tags.edges"
              class="flex items-center justify-between rounded-lg border border-stone-200 px-2 py-2"
              @click="toggleTag(tag)"
            >
              <div>{{ tag?.name }}</div>
              <div class="rounded-md hover:bg-stone-100">
                <Check v-if="activeTag(tag)" :size="20" class="stroke-stone-500" />
              </div>
            </div>
          </div>
        </div>
      </MSheet>
    </template>
  </div>
</template>

<script lang="ts" setup>
import MSheet from "@/components/mobile/m-sheet.vue";
import DTag from "@/components/d-tag/d-tag.vue";
import { useVModel } from "@vueuse/core";
import { Plus, Check } from "lucide-vue-next";
import { ref } from "vue";
import { graphql } from "@/gql";
import { useQuery } from "@urql/vue";

const sheetOpen = ref(false);

function addTag() {
  sheetOpen.value = true;
}

const props = defineProps<{
  // modelValue: Tag[];
  modelValue: any;
}>();
const emit = defineEmits(["update:modelValue"]);

const tags = useVModel(props, "modelValue", emit);

const { data } = useQuery({
  query: graphql(`
    query tags {
      tags {
        edges {
          id
          name
          color
        }
      }
    }
  `),
});

function toggleTag(tag: any) {
  if (tags.value.find((p: any) => p.id === tag.id)) {
    tags.value = tags.value.filter((p: any) => p.id !== tag.id);
  } else {
    tags.value = [...tags.value, tag];
  }
}

function activeTag(tag: any) {
  return tags.value.find((p: any) => p.id === tag.id);
}
</script>