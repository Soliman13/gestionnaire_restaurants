<template>
  <transition>
    <div key="menu">
      <div>Menu</div>
      <!-- <div>Entrée : {{menu[0][0]}}</div>
      <div>Plat : {{menu[1]}}</div>
      <div>Dessert : {{menu[2]}}</div>-->
      <ul class="card">
        <table>
          <li v-bind:key="value" v-for="(value,key) in entrees">{{ value }} : {{key}}</li>
        </table>
      </ul>
      <ul class="card">
        <li v-bind:key="value" v-for="(value,key) in plats">{{ value }} : {{key}}</li>
      </ul>
      <ul class="card">
        <li v-bind:key="value" v-for="(value,key) in desserts">{{ value }} : {{key}}</li>
      </ul>
    </div>
  </transition>
</template>

<script>
export default {
  name: "app-menu-restaurant",
  data: () => ({
    entrees: { "Salades de tomate": "2,50€", "Vermicelles": "3€", "Vinaigrette": "3€" },
    plats: {
      "Steak frites": "6€",
      "Ailes de poulet": "4€",
      "Pates à la carbonara": "3,50€"
    },

    desserts: {
      "Tiramisu": "4€",
      "Glace à la vanille": "5€",
      "Fondant au chocolat": "3,50€"
    },
  }),
  mounted() {
  },
  methods: {
  }
};
</script>