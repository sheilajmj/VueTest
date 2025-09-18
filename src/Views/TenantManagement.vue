<script setup>
import { ref, computed } from "vue";
import Header from "../Components/Header.vue";
import SearchBox from "../Components/SearchBox.vue";
import StatCard from "../Components/StatCard.vue";

// Sample tenant data
const tenants = ref([
  { id: 1, name: "Acme Corp", usage: 85, status: "active" },
  { id: 2, name: "Beta LLC", usage: 120, status: "overdue" },
  { id: 3, name: "Delta Inc", usage: 60, status: "active" },
  { id: 4, name: "Gamma Solutions", usage: 95, status: "active" },
  { id: 5, name: "Omega Industries", usage: 150, status: "overdue" },
  { id: 6, name: "Alpha Technologies", usage: 75, status: "active" },
]);

//Search
const searchQuery = ref("");
const statusFilterQuery = ref("");
const handleSearchUpdate = (newQuery) => {
  searchQuery.value = newQuery;
};
const handleFilterUpdate = (newQuery) => {
  console.log(newQuery);
  if (newQuery === "total") {
    statusFilterQuery.value = "";
    return;
  }
  statusFilterQuery.value = newQuery;
};

// Computed properties
const filteredTenants = computed(() => {
  if (!searchQuery.value && !statusFilterQuery.value) {
    return tenants.value;
  }

  if (searchQuery.value) {
    return tenants.value.filter((tenant) =>
      tenant.name.toLowerCase().includes(searchQuery.value.toLowerCase())
    );
  }

  if (statusFilterQuery.value) {
    return tenants.value.filter((tenant) =>
      tenant.status
        .toLowerCase()
        .includes(statusFilterQuery.value.toLowerCase())
    );
  }
});

const activeTenantsCount = computed(() => {
  return tenants.value.filter((tenant) => tenant.status === "active").length;
});

const overdueTenantsCount = computed(() => {
  return tenants.value.filter((tenant) => tenant.status === "overdue").length;
});
</script>

<template>
  <div class="min-h-screen bg-gray-50 py-8">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <Header />
      <SearchBox @update-search="handleSearchUpdate" />
      <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
        <StatCard
          :tenant-count="tenants.length"
          :variant="'total'"
          v-on:handle-card-click="handleFilterUpdate"
        />
        <StatCard
          :tenant-count="activeTenantsCount"
          :variant="'active'"
          v-on:handle-card-click="handleFilterUpdate"
        />
        <StatCard
          :tenant-count="overdueTenantsCount"
          :variant="'overdue'"
          v-on:handle-card-click="handleFilterUpdate"
        />
      </div>
      <!-- Tenants Table -->
      <div class="bg-white shadow overflow-hidden sm:rounded-md">
        <div class="px-4 py-5 sm:px-6 border-b border-gray-200">
          <h3 class="text-lg leading-6 font-medium text-gray-900">
            Tenant List
          </h3>
          <p class="mt-1 max-w-2xl text-sm text-gray-500">
            All tenant information and status
          </p>
        </div>

        <!-- Desktop Table View -->
        <div class="hidden md:block">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th
                  class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >
                  ID
                </th>
                <th
                  class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >
                  Name
                </th>
                <th
                  class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >
                  Usage
                </th>
                <th
                  class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >
                  Status
                </th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <tr
                v-for="tenant in filteredTenants"
                :key="tenant.id"
                :class="[
                  'hover:bg-gray-50 transition-colors duration-150',
                  tenant.status === 'overdue'
                    ? 'bg-red-50 border-l-4 !border-l-red-400'
                    : '',
                ]"
              >
                <td
                  class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900"
                >
                  {{ tenant.id }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                  {{ tenant.name }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                  {{ tenant.usage }}%
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <span
                    :class="[
                      'inline-flex px-2 py-1 text-xs font-semibold rounded-full',
                      tenant.status === 'active'
                        ? 'bg-green-100 text-green-800'
                        : 'bg-red-100 text-red-800',
                    ]"
                  >
                    {{ tenant.status }}
                  </span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- Mobile Card View -->
        <div class="md:hidden">
          <ul class="divide-y divide-gray-200">
            <li
              v-for="tenant in filteredTenants"
              :key="tenant.id"
              :class="[
                'px-4 py-4',
                tenant.status === 'overdue'
                  ? 'bg-red-50 border-l-4 border-red-400'
                  : '',
              ]"
            >
              <div class="flex items-center justify-between">
                <div class="flex-1 min-w-0">
                  <p class="text-sm font-medium text-gray-900 truncate">
                    {{ tenant.name }}
                  </p>
                  <p class="text-sm text-gray-500">
                    ID: {{ tenant.id }} • Usage: {{ tenant.usage }}%
                  </p>
                </div>
                <div class="ml-4 flex-shrink-0">
                  <span
                    :class="[
                      'inline-flex px-2 py-1 text-xs font-semibold rounded-full',
                      tenant.status === 'active'
                        ? 'bg-green-100 text-green-800'
                        : 'bg-red-100 text-red-800',
                    ]"
                  >
                    {{ tenant.status }}
                  </span>
                </div>
              </div>
            </li>
          </ul>
        </div>

        <!-- Empty State -->
        <div v-if="filteredTenants.length === 0" class="text-center py-12">
          <svg
            class="mx-auto h-12 w-12 text-gray-400"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M9.172 16.172a4 4 0 015.656 0M9 12h6m-6-4h6m2 5.291A7.962 7.962 0 0112 15c-2.34 0-4.29-1.009-5.824-2.709M15 12a3 3 0 11-6 0 3 3 0 016 0z"
            ></path>
          </svg>
          <h3 class="mt-2 text-sm font-medium text-gray-900">
            No tenants found
          </h3>
          <p class="mt-1 text-sm text-gray-500">
            Try adjusting your search criteria.
          </p>
        </div>
      </div>
    </div>
  </div>
</template>
