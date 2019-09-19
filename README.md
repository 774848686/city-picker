# lhdLogin
cms后台系统的公用登录页面

### 插件的安装
#### NPM 
```
npm i lhdcmslogin
```
#### 引入插件
```
import Vue from 'vue';
import lhdLogin from 'lhdcmslogin';

Vue.use(lhdLogin);
```

#### 基本用法  
```html
<template>
  <div id="app">
    <lhd-login 
    title="代理投放平台" 
    :rules="rules" 
    :loading="loading" 
    @loginSubmit='loginSubmit'>
    </lhd-login>
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

```

### API  
| 参数 | 说明 | 类型  |  
| - | :- | :- | :-: |  
| title | 要传入的标题 | String |  
| rules | 校验对象| Object | 
| loading | 按钮的loading | Boolean |
| loginSubmit | 提交事件 | Function |

