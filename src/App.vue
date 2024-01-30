<template>
  <div id="app">
    <TopBar2 />
    <ToDoListContainer />
  </div>
</template>

<script>
import ToDoListContainer from "@/components/ToDoListContainer.vue"; // Update the path accordingly
import TopBar2 from "@/components/TopBar2.vue"; // Update the path accordingly

export default {
  components: {
    ToDoListContainer,
    TopBar2,
  },
};
</script>

<style scoped>
div#app {
  display: flex;
  flex-flow: column nowrap;
  align-items: stretch;
  justify-content: start;
}
</style>
