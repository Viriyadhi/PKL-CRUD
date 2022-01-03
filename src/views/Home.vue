<template>
  <div>
    <form @submit.prevent="add">
      <input type="hidden" v-model="form.id" />
      <input type="text" v-model="form.name" />
      <button type="submit" v-show="!updateSubmit" v-bind:disabled="buttons">
        add
      </button>
      <button
        type="button"
        v-show="updateSubmit"
        v-bind:disabled="buttons"
        @click="update(form)"
      >
        Update
      </button>
    </form>
    <p>{{ result }}</p>
    <ul v-for="user in users" :key="user.id">
      <li>
        <span>{{ user.name }}</span> &#160; ||
        <button @click="del(user.id)" v-bind:disabled="buttons">Delete</button>
      </li>
    </ul>
  </div>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      buttons: true,
      result: null,
      form: {
        id: "",
        name: "",
      },
      users: null,
      updateSubmit: false,
    };
  },
  mounted() {
    this.load();
  },
  methods: {
    load() {
      axios
        .get("https://frontend.idaman.co.id/api/profession")
        .then((res) => {
          this.users = res.data.data;
          this.result = res.data.meta.message;
          setTimeout(() => {
            this.buttons = false;
          }, 1000);
        })
        .catch((err) => {
          console.log(err);
        });
    },
    add() {
      axios
        .post("https://frontend.idaman.co.id/api/profession", this.form)
        .then(() => {
          this.load();
          this.form.name = "";
        });
    },
    edit(user) {
      this.updateSubmit = true;
      this.form.id = user.id;
      this.form.name = user.name;
    },
    update(form) {
      return axios
        .put("https://frontend.idaman.co.id/api/profession" + form.id, {
          name: this.form.name,
        })
        .then(() => {
          this.load();
          this.form.id = "";
          this.form.name = "";
          this.updateSubmit = false;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    del(user) {
      axios
        .delete("https://frontend.idaman.co.id/api/profession/" + user)
        .then(() => {
          this.load();
        });
    },
  },
};
</script>

<style scoped>
li {
  list-style: none;
}
</style>
