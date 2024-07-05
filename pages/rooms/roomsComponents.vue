<script setup>
import { computed, toRef, useSlots } from "vue";
const { $api } = useNuxtApp();

import createUpdateModal from "./createUpdateModal.vue";

const emit = defineEmits();
const props = defineProps({
  uuid: {
    type: String,
    default: "",
  },
  name: {
    type: String,
    default: "",
  },
  description: {
    type: String,
    default: "",
  },
  room_code: {
    type: String,
    default: "",
  },
  media: {
    type: String,
    default: "",
  },
  created_at: {
    type: String,
    default: "",
  },
  user_id: {
    type: String,
    default: "",
  },
});

const deleteData = async (uuid) => {
  try {
    const response = await $api.delete(`/api/v1/rooms`, {data:{uuid}});
    useToast().add({ 
      title: 'Successfuly Delete!',
      description: 'You have been successfully delete room.',
      icon:"i-heroicons-check-circle",
      color:"success",
      ui:{
        icon: {
          base: 'flex-shrink-0 w-10 h-10',
          color: 'text-{color}-500 dark:text-{color}-400',
        },
      }
    })
    emit("getData");
  } catch (error) {
    useToast().add({ 
      title: 'Failed Delete!',
      description: 'Failed to delete room.',
      icon:"i-heroicons-x-mark-circle",
      color:"danger",
      ui:{
        icon: {
          base: 'flex-shrink-0 w-10 h-10',
          color: 'text-{color}-500 dark:text-{color}-400',
        },
      }
    })
  }
};

</script>

<template>
  <div class="relative rounded-lg p-4 shadow-md shadow-slate-100 group">
    <div class="absolute inset-0 rounded-lg bg-black bg-opacity-50 transition-opacity duration-300 opacity-0 group-hover:opacity-100">
      <div class="flex items-center justify-center h-full gap-2">
        <createUpdateModal
          :uuid="uuid"
          @getData="$emit('getData')"
        />
        <UPopover>
          <UButton
            icon="i-heroicons-trash"
            size="sm"
            color="red"
            variant="solid"
            label="Button"
            :trailing="false"
          >
            Delete
          </UButton>
          <template #panel="{ close }">
            <div class="flex flex-col gap-2 p-2">
              <p class="text-sm text-gray-500">Are you sure ?</p>
              <div class="flex justify-end gap-2">
                <UButton
                  color="red"
                  variant="solid"
                  label="Button"
                  :trailing="false"
                  @click="deleteData(uuid)"
                >
                  Yes
                </UButton>
                <UButton
                  color="gray"
                  variant="solid"
                  label="Button"
                  :trailing="false"
                  @click="close"
                >
                  No
                </UButton>
              </div>
            </div>
          </template>
        </UPopover>
        <UButton
          icon="i-heroicons-eye"
          size="sm"
          color="secondary"
          variant="solid"
          label="Button"
          :trailing="false"
        >
          Detail
        </UButton>
      </div>
    </div>
    <img
      alt=""
      crossorigin="anonymous"
      :src="media && (useRuntimeConfig().public.BASE_URL_PUBLIC + media) || '/images/no-image.jpg'"
      class="h-56 w-full rounded-md object-cover"
    />

    <div class="mt-2">
      <dl>
        <div>
          <dt class="sr-only">Created at</dt>
          <dd class="text-sm text-gray-500">{{ created_at }}</dd>
        </div>

        <div>
          <dt class="sr-only">Name</dt>
          <dd class="font-medium">{{ name }}</dd>
        </div>
        <div>
          <dt class="sr-only">Description</dt>
          <dd class="text-sm text-gray-500">{{ description }}</dd>
        </div>
        <div>
          <dt class="sr-only">Code</dt>
          <dd class="text-primary font-bold">{{ room_code }}</dd>
        </div>
      </dl>

      <div class="mt-6 flex items-center gap-8 text-xs">
        <div class="sm:inline-flex sm:shrink-0 sm:items-center sm:gap-2">
          <svg
            class="size-4 text-indigo-700"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M8 14v3m4-3v3m4-3v3M3 21h18M3 10h18M3 7l9-4 9 4M4 10h16v11H4V10z"
            />
          </svg>

          <div class="mt-1.5 sm:mt-0">
            <p class="text-gray-500">Parking</p>

            <p class="font-medium">2 spaces</p>
          </div>
        </div>

        <div class="sm:inline-flex sm:shrink-0 sm:items-center sm:gap-2">
          <svg
            class="size-4 text-indigo-700"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M5 3v4M3 5h4M6 17v4m-2-2h4m5-16l2.286 6.857L21 12l-5.714 2.143L13 21l-2.286-6.857L5 12l5.714-2.143L13 3z"
            />
          </svg>

          <div class="mt-1.5 sm:mt-0">
            <p class="text-gray-500">Bathroom</p>

            <p class="font-medium">2 rooms</p>
          </div>
        </div>

        <div class="sm:inline-flex sm:shrink-0 sm:items-center sm:gap-2">
          <svg
            class="size-4 text-indigo-700"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z"
            />
          </svg>

          <div class="mt-1.5 sm:mt-0">
            <p class="text-gray-500">Bedroom</p>

            <p class="font-medium">4 rooms</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>