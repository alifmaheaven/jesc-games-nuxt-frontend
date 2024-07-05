<script setup>
const props = defineProps({
  uuid: {
    type: String,
    default: "",
  },
});

// import
const { $api } = useNuxtApp();

import { useForm } from "vee-validate";
import * as yup from "yup";

const emit = defineEmits();
const isOpen = ref(false);
const validationSchema = yup.object({
  name: yup.string().required().label("Name"),
  description: yup.string().required().label("Description"),
  media: yup.string().required().label("Media"),
});
const { handleSubmit, values, setValues } = useForm({
  initialValues: {},
  validationSchema: validationSchema,
});

const setEditData = async (uuid) => {
  try {
    const response = await $api.get(`/api/v1/rooms/`, {
      params: {
        'uuid_exact': uuid,
      },
    });
    setValues(response?.data?.data?.data[0]);
    // isOpen.value = true;
  } catch (error) {
    useToast().add({ 
      title: 'Failed Get Data!',
      description: 'Failed to get data.',
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

const submit = handleSubmit(async (values, { resetForm, setErrors }) => {
  try {
    if(props.uuid){
      await $api.put(`/api/v1/rooms`, values);
    } else {
      await $api.post("/api/v1/rooms", values);
    }
    useToast().add({ 
      title: 'Successfuly Create!',
      description: 'You have been successfully create room.',
      icon:"i-heroicons-check-circle",
      color:"success",
      ui:{
        icon: {
          base: 'flex-shrink-0 w-10 h-10',
          color: 'text-{color}-500 dark:text-{color}-400',
        },
      }
    })
    resetForm();
    isOpen.value = false;
    emit("getData");
  } catch (error) {
    if(error?.response?.data?.data) setErrors(error.response.data.data);
    useToast().add({ 
      title: 'Failed Create!',
      description: 'Failed to create room.',
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
});

watchEffect(() => {
  if (isOpen.value && props.uuid) {
    setEditData(props.uuid);
  }
});
</script>

<template>
  <div>
    <UButton
      icon="i-heroicons-pencil-square"
      size="sm"
      :color="props.uuid ? 'warning' : 'primary'"
      variant="solid"
      label="Button"
      :trailing="false"
       @click="isOpen = true"
    >
      {{ props.uuid ? 'Edit' : 'Create' }}
    </UButton>
    <UModal v-model="isOpen" prevent-close>
      <UCard :ui="{ ring: '', divide: 'divide-y divide-gray-100 dark:divide-gray-800' }">
        <template #header>
          <div class="flex items-center justify-between">
            <h3 class="text-base font-semibold leading-6 text-gray-900 dark:text-white">
              Create
            </h3>
            <UButton color="gray" variant="ghost" icon="i-heroicons-x-mark-20-solid" class="-my-1" @click="isOpen = false" />
          </div>
        </template>
        <form
          class="flex min-h-max flex-col items-center justify-start gap-2 self-stretch border-gray-200"
          @submit="submit"
        >
          <div
            class="flex min-h-min flex-col items-start justify-start self-stretch"
          >
            <globalInputText
              label="Name"
              name="name"
              placeholder="Insert Name"
            />
            <globalInputTextarea
              label="Description"
              name="description"
              placeholder="Insert Description"
            />
            <globalInputFileupload
              icon="icon-park-outline:mail"
              label="Media"
              name="media"
              placeholder="Insert Media"
              accept="image/*"
              comment="Max file size 2MB"
              linkupload="/api/v1/rooms/upload"
            />
            <globalInputText
              v-if="props.uuid"
              label="Room Code"
              name="room_code"
              placeholder="Insert UUID"
              :disabled="props.uuid"
            />
          </div>
          <div
            class="w-full flex justify-end gap-2"
          >
            <UPopover>
              <UButton
                size="sm"
                color="primary"
                variant="solid"
                label="Button"
                :trailing="false"
              >
                {{ props.uuid ? 'Edit' : 'Create' }}
              </UButton>
              <template #panel="{ close }">
                <div class="flex flex-col gap-2 p-2">
                  <p class="text-sm text-gray-500">Are you sure ?</p>
                  <div class="flex justify-end gap-2">
                    <UButton
                      color="red"
                      variant="solid"
                      label="Button"
                      type="submit"
                      :trailing="false"
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
              size="sm"
              color="secondary"
              variant="solid"
              label="Button"
              :trailing="false"
              @click="isOpen = false"
            >
              Close
            </UButton>
          </div>
        </form>
        
      </UCard>
    </UModal>
  </div>
</template>