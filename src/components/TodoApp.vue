<template>
  <div class="container">
    <div class="flex justify-end mt-3 mb-3">
      <button
        @click="openListNameModal"
        class="bg-fuchsia-200 hover:bg-fuchsia-700 text-black font-bold py-1 px-1 rounded"
      >
        <i class="fas fa-pen"></i>
      </button>
      <button
        @click="confirmDeleteList"
        class="bg-fuchsia-200 hover:bg-fuchsia-700 text-black font-bold py-1 px-1 ml-1 rounded"
      >
        <i class="fas fa-trash"></i>
      </button>
    </div>

    <!-- Custom modal for setting the list name -->
    <div
      v-if="isListNameModalOpen"
      class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center"
    >
      <div class="rounded p-8 max-w-md w-full bg-fuchsia-200">
        <span
          class="text-lg font-bold cursor-pointer absolute top-4 right-4"
          @click="closeListNameModal"
          >&times;</span
        >
        <h5 class="text-xl font-bold mb-4 text-center">Set List Name</h5>
        <input
          v-model="displayListName"
          type="text"
          placeholder="Enter list name"
          class="form-input mb-4 placeholder:text-black placeholder:text-bold bg-fuchsia-500 placeholder-black placeholder:text-center rounded py-2 px-4 h-full w-full"
        />
        <button
          @click="saveListName"
          class="bg-fuchsia-500 hover:bg-fuchsia-700 text-black py-2 px-4 rounded block mx-auto h-full w-full"
        >
          <span class="mr-1">Save</span>
          <i class="fas fa-floppy-disk"></i>
        </button>
      </div>
    </div>

    <div
      v-if="isDeleteListModalOpen"
      class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center"
    >
      <div class="rounded p-8 max-w-md w-full bg-fuchsia-200">
        <span
          class="text-lg font-bold cursor-pointer absolute top-4 right-4"
          @click="closeDeleteListModal"
          >&times;
        </span>
        <h5 class="text-xl font-bold mb-4 text-center">Confirm Delete</h5>
        <p class="mb-4 text-center">
          Are you sure you want to delete the list?
        </p>
        <div class="flex justify-center">
          <button
            @click="deleteList"
            class="bg-fuchsia-500 hover:bg-fuchsia-700 text-black py-2 px-4 rounded mr-2"
          >
            Yes
          </button>
          <button
            @click="closeDeleteListModal"
            class="bg-fuchsia-500 hover:bg-fuchsia-700 text-black py-2 px-4 rounded"
          >
            No
          </button>
        </div>
      </div>
    </div>

    <h2 class="text-xl font-bold mb-4 text-center">
      {{ displayListName ? displayListName : "Untitled List" }}
    </h2>

    <!-- Input for adding tasks -->
    <div class="flex">
      <input
        v-model="task"
        type="text"
        placeholder="Enter a task"
        class="w-full form-input mr-1 border-fuchsia-500 bg-fuchsia-200 placeholder-black placeholder:text-center rounded py-1 px-1"
      />
      <button
        @click="submitTask"
        class="bg-fuchsia-200 hover:bg-fuchsia-700 text-black py-0 px-1 rounded"
      >
        <i class="fas fa-check-circle"></i>
      </button>
    </div>

    <!-- Task table -->
    <div class="overflow-x-auto">
      <table class="border-collapse border mt-5 w-full bg-fuchsia-200">
        <thead>
          <tr>
            <th class="py-2 px-4 border">Task</th>
            <th class="py-2 px-4 border">Status</th>
            <th class="py-2 px-4 border text-center">Edit</th>
            <th class="py-2 px-4 border text-center">Delete</th>
          </tr>
        </thead>
        <tbody class="font-bold">
          <tr v-for="(task, index) in list.tasks" :key="index">
            <td class="py-2 px-4 border">
              <span :class="{ 'line-through': task.status === 'finished' }">
                {{ task.name }}
              </span>
            </td>
            <td class="py-2 px-4 border font-bold" style="width: 120px">
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
    </div>
  </div>
</template>

<script>
export default {
  props: {
    list: Object,
    listIndex: Number,
    displayListNameProp: String,
  },
  data() {
    return {
      localList: {},
      task: "",
      editedTask: null,
      availableStatuses: ["to-do", "in-progress", "finished"],
      localDisplayListName: this.displayListNameProp,
      isListNameModalOpen: false,
      displayListName: "",
      isDeleteListModalOpen: false,
    };
  },
  watch: {
    list: {
      immediate: true,
      handler(newVal) {
        this.localList = { ...newVal };
        this.displayListName = this.localList.title || "";
      },
    },
  },
  methods: {
    confirmDeleteList() {
      this.isDeleteListModalOpen = true;
    },
    closeDeleteListModal() {
      this.isDeleteListModalOpen = false;
    },
    openListNameModal() {
      this.isListNameModalOpen = true;
    },
    closeListNameModal() {
      this.isListNameModalOpen = false;
    },
    saveListName() {
      this.closeListNameModal();
      this.$set(this, "displayListName", this.displayListName);
      this.$emit("saveListNameHandler", this.displayListName, this.listIndex);
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
      if (this.editedTask === index) {
        this.editedTask = null;
      }
      this.localList.tasks.splice(index, 1);
      this.emitChanges();
    },
    editTask(index) {
      if (this.localList.tasks[index]) {
        this.task = this.localList.tasks[index].name;
        this.editedTask = index;
      } else {
        alert("This task has been deleted and cannot be edited.");
        this.editedTask = null; // Ensure editedTask is reset after deletion
      }
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
  background-color: fuchsia-500;
  margin: 10% auto;
  padding: 20px;
  border: 1px solid fuchsia-500;
  width: 80%;
}

.close {
  color: fuchsia-500;
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
