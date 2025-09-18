<script setup>
import OverdueTenantIcon from "../Assets/OverdueTenantIcon.vue";
import ActiveTenantIcon from "../Assets/ActiveTenantIcon.vue";
import TotalTenantIcon from "../Assets/TotalTenantIcon.vue";
const props = defineProps(["tenantCount", "variant"]);

const emit = defineEmits(["handleCardClick"]);
</script>

<template>
  <!-- Stats Cards -->
  <div
    @click="$emit('handleCardClick', variant)"
    class="bg-white overflow-hidden shadow rounded-lg hover:shadow-lg hover:bg-gray-50 cursor-pointer active:outline-none active:ring-2 active:ring-gray-200 transition"
  >
    <div class="p-5">
      <div class="flex items-center">
        <div class="flex-shrink-0">
          <TotalTenantIcon v-show="variant === 'total'" />
          <ActiveTenantIcon v-show="variant === 'active'" />
          <OverdueTenantIcon v-show="variant === 'overdue'" />
        </div>
        <div class="ml-5 w-0 flex-1">
          <dl>
            <dt class="text-sm font-medium text-gray-500 truncate">
              {{
                variant === "total"
                  ? "Total Tenants"
                  : variant === "overdue"
                  ? "Overdue Tenants"
                  : "Active Tenants"
              }}
            </dt>
            <dd class="text-lg font-medium text-gray-900">
              {{ tenantCount }}
            </dd>
          </dl>
        </div>
      </div>
    </div>
  </div>
</template>
