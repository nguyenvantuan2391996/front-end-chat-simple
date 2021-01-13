<template>
  <div>
    <button v-on:click="increase(1)">increase</button>
    <button v-on:click="a(1)">decrease</button>
    <p>{{count}}</p>

    <input v-model="txtName">
    <p>Name : {{txtName}}</p>
  </div>
</template>

<script>

export default {
  name: 'test',
  data () {
    return {
      count: 0,
      txtName: ''
    }
  },
  // define methods under the `methods` object
  methods: {
    increase: function (step) {
      this.count += step
    },
    a: function (step) {
      this.decrease(step)
    },
    decrease: function (step) {
      this.count -= step
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>

</style>
