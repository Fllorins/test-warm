<script>
import AppHeader from './components/AppHeader.vue'
import AppCard from './components/AppCard.vue'

export default {
  name: 'App',
  components: {
    AppHeader,
    AppCard
  },
  data() {
    return {
      data: [],
      cards: [
        {
          title: 'Стандартный',
          price: 100,
          currency: 'рублей'
        },
        {
          title: 'Продвинутый',
          price: 150,
          currency: 'рублей'
        }
      ],
      options: ['RUB', 'KZT', 'CNY'],
      rateRub: '',
      rateKzt: '',
      rateCny: '',
      amountOne: 100,
      amountTwo: 150,
      selected: 'RUB'
    }
  },
  methods: {
    fetchData(option) {
      fetch(`https://v6.exchangerate-api.com/v6/068d3523bb1fc75a74761d99/latest/${this.options[0]}`)
        .then((res) => res.json())
        .then((data) => {
          this.data = data
          this.rateRub = data.conversion_rates[this.options[0]]
          this.rateKzt = data.conversion_rates[this.options[1]]
          this.rateCny = data.conversion_rates[this.options[2]]

          if (option === 'RUB') {
            this.cards[0].price = this.amountOne * this.rateRub
            this.cards[1].price = this.amountTwo * this.rateRub
          }

          if (option === 'KZT') {
            this.cards[0].price = Math.round(this.amountOne * this.rateKzt)
            this.cards[0].currency = 'тенге'
            this.cards[1].price = Math.round(this.amountTwo * this.rateKzt)
            this.cards[1].currency = 'тенге'
          }

          if (option === 'CNY') {
            this.cards[0].price = Math.round(this.amountOne * this.rateCny)
            this.cards[0].currency = 'юаней'
            this.cards[1].price = Math.round(this.amountTwo * this.rateCny)
            this.cards[1].currency = 'юаней'
          }
        })
    }
  },
  mounted() {
    this.fetchData()
  }
}
</script>

<template>
  <div class="wrapper">
    <app-header></app-header>
    <main>
      <section class="flex justify-center flex-col">
        <div class="container">
          <div class="currency">
            <select class="text-black" @change="fetchData($event.target.value)">
              {{
                selected
              }}
              <option class="" v-for="(option, index) in options" :key="index" :value="option">
                {{ option }}
              </option>
            </select>
          </div>
          <div class="flex justify-center flex-wrap gap-6">
            <app-card v-for="card in cards" :key="card.title">
              <h3 class="text-xl font-bold uppercase my-5">{{ card.title }}</h3>
              <p class="text-center">
                {{ card.price }} {{ card.currency }} в месяц или
                {{ Math.floor(card.price * 12 - Math.round((card.price * 12 * 20) / 100)) }}
                {{ card.currency }} в год
              </p>
            </app-card>
          </div>
        </div>
      </section>
    </main>
  </div>
</template>
