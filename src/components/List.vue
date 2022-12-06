<template lang="">

<div class="relative mt-10">
  <label class="sr-only"> Categorie ou titre ou contenu </label>

  <input
    class="w-full py-3 pl-3 pr-12 text-sm border-2 border-gray-200 rounded text-gray-800 "
    type="text"
    placeholder="Categorie ou titre ou contenu..."
    v-model="search"
    v-on:input="loadCardsFromCategory"
  />
</div>

<a class="mt-8 inline-block p-[2px] rounded-full bg-gradient-to-r from-pink-500 via-red-500 to-yellow-500 hover:text-white active:text-opacity-75 focus:outline-none focus:ring" target="_blank" rel="noopener noreferrer" href="https://vetea.synology.me/wordpress/wp-admin/post-new.php">
  <span class="block px-8 py-3 text-large font-medium  bg-gray-900 rounded-full hover:bg-transparent">
    Ajouter une carte
  </span>
</a>

<a class="sm:ml-8  mt-8 inline-block p-[2px] rounded-full bg-gradient-to-r from-pink-500 via-red-500 to-yellow-500 hover:text-white active:text-opacity-75 focus:outline-none focus:ring" target="_blank" rel="noopener noreferrer" href="https://vetea.synology.me/wordpress/wp-admin/edit-tags.php?taxonomy=category">
  <span class="block px-8 py-3 text-large font-medium  bg-gray-900 rounded-full hover:bg-transparent">
    Ajouter une catégorie
  </span>
</a>


<div v-if="research==false" class="grid md:grid-cols-3 gap-4 mt-10 w-full h-full">
    <div  v-for="(card, index) in cards" :key="index">
       <div class="p-1  bg-gradient-to-r from-pink-500 via-red-500 to-yellow-500 rounded-2xl m-0 h-full">
            <div class="block p-6 bg-gray-900 sm:p-8 rounded-xl h-full">
                <h5 class="lg:text-xl text-xs font-bold text-white">{{ card.title.rendered }}</h5>
                <p class="mt-2 text-sm text-gray-400 mb-8" v-html="card.content.rendered"></p>
                    <li v-for="(category, cat) in card.meta" :key="cat" class="inline-block rounded-full text-white text-sm md:text-large md:font-medium px-3 py-1.5 bg-gray-800 m-1">
                        {{category}}
                    </li>
            <p class="p-3 text-sm">{{ card.modified }}</p>
            <p class="p-3 text-sm">{{ card.date }}</p>
            </div>
        </div>
    </div>
</div>

<div v-else class="grid md:grid-cols-2 gap-4 mt-10 w-full h-full">
    <div  v-for="(card, index) in result" :key="index">
       <div class="p-1  bg-gradient-to-r from-pink-500 via-red-500 to-yellow-500 rounded-2xl w-96 m-0 w-full h-full">
            <a class="block p-6 bg-gray-900 sm:p-8 rounded-xl h-full" href="">
                <h5 class="lg:text-xl text-xs font-bold text-white">{{ card.title.rendered }}</h5>
                <p class="mt-2 text-sm text-gray-400 mb-2" v-html="card.content.rendered"></p>
                    <li v-for="(category, cat) in card.meta" :key="cat" class="inline-block rounded-full text-white text-xs font-medium px-3 py-1.5 bg-gray-800 m-1">
                        {{category}}
                    </li>
            <p class="p-3 text-sm">{{ card.modified }}</p>
            <p class="p-3 text-sm">{{ card.date }}</p>
            </a>
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
        changeDateFormat(time, title){
            let temp = time.split("-")
            temp[2] = temp[2].split('T');
            return(title + " : " + temp[2][0] + '-' + temp[1] + '-' + temp[0] + ' | ' + temp[2][1])
        },
        async loadAllCards(){
            const fetched_cards = await fetch("https://vetea.synology.me/wordpress/wp-json/wp/v2/posts/");
            this.cards = await fetched_cards.json();

            const fetched_category = await fetch("https://vetea.synology.me/wordpress/wp-json/wp/v2/categories/");
            this.categories = await fetched_category.json();

            this.categories.forEach(element_categories => {
                this.cards.forEach(element_cards => {
                    if (element_cards.categories.includes(element_categories.id))
                    {
                        element_cards.meta.push(element_categories.name)
                    }
                })
            })

            this.cards.forEach(element_cards => {
                element_cards.modified = this.changeDateFormat(element_cards.modified, "Modification")
                element_cards.date = this.changeDateFormat(element_cards.date, "Création")                
            })
        },
        loadCardsFromCategory(){
            if (this.search == "")
            this.research = false
            else
            {
                this.research = true
                this.result = [];
                this.cards.forEach(element_cards => {
                    if (element_cards.meta.includes(this.search) || element_cards.title.rendered.includes(this.search) || element_cards.content.rendered.includes(this.search))
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