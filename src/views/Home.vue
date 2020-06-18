<template>
  <b-container fluid class="homecontainer">
    <b-row>
      <b-col sm="8" class="p-0">
        <h1 class="title p-3 m-0">BOARD BOOK</h1>
      </b-col>
      <b-col sm="4" class="p-0">
        <h1 class="title p-3 m-0">PROBLEMS</h1>
      </b-col>
    </b-row>
    <b-row class="contentrow">
      <b-col sm="8" class="">
        <Board :problem="selectedProblem" :editMode="editMode"/>
      </b-col>
      <b-col sm="4" class="">
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
  .test{
    background-color: darkseagreen;
    width:100%;
    height:100px;
  }

  .title {
    font-size: 24px;
    font-weight: bold;
    text-align: left;
    background-color: #00000088;
  }

  .title.problems {
        text-align: right;
  }

  .homecontainer {
    flex: 1;

    flex-direction: column;
    display: flex;
  }

  .contentrow {
    flex: 1;
  }
</style>
