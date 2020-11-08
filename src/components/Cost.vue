<template>
  <div class="container">
    <div class="card p-2">
      <form @submit.prevent @input="$emit('input', value)">
        <div class="row justify-content-center">
          <div class="form-group col-md-6">
            <label>Name</label>
            <input
              type="text"
              class="form-control"
              placeholder="Cost Name"
              v-model="value.name"
            />
          </div>
        </div>

        <div class="form-group">
          <div class="form-check">
            <input
              class="form-check-input"
              type="checkbox"
              v-model="value.copay"
            />
            <label class="form-check-label" for="gridCheck">
              Covered By Copay
            </label>
          </div>
        </div>

        <div class="form-row">
          <div class="form-group col-md-4">
            <label>Total Cost Before Insurance</label>
            <input
              type="number"
              class="form-control"
              v-model="value.total_cost"
            />
          </div>

          <div
            class="form-group col-md-4 text-left"
            style="padding-left: 10%"
            v-if="value.copay"
          >
            <div class="form-check">
              <input
                class="form-check-input"
                type="radio"
                v-model="value.type"
                value="appt"
                checked
              />
              <label class="form-check-label">
                Appointment
              </label>
            </div>
            <div class="form-check">
              <input
                class="form-check-input"
                type="radio"
                v-model="value.type"
                value="prescription"
              />
              <label class="form-check-label" >
                Prescription
              </label>
            </div>
            <div class="form-check">
              <input
                class="form-check-input"
                type="radio"
                v-model="value.type"
                value="other"
              />
              <label class="form-check-label" >
                Other
              </label>
            </div>
          </div>

           <div class="form-group col-md-2">
            <label>Quantity</label>
            <input
              type="number"
              class="form-control"
              v-model="value.quantity"
            />
          </div>

        </div>

       
        <button
          @click="$emit('remove', value)"
          class="btn btn-outline-danger float-right"
        >
          <i class="far fa-trash-alt"></i>
        </button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: "Cost",
  props: {
    value: Object,
  },
  data() {
    return {
      defaults: {
        quantity: 1
      }
    }
  },
   created() {
    // set empty defaul values
    for (let key of Object.keys(this.defaults)) {
      this.value[key] = this.value[key] ? this.value[key] : this.defaults[key];
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
