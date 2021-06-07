
<template>
  <div class="container">
    <div class="header">
      <h1>Usuários</h1>
    </div>
    <div class="table-users">
      <table>
        <thead>
          <tr>
            <th scope="col"></th>
            <th scope="col">Nome</th>
            <th scope="col">Altura 
              <div class="div-select">
                <select v-model="heightFilter" v-on="{change: heightHandler}">
                  <option value="todos">Todos</option>
                  <option value="alto">Altos</option>
                  <option value="mediano">Medianos</option>
                  <option value="baixo">Baixos</option>
                </select>
              </div>
            </th>
            <th scope="col">Intolerante a lactose
              <div class="div-select">
                <select v-model="lactoseFilter" v-on="{change: lactoseHandler}">
                  <option value="todos">Todos</option>
                  <option value="sim">Sim</option>
                  <option value="nao">Não</option>
                </select>
              </div>
            </th>
            <th scope="col">Peso 
              <div class="div-select">
                <select v-model="weightFilter" v-on="{change: weightHandler}">
                  <option value="todos">Todos</option>
                  <option value="acima">Acima do peso</option>
                  <option value="ideal">Peso ideal</option>
                  <option value="abaixo">Abaixo do peso</option>
                </select>
              </div>
            </th>
            <th scope="col">Atleta
              <div class="div-select">
                <select v-model="athleteFilter" v-on="{change: athleteHandler}">
                  <option value="todos">Todos</option>
                  <option value="sim">Sim</option>
                  <option value="nao">Não</option>
                </select>
              </div>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(user) of usersSorted" :key="user.Identificador">
            <td> {{user.Identificador}} </td>
            <td> {{user.nome_usuario}} </td>
            <td> {{user.altura}} </td>
            <!-- <td> {{lactoseToStr(index)}} </td> -->
            <td> 
              <div class="box-boolean" v-bind:class="{'class-yes': user.lactose == 1}">
                <span class="box-text">
                  <!-- {{user.lactoseToStr}} -->
                  {{user.lactose ? 'Sim' : 'Não'}}
                </span>
              </div>  
            </td>
            <td> {{user.peso}} </td>
            <!-- <td> {{atletaToStr(index)}} </td> -->
            <td> 
              <div class="box-boolean" v-bind:class="{'class-yes': user.atleta == 1}">
                <span class="box-text">
                  {{user.atleta ? 'Sim' : 'Não'}}
                </span>
              </div>  
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
  
</template>

<script>
  export default{
    el: '#app',
    data: function () {
      return {
        isHeightFiltered: false,
        isWeightFiltered: false,
        isLactoseFiltered: false,
        isAthleteFiltered: false,
        weightFilter: 'todos',
        heightFilter: 'todos',
        lactoseFilter: 'todos',
        athleteFilter: 'todos',
        users: [],
        usersSorted: [],
      }
    },
    created:function() {
      fetch('http://127.0.0.1:3000/api/users/')
        .then(res => res.json())
        .then(res => {
          this.users = res
          this.usersSorted = res
        })
    },
    methods: {
      lactoseHandler: function () {
        if(this.isWeightFiltered || this.isHeightFiltered || this.isAthleteFiltered){
          this.weightFilter = 'todos'
          this.heightFilter = 'todos'
          this.athleteFilter = 'todos'
        } 
        this.usersSorted = this.users
        
        switch (this.lactoseFilter) {
          case "sim":
              this.usersSorted = this.usersSorted.filter( user => user.lactose == 1)
              this.isLactoseFiltered = true
            break;
          case "nao":
              this.usersSorted = this.usersSorted.filter( user => user.lactose == 0)
              this.isLactoseFiltered = true
            break;
          default:
              this.usersSorted = this.users
              this.isLactoseFiltered = false
            break;
          }
      },
      athleteHandler: function () {
        if(this.isWeightFiltered || this.isHeightFiltered || this.isLactoseFiltered){
          this.weightFilter = 'todos'
          this.heightFilter = 'todos'
          this.lactoseFilter = 'todos'
        } 
        this.usersSorted = this.users
        
        switch (this.athleteFilter) {
          case "sim":
              this.usersSorted = this.usersSorted.filter( user => user.atleta == 1)
              this.isAthleteFiltered = true
            break;
          case "nao":
              this.usersSorted = this.usersSorted.filter( user => user.atleta == 0)
              this.isAthleteFiltered = true
            break;
          default:
              this.usersSorted = this.users
              this.isAthleteFiltered = false
            break;
          }
      },
      heightHandler: function () {
        if(this.isWeightFiltered || this.isLactoseFiltered || this.isAthleteFiltered){
          this.weightFilter = 'todos'
          this.lactoseFilter = 'todos'
          this.athleteFilter = 'todos'
        } 
        this.usersSorted = this.users
        
        switch (this.heightFilter) {
          case "alto":
              this.usersSorted = this.usersSorted.filter( user => Number(user.altura.replace(',', '.')) >= 1.8)
              this.isHeightFiltered = true
            break;
          case "mediano":
              this.usersSorted = this.usersSorted.filter( user =>  Number(user.altura.replace(',', '.')) >= 1.6 && 
              Number(user.altura.replace(',', '.')) < 1.8)
              this.isHeightFiltered = true
            break;
          case "baixo":
              this.usersSorted = this.usersSorted.filter( user => Number(user.altura.replace(',', '.')) < 1.6)
              this.isHeightFiltered = true
            break;
          default:
              this.usersSorted = this.users
              this.isHeightFiltered = false
            break;
          }
      },
      weightHandler: function () {
        if(this.isHeightFiltered || this.isLactoseFiltered || this.isAthleteFiltered){
          this.heightFilter = 'todos'
          this.lactoseFilter = 'todos'
          this.athleteFilter = 'todos'
        }
        this.usersSorted = this.users
        
        switch (this.weightFilter) {
          case "acima":
            this.usersSorted = this.usersSorted.filter( user => Number(user.peso.replace(',', '.')) >= 90)
            this.isWeightFiltered = true
            break;
          case "ideal":
            this.usersSorted = this.usersSorted.filter( user =>  Number(user.peso.replace(',', '.')) >= 70 && 
            Number(user.peso.replace(',', '.')) < 90)
            this.isWeightFiltered = true
            break;
          case "abaixo":
            this.usersSorted = this.usersSorted.filter( user => Number(user.peso.replace(',', '.')) < 70)
            this.isWeightFiltered = true
            break;
          default:
            this.usersSorted = this.users
            this.isWeightFiltered = false
            break;
          }
      },
    },
  }
</script>

<style scoped>
  @import './../assets/style.css';
</style>