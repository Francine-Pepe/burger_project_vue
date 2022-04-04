<template>
  <div id="burger_table" v-if="burgers">
      <Message :msg="msg" v-show="msg" />
    <div>
      <div id="burger_table_heading">
        <div class="order_id">#:</div>
        <div>Client:</div>
        <div>Bread:</div>
        <div>Meat:</div>
        <div>Opcionais:</div>
        <div>Actions:</div>
      </div>
    </div>
    <div id="burger_table_rows">
      <div class="burger_table_row" v-for="burger in burgers" :key="burger.id">
        <div class="order_number">{{ burger.id }}</div>
        <div>{{ burger.name }}</div>
        <div>{{ burger.bread }}</div>
        <div>{{ burger.meat }}</div>
        <div>
          <ul v-for="(opcional, index) in burger.opcionais" :key="index">
            <li>{{ opcional }}</li>
          </ul>
        </div>
        <div>
            <!-- @change é um trigger para fazer o update do evento -->
          <select
            name="status"
            class="status"
            @change="updateBurger($event, burger.id)" 
            
          >
            <option
              :value="s.type"
              v-for="s in status"
              :key="s.id"
              :selected="burger.status == s.type"
            >
              {{ s.type }}
            </option>
          </select>
          <button class="delete_btn" @click="deleteBurger(burger.id)">Cancel</button>
        </div>
      </div>
    </div>
  </div>
  <div v-else>
    <h2>Não há pedidos no momento!</h2>
  </div>
</template>

<script>
import Message from "./Message.vue";
export default {
  name: "Dashboard",
  components: {
    Message,
  },
  data() {
    return {
      burgers: null,
      burgers_id: null,
      status: [],
      message: null,
    };
  },
   methods: {
      async getOrders() {
        const req = await fetch('http://localhost:3000/burgers')
        const data = await req.json()
        this.burgers = data
        // Resgata os status de pedidos
        this.getStatus()
      },
      async getStatus() {
        const req = await fetch('http://localhost:3000/status')
        const data = await req.json()
        this.status = data
      },
      async deleteBurger(id) {
        const req = await fetch(`http://localhost:3000/burgers/${id}`, {
          method: "DELETE"
        });
        const res = await req.json()

        // put a message on the system (condition - when the burguer is requested, this message will appear)
        this.msg = `Order successfully removed!`;
        
        // clear msg
        setTimeout(() => this.msg = "", 3000);
        // pra forcar uma atualizacao do backend:
        this.getOrders()
      },
      async updateBurger(event, id) {
        //   pra saber que status o usuário está colocando:
        const option = event.target.value;
        const dataJson = JSON.stringify({status: option});
            // url dinâmica tem que usar os `${}`
        const req = await fetch(`http://localhost:3000/burgers/${id}`, {
            // patch é um update que atualiza somente o que enviamos (as outras informacoes nao sao alteradas)
          method: "PATCH",
          headers: { "Content-Type" : "application/json" },
          body: dataJson
        });
        const res = await req.json()

        // put a message on the system (condition - when the burguer is requested, this message will appear)
        this.msg = `The order number ${res.id} was updated to ${res.status}!`;
        
        // clear msg
        setTimeout(() => this.msg = "", 3000);
            console.log(res)
      }
    },
    mounted () {
    this.getOrders()
    }
  }
</script>

<style scoped>
#burger_table {
  max-width: 1200px;
  margin: 0 auto;
}

#burger_table_heading,
#burger_table_rows,
.burger_table_row {
  display: flex;
  flex-wrap: wrap;
}

#burger_table_heading {
  font-weight: bold;
  padding: 12px;
  border-bottom: 3px solid #333;
}

#burger_table_heading div,
.burger_table_row div {
  width: 19%;
}

.burger_table_row {
  width: 100%;
  padding: 12px;
  border-bottom: 1px solid #ccc;
}

#burger_table_heading .order_id,
.burger_table_row .order_number {
  width: 5%;
}

select {
  padding: 12px 6px;
  margin-right: 12px;
}

.delete_btn {
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

.delete_btn:hover {
  background-color: transparent;
  color: #222;
}
</style>
