<template>
    <div class="w-full h-[340px]">
        <div v-if="loading" class="flex justify-center items-center h-full">
           <div class="loader"></div>
        </div>
        
        <div v-else>
            <!-- btn-next,btn-previous -->
            <div class="w-full h-[100px] flex justify-between items-center px-8 ">
                <h1 class="text-[#364667] text-xl font-medium">Cryptocurrencies</h1>
                <div class="flex w-[150px] justify-between">
                    <button @click="previousPage" :disabled="currentPage === 1" class="w-[70px] h-[30px] bg-[#ffffff] flex items-center justify-center rounded-lg border-2 border-[#cb0b9f]">
                        <img src="/home/arrow-prev-svgrepo-com.svg" alt="" class="w-[18px] h-[18px]">
                    </button>
                    <button @click="nextPage" :disabled="currentPage === totalPages" class="w-[70px] h-[30px] bg-[#ffffff] flex items-center justify-center rounded-lg border-2 border-[#cb0b9f]">
                        <img src="/home/arrow-prev-svgrepo-com (1).svg" alt="" class="w-[18px] h-[18px]">
                    </button>
                </div>
            </div>

            <div class="w-full h-full flex flex-col">
                <div class="h-[40px] flex justify-between items-center text-[16px] font-semibold text-[#a8b3c5] pr-[48px]">
                    <div class="w-[100px] h-[40px] pl-4 flex items-center justify-center">
                        <h1>NO</h1>
                    </div>
                    <div class="w-[230px] h-[40px] flex items-center justify-start">
                        <h1>NAME</h1>
                    </div>
                    <div class="w-[65px]  h-[40px] flex items-center justify-center">
                        <h1>SYMBOL</h1>
                    </div>
                    <div class="w-[300px] h-[40px] flex items-center justify-end">
                        <h1>SUPPLY/MAX SUPPLY</h1>
                    </div>
                    <div class="w-[120px] h-[40px] flex items-center justify-end">
                        <h1>USD</h1>
                    </div>
                    <div class="w-[110px] h-[40px] flex items-center justify-end">
                        <h1>24 HR</h1>
                    </div>
                </div>

                <div v-for="(item) in paginatedItems" :key="item.id" class="border-t-2 pr-[48px] border-[#eaecee] h-[40px] w-full flex justify-between text-[#68748e] text-[14px] font-medium">
                    <div class="w-[100px] h-[40px] pl-4 flex items-center justify-center">
                        <h1>{{ item.rank }}.</h1>
                    </div>
                    <div class="w-[224px] h-[40px] flex items-center justify-start">
                        <h1>{{ item.name }}</h1>
                    </div>
                    <div class="w-[65px] h-[40px] flex items-center justify-center">
                        <h1>{{ item.symbol }}</h1>
                    </div>
                    <div class="w-[300px] h-[40px] flex items-center justify-end">
                        <h1 v-if="item.maxSupply === null">
                            {{ parseInt(item.supply).toLocaleString() }} / No Limit
                        </h1>
                        <h1 v-else>
                            {{ parseInt(item.supply).toLocaleString() }} / {{ parseInt(item.maxSupply).toLocaleString() }}
                        </h1>
                    </div>
                    <div class="w-[120px] h-[40px] flex items-center justify-end">
                        <h1>${{ parseInt(item.priceUsd).toLocaleString(undefined, { minimumFractionDigits: 2, maximumFractionDigits: 2 }) }}</h1>
                    </div>
                    <div class="w-[110px] h-[40px] flex items-center justify-end">
                        <h1 :class="{ 'text-[#84d619]': item.changePercent24Hr > 0, 'text-[#eb1110]': item.changePercent24Hr < 0 }">
                            {{ Number(item.changePercent24Hr).toFixed(2) }}%
                        </h1>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
  name: 'Content',
  data() {
    return {
      items: [],
      loading: true,
      error: null,
      currentPage: 1,
      itemsPerPage: 5,
    };
  },
  mounted() {
    this.fetchData();
  },
  computed: {
    paginatedItems() {
      const start = (this.currentPage - 1) * this.itemsPerPage;
      const end = start + this.itemsPerPage;
      return this.items.slice(start, end);
    },
    totalPages() {
      return Math.ceil(this.items.length / this.itemsPerPage);
    },
  },
  methods: {
    async fetchData() {
      try {
        const response = await fetch('https://komgrip.co.th/coincap/assets');
        if (!response.ok) {
          throw new Error('Page Not Found');
        }
        const jsonData = await response.json();
        this.items = jsonData.data;
      } catch (error) {
        this.error = error.message;
        console.error('Error fetching data:', error);
      } finally {
        this.loading = false;
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    previousPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
  },
};
</script>

<style scoped>
    .loader {
        width: 50px;
        aspect-ratio: 1;
        border-radius: 50%;
        background: 
            radial-gradient(farthest-side,#f03355 95%,#0000) 50% 1px/12px 12px no-repeat,
            radial-gradient(farthest-side,#0000 calc(100% - 14px),#ccc 0);
            animation: l9 2s infinite linear;
    }
    @keyframes l9 {to{transform: rotate(1turn)}}
</style>