<!-- src/components/Problems.vue -->

<template>
    <b-container class = "p-0">
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
        <button v-if="!editMode" type="button" class="btn btn-success btn-sm"
          v-on:click="addProblem">
          Add Problem
        </button>
        <button v-if="editMode" type="button" class="btn btn-danger btn-sm" v-on:click="cancelAdd">
          Cancel
        </button>
        <p/>
        <b-form v-if="editMode" @submit.prevent="submitProblem">
          <div class="form-group form-inline">
            <b-form-input label="Name" type="text" :value="nameInput"
              class="form-control mx-sm-2" id="inputName" placeholder="Enter Name"/>
            <div class="btn btn-secondary" @click='randomName'>R</div>
          </div>
          <div class="form-group form-inline">
            <b-form-select
              class="form-control mx-sm-2"
              v-model="gradeInput"
              id="inputGrade"
              label="Grade">
              <option disabled value="">Select a grade</option>
              <option>4</option>
              <option>5</option>
              <option>6A</option>
              <option>6A+</option>
              <option>6B</option>
              <option>6B+</option>
              <option>6C</option>
              <option>6C+</option>
              <option>7A</option>
              <option>7A+</option>
              <option>7B</option>
              <option>7B+</option>
              <option>7C</option>
              <option>7C+</option>
              <option>8A</option>
            </b-form-select>
          </div>
          <button class="btn btn-primary">Submit</button>
        </b-form>
    </b-container>
</template>

<script>
import axios from 'axios';
import randomwords from 'random-words';

export default {
  props: {

  },
  data() {
    return {
      problems: [],
      activeIndex: -1,
      nameInput: '',
      gradeInput: '',
      activeProblem: null,
      editMode: false,
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
          this.console.error(error);
        });
    },
    submitProblem() {
      this.console.log('Submitting');
      this.console.log(this.activeProblem);

      const prob = this.activeProblem;
      prob.name = this.nameInput;
      prob.grade = this.gradeInput;

      const path = `${process.env.VUE_APP_ROOT_API}/problems`;

      axios.post(path, prob)
        .then(() => {
          this.getProblems();
        })
        .catch((error) => {
          this.console.log(error);
          this.getProblems();
        })
        .finally(() => {
          this.selectItem(this.problems.length - 1);
        });
    },
    setProblem(problem) {
      this.console.log(problem);
      this.activeProblem = problem;
      this.$emit('problemSelected', problem);
    },
    setEditMode(mode) {
      this.editMode = mode;
      this.$emit('editMode', mode);
    },
    selectItem(i) {
      this.activeIndex = i;
      if (i >= 0) {
        this.setProblem(this.problems[i]);
        this.setEditMode(false);
      }
    },
    addProblem() {
      this.setProblem({
        id: -1,
        name: '',
        grade: '4',
        holds: [],
      });
      this.setEditMode(true);
      this.selectItem(-1);
    },
    randomName() {
      const name = randomwords(2).map((x) => x.charAt(0).toUpperCase() + x.slice(1)).join(' ');
      this.nameInput = name;
    },
    cancelAdd() {
      this.setEditMode(false);
      this.selectItem(0);
    },
  },
  computed: {
    console: () => console,
  },
  watch: {
    activeProblem(newValue) {
      this.console.log(newValue);
    },
  },
  created() {
    this.getProblems();
  },
};
</script>

<style>
  .title {
    text-align: center;
  }

  .container
  {
  }

  .active {
    background: #f00;
  }

  button {
    width: 100%;
  }

  form {
    align-items: left;
    text-align: left;
  }
</style>
