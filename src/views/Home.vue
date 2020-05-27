<template>
  <b-container fluid class="homecontainer">
    <b-row>
      <b-col sm="8" class="p-3">
        <Board :problem="selectedProblem" :editMode="editMode"/>
      </b-col>
      <b-col sm="4">
        <Problems
          v-on:problemSelected="onProblemSelected"
          @editMode="onEditMode" />
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
// @ is an alias to /src
import Problems from '@/components/Problems.vue';
import Board from '@/components/Board.vue';

export default {
  name: 'Home',
  components: {
    Problems,
    Board,
  },
  methods: {
    onProblemSelected(problem) {
      this.selectedProblem = problem;
    },
    onEditMode(isEdit) {
      this.editMode = isEdit;
    },
  },
  data() {
    return {
      selectedProblem: null,
      editMode: false,
    };
  },
};
</script>

<style>
  .homecontainer {
    display : flex;
    flex-direction: column;
  }

  .row {
    flex-grow: 1;
  }
</style>
