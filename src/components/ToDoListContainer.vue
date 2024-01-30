<template>
  <div class="flex p-5">
    <!-- Sidebar with list titles -->
    <div class="w-1/4 p-4 bg-gray-200">
      <h2 class="text-xl font-bold mb-4">My To-Do Lists</h2>
      <button
        v-for="(list, index) in todoLists"
        :key="index"
        @click="selectList(index)"
        class="w-full mb-2 p-2 bg-blue-500 hover:bg-blue-700 text-white font-bold rounded"
      >
        {{ list.title || "Untitled List" }}
      </button>

      <!-- Button to create a new list -->
      <button
        @click="addNewList"
        class="w-full mt-4 p-2 bg-green-500 hover:bg-green-700 text-white font-bold rounded"
      >
        Create New List
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
        <p class="text-center">Select a list from the sidebar</p>
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
