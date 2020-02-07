<template>
  <div>
    <div class="centerDiv">
      <div class="card customCard" style="width: 22rem;">
        <img src="/images/bot.png" class="card-img-top" alt="..." />

        <div class="card-body">
          <h5 class="card-title centerTitleClass">Goku@911</h5>
          <hr />
          <div ref="chatBox" class="chatBox">
            <div v-for="(el, i) in chatHistory" :key="i">
              <span class="badge badge-primary">{{ el.query }}</span>
            </div>
          </div>
        </div>

        <div class="centerHeightedButton">
          <div class="buttonCenterClass">
            <input
              ref="inputBox"
              v-model="currQuery"
              type="text"
              @keyup.enter="sendQuery"
              class="form-control"
            />
            <button href="#" @click="sendQuery" class="btn btn-primary">
              <i class="fa fa-telegram" aria-hidden="true"></i>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
const serverUrl = process.env.VUE_APP_FUNCTIONS
export default {
  name: "app",
  components: {},
  data() {
    return {
      chatHistory: [],
      id: 0,
      currQuery: "",
     
    };
  },
  created() {
    let data = {
      query: "hi",
      languageCode: "en-us"
    };
    axios
      .post(`${serverUrl}askQuery`, data)
      .then(res => {
        console.log("TCL: created -> res", res);
        let welcomeData = res.data[0].queryResult.fulfillmentText;
        this.chatHistory.push({
          id: this.id,
          query: welcomeData
        });
        this.id++;
      })
      .catch(err => {
        console.log("TCL: created -> err", err);
      });
  },

  methods: {
    async sendQuery() {
      if (this.currQuery) {
       
        this.chatHistory.push({
          id: this.id,
          query: this.currQuery
        });
        this.id++;
        this.$refs.chatBox.scrollTop = this.$refs.chatBox.scrollHeight;

        try {
          let data = {
            query: this.currQuery,
            languageCode: "en-us"
          };
          let response = await axios({
            method: "post",
            url: `${serverUrl}askQuery`,
            data
          });
          let textData = response.data[0].queryResult.fulfillmentText;
          this.chatHistory.push({
            id: this.id,
            query: textData
          });
          this.id++;
          this.currQuery = ''
        } catch (err) {
          throw err;
        }

        this.id++;
        this.$refs.chatBox.scrollTop = this.$refs.chatBox.scrollHeight + 120;
      } else {
        return;
      }
    }
  }
};
</script>

<style scoped>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.customCard {
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24) !important;
}
.centerDiv {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
}
.buttonCenterClass {
  display: flex;
  justify-content: flex-end;
}
.centerHeightedButton {
  padding: 9px;
}
.card {
  border-radius: 0;
}
.centerTitleClass {
  display: flex;
  justify-content: center;
}
.chatBox {
  padding: 5px;
  height: 30vh;
  overflow: auto;
}
.form-control {
  border-radius: 0;
}
.btn {
  border-radius: 0;
}
.badge-primary {
  white-space: pre-wrap;
  text-align: justify;
}
.fa {
  font-size: 1.36rem;
}
</style>
