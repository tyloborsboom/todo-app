<template>
  <div
    id="app"
    class="flex flex-col items-stretch justify-start bg-fuchsia-300 h-full w-full"
  >
    <TopBar2 />
    <ToDoListContainer />
  </div>
</template>

<script>
import ToDoListContainer from "@/components/ToDoListContainer.vue";
import TopBar2 from "@/components/TopBar2.vue";

export default {
  components: {
    ToDoListContainer,
    TopBar2,
  },
};
</script>
