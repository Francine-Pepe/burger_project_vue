<template>
  <div>
    <Message :msg="msg" v-show="msg" />
    <div>
      <form id="burger_form" method="POST" @submit="createBurguer">
        <div class="input_container">
          <label for="name">Client Name</label>
          <input
            type="text"
            id="name"
            name="name"
            v-model="name"
            placeholder="Type your name"
          />
        </div>
        <div class="input_container">
          <label for="bread">Choose your bread:</label>
          <select name="bread" id="bread" v-model="bread">
            <option value="">Select your bread</option>
            <option v-for="bread in breads" :key="bread.id" :value="bread.type">
              {{ bread.type }}
            </option>
          </select>
        </div>
        <div class="input_container">
          <label for="meat">Choose your meat:</label>
          <select name="meat" id="meat" v-model="meat">
            <option value="">Select your meat</option>
            <option v-for="meat in meats" :key="meat.id" :value="meat.type">
              {{ meat.type }}
            </option>
          </select>
        </div>
        <div class="input_container" id="opcionais_container">
          <label for="opcionais" id="opcionais_title"
            >Choose your options</label
          >
          <div
            class="checkbox_container"
            v-for="opcional in opcionaisdata"
            :key="opcional.id"
          >
            <input
              type="checkbox"
              name="opcionais"
              v-model="opcionais"
              value="opcional.type"
            />
            <span> {{ opcional.type }} </span>
          </div>
        </div>

        <div class="input_container">
          <input type="submit" class="submit-btn" value="Create my Burger" />
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import Message from "./Message.vue";
export default {
  name: "BurgerForm",
  components: {
    Message,
  },
  data() {
    return {
      // data from the server:
      breads: null,
      meats: null,
      opcionaisdata: null,

      // sent data:
      name: null,
      bread: null,
      meat: null,
      opcional: [],
      status: "Requested",
      message: null,
    };
  },
  methods: {
    async getIngredients() {
      // req = request
      const req = await fetch("http://localhost:3000/ingredients");

      // will require and await for the json
      const data = await req.json();

      this.breads = data.breads;
      this.meats = data.meats;
      this.opcionaisdata = data.opcionais;
      console.log(data);
    },
    async createBurguer(e) {
      e.preventDefault();

      const data = {
        name: this.name,
        meat: this.meat,
        bread: this.bread,
        opcionais: Array.from(this.opcionais),
        status: "Requested",
      };

      const dataJson = JSON.stringify(data);

      const req = await fetch("http://localhost:3000/burgers", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: dataJson,
      });

      const res = await req.json();

      // put a message on the system (condition - when the burguer is requested, this message will appear)
      this.msg = `Order number ${res.id} was successfully requested. Thank you!`;
      
      // clear msg
       setTimeout(() => this.msg = "", 3000);
      

      // setTimeout(() => {
      //   this.msg = true;
      // }, 2000);

      // clean the fields
      this.name = "";
      this.meat = "";
      this.bread = "";
      this.opcionais = [];
    },
  },
  // when the component is initialized, this MOUNTED calls it
  mounted() {
    this.getIngredients();
    
  },
};
</script>

<style scoped>
#burger_form {
  max-width: 400px;
  margin: 0 auto;
}

.input_container {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

label {
  font-weight: bold;
  margin-bottom: 15px;
  color: #222;
  padding: 5px 10px;
  border-left: 4px solid #fcba03;
}

input,
select {
  padding: 5px 10px;
  width: 300px;
}

#opcionais_container {
  flex-direction: row;
  flex-wrap: wrap;
  display: flex;
}

#opcionais_title {
  width: 100%;
}

.checkbox_container {
  display: flex;
  align-items: flex-start;
  width: 50%;
  margin-bottom: 20px;
}

.checkbox_container span,
.checkbox_container input {
  width: auto;
}

.checkbox_container span {
  margin-left: 6px;
  font-weight: bold;
}

.submit-btn {
  background-color: #222;
  color: #fcba03;
  font-weight: bold;
  border: 2px solid #222;
  padding: 10px;
  font-size: 16px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
}

.submit-btn:hover {
  background-color: transparent;
  color: #222;
}
</style>
