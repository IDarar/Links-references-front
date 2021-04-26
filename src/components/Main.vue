<template>
  <div class="text">
    On this page you can put an URL link to the form and see what further links
    it contains, until 3rd references's depth
  </div>
  <div class="text">
    After checking you can see the unique links tree and total number of unique
    references
  </div>

  <hr class="style1" />
  <form class="formurl">
    <input
      type="text"
      name="name"
      class="question"
      id="nme"
      v-model="url"
      autocomplete="off"
    />
    <label for="nme"><span>URL</span></label>
  </form>
  <input type="button" @click="wsConnect" class="regbutton" value="submit" />
  <hr class="style1" />
  <div id="recieved">
    <div>Your link: {{ your_link }}</div>
    <div>Total unique links: {{ number_of_unique_refs }}</div>
    <div class="list" @click="showlist">All unique links:</div>
    <ol v-if="!closed">
      <li class="link" v-for="link in all_unique_links" :key="link" href="link">
        {{ link }}
      </li>
    </ol>
    <div>
      Links tree:
      <TreeBrowser :str="strs" />
    </div>
    <div v-if="showload">
      Finding ...
      <ol v-if="!closed">
        <li v-for="link in wslinks.slice().reverse()" :key="link">
          {{ link }}
        </li>
      </ol>
    </div>
  </div>
</template>

<script>
import TreeBrowser from "./TreeBrowser.vue";
export default {
  name: "Main",
  components: {
    TreeBrowser,
  },
  data() {
    return {
      url: "",
      your_link: "",
      number_of_unique_refs: 0,
      all_unique_links: [],
      strs: [],
      closed: false,
      showload: false,
      wslinks: [],
      connection: null,
    };
  },

  methods: {
    addrow(data) {
      this.wslinks.push(data);
    },
    showlist() {
      if (this.closed == true) {
        this.closed = false;
      } else {
        this.closed = true;
      }
    },
    wsConnect() {
      console.log("starting ws");
      this.showload = true;
      this.connection = new WebSocket("https://links-references.herokuapp.com/link/");
      this.connection.onopen = () => this.connection.send(this.url);

      this.connection.onmessage = (event) => {
        console.log("Event is ", event);
        this.addrow(event.data);
        try {
          var str = JSON.parse(event.data);
          console.log("str is", str.unique_refs);
          this.your_link = str.link;
          this.number_of_unique_refs = str.number_of_unique_refs;
          this.all_unique_links = str.unique_refs;
          this.strs = str.references;
        } catch (e) {
          console.log("Event is ", event);
          this.addrow(event.data);
        }
      };

      this.connection.onclose = function (event) {
        console.log("closing ws connection ", event);
      };
    },
  },
};
</script>



<style >
.list {
  display: inline-flex;
  padding: 3px, 3px, 3px, 3px;
  background-color: rgb(192, 240, 248);
}
.link {
  margin-top: 3px;
  background-color: rgb(165, 233, 245);
}
.regbutton {
  border: 1px solid #101d2b;
  max-width: 1000px;
  min-width: 200px;
  max-height: 1500px;
  background-color: #fcfcfc;
  color: #123;
  line-height: 27px;
  margin-top: 50px;
  margin-bottom: 30px;
  padding: 0 7px;
}

hr.style1 {
  border-top: 1px solid #8c8b8b;
  margin-bottom: 50px;
}
html {
  font-family: "Lora", sans-serif;
}

input,
span,
label,
textarea {
  font-family: "Ubuntu", sans-serif;
  display: block;
  margin: 10px;
  padding: 5px;
  border: none;
  font-size: 14px;
}

textarea:focus,
input:focus {
  outline: 0;
}

input.question,
textarea.question {
  font-size: 20px;
  border-radius: 2px;
  margin: 0;
  border: none;
  width: 50%;
  background: rgba(0, 0, 0, 0);
  transition: padding-top 0.2s ease, margin-top 0.2s ease;
  overflow-x: hidden; /* Hack to make "rows" attribute apply in Firefox. */
}

input.question + label,
textarea.question + label {
  display: block;
  position: relative;
  white-space: nowrap;
  padding: 0;
  margin: 0;
  width: 30%;
  border-top: 1px solid rgb(0, 0, 0);
  -webkit-transition: width 0.4s ease;
  transition: width 0.4s ease;
  height: 0px;
}

input.question:focus + label,
textarea.question:focus + label {
  width: 80%;
}

input[type="submit"]:hover {
  background: #eee;
}

input[type="submit"]:active {
  background: #999;
}
</style>
