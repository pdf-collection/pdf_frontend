<template>
  <div class="flex justify-center">
    <!-- message -->
    <div
      v-if="message"
      class="absolute z-50 p-2 px-4 mt-1 rounded right-4 top-12"
      style="background-color: #252c53"
    >
      <i class="fa-solid fa-star"></i> {{ messageStore.name }}
      {{ messageStore.message }}
    </div>

    <!-- categories -->
    <vue-good-table
      class="mt-4 sm:w-3/4"
      :columns="columns"
      :rows="categories"
      styleClass="vgt-table"
      :search-options="{
        enabled: true,
        trigger: 'enter',
      }"
      :pagination-options="{
        enabled: true,
        mode: 'records',
        perPage: 4,
        perPageDropdown: [3, 4, 5],
      }"
    >
      <template #table-actions>
        <router-link
          :to="{ name: 'CreateCategory' }"
          class="p-2 mr-1 text-white bg-teal-600 rounded"
        >
          create
        </router-link>
      </template>

      <template #table-row="props">
        <span v-if="props.column.field == 'slug'">
          <router-link
            class="px-3 py-1.5 m-1 text-white bg-teal-600 rounded hover:text-gray-700"
            :to="{ name: 'EditCategory', params: { slug: props.row.slug } }"
            ><i class="fa-solid fa-pen"></i
          ></router-link>
          <button
            @click="deleteFun(props.row.slug, props.row.name)"
            class="px-3 py-1 m-1 text-white bg-teal-600 rounded hover:text-gray-700"
          >
            <i class="fa-solid fa-trash"></i>
          </button>
        </span>
        <span v-else>
          {{ props.formattedRow[props.column.field] }}
        </span>
      </template>
    </vue-good-table>
  </div>
</template>

<script>
import router from "../../../router";
import ApiService from "../../../Apiservice";
import { useMessageStore } from "../../../stores/message.js";
import "vue-good-table-next/dist/vue-good-table-next.css";
import { VueGoodTable } from "vue-good-table-next";
export default {
  components: {
    VueGoodTable,
  },
  data() {
    return {
      messageStore: useMessageStore(),
      message: false,
      categories: [],
      columns: [
        {
          label: "Id",
          field: "id",
        },
        {
          label: "Name",
          field: "name",
        },
        {
          label: "",
          field: "slug",
        },
      ],
    };
  },
  mounted() {
    if (this.messageStore.name) {
      this.message = true;
      setTimeout(() => {
        this.message = false;
      }, 3000);
    }

    ApiService.get("categories")
      .then((response) => {
        this.categories = response.data.data;
      })
      .catch((response) => {
        console.log(response);
      });
  },
  methods: {
    deleteFun(slug, name) {
        ApiService.delete(`categories/${slug}`)
        .then((response) => {
          this.messageStore.updateMessage(
            name,
            "has been deleted successfully!"
          );
          router.push("/admin/categories");
        })
        .catch((response) => {
          console.log(response);
        });
    },
  },
};
</script>

<style>
th,
td {
  padding: 10px;
}
</style>