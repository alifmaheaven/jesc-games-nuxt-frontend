<script setup>
// layout

// import
const { $api } = useNuxtApp();
import { useAuth } from "@/stores/auth";

// define
const authStore = useAuth();
const router = useRouter();
const route = useRoute();
// const user_data = authStore.getProfile();

const breadcrumbs = computed(() => {
  return router.options.routes
    .filter((value_router) => {
      const current_route = route.path.split("/").filter((x) => x);
      const route_value = value_router.path.split("/").filter((x) => x);
      const isMatched = route_value.every((value_route_value) => {
        if (value_route_value.indexOf(":") > -1) {
          return route.params[
            value_route_value?.split(":")?.[1]?.replace("()", "")
          ];
        } else {
          return current_route.includes(value_route_value);
        }
      });
      return isMatched && value_router.meta && value_router.meta.breadcrumb;
    })
    .sort((a, b) => a.path.length - b.path.length)
    .map((value) => {
      const to_breadcrumbs = {
        path: value.path,
        meta: value.meta,
      };
      Object.keys(route.params).forEach((key) => {
        to_breadcrumbs.path = to_breadcrumbs.path.replace(
          `:${key}()`,
          route.params[key],
        );
      });
      return to_breadcrumbs;
    });
});

// tiap page kasih parameter breadcrumb. contoh :
// definePageMeta({
//   layout: "autentication",
//   // middleware: ["auth"],
//   breadcrumb: {
//     text: "Kesesuaian Pengisian",
//     href: "/risk-function/performance-review/personnel-rm-admin/kesesuaian-pengisian-risk-register",
//   },
// });

// data

const menu_sidebar = ref([
  {
    name: "Beranda",
    icon: "ri:home-6-line",
    link: "/",
    sub_menu: [
      {
        name: "Overview",
        link: "/",
      },
      {
        name: "Notifikasi",
        link: "/notification",
        label: "10",
      },
    ],
  },
  {
    name: "Tanda Tangan",
    icon: "material-symbols:layers-outline-rounded",
    link: "/tanda-tangan",
    sub_menu: [
      {
        name: "Tanda Tangan Individu",
        link: "/",
      },
      {
        name: "Tanda Tangan Kelompok",
        link: "/",
      },
    ],
  },
  {
    name: "Pengaturan",
    icon: "mdi:account-multiple-outline",
    link: "/pengaturan",
    sub_menu: [
      {
        name: "Pengaturan Akun",
        link: "/",
      },
      {
        name: "Pengaturan Aplikasi",
        link: "/",
      },
    ],
  },
]);

const sub_menu = computed(() => {
  return menu_sidebar.value.find((item) => item.link === route.href);
});

// methods
</script>

<template>
  <!-- component -->
  <div class="flex">
    <div class="mb-6 ml-auto w-full">
      <div class="px-4 pt-4 2xl:container">
        <slot />
      </div>
    </div>
  </div>
  <LayoutLanguagedarkswitcher />
</template>

<style lang="scss" scoped>
</style>
