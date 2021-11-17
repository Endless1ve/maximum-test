
<template>
<div class="body">
  <form class="form" ref="form">
    <h1 class="form-title">Напишите нам</h1>
    <div class="select-block">
        <h3 class="select-title">Ваш филиал:</h3>
        <div class="online-block">
          <div class="select-online">
          <input value='false' v-on:input="click" type="checkbox" class="online" @click='form.city = null' @change="onChange()" v-model="form.online">
        <label for=".online" class='online-label'> Онлайн</label>
        </div>
        <select class='selector' v-model="form.city" @click="onChange()">
        <option  v-for="item in cities" :value="item.id" :key="item.title">{{item.title}}</option>
        </select>
        </div>
    </div>
    <div class="theme">
        <h3 class="theme-title">Тема обращения:</h3>
        <div class="theme-checkboxes">
            <div class="checkbox" v-for='error in recentQuestions'  :key="error.name" >
            <input v-on:input="check" :value="error.value" type="radio" class="theme-checkbox" name='all' v-model="form.errorCode" @click="form.errorInput = null" @change="onChange()">
            <label class="theme-checkbox-name" for=".theme-checkbox"> {{error.name}}</label>
            </div>
        </div>
    <input v-on:input="input" type="text" class='theme-input' v-model.trim="form.errorInput" @input='form.errorCode = null' placeholder="Введите тему ошибки" @change="onChange()">
    </div>
    <textarea v-model.trim="form.errorText" class='textarea' name="text" cols="30" rows="10" placeholder="Опишите Вашу проблему" @change="onChange()"></textarea>
    <div class="file-block">
        <label class='file-label' for=".file-input" >Загрузите лог или скриншот Вашей ошибки</label>
        <input type="file" ref="file" class="file-input" v-on:change="fileUpload() " @change="onChange()">
    </div>
    <button disabled  class='form-button' type="submit" v-on:click="subm" @change="onChange()">Отправить</button>
</form>
<div class="overlay">
  <div class="popup">
    <img src="../assets/cross.png" alt="" class="popup-close" v-on:click="close">
    <h2 class="popup-text">Успешно!</h2>
  </div>
</div>
</div>
</template>

<script>
/* eslint-disable */
export default {
  data () {
    return {
      form: {
        online: false,
        city: null,
        errorCode: null,
        errorInput: null,
        errorText: null,
        fileError: null
      },
       recentQuestions: [
        {
          name: 'Ошибка авторизации',
          value: 'authorisation',
        },
        {
          name: 'Ошибка регистрации',
          value: 'registration',
        },
        {
          name: 'Ошибка обновления',
          value: 'update',},
        {
          name: 'Ошибка переадресации',
          value: 'redirection',
        },
        {
          name: 'Ошибка соединения',
          value: 'connection',
        },
        {
          name: 'Ошибка отправки',
          value: 'send',
        }
      ],
      cities: [],
    }
  },
  mounted () {
    const axios = require('axios').default
    axios
      .get('https://60254fac36244d001797bfe8.mockapi.io/api/v1/city')
      .then(res => (this.cities = res.data))
  },
  methods: {
    click: () => {
      let selector = document.querySelector('.selector')
      let checkBox = document.querySelector('.online')
      if (checkBox.checked) {
        selector.disabled = true
        selector.value = null
      } 
      else selector.disabled = false
    },
    check: () => {
      let checkBox = document.querySelectorAll('.theme-checkbox')
      let inputText = document.querySelector('.theme-input')
      checkBox.forEach(elem => {
        if (elem.checked) {
          inputText.value = ''
        }
      })
    },
    input: () => {
      let inputText = document.querySelector('.theme-input')
      if (inputText.value !== '') {
        let checkBox = document.querySelectorAll('.theme-checkbox')
        checkBox.forEach(elem => {
          elem.checked = false
        })
      }
    },
    fileUpload: function() {
      let forma = this.form
      let fileUpldr = document.querySelector('.file-input')
      forma.fileError = fileUpldr.files[0]
    },
    onChange: () =>{
      let filialCb = document.querySelector('.online')
      let filialSelector = document.querySelector('.selector')
      let themeCb = document.querySelectorAll('.theme-checkbox')
      let themeInput = document.querySelector('.theme-input')
      let button = document.querySelector('.form-button')
      for (let i = 0; i < themeCb.length; i++){
        if((themeCb[i]. checked == true || themeInput.value !== '') && (filialCb.checked == true || filialSelector.value !== '')){
          button.disabled = false
          break
        } else button.disabled = true
      }
  },
  subm: function (event) {
    event.preventDefault();
    const axios = require('axios').default
    let forma = this.form
    let overlay = document.querySelector('.overlay')
    axios.post("https://60254fac36244d001797bfe8.mockapi.io/api/v1/send-form", JSON.stringify(forma))
    .then(() => {
    overlay.style.display = 'block'
    this.$refs.form.reset();
        forma.online = false,
        forma.city = null,
        forma.errorCode = null,
        forma.errorInput = null,
        forma.errorText = null,
        forma.fileError = null
  })
  .catch(alert('Произошла ошибка'))},
  
  close: function () {
    let overlay = document.querySelector('.overlay')
    overlay.style.display = 'none'
  }
}
}
</script>

