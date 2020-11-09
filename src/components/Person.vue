<template>
  <div class="container">
    <div class="card px-2 py-3 mt-3">
      <div class="row mt-3">
        <h4 class="text-left mr-auto ml-5">Person</h4>
        <button class="ml-auto mr-5 btn" @click="$emit('copy', value)">
          <i class="far fa-copy"></i>
        </button>
      </div>

      <form @submit.prevent >
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

        <div class="row">
          <a class="ml-auto mr-5"
             v-if="value.costs && value.costs.length > 0"
             @click.prevent="showCosts = !showCosts">
            {{ showCosts ? "Hide" : "Show" }} Costs</a
          >
        </div>
      </form>
    </div>
    <ul class="list-group">
      <li
        v-for="(cost, index) in value.costs"
        :key="index"
        class="list-group-item bg-light"
        v-show="showCosts"
      >
        <cost
          v-model="value.costs[index]"
          @remove="(p) => removeCost(p, index)"
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
          costs: [],

      },
      showCosts: true,
    };
  },
  created() {
    for (let key of Object.keys(this.defaults)) {
      this.value[key] = this.value[key] ? this.value[key] : this.defaults[key];
    }
  },
  methods: {
    removeCost(object, index) {
      let copy = JSON.parse(JSON.stringify(this.value));
      copy.costs.splice(index, 1);
      this.$emit('input', copy)
    },
    addCost() {
      let copy = JSON.parse(JSON.stringify(this.value));
      copy.costs.unshift({})
      this.$emit('input', copy)
    },
    copyCost(p) {
      let copy = JSON.parse(JSON.stringify(p));
      copy.name = copy.name + " (copy)";
      this.costs.push(copy);
    },
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
