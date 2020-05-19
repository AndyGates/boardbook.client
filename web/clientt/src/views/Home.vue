<template>
  <b-container fluid>
    <b-row>
      <b-col sm="8">
        <Board :problem="selectedProblem" />
      </b-col>
      <b-col sm="4">
        <Problems v-on:problemSelected="onProblemSelected"/>
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
  },
  data() {
    return {
      selectedProblem: null,
    };
  },
};
</script>
