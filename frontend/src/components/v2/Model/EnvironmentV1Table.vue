<template>
  <BBGrid
    :column-list="columnList"
    :data-source="environmentList"
    :show-header="true"
    :show-placeholder="true"
    class="border-y"
    @click-row="clickEnvironment"
  >
    <template #item="{ item: environment }: EnvironmentRow">
      <div class="bb-grid-cell text-gray-500">
        <span class="">#{{ environment.uid }}</span>
      </div>
      <div class="bb-grid-cell">
        <EnvironmentV1Name :environment="environment" :link="false" />
      </div>
    </template>
  </BBGrid>
</template>

<script lang="ts" setup>
import { computed } from "vue";
import { useI18n } from "vue-i18n";
import { useRouter } from "vue-router";
import { BBGridColumn, BBGridRow, BBGrid } from "@/bbkit";
import { Environment } from "@/types/proto/v1/environment_service";
import { EnvironmentV1Name } from ".";

export type EnvironmentRow = BBGridRow<Environment>;

defineProps<{
  environmentList: Environment[];
}>();

const router = useRouter();

const { t } = useI18n();

const columnList = computed((): BBGridColumn[] => [
  {
    title: t("common.id"),
    width: "minmax(auto, 10rem)",
  },
  {
    title: t("common.name"),
    width: "1fr",
  },
]);

const clickEnvironment = function (
  environment: Environment,
  section: number,
  row: number,
  e: MouseEvent
) {
  const url = `/${environment.name}`;
  if (e.ctrlKey || e.metaKey) {
    window.open(url, "_blank");
  } else {
    router.push(url);
  }
};
</script>
