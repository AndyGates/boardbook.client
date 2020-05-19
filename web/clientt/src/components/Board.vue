<!-- src/components/Problems.vue -->

<template>
  <b-container ref="container" fluid>
    <v-stage ref="stage" :config="stageSize">
      <v-layer>
        <v-image :config="{
          image: image,
        }"/>
      </v-layer>

      <v-layer>
        <v-ring v-for="(hold, index) in problemHolds"
          :key="index"
          :config="{
            x: hold.x * stageSize.width,
            y: hold.y * stageSize.height,
            innerRadius: hold.size,
            outerRadius: hold.size + 4,
            fill: 'red'
          }"
        />
      </v-layer>
    </v-stage>
  </b-container>
</template>

<script>
import axios from 'axios';

export default {
  props: {
    problem: Object,
  },
  data() {
    return {
      board: {},
      image: null,
      imageSize: {
        x: 1,
        y: 1,
      },
      stageSize: {
        width: 0,
        height: 0,
      },
    };
  },
  methods: {
    getBoard() {
      const path = 'http://192.168.1.104:5000/board';
      axios.get(path)
        .then((res) => {
          this.onBoardReceived(res.data.board);
        })
        .catch((error) => {
          this.console.error(error);
        });
    },
    resizeToContainer() {
      const { container } = this.$refs;

      if (!container) {
        return;
      }

      // Calculate new size from container with and aspect
      const newWidth = container.offsetWidth;
      const newHeight = container.offsetWidth * this.imgAspect;

      this.console.log(`${newWidth}x${newHeight}`);

      if (this.image) {
        this.image.width = newWidth;
        this.image.height = newHeight;
      }

      this.stageSize.width = newWidth;
      this.stageSize.height = newHeight;

      this.console.log(`Resized stage to ${this.stageSize.width}x${this.stageSize.height}`);
    },
    onBoardReceived(board) {
      this.board = board;
      const image = new window.Image();
      image.src = `/images/${board.image}`;
      image.onload = () => {
        this.image = image;
        this.imgAspect = this.image ? this.image.height / this.image.width : 1.0;

        this.resizeToContainer();
      };
    },
  },
  created() {
    this.imgAspect = 1;
    this.getBoard();

    window.addEventListener('resize', this.resizeToContainer);
  },
  mounted() {
    this.resizeToContainer();
  },
  computed: {
    console: () => console,
    window: () => window,

    problemHolds() {
      let holdData = [];

      if (this.problem && this.board) {
        const holdIndices = this.problem ? this.problem.holds : [];
        holdData = holdIndices.map((x) => this.board.holds[x]);
      }

      this.console.log(`Computed holds: ${holdData}`);
      return holdData;
    },
  },
};
</script>
