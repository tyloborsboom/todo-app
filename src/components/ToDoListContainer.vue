<template>
  <div class="flex p-5">
    <!-- Sidebar with list titles -->
    <div class="w-1/4 p-4 bg-fuchsia-200 rounded">
      <h2 class="text-xl font-bold mb-4">My To-Do Lists</h2>
      <!-- Button to create a new list -->
      <button
        @click="addNewList"
        class="w-full mt-4 mb-2 p-2 bg-fuchsia-500 hover:bg-fuchsia-700 text-black rounded"
      >
        <span class="mr-1">New List</span>
        <i class="fas fa-plus"></i>
      </button>
      <button
        v-for="(list, index) in todoLists"
        :key="index"
        @click="selectList(index)"
        class="w-full mb-2 p-2 bg-fuchsia-500 hover:bg-fuchsia-700 text-black rounded"
      >
        {{ list.title || "Untitled List" }}
      </button>
    </div>

    <!-- Content area to display selected list -->
    <div class="w-3/4 p-4">
      <div v-if="selectedListIndex !== null && selectedListIndex !== undefined">
        <IndividualList
          :list="selectedList"
          :listIndex="selectedListIndex"
          :displayListName="selectedList.title"
          @updateList="updateSelectedList"
          @deleteList="deleteSelectedList"
          @saveListNameHandler="saveListNameHandler"
        />
      </div>
      <div v-else>
        <p class="text-center font-semibold">Select a list from the sidebar</p>
      </div>
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
      selectedListIndex: null,
    };
  },
  methods: {
    // Handle the event emitted from TodoApp.vue to update list name
    saveListNameHandler(newListName, listIndex) {
      console.log("New list name:", newListName);

      // Check if listIndex is valid
      if (listIndex !== null && newListName !== undefined) {
        // Update the title of the selected list in todoLists array
        this.$set(this.todoLists, listIndex, {
          ...this.todoLists[listIndex],
          title: newListName,
        });
        this.$emit("updateListName", newListName, listIndex);
      }
    },

    // Add a new list to the todoLists array
    addNewList() {
      this.todoLists.push({
        title: "",
        tasks: [],
        // Add initial data for a new list
      });
    },

    // Select a list from the sidebar
    selectList(index) {
      this.selectedListIndex = index;
    },

    // Update the selected list with the updatedList data
    updateSelectedList(updatedList) {
      if (this.selectedListIndex !== null) {
        this.$set(this.todoLists, this.selectedListIndex, updatedList);
      }
    },

    // Delete the selected list
    deleteSelectedList() {
      if (this.selectedListIndex !== null) {
        this.todoLists.splice(this.selectedListIndex, 1);
        this.selectedListIndex = null;
      }
    },
  },
  computed: {
    // Get the selected list based on the selectedListIndex
    selectedList() {
      return this.selectedListIndex !== null
        ? this.todoLists[this.selectedListIndex]
        : null;
    },
  },
};
</script>
