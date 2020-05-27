<!-- src/components/Problems.vue -->

<template>
  <b-container ref="container" class="boardcontainer p-0" fluid>
    <v-stage ref="stage" :config="stageSize" v-if="board">
      <v-layer>
        <v-image :config="{
          image: image,
        }"/>
      </v-layer>

      <v-layer>
        <v-shape v-for="(hold, index) in board.holds"
          @click="onHoldSelected(index)"
          :key="index"
          :config="{
          sceneFunc: function(context, shape) {
            context.beginPath();

            console.log('hold');
            console.log(board.holds);

            fixedPath = hold.path.map(x => x.map(y => Math.floor(y * stageSize.width)));
            context.moveTo(fixedPath[0][0], fixedPath[0][1]);

            fixedPath.forEach(function(p) {
              context.lineTo(p[0], p[1]);
            })

            context.closePath();

            // special Konva.js method
            context.fillStrokeShape(shape);
          },
          fill: '#00000000',
          stroke: problem && problem.holds && problem.holds.includes(index) ? 'red' : '#00000000',
          strokeWidth: 0.005 * stageSize.width,
          }
        "/>
      </v-layer>
    </v-stage>
  </b-container>
</template>

<script>
import axios from 'axios';

export default {
  props: {
    problem: Object,
    editMode: Boolean,
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
      const path = `${process.env.VUE_APP_ROOT_API}/board`;
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

      const compStyle = getComputedStyle(container);
      const w = container.clientWidth
            - (parseFloat(compStyle.paddingLeft) + parseFloat(compStyle.paddingRight));
      const h = container.clientHeight
            - (parseFloat(compStyle.paddingTop) + parseFloat(compStyle.paddingBottom));

      // this.console.log(`Resizing. Container ${w}x${h}`);

      let newWidth = w;
      let newHeight = newWidth * this.imgAspect;

      // this.console.log(`New: ${newWidth}x${newHeight}`);

      if (newHeight > h) {
        this.console.log('Too high');

        newHeight = h;
        newWidth = newHeight * (1.0 / this.imgAspect);
      }

      // this.console.log(`${newWidth}x${newHeight}`);

      if (this.image) {
        this.image.width = newWidth;
        this.image.height = newHeight;
      }

      this.stageSize.width = newWidth;
      this.stageSize.height = newHeight;

      // this.console.log(`Resized stage to ${this.stageSize.width}x${this.stageSize.height}`);
    },
    onBoardReceived(board) {
      this.board = board;
      this.board.holds = JSON.parse(board.holds);

      this.console.log(board);
      const image = new window.Image();
      image.src = `/images/${board.image}`;
      image.onload = () => {
        this.image = image;
        this.imgAspect = this.image ? this.image.height / this.image.width : 1.0;

        this.resizeToContainer();
      };
    },
    onEdit() {
      this.console.log('Board component edit mode');
    },
    onHoldSelected(boardHoldIndex) {
      if (this.editMode && this.problem) {
        const problemHoldIndex = this.problem.holds.indexOf(boardHoldIndex);
        if (problemHoldIndex >= 0) {
          this.problem.holds.splice(problemHoldIndex, 1);
        } else {
          this.problem.holds.push(boardHoldIndex);
        }
      }
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
  watch: {
    editMode(newVal, oldVal) {
      this.console.log(`EDIT MODE ${oldVal} -> ${newVal}`);
      if (newVal) {
        this.onEdit();
      }
    },
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

<style>
  .boardcontainer {
    height:100%;
  }
</style>
