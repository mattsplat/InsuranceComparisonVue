<template>
  <div class="container">
    <div class="card px-2 py-3 mt-3">
      <div class="row mt-3">
        <h4 class="text-left mr-auto ml-5">Person</h4>
        <button class="ml-auto mr-5 btn" @click="$emit('copy', value)">
          <i class="far fa-copy"></i>
        </button>
      </div>

      <form @submit.prevent @input="handlePersonChange">
        <div class="row justify-content-center">
          <div class="form-group col-md-6">
            <label>Name</label>
            <input
              type="text"
              class="form-control mx-auto"
              placeholder="Name"
              v-model="value.name"
            />
          </div>
        </div>

        <div class="row mt-3">
          <div class="form-group col-md-4 text-left">
            <button
              class="btn btn-outline-secondary ml-3"
              @click="addCost"
            >
              Add Cost
            </button>
          </div>

          <div class="form-group col-md-8">
            <button
              @click="$emit('remove', value)"
              class="btn btn-outline-danger float-right"
            >
              <i class="far fa-trash-alt"></i>
            </button>
          </div>
        </div>
      </form>
    </div>
    <ul class="list-group">
      <li
        v-for="(cost, index) in value.costs"
        :key="index"
        class="list-group-item bg-light"
      >
        <cost
          v-model="value.costs[index]"
          @delete="(p) => removeCost(p, index)"
          @input="(v) => updateCost(v, index)"
        ></cost>
      </li>
    </ul>
  </div>
</template>

<script>
import Cost from "./Cost";

export default {
  components: { Cost },
  name: "Person",
  props: {
    value: Object,
  },
  data() {
    return {
      defaults: {
          costs: []
      }
    };
  },
  created() {
      for (let key of Object.keys(this.defaults)) {
      this.value[key] = this.value[key] ? this.value[key] : this.defaults[key];
    }
  },
  methods: {
    removeCost(object, index) {
      this.costs.splice(index, 1);
    },
    addCost() {
        console.log('add cost')
        let copy = JSON.parse(JSON.stringify(this.value));
        copy.costs.push({})
        this.$emit('input', copy)
    },
    updateCost(cost, index) {
    //   let copy = JSON.parse(JSON.stringify(this.costs));
      cost;index;
    //   copy.costs[index] = cost;
    //   console.log("input", this.value);
    //   this.$emit("input", this.value);
    },
    copyCost(p) {
      let copy = JSON.parse(JSON.stringify(p));
      copy.name = copy.name + " (copy)";
      this.costs.push(copy);
    },
    handlePersonChange(e) {
        console.log(e)
        // this.$emit('input', value)
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
