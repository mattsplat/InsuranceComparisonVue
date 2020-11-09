<template>
    <div id="app">
        <h3>Insurance Compare Tool</h3>
        <div class="row justify-content-right" v-if="policies.length > 0">
            <div class="col-md-10">

                <a class="float-right mr-5" @click.prevent="clearData" v-if="!showCompare">
                    Reset Data
                </a>
                <a class="float-right mr-5" @click.prevent="toggleShow">
                    {{ showCompare ? "Hide" : "Save and Show" }} Comparison
                </a>
            </div>
        </div>

        <div v-show="!showCompare">
            <div class="container">
                <div class="card p-2 mb-5 bg-light">
                    <label>Add Policy</label>
                    <button
                            class="btn w-25 btn-outline-secondary mx-auto"
                            @click="policies.unshift({})"
                    >
                        &plus;
                    </button>
                    <a class="ml-auto mr-5"
                       v-if="policies.length > 0"
                       @click.prevent="showPolicies = !showPolicies">
                        {{ showPolicies ? "Hide" : "Show" }} Policies</a
                    >
                </div>
                <template v-for="(policy, index) in policies">
                    <policy-form
                            v-show="showPolicies"
                            v-model="policies[index]"
                            :key="index"
                            @delete="(p) => removePolicy(p, index)"
                            @copy="copyPolicy"
                    ></policy-form>
                </template>
            </div>

            <div class="container">
                <div class="card p-2 mb-5 bg-light">
                    <label>Add Person</label>
                    <button
                            class="btn w-25 btn-outline-secondary mx-auto"
                            @click="persons.push({})"
                    >
                        &plus;
                    </button>
                    <a class="ml-auto mr-5"
                       v-if="persons.length > 0"
                       @click.prevent="showPersons = !showPersons">
                        {{ showPersons ? "Hide" : "Show" }} Persons</a
                    >
                </div>
                <template v-for="(person, index) in persons">
                    <person
                            v-model="persons[index]"
                            v-show="showPersons"
                            :key="index"
                            @remove="(p) => removePerson(p, index)"
                            @copy="copyPerson"
                    ></person>
                </template>
            </div>
        </div>

        <div class="container mt-5" v-if="showCompare">
            <div class="row">
                <div
                    class="card col-md-4 pt-5"
                    v-for="(policy, index) in policies"
                    :key="index"
                >
                    <i class="card-img-top fas fa-balance-scale-left fa-10x mx-auto"></i>
                    <div class="card-body mt-3">
                        <h4 class="card-title">{{ policy.name || "Unnamed" }}</h4>
                        <p class="card-text"></p>

                        <ul class="list-group">
                            <li class="list-group-item active">
                                Yearly Policy Cost: ${{ policy.yearly_cost }}
                            </li>
                            <li class="list-group-item">
                                Copay Cost: {{ policy.copay_total }}
                            </li>
                            <li class="list-group-item">
                                Cost for Services: {{ policy.actual_cost - policy.yearly_cost }}
                            </li>
                            <li class="list-group-item">
                                Actual Cost for Year: {{ policy.actual_cost }}
                            </li>
                            <li class="list-group-item">
                                Covered by HAS: {{ policy.covered_by_hsa }}
                            </li>
                            <li class="list-group-item">
                                Paid by Insurane: ${{ policy.paid_by_insurance.toFixed(2) }}
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  import PolicyForm from "./components/PolicyForm";
  import Person from "./components/Person";

  export default {
    name: "App",
    components: {
      PolicyForm,
      Person,
    },
    mounted() {
      let savedData = localStorage.getItem('saved-insurance')

      if (savedData) {
        savedData = JSON.parse(savedData)
        for (let key of Object.keys(savedData)) {
          this[key] = savedData[key];
        }
        this.showCompare = false
      }
    },
    data() {
      return {
        policies: [],
        persons: [],
        showCompare: false,
        showPolicies: true,
        showPersons: true
      };
    },
    methods: {
      removePerson(object, index) {
        this.persons.splice(index, 1);
      },
      removePolicy(object, index) {
        this.policies.splice(index, 1);
      },
      removeCost(object, index) {
        this.costs.splice(index, 1);
      },
      copyPolicy(p) {
        let copy = JSON.parse(JSON.stringify(p));
        copy.name = copy.name + " (copy)";
        this.policies.push(copy);
      },
      copyPerson(p) {
        let copy = JSON.parse(JSON.stringify(p));
        copy.name = copy.name + " (copy)";
        this.persons.push(copy);
      },
      calculateCopay(policy) {
        if (!this.persons) {
          console.log("No Persons");
          return 0;
        }

        let policy_totals = {
          copay_total: 0,
          non_copay_total: 0,
          total_paid: 0,
          remaining_hsa: policy.hsa || 0,
          paid_by_insurance: 0
        };

        this.persons.forEach((person) => {
          if (!person.costs) {
            console.log("No Costs", person);
            return;
          }
          let person_totals = {
            //  non_copay_total: 0,
            non_copay_total: 0,
          };

          // costs loop
          person.costs.forEach((cost) => {
            if (cost.copay) {
              let copay_cost = policy["copay_" + cost.type + "_is_percent"]
                ? cost.total_cost * (policy["copay_" + cost.type] / 100)
                : policy["copay_" + cost.type];
              let total = copay_cost * (cost.quantity || 1)
              policy_totals.copay_total += total;
              person_totals.non_copay_total += total;

              policy_totals.paid_by_insurance += (cost.total_cost - copay_cost) * (cost.quantity || 1)
            } else {
              // goes towards deductable
              let total = cost.total_cost * (cost.quantity || 1);
              let total_bill = total;

              if (
                (policy_totals.non_copay_total > policy.out_of_pocket ||
                  person_totals.non_copay_total > policy.individual_out_of_pocket)
              ) {

                total = (1 - policy.percent_after_out_of_pocket / 100) * total;

              } else if (
                policy_totals.non_copay_total + total > policy.out_of_pocket ||
                person_totals.non_copay_total + total > policy.individual_out_of_pocket
              ) {
                // TODO if not reached  deductable yet account for that

                // will reach out of pocket
                let overage =
                  policy_totals.non_copay_total + total - policy.out_of_pocket >
                  person_totals.non_copay_total + total - policy.individual_out_of_pocket
                    ? policy_totals.non_copay_total + total - policy.out_of_pocket
                    : person_totals.non_copay_total + total - policy.individual_out_of_pocket;
                total = (total - overage) + (overage * (1 - policy.percent_after_out_of_pocket / 100))


              } else if (
                policy_totals.non_copay_total > policy.family_deductable ||
                person_totals.non_copay_total > policy.individual_deductable
              ) {
                // reached deductable
                total = (1 - policy.percent_after_deductable / 100) * total;

              } else if (
                (policy_totals.non_copay_total + total) > policy.family_deductable ||
                (person_totals.non_copay_total + total) > policy.individual_deductable
              ) {
                console.log('will reach deductable')
                // will reach deductable
                let overage =
                  policy_totals.non_copay_total + total - policy.family_deductable >
                  person_totals.non_copay_total + total - policy.individual_deductable
                    ? policy_totals.non_copay_total + total - policy.family_deductable
                    : person_totals.non_copay_total + total - policy.individual_deductable;
                total = (total - overage) + (overage * (1 - policy.percent_after_deductable / 100))

              }


              policy_totals.non_copay_total += total;
              person_totals.non_copay_total += total;
              policy_totals.paid_by_insurance += total_bill - total
            }
          });

          policy_totals.total_paid += person_totals.non_copay_total;
        });

        policy.copay_total = policy_totals.copay_total;
        policy.yearly_cost = policy.monthly ? policy.monthly * 12 : 0;
        policy.actual_cost = policy.yearly_cost + policy.copay_total + policy_totals.non_copay_total;

        // subtract hsa
        if (policy_totals.remaining_hsa > 0) {
          if (policy_totals.remaining_hsa > policy_totals.total_paid) {
            policy_totals.remaining_hsa -= policy_totals.total_paid
            policy_totals.total_paid = 0
          } else {
            policy_totals.total_paid -= policy_totals.remaining_hsa
            policy_totals.remaining_hsa = 0
          }
        }

        policy.total_spent = policy_totals.total_paid;
        policy.covered_by_hsa = policy.hsa - policy_totals.remaining_hsa
        policy.paid_by_insurance = policy_totals.paid_by_insurance
      },
      updateCalculations() {
        this.policies.forEach((p) => this.calculateCopay(p));
      },
      toggleShow() {
        this.saveData()
        if (!this.showCompare) {
          this.updateCalculations();
        }
        this.showCompare = !this.showCompare;
      },
      calculateAdjustments(policy, amount) {
      },
      clearData() {
        this.persons = []
        this.policies = []
        this.showCompare = false
        this.showPolicies = true
        this.showPersons = true
        localStorage.removeItem('saved-insurance')
      },
      saveData() {
        let data = JSON.stringify(this.$data)
        console.log('data', data)
        localStorage.setItem('saved-insurance', data)
      }
    },
  };
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>
