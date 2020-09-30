<template>
  <div class="col-md-12">
    <div class="col-md-4">
      <b-form-group
        label="Enter Amount"
        label-align="left"
        label-cols-sm="4"
        label-cols-lg="3"
      >
        <b-form-input
          v-model="amount"
          type="number"
          placeholder="Enter amount"
          size="sm"
          @keyup="convertedAmount()"
        ></b-form-input>
      </b-form-group>
    </div>
    <div class="col-md-4">
      <b-form-group
        label="Convert From :"
        label-align="left"
        label-cols-sm="4"
        label-cols-lg="3"
      >
        <b-form-select
          v-model="currencyfrom"
          :options="options"
          v-on:change="convertedAmount()"
          size="sm"
        ></b-form-select>
      </b-form-group>
    </div>
    <div class="col-md-4">
      <b-form-group
        label="Convert From :"
        label-align="left"
        label-cols-sm="4"
        label-cols-lg="3"
      >
        <b-form-select
          v-model="currencyto"
          :options="options"
          v-on:change="convertedAmount()"
          size="sm"
        ></b-form-select>
      </b-form-group>
    </div>
    <p v-if="finalAmount != 0 && finalAmount">{{ finalAmount }}</p>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from "vue-property-decorator";
import axios from "axios";

@Component
export default class Currency extends Vue {
  private url = "https://api.exchangeratesapi.io/latest";
  private amount = 1;
  private options: Array<object> = [
    { value: "USD", text: "US Dollar" },
    { value: "EUR", text: "Euro" },
    { value: "INR", text: "Indian Rupee" },
    { value: "AUD", text: "Australian dollar" },
    { value: "BGN", text: "Bulgarian lev" },
    { value: "BRL", text: "Brazilian real" },
    { value: "JPY", text: "Japanese yen" }
  ];
  private currencyfrom = "INR";
  private currencyto = "USD";
  finalAmount = 0;
  apiData: any;

  async mounted() {
    await this.getData("INR");
    await this.convertedAmount();
  }

  async getData(from: string) {
    const modifiedUrl = from ? this.url + "?base=" + from : this.url;
    const response = await axios
      .get(modifiedUrl)
      .then(r => {
        return (this.apiData = r);
      })
      .catch(function(error) {
        // handle error
        console.log(error);
      });
  }

  async convertedAmount() {
    const from = this.currencyfrom;
    const to = this.currencyto;
    let currentRate = 0;
    if (this.apiData && this.apiData.data.base == this.currencyfrom) {
      currentRate = this.apiData.data.rates[to];
    } else {
      const r = (await this.getData(from)) as any;
      currentRate = this.apiData.data.rates[to];
    }
    this.finalAmount = currentRate * this.amount;
  }
}
</script>

<style scoped></style>
