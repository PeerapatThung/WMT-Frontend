<template>
  <h1>Recipe Lists</h1>

  <div class="recipes" >
    <div class="container">
      <div class="row">
      <RecipeCard v-for="recipe in recipes" :key="recipe.id" :recipe="recipe" />
    </div>
  </div>
    <div class="pagination">
     <Button
       class="p-button-raised p-button-success p-button p-component"
        v-if="page != 1"
      >
        <router-link
          id="page-prev"
          :to="{ name: 'RecipeList', query: { page: page - 1 } }"
          rel="prev"
        >
          Prev Page</router-link
        >
     </Button>

      <Button
        class="p-button-raised p-button-success p-button p-component"
        v-if="hasNextPage"
      >
        <router-link
          id="page-next"
          :to="{ name: 'RecipeList', query: { page: page + 1 } }"
          rel="next"
        >
          Next Page</router-link
        >
      </Button>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import RecipeCard from '@/components/RecipeCard.vue'
import RecipeService from '@/services/RecipeService.js'

// import axios from 'axios'
export default {
  name: 'RecipeList',
  props: {
    page: {
      type: Number,
      required: true
    }
  },
  components: {
    RecipeCard // register it as a child component
  },
  data() {
    return {
      recipes: null,
      totalrecipes: 0, // <--- Added this to store totalrecipes
      keyword: null
    }
  },
  // eslint-disable-next-line no-unused-vars
  beforeRouteEnter(routeTo, routeFrom, next) {
    RecipeService.getRecipes(parseInt(routeTo.query.page) || 1)
      .then((response) => {
        next((comp) => {
          comp.recipes = response.data.result
          comp.totalrecipes = 13493
          console.log(response)
        })
      })
      .catch(() => {
        next({ name: 'NetworkError' })
      })
  },
  beforeRouteUpdate(routeTo) {
    var queryFunction
    if (this.keyword == null || this.keyword === '') {
      queryFunction = RecipeService.getRecipes(
        parseInt(routeTo.query.page) || 1
      )
    }
    queryFunction
      .then((response) => {
        console.log(response)
        this.recipes = response.data.result // <-----
        this.totalrecipes = 13493 // <-----
      })
      .catch(() => {
        return { name: 'NetworkError' }
      })
  },
  computed: {
    hasNextPage() {
      // First, calculate total pages
      let totalPages = Math.ceil(this.totalrecipes / 10) // 2 is recipes per page

      // Then check to see if the current page is less than the total pages.
      return this.page < totalPages
    }
  }
}
</script>
<style scoped>
.container{
  align-content: center;
}
.recipes {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

}
.row{
  align-content: center;
  align-self: center;
}

.recipes {
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  justify-content: center;
  margin-left: 30px;
  margin-right: 30px;
}
.pagination {
  display: flex;
  width: 290px;
  align-items: center;
  margin: 50px;
}
.pagination a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}

#page-prev {
  text-align: left;
}

#page-next {
  text-align: right;
}
</style>
