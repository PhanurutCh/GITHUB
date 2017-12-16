<template>
  <div class='hello'>
    <div>
      <input type="text" v-model="name" placeholder="NAME">
      <input type="text" v-model="tel" placeholder="TEL">
      <input type="text" v-model="address" placeholder="ADDRESS">
      <button @click="insertToContact(tel, name, address)">Add</button>
    </div>

    <hr>

    <ul :key="key" v-for="(contact, key) in contacts">
      <li v-if="updateKey === key">
        <input type="text" v-model="updateName" placeholder="NAME">
        <input type="text" v-model="updateTel" placeholder="TEL">
        <input type="text" v-model="updateAddress" placeholder="ADDRESS">
        <button @click="updateContact(updateTel, updateName, updateAddress)">Save</button>
      </li>
      <li v-else>
        {{contact.name}} : {{contact.tel}} : {{contact.address}}
        <button @click="setUpdateContact(key, contact)">Update</button>
        <button @click="deleteContact(key)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>

import * as firebase from 'firebase'

var config = {
  apiKey: 'AIzaSyB6HdjIcK8aTseWPP_xl6zwE5YQMkHCb4Y',
  authDomain: 'vue-firebase-bd624.firebaseapp.com',
  databaseURL: 'https://vue-firebase-bd624.firebaseio.com',
  projectId: 'vue-firebase-bd624',
  storageBucket: 'vue-firebase-bd624.appspot.com',
  messagingSenderId: '801893275439'
}
firebase.initializeApp(config)

var database = firebase.database()
var contactRef = database.ref('/contacts')

export default {
  name: 'HelloWorld',
  data () {
    return {
      contacts: {},
      tel: '',
      name: '',
      address: '',
      updateTel: '',
      updateName: '',
      updateAddress: '',
      updateKey: ''
    }
  },
  methods: {
    insertToContact (tel, name, address) {
      let data = {
        tel: tel,
        name: name,
        address: address

      }
      contactRef.push(data)
      this.tel = ''
      this.name = ''
      this.address = ''
    },
    setUpdateContact (key, contact) {
      this.updateKey = key
      this.updateTel = contact.tel
      this.updateName = contact.name
      this.updateAddress = contact.address
    },
    updateContact (tel, name, address) {
      contactRef.child(this.updateKey).update({
        tel: tel,
        name: name,
        address: address
      })
      this.updateKey = ''
      this.updateTel = ''
      this.updateName = ''
      this.updateAddress = ''
    },
    deleteContact (key) {
      contactRef.child(key).remove()
    }
  },
  mounted () {
    contactRef.on('value', (snapshot) => {
      this.contacts = snapshot.val()
    })
  }
}
</script>

<!-- Add 'scoped' attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
