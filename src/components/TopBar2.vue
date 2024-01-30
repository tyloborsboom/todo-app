<template>
  <div
    id="topBar2"
    class="h-8 w-full bg-black text-white flex items-center justify-center drag-region"
  >
    <span>Top Bar</span>
  </div>
</template>

<script>
export default {
  data() {
    return {};
  },
  methods: {},
};
</script>

<style scoped>
/* Style for the entire todo container */
#topBar2 {
  /* Add space around the container */
  height: 2rem;
  width: 100%;
  background-color: black;
  color: white;
  display: flex;
  flex-flow: row nowrap;
  align-items: center;
  justify-content: center;
  -webkit-app-region: drag;
}
</style>
