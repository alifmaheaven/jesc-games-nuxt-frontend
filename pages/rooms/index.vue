<script setup>
// layout
definePageMeta({
  layout: "autenticated",
  // middleware: ["auth"],
  breadcrumb: computed(
    () =>
      `${
        (useRoute().params.RISKCD == "-" && "Add") || "Edit"
      } Risk: General Information`,
  ),
});

// import
const { $api } = useNuxtApp();

import roomsComponents from './roomsComponents.vue';
import createUpdateModal from './createUpdateModal.vue';

// define

// data

const rows = ref([]);

const pagination = ref({
  page: 1,
  per_page: 8,
  total: 0,
  total_pages: 0,
});

// methods
const getData = async () => {
  const response = await $api.get("/api/v1/rooms", {
    params: {
      ...pagination.value,
    },
  })
  rows.value = response?.data?.data?.data;
  pagination.value = response?.data?.data?.meta;
};
getData();
</script>

<template>
  <div>
    <div class="flex flex-row-reverse">
      <createUpdateModal
      @getData="getData"
      />
    </div>
    <div class="flex flex-row">
      <div class="basis-12/12 w-full p-1">
        <div class="grid grid-cols-4 gap-4">
          <template v-for="item in rows" :key="item.uuid">
            <roomsComponents
              :uuid="item.uuid"
              :name="item.name"
              :description="item.description"
              :room_code="item.room_code"
              :media="item.media"
              :created_at="item.created_at"
              :user_id="item.user_id"
              @getData="getData"
            />
          </template>
        </div>
        <globalPaginationCustom
          :total-page="pagination.total_pages"
          :current-page="pagination.page"
          :per-page="pagination.per_page"
          @change="getData"
          @update:per-page="(value) => (pagination.per_page = value)"
          @update:current-page="(value) => (pagination.page = value)"
        >
        </globalPaginationCustom>
      </div>
    </div>
  </div>
</template>
