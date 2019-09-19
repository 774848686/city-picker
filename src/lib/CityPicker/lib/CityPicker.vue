<template>
  <div class="city-picker">
    <address-list></address-list>
  </div>
</template>

<script>
import addressList from './components/AddressList'
export default {
  name: "cityPicker",
  components:{
    addressList
  },
  props: {},
  data() {
    return {
    };
  },
  created() {},
  methods: {
  },
  watch: {
    
  }
};
</script>
<style rel="stylesheet/scss" lang="scss"></style>
