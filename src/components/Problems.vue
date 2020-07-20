<!-- src/components/Problems.vue -->

<template>
    <b-container class = "p-0">
        <table class="table-scroll-y custom-scrollbar table table-hover">
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
        <div class = notes v-if="activeIndex !== -1">
          {{activeProblem.notes}}
        </div>
        <div v-if="!editMode">
          <button type="button" class="btn my-sm-2 btn-success btn-sm"
            v-on:click="addProblem">
            Add Problem
          </button>
          <button v-if="activeIndex !== -1" type="button" class="btn my-sm-2 btn-primary btn-sm"
            v-on:click="editProblem">
            Edit Problem
          </button>
        </div>
        <div v-if="editMode">
          <b-form @submit.prevent="submitProblem">
            <div class="form-group form-inline">
              <b-form-input label="Name" type="text" v-model="nameInput"
                class="form-control mx-sm-2 my-sm-3" id="inputName" placeholder="Enter Name"/>
              <div class="btn btn-secondary" @click='randomName'>R</div>
              <b-form-input  style="width: 450px;" label="Notes" type="text" v-model="notesInput"
                class="form-control mx-sm-2" id="inputNotes" placeholder="Notes"/>
            </div>
            <div class="form-group form-inline">
              <b-form-select
                class="form-control mx-sm-2"
                v-model="gradeInput"
                id="inputGrade"
                label="Grade">
                <option disabled value="">Select a grade</option>
                <option>?</option>
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
            <button class="btn btn-primary btn-sm">Submit</button>
          <p/>
          </b-form>
          <button class="btn btn-danger btn-sm" v-on:click="cancelAdd">Cancel</button>
          <button v-if="activeIndex !== -1" class="btn my-sm-3 btn-danger btn-sm"
          v-on:click="deleteProblem">Delete Problem</button>
        </div>
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
      notesInput: '',
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
      prob.notes = this.notesInput;

      this.nameInput = '';

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
          this.selectItem(-1);
        });
    },
    deleteProblem() {
      this.console.log('Deleting');
      this.console.log(this.activeProblem);

      const probId = this.activeProblem.id;

      const path = `${process.env.VUE_APP_ROOT_API}/problems`;

      axios.delete(path, { data: { id: probId } })
        .then(() => {
          this.console.log('Successfully called delete');
          this.getProblems();
        })
        .catch((error) => {
          this.console.log(error);
          this.console.log('Failed to call delete');
          this.getProblems();
        })
        .finally(() => {
          this.selectItem(-1);
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
      } else {
        this.clearActiveProblem();
      }
      this.setEditMode(false);
    },
    clearActiveProblem() {
      this.setProblem({
        id: -1,
        name: '',
        grade: '4',
        notes: '',
        holds: [],
      });
    },
    addProblem() {
      this.clearActiveProblem();
      this.selectItem(-1);
      this.setEditMode(true);
    },
    editProblem() {
      this.setEditMode(true);
      this.nameInput = this.activeProblem.name;
      this.gradeInput = this.activeProblem.grade;
      this.notesInput = this.activeProblem.notes;
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

  .notes {
    text-align: left;
    color: black;
  }
  button {
    width: 100%;
  }

  form {
    align-items: left;
    text-align: left;
  }

  .custom-scrollbar {
    position: relative;
    height: 50vh;
    overflow-y: auto;
    overflow-x: hidden;
  }

  .table-scroll-y {
    display: inline-block;
  }
</style>
