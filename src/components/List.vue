<template lang="">

<div class="relative">
  <label class="sr-only"> Category </label>

  <input
    class="w-full py-3 pl-3 pr-12 text-sm border-2 border-gray-200 rounded text-gray-800 "
    type="text"
    placeholder="Category"
    v-on:keyup.enter="loadCardsFromCategory"
    v-model="search"
  />

  <span class="absolute text-gray-500 -translate-y-1/2 pointer-events-none top-1/2 right-4">
     <svg
      class="w-3 h-3 sm:w-6 sm:h-6"
      fill="none"
      stroke="currentColor"
      viewBox="0 0 24 24"
      xmlns="http://www.w3.org/2000/svg"
     >
      <path
        stroke-linecap="round"
        stroke-linejoin="round"
        stroke-width="2"
        d="M19.428 15.428a2 2 0 00-1.022-.547l-2.387-.477a6 6 0 00-3.86.517l-.318.158a6 6 0 01-3.86.517L6.05 15.21a2 2 0 00-1.806.547M8 4h8l-1 1v5.172a2 2 0 00.586 1.414l5 5c1.26 1.26.367 3.414-1.415 3.414H4.828c-1.782 0-2.674-2.154-1.414-3.414l5-5A2 2 0 009 10.172V5L8 4z"
      ></path>
    </svg>
  </span>
</div>


<div v-if="research==false" class="grid grid-flow-col p-6 gap-4">
    <div  v-for="(card, index) in cards" :key="index">
       <div class="p-1  bg-gradient-to-r from-pink-500 via-red-500 to-yellow-500 rounded-2xl w-96 m-0">
            <a class="block p-6 bg-gray-900 sm:p-8 rounded-xl" href="">
                <h5 class="text-xl font-bold text-white">{{ card.title.rendered }}</h5>
                <p class="mt-2 text-sm text-gray-400 mb-2" v-html="card.content.rendered"></p>
                    <li v-for="(category, cat) in card.meta" :key="cat" class="inline-block rounded-full text-white text-xs font-medium px-3 py-1.5 bg-gray-800 m-1">
                        {{category}}
                    </li>
            </a>
            <p class="p-6 text-xl">{{ card.modified }}</p>
        </div>
    </div>
</div>


<div v-else class="grid grid-flow-col p-6 gap-4">
    <div  v-for="(card, index) in result" :key="index">
       <div class="p-1  bg-gradient-to-r from-pink-500 via-red-500 to-yellow-500 rounded-2xl w-96 m-0">
            <a class="block p-6 bg-gray-900 sm:p-8 rounded-xl" href="">
                <h5 class="text-xl font-bold text-white">{{ card.title.rendered }}</h5>
                <p class="mt-2 text-sm text-gray-400 mb-2" v-html="card.content.rendered"></p>
                    <li v-for="(category, cat) in card.meta" :key="cat" class="inline-block rounded-full text-white text-xs font-medium px-3 py-1.5 bg-gray-800 m-1">
                        {{category}}
                    </li>
            </a>
            <p class="p-6 text-xl">{{ card.modified }}</p>
        </div>
    </div>
</div>


</template>
<script>
export default {
    
    data() {
        return {
            cards: [],
            categories: [],
            search: "",
            research: false,
            result: []
        }
    },
    created() {
        this.loadAllCards();
    },
    methods: {
        async loadAllCards(){
            const fetched_cards = await fetch("http://vetea.synology.me/wordpress/wp-json/wp/v2/posts/");
            this.cards = await fetched_cards.json();

            const fetched_category = await fetch("http://vetea.synology.me/wordpress/wp-json/wp/v2/categories/");
            this.categories = await fetched_category.json();

            this.categories.forEach(element_categories => {
                this.cards.forEach(element_cards => {
                    if (element_cards.categories.includes(element_categories.id))
                    {
                        element_cards.meta.push(element_categories.name)
                        console.log(element_cards, element_categories)
                    }
                })
            })

            this.cards.forEach(element_cards => {
                let temp = element_cards.modified.split("-")
                temp[2] = temp[2].split('T');
                element_cards.modified = "Modification : " + temp[2][0] + '-' + temp[1] + '-' + temp[0] + ' | ' + temp[2][1]
            })
        },
        loadCardsFromCategory(){
            console.log(this.search)
            if (this.search == "")
            this.research = false
            else
            {
                this.research = true
                this.result = [];
                this.cards.forEach(element_cards => {
                    if (element_cards.meta.includes(this.search))
                        {
                            this.result.push(element_cards)
                        }
                })
            }

        }
    },

    async beforeCreate() {
        loadAllCards()
    },
}
</script>
<style lang="">
</style>