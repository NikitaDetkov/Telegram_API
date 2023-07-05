<template>
  <div class="grow_1">
    <div class="bot_menu flex text_left">
      <form @submit="sendMessage" class="form_elem" id="form_telegram" method="post">
        <div v-for="(message, num_message) in messages" class="list_input" :key="num_message">
          <label class="form-label">{{ message.key_message }}</label>
          <div class="flex content_left">
            <input type="text" class="form-control" v-model="message.data_message">
            <button type="button" class="icon_delete" @click="deleteField(message.key_message)">
              <img class="icon_delete_img" src="../assets/images/delete.png">
            </button>
          </div>
        </div>
        <div v-if="!isOpenForm" class="mb-3 flex">
          <button type="button" class="btn btn_transparent" @click="openFormForAdd">Add field</button>
        </div>
        <div v-else class="add_elem_form">
          <label class="form-label">Name of field</label>
          <input type="text" v-model="nameForAdd" class="form-control mb-3">
          <div>
            <button type="button" class="btn btn-primary" @click="addField">Add</button>
            <button type="button" class="btn btn-primary" @click="isOpenForm = false">Cancel</button>
          </div>
        </div>
        <div v-if="!isOpenForm">
          <button type="submit" class="btn btn-primary">Send Message</button>
        </div>
      </form>

      <div class="border_vertical"></div>

      <div v-if="!isOpenFormLoadFile" class="flex_culomn form_elem center">
        <div class="mb-3">
          <button type="button" class="btn width_9 btn-primary" @click="openFormForFile('Photo')">Send Photo</button>
        </div>
        <div class="mb-3">
          <button type="button" class="btn width_9 btn-primary" @click="openFormForFile('Video')">Send Video</button>
        </div>
        <div class="mb-3">
          <button type="button" class="btn width_9 btn-primary" @click="openFormForFile('Audio')">Send Audio</button>
        </div>
        <div class="mb-3">
          <button type="button" class="btn width_9 btn-primary" @click="openFormForFile('Document')">Send Document</button>
        </div>
      </div>
      <div v-else class="flex_culomn form_elem">
        <label class="form-label">Comment</label>
        <input type="text" class="form-control" v-model="comment">
        <div class="flex mt-3 mb-3">
          <input id="input_file" class="input_file" type="file" @change="loadFile">
          <label id="file_name" class="btn btn_transparent" for="input_file">Select a file</label>
        </div>

        <div>
          <button type="button" class="btn btn-primary" @click="sendFile">Send {{ typeFile }}</button>
          <button type="button" class="btn btn-primary" @click="cancelSendFile">Cancel</button>
        </div>
      </div>

      <div class="border_vertical"></div>

      <div class="flex form_elem content_left">
        <div v-if="isOpenInf" class="text_left">
          <div>
                        
            <div class="list_input">
              <label class="form-label">Token</label>
              <div class="flex">
                <input type="text" class="form-control" v-model="token" disabled>
              </div>
            </div>

            <div class="list_input">
              <label class="form-label">Chat Id</label>
              <div class="flex">
                <input type="text" class="form-control" v-model="chatId" disabled>
              </div>
            </div>

            <div class="mb-3 flex">
              <button type="button" class="btn btn_transparent" @click="openFormForSet">Set token</button>
            </div>

          </div>
        </div>

        <div v-else class="text_left">        
          <div class="list_input">
            <label class="form-label">Token</label>
            <div class="flex">
              <input type="text" class="form-control" v-model="tokenForChange" placeholder="Enter your token">
            </div>
          </div>

          <div class="list_input">
            <label class="form-label">Chat Id</label>
            <div class="flex">
              <input type="text" class="form-control" v-model="chatIdForChange" placeholder="Enter your chat id">
            </div>
          </div>

          <div>
            <button class="btn btn-primary" @click="setTokenAndId">Set</button>
            <button class="btn btn-primary" @click="isOpenInf = true">Cancel</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  @import url('~assets/css/bot.css');
</style>

