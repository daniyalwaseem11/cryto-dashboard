<template>
  <div>
    <div class="loader" v-if="loader">
      <img src="@/assets/loader2.webp" alt="" />
    </div>
    <div class="overlay" @click="close" v-if="popup"></div>
    <div class="px-5 popups" v-if="popup">
      <div class="mb-3" style="display: flex; flex-direction: column">
        <label for="exampleFormControlInput1" class="form-label">Name</label>
        <span>{{ name }}</span>
      </div>
      <div class="mb-3" style="display: flex; flex-direction: column">
        <label for="exampleFormControlInput1" class="form-label">Email</label>
        <span>{{ email }}</span>
      </div>
      <div class="mb-3" style="display: flex; flex-direction: column">
        <label for="exampleFormControlInput1" class="form-label">Phone</label>
        <span>{{ phone }}</span>
      </div>
      <div class="mb-3">
        <label for="exampleFormControlInput1" class="form-label">Message</label>
        <textarea
          v-model="message"
          class="form-control"
          cols="30"
          rows="10"
          disabled
        ></textarea>
      </div>
    </div>
    <div style="padding: 1rem 2rem">
      <table>
        <tr>
          <th>Name</th>
          <th>Phone</th>
          <th>Email</th>
          <th>Action</th>
        </tr>
        <tr v-for="contact in contacts" :key="contact._id">
          <td style="width: 30%">
            {{ contact.Name }}
          </td>
          <td>{{ contact.Phone }}</td>
          <td>{{ contact.Email }}</td>
          <td style="display: flex; gap: 1rem; height: 100%">
            <i class="fa fa-eye" @click="ViewDialog(contact)"></i>
          </td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import Swal from "sweetalert2";

definePageMeta({
  layout: "dashboard",
});
</script>
<script>
export default {
  name: "DemoNewContactUs",

  data() {
    return {
      contacts: [],
      name: "",
      phone: "",
      email: "",
      message: "",
      popup: false,
      config: null,
      loader: false,
    };
  },

  mounted() {
    this.config = useRuntimeConfig();
    if (localStorage.getItem("UserSession") == null) {
      navigateTo("/Admin/Login");
    }
    var session = JSON.parse(localStorage.getItem("UserSession"));
    console.log("asdf");
    if (new Date(session.exp).getTime() <= new Date().getTime()) {
      console.log("Session Expired");
      localStorage.removeItem("UserSession");
      navigateTo("/Admin/Login");
    } else {
      this.loader = true;

      axios.get(`${this.config.public.BaseUrl}/Contacts`).then((res) => {
        this.loader = false;
        this.contacts = res.data.data;
      });
    }
  },

  methods: {
    ViewDialog(contact) {
      this.name = contact.Name;
      this.phone = contact.Phone;
      this.email = contact.Email;
      this.message = contact.Message;
      this.popup = true;
    },
    close() {
      this.popup = false;
    },
  },
};
</script>

<style lang="scss" scoped>
.popups {
  position: absolute;
  top: 50%;
  left: 55%;
  transform: translate(-50%, -50%);
  background: white;
  padding: 1rem 2rem;
  width: 70vw;
  max-height: 90vh;
  overflow: auto;
}
.overlay {
  position: fixed;
  background: black;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  opacity: 0.4;
}
table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
  color: black;
}

td,
th {
  border: 1px solid #fff;
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #fff;
}
.loader {
  width: 100vw;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 7%;
  z-index: 100;
  background: white;
  opacity: 0.5;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: 100ms ease-in-out;
}
</style>
