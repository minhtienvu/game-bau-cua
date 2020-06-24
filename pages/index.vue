<template>
  <div class="container">
    <h1 class="text-center">Quẩy lên mấy bạn ơi</h1>
    <h6 class="random__number">Random number: {{ random_number }}</h6>
    <h1>Choose User: {{  choose_user }}</h1>
    <h1>Point: {{ point }}</h1>
    <div class="result_random_number">
      <img v-for="(number, i) in random_number" :key="`${i}-${number}`" :src="images['image_' + number]">
    </div>
    <div class="game__maxtrix">
      <div class="position-relative" v-for="(image, key) in images" :key="image">
        <img :src="image" @click="set_user_options(image, key)">
        <div class="centered" :id="key" :ref="key">
          <span :id="`count_${key}`"></span>
        </div>
      </div>
      
    </div>

    <div class="m-auto">
      <button class="btn btn-success" @click="get_random_number()">Lắc bầu cua</button>
      <button class="btn btn-danger" @click="restart()">Restart</button>
      <button class="btn btn-danger" @click="reset_point_user()">Reset point</button>
    </div>
  </div>
</template>

<script>
import Logo from '~/components/Logo.vue'

const DEFAULT_CHOOSE_USER = {
        "1": 0,
        "2": 0,
        "3": 0,
        "4": 0,
        "5": 0,
        "6": 0
      }

export default {
  data (){
    return {
      random_number: [],
      images: {
        'image_1': '/images/huou.png',
        'image_2': '/images/bau.png',
        'image_3': '/images/ga.png',
        'image_4': '/images/ca.png',
        'image_5': '/images/cua.png', 
        'image_6': '/images/tom.png',
      },
      choose_user: {
        "1": 0,
        "2": 0,
        "3": 0,
        "4": 0,
        "5": 0,
        "6": 0
      },
      point: 100,
    }
  },
  components: {
    Logo
  },
  methods: {
    get_random_number() {
      this.random_number = [];
      for (let i = 0; i < 3; i++) { 
        this.random_number.push(Math.floor(Math.random() * (6 - 1 + 1)) + 1);
      }
      this.result_random_number();
      this.reset_choose_options();
      if(this.point <= 0){
        alert("Game over");
      }
    },
    set_user_options(image, key) {
      this.choose_user = JSON.parse(JSON.stringify(this.choose_user))
      let itemChoose = key.split('_')[1];
      this.choose_user[itemChoose] += 1;
      let numberOfOptions = Object.values(this.choose_user).reduce((value, total) => { return total += value });
      let totalPointChoose = numberOfOptions * 10;
      if(totalPointChoose > this.point){
        alert("Bạn không đủ itemChoose để đặt thêm!")
        this.choose_user[itemChoose] -= 1;
      }
      document.getElementById(`${key}`).style = 'display: block'
      document.getElementById(`count_${key}`).innerHTML = this.choose_user[itemChoose];
    },
    reset_point_user(){
      this.point = 100;
    },
    restart(){
      this.point = 100;
      this.reset_choose_user();
      this.hide_labels_count();
    },
    result_random_number(){
      for (const [option_of_users_set, number_of_users_set] of Object.entries(this.choose_user)) {
        if(this.random_number.includes(parseInt(option_of_users_set))){
          let length_option = this.random_number.filter(number => number == option_of_users_set).length;
          this.point = this.point + (10 * number_of_users_set * length_option)
        }
        else {
          this.point = this.point - (10 * number_of_users_set)
        }
      }
    },
    reset_choose_options(){
      this.reset_choose_user();
      // this.choose_user = this.choose_user.map(key)
      this.hide_labels_count();
    },
    reset_choose_user(){
      this.choose_user = JSON.parse(JSON.stringify(this.choose_user))
      this.choose_user = DEFAULT_CHOOSE_USER
    },
    hide_labels_count(){
      let labelsCount = document.querySelectorAll('*[id^="image_"]');
      labelsCount.forEach(function(labelCount){
        labelCount.style = 'display: none'
      })
    }
  }
}
</script>

<style>
.random__number {
  text-align: center;
}

.container {
  margin: 0 auto;
  min-height: 100vh;
  margin-top: 50px;
}

.result_random_number {
  width: 400px;
  display: flex;
  justify-content: space-between;
  margin: 0 auto;
  margin-bottom: 30px;
}

.result_random_number img{
  width: 30%;
}

.game__maxtrix {
  width: 700px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

.game__maxtrix img {
  width: 100%;
}

.centered {
  position: absolute;
  top: 40%;
  left: 40%;
  color: white;
  background: red;

  height: 50px;
  width: 50px;
  text-align: center;
  line-height: 50px;
  border-radius: 99px;
  font-weight: 600;
  font-size: 20px;
  display: none;
}
</style>