<script>
  import axios from 'axios'
  export default {
    name: 'BotMenu',
    layout: 'menu_layout',

    // Поля --------------------------------------------------
    data: () => ({
      tokenForChange: '', // Токен для изменения 
      chatIdForChange: '', // Id чата для изменения
      nameForAdd: '', // Название нового поля для текстового сообщения
      isOpenForm: false, // Открытие формы для задания токена и id чата
      isOpenInf: true, // Открытие формы с информацией 
      isOpenFormLoadFile: false,
      chatId: 'XXXXXXXXXXXXXXXXXXXXXXX', // Id чата
      token: 'XXXXXXXXXXXXXXXXXXXXXXX', // Токен
      typeFile: '', // Тип загружаемого файла
      comment: '', // Комментарии к загружаемому файлу
      file: null, // Файл для загрузки

      // Поля и введенные данные текстового сообщения
      messages: [
        {
          key_message: 'Sender',
          data_message: ''
        }, 
        {
          key_message: 'Message',
          data_message: ''
        }]
    }),

    // Методы --------------------------------------------------
    methods: {
      // Метод. Устанавливает токен и id чата
      setTokenAndId () {
        this.token = this.tokenForChange
        this.chatId = this.chatIdForChange
        this.tokenForChange = ''
        this.chatIdForChange = ''
        this.isOpenInf = true
      },
      // Метод. Добавляет новые поля для отправки сообщения
      addField() {
        if (this.nameForAdd !== '') {
          this.messages.push({key_message: this.nameForAdd, data_message: ''}) 
          this.nameForAdd = '' 
          this.isOpenForm = false
        } 
        else 
          alert('Error! Enter a name!')
      },
      // Метод. Удаляет поле
      deleteField(key_m) {
        this.messages.forEach(message => {
          if(message.key_message === key_m) {
            this.messages.pop(message)
          }
        })
      },
      // Метод. Открывает форму для добавления полей сообщения
      openFormForAdd() {
        this.isOpenForm = true
      },
      // Метод. Открывает форму для учтановки токена и id чата
      openFormForSet() {
        this.isOpenInf = false
      },
      // Метод. Открывает форму для отправки файла и устанавливает тип файла
      openFormForFile(typeFile) {
        this.isOpenFormLoadFile = true
        this.typeFile = typeFile
      },
      //  Метод. Меняет 
      loadFile(e) {
        this.file = e.target.files[0]
        let input = document.querySelector('#input_file')

        let label = input.nextElementSibling
        let labelVal = label.innerHTML

        const fileName = input.files[0].name
        document.querySelector('#file_name').innerHTML = fileName
      },
      //  Метод. Закрывает форму отпраки файла
      cancelSendFile() {
        this.isOpenFormLoadFile = false
        this.comment = ''
        this.file = null
      },
      //  Метод. Отправляет текстовое сообщение с помощью бота 
      sendMessage(e) {
        e.preventDefault()

        // const axios = require('axios')
        // console.log(axios)

        var messageToSend = ''
        // Формирование url
        const uriApi = `https://api.telegram.org/bot${ this.token }/sendMessage`

        // Формирование текста сообщения
        for(let i = 0; i < this.messages.length; ++i) {
          messageToSend += `<b>${this.messages[i].key_message}:</b> ${this.messages[i].data_message}\n`
          this.messages[i].data_message = ''
        }

        // POST-запрос по отправке текстового сообщения
        axios.post(uriApi, {
          chat_id: this.chatId,
          parse_mode: 'html',
          text: messageToSend
        }).then(result => alert('Message was send'), error => alert('Error'))
      },
      
      // Метод. Отправляет файл с комментариями с помощью бота
      sendFile() {
        // Формирование url
        const uriApi = `https://api.telegram.org/bot${ this.token }/send${ this.typeFile }`

        const formData = new FormData()
        formData.append('chat_id', this.chatId)
        formData.append(this.typeFile.toLowerCase(), this.file)
        formData.append('caption', this.comment) 

        // POST-запрос по отправке файла с комментарием
        axios.post(uriApi, formData, { 
          headers: { 
            'Content-Type': 'multipart/form-data'
          }  
        }).then(result => alert('Message was send'), error => alert('Error'))

        this.comment = ''
        this.file = null
        document.querySelector('#file_name').innerHTML = 'Select a file'
      }
    },
    
  }
</script>