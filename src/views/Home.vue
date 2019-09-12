<template>
  <div class="home">
  	<router-link :to="'/books/add'">Add Book</router-link>
    <ApolloQuery
	    :query="require('../graphql/queries/Categories.gql')"
	    :variables="{ name }"
	  >
	    <template v-slot="{ result: { loading, error, data }, isLoading }">
	      <!-- Loading -->
	      <div v-if="isLoading" class="loading apollo">Loading...</div>

	      <!-- Error -->
	      <div v-else-if="error" class="error apollo">An error occurred</div>

	      <!-- Result -->
	      <div v-else-if="data" class="result apollo">
	      	<div>
	      		<a href="" @click.prevent="selectCategory('all')" class="link-margin">All</a>
	      		<a href="" @click.prevent="selectCategory('featured')" class="link-margin">Featured</a>
	      		<a v-for="category in data.categories" 
	      		:key="category.id" href="#" class="link-margin"
	      		@click.prevent="selectCategory(category.id)">
	      		{{ category.id }} {{ category.name }}</a>
	      	</div>
	      </div>

	      <!-- No result -->
	      <div v-else class="no-result apollo">No result :(</div>
	    </template>
	  </ApolloQuery>
		
	<div v-if="selectedCategory == 'all'">
	  <ApolloQuery
	    :query="query"
	    :variables="{ name }"
	  >
	    <template v-slot="{ result: { loading, error, data }, isLoading }">
	      <div v-if="isLoading" class="loading apollo">Loading...</div>

	      <div v-else-if="error" class="error apollo">An error occurred</div>

	      <div v-else-if="data" class="result apollo">
	      	<div v-for="book in data.books" :key="book.id">
	      		<router-link :to="`/books/${book.id}`" class="link-margin">{{ book.id }} {{ book.title }}</router-link>
	      		<div>{{book.author}}</div>
	      		<img :src="book.image" alt="cover image">
	      	</div>
	      </div>

	      <div v-else class="no-result apollo">No result :(</div>
	    </template>
	  </ApolloQuery>
	  </div>

	  <div v-else-if="selectedCategory == 'featured'">
	  <ApolloQuery
	    :query="query"
	    :variables="{ featured: true }"
	  >
	    <template v-slot="{ result: { loading, error, data }, isLoading }">
	      <div v-if="isLoading" class="loading apollo">Loading...</div>

	      <div v-else-if="error" class="error apollo">An error occurred</div>

	      <div v-else-if="data" class="result apollo">
	      	<div v-for="book in data.booksByFeatured" :key="book.id">
	      		<router-link :to="`/books/${book.id}`" class="link-margin">{{ book.id }} {{ book.title }}</router-link>
				<div>{{book.author}}</div>
	      		<img :src="book.image" alt="cover image">
	      	</div>
	      </div>

	      <div v-else class="no-result apollo">No result :(</div>
	    </template>
	  </ApolloQuery>
	  </div>	

	<div v-else>
	  <ApolloQuery
	    :query="query"
	    :variables="{ id: selectedCategory }"
	  >
	    <template v-slot="{ result: { loading, error, data }, isLoading }">
	      <div v-if="isLoading" class="loading apollo">Loading...</div>

	      <div v-else-if="error" class="error apollo">An error occurred</div>

	      <div v-else-if="data" class="result apollo">
	      	<div v-for="book in data.category.books" :key="book.id">
	      		<router-link :to="`/books/${book.id}`" class="link-margin">{{ book.id }} {{ book.title }}</router-link>
	      		<div>{{book.author}}</div>
	      		<img :src="book.image" alt="cover image">
	      	</div>
	      </div>

	      <div v-else class="no-result apollo">No result :(</div>
	    </template>
	  </ApolloQuery>
	 </div>
  </div>
</template>

<script>
// @ is an alias to /src

import categoryQuery from '@/graphql/queries/Category.gql'
import categoriesQuery from '@/graphql/queries/Categories.gql'
import booksQuery from '@//graphql/queries/Books.gql'
import booksFeaturedQuery from '@/graphql/queries/BooksFeatured.gql'

export default {
  name: 'home',
  components: {
    
  },

  data(){
  	return{
  		name: '',
  		categoryQuery,
  		categoriesQuery,
  		booksFeaturedQuery,
  		categories: [],
  		selectedCategory: 1,
  		query: booksQuery
  	}
  },

  methods: {
  	selectCategory(category){
  		if(category === 'all'){
  			this.query = booksQuery
  		}else if(category === 'featured'){
  			this.query = booksFeaturedQuery
  		}else{
  			this.query = categoryQuery
  		}
  		this.selectedCategory = category
  	}
  }
}
</script>

<style>
	.link-margin{
		margin-right: 24px;
	}
</style>
