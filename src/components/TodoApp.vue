<template>
  <div class="container">
    <div>
      <button
        @click="openListNameModal"
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
      >
        Set List Name
      </button>
    </div>

    <!-- Custom modal for setting the list name -->
    <div
      v-if="isListNameModalOpen"
      class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center"
    >
      <div class="bg-white rounded p-8 max-w-md w-full">
        <span
          class="text-lg font-bold cursor-pointer absolute top-4 right-4"
          @click="closeListNameModal"
          >&times;</span
        >
        <h5 class="text-xl font-bold mb-4">Set List Name</h5>
        <input
          v-model="displayListName"
          type="text"
          placeholder="Enter list name"
          class="form-input mb-4"
        />
        <button
          @click="saveListName"
          class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded"
        >
          Save
        </button>
      </div>
    </div>

    <h2 class="text-center mt-5">
      {{ displayListName ? displayListName : "Untitled List" }}
    </h2>

    <!-- Input for adding tasks -->
    <div class="flex items-center">
      <input
        v-model="task"
        type="text"
        placeholder="Enter task"
        class="form-input mr-4"
      />
      <button
        @click="submitTask"
        class="bg-yellow-500 hover:bg-yellow-700 text-white font-bold py-2 px-4 rounded"
      >
        SUBMIT
      </button>
    </div>

    <!-- Task table -->
    <table class="border-collapse border mt-5 w-full">
      <thead>
        <tr>
          <th class="py-2 px-4 border">Task</th>
          <th class="py-2 px-4 border">Status</th>
          <th class="py-2 px-4 border text-center">Edit</th>
          <th class="py-2 px-4 border text-center">Delete</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in list.tasks" :key="index">
          <td class="py-2 px-4 border">
            <span :class="{ 'line-through': task.status === 'finished' }">
              {{ task.name }}
            </span>
          </td>
          <td class="py-2 px-4 border" style="width: 120px">
            <span
              @click="changeStatus(index)"
              :class="{
                'text-danger': task.status === 'to-do',
                'text-warning': task.status === 'in-progress',
                'text-success': task.status === 'finished',
              }"
              class="cursor-pointer"
            >
              {{ firstCharUpper(task.status) }}
            </span>
          </td>
          <td class="py-2 px-4 border text-center">
            <div @click="editTask(index)" class="cursor-pointer">
              <span class="fa fa-pen"></span>
            </div>
          </td>
          <td class="py-2 px-4 border text-center">
            <div @click="deleteTask(index)" class="cursor-pointer">
              <span class="fa fa-trash"></span>
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Delete List button -->
    <button
      @click="deleteList"
      class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 mt-3 rounded"
    >
      Delete List
    </button>
  </div>
</template>

<script>
export default {
  props: {
    list: Object,
  },
  data() {
    return {
      localList: {},
      task: "",
      editedTask: null,
      availableStatuses: ["to-do", "in-progress", "finished"],
      displayListName: "",
      isListNameModalOpen: false,
    };
  },
  watch: {
    list: {
      immediate: true,
      handler(newVal) {
        this.localList = { ...newVal };
      },
    },
  },
  methods: {
    openListNameModal() {
      this.isListNameModalOpen = true;
    },
    closeListNameModal() {
      this.isListNameModalOpen = false;
    },
    saveListName() {
      // Handle saving the list name
      this.closeListNameModal();
    },
    submitTask() {
      if (this.task.length === 0) {
        return;
      }
      if (!this.localList.tasks) {
        this.$set(this.localList, "tasks", []); // Ensure tasks is initialized
      }
      if (this.editedTask === null) {
        this.localList.tasks.push({
          name: this.task,
          status: "to-do",
        });
      } else {
        this.localList.tasks[this.editedTask].name = this.task;
        this.editedTask = null;
      }
      this.task = ""; // Clear the input field
      this.emitChanges();
    },
    deleteTask(index) {
      this.localList.tasks.splice(index, 1);
      this.emitChanges();
    },
    editTask(index) {
      this.task = this.localList.tasks[index].name;
      this.editedTask = index;
    },
    changeStatus(index) {
      let newIndex = this.availableStatuses.indexOf(
        this.localList.tasks[index].status
      );
      if (++newIndex > 2) newIndex = 0;
      this.localList.tasks[index].status = this.availableStatuses[newIndex];
      this.emitChanges();
    },
    deleteList() {
      this.$emit("deleteList");
    },
    emitChanges() {
      this.$emit("updateList", this.localList);
    },
    firstCharUpper(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
  },
};
</script>

<style scoped>
/* Style for the custom modal */
.custom-modal {
  display: block;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-content {
  background-color: #fefefe;
  margin: 10% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
}

.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}

.finished {
  text-decoration: line-through;
}
</style>
