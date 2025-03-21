<template>
  <BBModal :title="$t('role.import-from-role')" @close="$emit('cancel')">
    <div class="w-96 mb-2 space-y-2">
      <div>
        <p class="textlabel mb-1">{{ $t("role.select-role") }}</p>
        <NSelect
          v-model:value="state.selectedRole"
          :options="availableRoleOptions"
          :placeholder="$t('role.select-role')"
        />
      </div>
      <template v-if="selectedRole">
        <p class="textinfolabel">
          {{ displayRoleDescription(selectedRole.name) }}
        </p>
        <div>
          <p class="textlabel mb-1">
            {{ $t("common.permissions") }} ({{
              filterDisplayPermissions.length
            }})
          </p>
          <div class="max-h-[10em] overflow-auto border rounded p-2">
            <p
              v-for="permission in filterDisplayPermissions"
              :key="permission"
              class="text-sm leading-5"
            >
              {{ displayPermissionTitle(permission) }}
            </p>
          </div>
        </div>
      </template>
      <div class="!mt-4 w-full flex flex-row justify-end items-center gap-2">
        <NButton @click.prevent="$emit('cancel')">
          {{ $t("common.cancel") }}
        </NButton>
        <NButton
          :disabled="!allowConfirm"
          type="primary"
          @click.prevent="handleConfirm"
        >
          {{ $t("common.confirm") }}
        </NButton>
      </div>
    </div>
  </BBModal>
</template>

<script lang="ts" setup>
import { NSelect, NButton } from "naive-ui";
import { computed, reactive } from "vue";
import { BBModal } from "@/bbkit";
import { useRoleStore } from "@/store";
import { displayRoleTitle, displayRoleDescription } from "@/utils";
import { displayPermissionTitle } from "@/utils/permission";

interface LocalState {
  selectedRole?: string;
}

const emit = defineEmits<{
  (event: "cancel"): void;
  (event: "import", permissions: string[]): void;
}>();

const roleStore = useRoleStore();
const state = reactive<LocalState>({});

const availableRoleOptions = computed(() => {
  const roles = roleStore.roleList.map((role) => role.name);
  return roles.map((role) => ({
    label: displayRoleTitle(role),
    value: role,
  }));
});

const selectedRole = computed(() => {
  return roleStore.roleList.find((role) => role.name === state.selectedRole);
});

const filterDisplayPermissions = computed(() => {
  return selectedRole.value?.permissions || [];
});

const allowConfirm = computed(() => {
  return !!selectedRole.value;
});

const handleConfirm = () => {
  if (!allowConfirm.value) {
    return;
  }

  emit("import", selectedRole.value?.permissions || []);
};
</script>
