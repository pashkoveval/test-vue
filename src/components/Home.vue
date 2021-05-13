<template>
  <div class="home">
    <ul v-for="(dock,i) in documents" :key="i">
      <li class="docs">
        <img
          class="docs-img"
          @click="downloadDocuments(dock.id_document,dock.doc_type)"
          src="../assets/pdf.svg"
          alt="format of document"
        />
        <p class="docs-text">{{dock.doc_name}} от {{dock.date_doc}}</p>
      </li>
    </ul>
    <form action="login" v-if="!logOK" class="form">
      <h2 class="form-title">Вход в личный кабинет</h2>
      <p class="form-cubtitle">Используйте логин и пароль из памятки</p>
      <input class="form-input" type="text" name="login" id="login" v-model="login" />
      <input class="form-input" type="password" name="password" id="password" v-model="password" />
      <button class="form-btn" @click="logIn">ВОЙТИ</button>
      <h2 class="error" v-if="this.wrongIn">Введен не верный пароль или логин</h2>
    </form>
  </div>
</template>

<script>
const axios = require("axios");
import md5 from "js-md5";
import FingerprintJS from "@fingerprintjs/fingerprintjs";
export default {
  name: "Home",
  data() {
    return {
      info: null,
      login: "",
      password: "",
      imei: md5(`${this.realIMEI}`),
      realIMEI: null,
      data: null,
      logOK: false,
      dockOK: false,
      id_login: null,
      id_people: this.id_login,
      TK: null,
      documents: null,
      linkForDownloads: null,
      wrongIn: false,
    };
  },
  methods: {
    getIMEI() {
      const fpPromise = FingerprintJS.load();
      (async () => {
        const fp = await fpPromise;
        const result = await fp.get();
        this.realIMEI = result.visitorId;
      })();
    },

    logIn(e) {
      e.preventDefault();
      axios
        .get("https://host1.medsafe.tech:40443/api/client_login", {
          params: {
            json: {
              login: this.login,
              password: this.password,
              IMEI: this.imei,
              Name_app: "connect",
            },
          },
        })
        .then((response) => {
          this.data = response.data;
          this.TK = response.data[0].TK;
          this.id_login = response.data[0].id_login;
          if (response.data[0].err === 0) {
            this.logOK = !this.logOK;
          } else {
            this.wrongIn = !this.wrongIn;
          }
          if (response.data[0].id_login != 0) {
            axios
              .get("https://host1.medsafe.tech:40443" + "/api/test", {
                params: {
                  json: {
                    id_login: this.id_login,
                    id_people: this.id_login,
                    TK: this.TK,
                    IMEI: this.imei,
                    Name_app: "connect",
                    Name_event: "list_load",
                  },
                },
              })
              .then((response) => {
                this.documents = response.data.body;
                this.$emit("log", this.id_login);
                if (response.errorType === 0) {
                  this.dockOK = !this.dockOK;
                }
              })
              .catch((error) => {
                console.log(error);
              });
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    downloadDocuments(id_document, doc_type) {
      axios
        .get("https://host1.medsafe.tech:40443" + "/api/test", {
          params: {
            json: {
              id_login: this.id_login,
              id_people: this.id_login,
              TK: this.TK,
              IMEI: this.imei,
              Name_app: "connect",
              Name_event: "get_pic_path",
              id_document,
              doc_type,
            },
          },
        })
        .then((response) => {
          window.location.href =
            "https://host1.medsafe.tech:40443/" + response.data.body[0].hash;
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted() {
    this.getIMEI();
  },
};
</script>

<style lang="scss">
.home {
  height: 600px;
  width: 100%;
  max-width: 960px;
  margin: 100px auto 0;
  background: #fff;
  & ul {
    list-style: none;
  }
}

.docs {
  display: flex;
  align-items: center;
  &-img {
    width: 50px;
    margin: 5px;
    cursor: pointer;
  }
}

.form {
  display: flex;
  flex-direction: column;
  width: 250px;
  margin: auto;
  text-align: center;
  color: #797979;
  &-title {
    font-size: 30px;
    font-weight: 700;
    margin: 0;
  }
  &-subtitle {
    font-size: 18px;
  }
  &-input {
    display: block;
    width: 100%;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.428571429;
    color: #555;
    background-color: #fff;
    border: 1px solid #ccc;
    margin: 10px 0;
    &:valid {
      background: rgb(232, 240, 254);
    }
  }
  &-btn {
    transition: all 0.3s ease;
    background: #475168;
    color: #fff;
    text-transform: uppercase;
    border-radius: 4px;
    padding: 20px 40px;
    width: 50%;
    margin: 10px auto;
    border: none;
    &:hover {
      background: #48cfad;
    }
    &:active {
      transform: scale(0.9);
    }
  }
}

.error {
  font-size: 2rem;
  color: red;
}
</style>
