<template>
  <div class="container">
    <div>
      <select v-model="camera">
        <option value="0"></option>
        <option value="1">Nikon D7000/D5200</option>
        <option value="2">Canon 1000D</option>
      </select>
    </div>
    <input v-model="focal" type="text">
    <input v-model="distance" type="text">
    <input v-model="aperture" type="text">
    <div>Near limit: {{dofNear}}</div>
    <div>Far limit: {{dofFar}}</div>
    <div>DOF total: {{dofTotal}}</div>
    <div>Hyperfocal: {{hyperFocal}}</div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      camera: 0,
      focal: '',
      distance: '',
      aperture: '',
      dofNear: '',
      dofFar: '',
      dofTotal: '',
      hyperFocal: ''
    }
  },
  watch: {

  },
  created() {
    this.watchCollection(
        ['focal', 'camera', 'aperture','distance'],
        this.calcDOF)
  },
  methods: {
    watchCollection(arr, cb) {
      arr.forEach((val) => this.$watch(val, cb))
    },
    calcDOF() {
      {
        let coc = 0.020;
        let distance = parseFloat(this.distance);
        let aperture = parseFloat(this.aperture);
        let focal = parseFloat(this.focal);
        if (!isNaN(coc) && !isNaN(distance) && !isNaN(aperture) && !isNaN(focal)) {
          let hyperFocal = (focal * focal) / (aperture * coc) + focal;
          distance = distance * 1000;
          let dofNear = ((hyperFocal - focal) * distance) / (hyperFocal + distance - (2 * focal));
          let dofFar = (hyperFocal - distance) <= 0.00001?10000000.0:((hyperFocal - focal) * distance) / (hyperFocal - distance);

          dofNear = dofNear / 1000.0;
          dofFar = dofFar / 1000.0;
          let dofTotal = dofFar - dofNear;
          distance = distance / 1000.0;
          hyperFocal = hyperFocal / 1000.0;

          if (dofFar < 10000.0) {
            this.dofNear = dofNear;
            this.dofFar = dofFar;
            this.dofTotal = dofFar - dofNear;
            this.hyperFocal = hyperFocal;
          }
          else {
            this.dofNear = dofNear;
            this.dofFar = "Infinity";
            this.dofTotal = "Infinity";
            this.hyperFocal = hyperFocal;
          }
        }
      }
    }
  }
}
</script>
