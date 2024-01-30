<template>
  <div class="p-5">
    <h2 class="text-center mt-5 text-3xl">My To-Do Lists</h2>
    <button
      @click="addNewList"
      class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 mt-3 mb-3 rounded"
    >
      Add New List
    </button>

    <div v-for="(list, index) in todoLists" :key="index" class="mt-4">
      <IndividualList
        :list="list"
        @updateList="updateList(index, $event)"
        @deleteList="deleteList(index)"
      />
    </div>
  </div>
</template>

<script>
import IndividualList from "@/components/TodoApp.vue";

export default {
  components: {
    IndividualList,
  },
  data() {
    return {
      todoLists: [],
    };
  },
  methods: {
    addNewList() {
      this.todoLists.push({
        // Add initial data for a new list
      });
    },
    updateList(index, updatedList) {
      // Update the list at the specified index with the updatedList data
      this.$set(this.todoLists, index, updatedList);
    },
    deleteList(index) {
      this.todoLists.splice(index, 1);
    },
  },
};
</script>
