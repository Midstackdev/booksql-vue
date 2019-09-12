<template>
  <div class="book">
    <ApolloQuery
	    :query="require('@/graphql/queries/Book.gql')"
	    :variables="{ id: $route.params.id }"
	  >
	    <template v-slot="{ result: { loading, error, data }, isLoading }">
	      <div v-if="isLoading" class="loading apollo">Loading...</div>

	      <div v-else-if="error" class="error apollo">An error occurred</div>

	      <div v-else-if="data" class="result apollo">
	      	<div>
	      		<div>{{ data.book.title }}</div>
	      		<div>{{data.book.author}}</div>
	      		<img :src="data.book.image" alt="cover image">
	      		<div>
	      			<router-link :to="`/books/${data.book.id}/edit`" class="link-margin">Edit</router-link>
	      			<a href="#" class="link-margin" @click.prevent="deleteBook">Delete</a>
	      		</div>
	      	</div>
	      </div>

	      <div v-else class="no-result apollo">No result :(</div>
	    </template>
	  </ApolloQuery>
  </div>
</template>


<script>
	import deleteBook from '@/graphql/mutations/DeleteBook.gql'

	export default {
		methods: {
			deleteBook(){
				this.$apollo.mutate({
					mutation: deleteBook,
					variables: {
						id: this.$route.params.id
					}
				}).then(data => {
					console.log(data)
					this.$router.push('/')
				})
			}
		}
	}
	
</script>