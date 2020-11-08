<template>
  <div class="container mb-3">
    <div class="card p-2">
      <div class="row mt-3">
        <h4 class="text-left mr-auto ml-5">Policy</h4>
        <button class="ml-auto mr-5 btn" @click="$emit('copy', value)">
          <i class="far fa-copy"></i>
        </button>
      </div>

      <form @submit.prevent>
        <div class="row justify-content-center">
          <div class="form-group col-md-6">
            <label>Name</label>
            <input
              type="text"
              class="form-control mx-auto"
              placeholder="Policy Name"
              v-model="value.name"
            />
          </div>
        </div>

        <div class="form-row">
          <div class="form-group col-md-4">
            <label>Monthly Cost</label>
            <input
              min="0"
              type="number"
              class="form-control"
              v-model="value.monthly"
            />
          </div>
          <div class="form-group col-md-4">
            <label>Individual Deductable</label>
            <input
              min="0"
              step="25"
              type="number"
              class="form-control"
              v-model="value.individual_deductable"
            />
          </div>
          <div class="form-group col-md-4">
            <label>Family Deductable</label>
            <input
              min="0"
              step="25"
              type="number"
              class="form-control"
              v-model="value.family_deductable"
            />
          </div>
        </div>

        <div class="form-row justify-content-center">
          <div class="form-group col-md-3">
            <label>Indiv Out of Pocket</label>
            <input
              min="0"
              type="number"
              class="form-control"
              v-model="value.individual_out_of_pocket"
            />
          </div>
          <div class="form-group col-md-3">
            <label>Out of Pocket</label>
            <input
              min="0"
              type="number"
              class="form-control"
              v-model="value.out_of_pocket"
            />
          </div>
          
          <div class="form-group col-md-3">
            <label>% After Deductable</label>
            <input
              type="number"
              min="0"
              max="100"
              class="form-control"
              v-model="value.percent_after_deductable"
            />
          </div>
          <div class="form-group col-md-3">
            <label>% After Out of Pocket</label>
            <input
              type="number"
              min="0"
              max="100"
              class="form-control"
              v-model="value.percent_after_out_of_pocket"
            />
          </div>
        </div>

        <div class="form-row justify-content-center">
          <div class="form-group col-md-3">
            <label>Appt Copay</label>
            <input
              min="0"
              type="number"
              class="form-control"
              v-model="value.copay_appt"
            />
            <div class="form-check form-check-inline">
              <input
                class="form-check-input"
                type="checkbox"
                v-model="value.copay_appt_is_percent"
              />
              <label class="form-check-label" for="gridCheck">
                Is Percent
              </label>
            </div>
          </div>

          <div class="form-group col-md-3">
            <label>Prescription Copay</label>
            <input
              min="0"
              type="number"
              class="form-control"
              v-model="value.copay_prescription"
            />
            <div class="form-check form-check-inline">
              <input
                class="form-check-input"
                type="checkbox"
                v-model="value.copay_prescription_is_percent"
              />
              <label class="form-check-label" for="gridCheck">
                Is Percent
              </label>
            </div>
          </div>

          <div class="form-group col-md-3">
            <label>Other Copay</label>
            <input
              type="number"
              min="0"
              max="100"
              class="form-control"
              v-model="value.copay_other"
            />
            <div class="form-check form-check-inline">
              <input
                class="form-check-input"
                type="checkbox"
                v-model="value.copay_other_is_percent"
              />
              <label class="form-check-label" for="gridCheck">
                Is Percent
              </label>
            </div>
          </div>

          <div class="form-group col-md-3">
            <label>HSA</label>
            <input
              min="0"
              type="number"
              class="form-control"
              v-model="value.hsa"
            />
          </div>
        </div>

        <button
          @click.prevent="$emit('delete', value)"
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
  name: "PolicyForm",
  props: {
    value: Object,
  },
  data() {
    return {
      defaults: {
        monthly: 200,
        family_deductable: 2000,
        individual_deductable: 1000,
        out_of_pocket: 10000,
        individual_out_of_pocket: 6000,
        hsa: 1500,
        percent_after_deductable: 80,
        percent_after_out_of_pocket: 100,
        copay_appt: 25,
        copay_appt_is_percent: false,
        copay_prescription: 25,
        copay_prescription_is_percent: false,
        copay_other: 25,
        copay_other_is_percent: false,
      },
    };
  },
  created() {
    // set empty defaul values
    for (let key of Object.keys(this.defaults)) {
      this.value[key] = this.value[key] ? this.value[key] : this.defaults[key];
    }
  },
};
</script>

<style scoped>
</style>
