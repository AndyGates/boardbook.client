<!-- src/components/Problems.vue -->

<template>
    <b-container>
        <h1>Problems</h1>
        <br>
        <table class="table table-hover">
            <tr>
                <th scope="col">Name</th>
                <th scope="col">Grade</th>
            </tr>
            <tbody>
            <tr v-for="(problem, index) in problems"
              :key="index"
              :class="{ active: index === activeIndex}"
              @click="selectItem(index)">
                <td>{{ problem.name }}</td>
                <td>{{ problem.grade }}</td>
            </tr>
            </tbody>
        </table>
        <button type="button" class="btn btn-success btn-sm">Add Problem</button>
    </b-container>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      problems: [],
      activeIndex: -1,
    };
  },
  methods: {
    getProblems() {
      const path = `${process.env.VUE_APP_ROOT_API}/problems`;
      axios.get(path)
        .then((res) => {
          this.problems = res.data.problems;
        })
        .catch((error) => {
          console.error(error);
        });
    },
    selectItem(i) {
      this.activeIndex = i;
      this.$emit('problemSelected', this.problems[i]);
    },

  },
  created() {
    this.getProblems();
    console.log(process.env.VUE_APP_ROOT_API);
  },
};
</script>

<style>
  .active {
    background: #f00;
  }
</style>