<style>
  body{
  background:#59ABE3;
  }
 .form{
   width: 450px;
   padding:20px 30px;
   background:#e6e6e6;
   border-radius:8px;
   box-shadow:0 0 40px -10px #000;
   margin: auto;
 }

 .form-title{
   font-family:'Montserrat',sans-serif;
   margin: 10px 0;
   padding-bottom: 10px;
   width: 250px;
   color:#78788c;
   border-bottom: 3px solid #78788c;
 }

 .select-block{
   display: flex;
   flex-direction: column;
   align-items: flex-start;
 }

 .online-block{
   width: 150px;
   display: flex;
   flex-direction: column;
   align-items: flex-start;
 }

 .online-label{
  font-size: 15px;
  color:#5a5a5a;
  
 }

 .selector{
  margin-top: 10px;
  font-size: 14px; 
  font-family:'Montserrat',sans-serif;
  color: #444; 
  line-height: 1.3; 
  padding: 3px; width: 100%; 
  max-width: 100%; 
  box-sizing: border-box; 
  border: 1px solid #aaa;
  box-shadow: 0 1px 0 1px rgba(0,0,0,.04); 
  border-radius: .5em;
  -moz-appearance: none;
  -webkit-appearance: none;
  appearance: none;
  background-color: #fff; 
  background-image: url('data:image/svg+xml;charset=US-ASCII,%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%22292.4%22%20height%3D%22292.4%22%3E%3Cpath%20fill%3D%22%23007CB2%22%20d%3D%22M287%2069.4a17.6%2017.6%200%200%200-13-5.4H18.4c-5%200-9.3%201.8-12.9%205.4A17.6%2017.6%200%200%200%200%2082.2c0%205%201.8%209.3%205.4%2012.9l128%20127.9c3.6%203.6%207.8%205.4%2012.8%205.4s9.2-1.8%2012.8-5.4L287%2095c3.5-3.5%205.4-7.8%205.4-12.8%200-5-1.9-9.2-5.5-12.8z%22%2F%3E%3C%2Fsvg%3E'), linear-gradient(to bottom, #ffffff 0%,#e5e5e5 100%); 
  background-repeat: no-repeat, repeat;
  background-position: right .7em top 50%, 0 0;
  background-size: .65em auto, 100%; 
 }

 .select-title{
  margin:28px 0 14px;
  font-family:'Montserrat',sans-serif;
  font-size:14px;
  color:#5a5a5a;
 }
 .theme{
  display: flex;
  flex-direction: column;
  align-items: flex-start;
 }

  .theme-title{
    margin:28px 0 14px;
    font-size:14px;
    color:#5a5a5a;
  }
  .theme-checkboxes{
    display: grid;
    grid-template-rows: 0.5fr 0.5fr 0.5fr;
    grid-template-columns: 200px 200px;
    margin: 0;
    gap: 5px;
  }

  .checkbox{
    display: flex;
    flex-direction: row;
    align-items: flex-start;
  }

  .theme-checkbox{
    margin: 0 10px 0 0;
  }

  .theme-checkbox-name{
  font-size:14px;
  color:#5a5a5a;
  }
 .theme-input{
   width:80%;
   padding:10px;
   margin-top: 15px;
   box-sizing:border-box;
   background:none;
   outline:none;
   resize:none;
   border:0;
   font-family:'Montserrat',sans-serif;
   transition:all .3s;
   border:2px solid #bebed2;
 }
 .theme-input:focus{
   border:2px solid #78788c
   }

   .textarea{
    float: left;
    width: 80%;
    margin-top: 20px;
    box-sizing:border-box;
    background:none;
    outline:none;
    resize:none;
    border:0;
    font-family:'Montserrat',sans-serif;
    transition:all .3s;
    border:2px solid #bebed2;
    align-self: start;
   }

   .textarea:focus{
   border:2px solid #78788c
   }

   .file-block{
     clear: both;
     margin-top: 20px;
     display: flex;
    flex-direction: column;
    align-items: flex-start;
   }

   .file-label{
    margin: 10px 0;
    font-size: 14px;
    color: #5a5a5a;
   }
  .form-button:disabled{
    cursor: default;
    color: #5a5a6e;
    background: 0;
  }
  .form-button{
     padding: 8px 20px;
     margin: 15px 0 0;
     font-family:'Montserrat',sans-serif;
     border: 2px solid #78788c;
     background:#78788c;
     color:#fff;
     cursor: pointer;
     transition: all .5s;
     }

  .form-button:hover{
    opacity: .8;
  }
  .overlay{
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    background-color:rgba(0,0,0,.5);
    display: none;
  }
  .popup{
    position: absolute;
    top: 10%;
    left: 46%;
    width: 200px;
    height: 200px;
    background:#e6e6e6;
    border-radius:8px;
    box-shadow:0 0 40px -10px #000;
  }
  .popup-close{
    width: 20px;
    margin: 10px;
    cursor: pointer;
    float: right;
  }
  .popup-text{
    font-family:'Montserrat',sans-serif;
    margin: 80px auto;
    font-size:20px;
    color:#5a5a5a;
  }
</style>
