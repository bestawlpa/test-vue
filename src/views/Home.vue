<template>
  <div class="flex flex-col min-h-screen">
    <div v-if="loading" class="flex justify-center items-center h-screen">
      <div class="loader"></div>
    </div>
    <div v-else>
      <Header />
      <div class="flex-grow flex w-full h-full pl-[65px] pr-[55px]">
        <div class=" py-[20px] w-[433px] border-t-2 border-[#eaeaeb] border-opacity-55">
          <Sidebar/>                                                                                                                                               
        </div>

        <div class=" flex flex-col mt-[10px] w-full items-center ">

          <div class=" max-w-[1100px]">
            <div class=" w-full h-[130px]">
            <div class=" flex justify-between">
              <div v-for="random in randomItems" :key="random.id" class=" flex flex-col justify-center w-[230px] h-[100px] text-black bg-[#ffffff] shadow-md rounded-2xl overflow-hidden ">
                <div class=" flex justify-between px-6">
                  <div>
                    <h1 class=" text-[12px] font-bold text-[#65748d]">{{ random.name }}</h1>
                    <h1 class=" text-[20px] font-bold text-[#354567]">${{ parseInt(random.priceUsd).toLocaleString(undefined, { minimumFractionDigits: 2, maximumFractionDigits: 2 }) }}</h1>
                  </div>
                  <div class=" w-[50px] h-[50px] bg-gradient-to-tl flex items-center justify-center rounded-lg from-[#8825c2] to-[#f20888] shadow-md">
                    <img src="/home/money-integral-line-svgrepo-com.svg" alt="" class=" w-[25px] h-[25px]">
                  </div>
                </div>
                <div class=" px-6">
                  <h1 class=" text-[12px] font-bold" :class="{ 'text-green-500': random.changePercent24Hr > 0, 'text-red-500': random.changePercent24Hr < 0 }">{{ Number(random.changePercent24Hr).toFixed(2) }}%</h1>
                </div>
                
              </div>              
            </div>
          </div> 

          <div class=" w-full h-[340px] bg-[#ffffff] shadow-md rounded-2xl flex flex-col overflow-hidden"> 
            <Content/>
          </div>
          </div>

          <div class=" mt-[50px] pl-[50px] relative -left-[340px]">
            <Footer/>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Content from '../components/Content.vue';
import Footer from '../components/Footer.vue';
import Header from '../components/Header.vue';
import Sidebar from '../components/Sidebar.vue';

export default {
  name: 'Home',
  components: {
    Header,
    Sidebar,
    Footer,
    Content
  },data() {
    return {
      randomItems:[],
      loading: true,
      error: null 
    };
  },
  mounted() {
    this.fetchDataRandom();
  },
  methods: {
    async fetchDataRandom() {
      try {
        const response = await fetch('https://komgrip.co.th/coincap/assets');
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        const jsonData = await response.json();

        this.items = jsonData.data;

        
        this.randomItems = this.items.slice(0, 4);

      } catch (error) {
        this.error = 'Error fetching data: ' + error.message;
        console.error('Error fetching data:', error);
      } finally {
        this.loading = false; 
      }
    },
    // getRandomItems(arr, count) {
    //   const shuffled = arr.sort(() => 0.5 - Math.random()); 
    //   return shuffled.slice(0, count); 
    // },
  },
};
</script>

<style scoped>

  .loader {
    width: 100px;
    aspect-ratio: 1;
    padding: 10px;
    box-sizing: border-box;
    display: grid;
    background: #fff;
    filter: blur(5px) contrast(10) hue-rotate(180deg);
    mix-blend-mode: darken;
  }
  .loader:before,.loader:after {
    content: "";
    grid-area: 1/1;
    margin: 30px 0;
    border-radius: 100px;
    background: #ff00ff;
    animation: l4 2s infinite linear;
  }
  .loader:after {
    --s:-1;
  }
  @keyframes l4{
    100% {transform: rotate(calc(var(--s,1)*1turn))}
  }
</style>