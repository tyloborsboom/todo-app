<template>
  <div class="todo-container">
    <h2 class="text-center mt-5">My To-Do Lists</h2>
    <button @click="addNewList" class="btn btn-success mt-3 mb-3">Add New List</button>

    <div v-for="(list, index) in todoLists" :key="index" class="list-container">
      <IndividualList :list="list" @updateList="updateList(index, $event)" @deleteList="deleteList(index)" />
    </div>
  </div>
</template>

<script>
import IndividualList from '@/components/TodoApp.vue';

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

<style scoped>
/* Style for the entire todo container */
.todo-container {
  /* Add space around the container */
  padding: 20px;
}

/* Style for IndividualList components */
.list-container {
  margin-bottom: 20px; /* Add space between lists */
  padding: 10px; /* Add padding to the individual list container */
  border: 1px solid #ccc; /* Add a border for better separation */
  border-radius: 8px; /* Add border radius for a rounded appearance */
}
</style>
