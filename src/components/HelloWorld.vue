<template>
  <div>
    <h1>Web3 with Vue.js</h1>
    <div v-if="accounts.length">
      <p>Connected Accounts:</p>
      <ul>
        <li v-for="account in accounts" :key="account">{{ account }}</li>
      </ul>
    </div>
    <div v-else>
      <p>No accounts connected</p>
    </div>
  </div>
  <div>
    <input type="number" v-model="numValue" />
    <button @click="handleSet">Set Value</button>
    <button @click="handleGet">Get Value</button>
  </div>
</template>

<script>
import web3 from '@/web3';

import SimpleStorage from "../contracts/SimpleStorage.json";

export default {
  data() {
    return {
      accounts: [],
      numValue: 0
    };
  },
  async mounted() {
    this.accounts = await web3.eth.getAccounts();

    const networkId = await web3.eth.net.getId();
    const deployedNetwork = SimpleStorage.networks[networkId];
    console.log(Number(networkId), deployedNetwork);
    if (deployedNetwork) {
      this.contract = new web3.eth.Contract(
        SimpleStorage.abi,
        deployedNetwork && deployedNetwork.address
      );
    }
  },
  methods: {
    async handleGet() {
      console.log("value: ", this.numValue);
      const response = await this.contract.methods.get().call();
      console.log(Number(response));

    },
    async handleSet() {
      try {
        console.log("get contract");
        await this.contract.methods.set(this.numValue).send({ from: this.accounts[0] });
      } catch (err) {
        console.log(err);
      }
    }
  }

};
</script>