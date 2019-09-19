<template>
  <div id="app">
    <city-picker 
    title="代理投放平台" 
    :rules="rules" 
    :loading="loading" 
    @loginSubmit='loginSubmit'>
    </city-picker>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    const validateName = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入账号"));
        return;
      }
      callback();
    };
    const validatePass = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入密码"));
        return;
      }
      callback();
    };
    return {
      rules: {
        username: [{ validator: validateName, trigger: "blur" }],
        password: [{ validator: validatePass, trigger: "blur" }]
      },
      loading:true
    };
  },

  methods: {
    loginSubmit(param){
      console.log(param)
      setTimeout(()=>{
        this.loading = false;
      },1000)
    }
  }
};
</script>

<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
  user-select: none;
}
</style>
