<template>
	<div class="edit">
		<h1>Edit book</h1>
		<form action="" method="POST" @submit.prevent="editBook">
			<div class="form-group">
				<label for="title">Title</label>
				<input type="text" name="title" id="title" v-model="title">
			</div>
			<div class="form-group">
				<label for="author">Author</label>
				<input type="text" name="author" id="author" v-model="author">
			</div>
			<div class="form-group">
				<label for="image">Image</label>
				<input type="text" name="image" id="image" v-model="image">
			</div>
			<div class="form-group">
				<label for="description">Description</label>
				<textarea type="text" name="description" id="description" cols="30" rows="10" v-model="description"></textarea>
			</div>
			<div class="form-group">
				<label for="link">Link</label>
				<input type="text" name="link" id="link" v-model="link">
			</div>
			<div class="form-group">
				<label><input type="checkbox" name="featured" id="featured" v-model="featured">Featured</label>
			</div>
			<div class="form-group">
				
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
			      <select v-else-if="data" class="result apollo" v-model="category">
			      	<option v-for="category in data.categories" 
			      		:key="category.id" :value="category.id">
			      		{{ category.name }}
			      	</option>
			      </select>

			      <!-- No result -->
			      <div v-else class="no-result apollo">No result :(</div>
			    </template>
			  </ApolloQuery>
			</div>

			<div class="form-group">
				<button type="submit">Update Book</button>
			</div>

		</form>
	</div>
</template>

<script>
	import updateBook from '@/graphql/mutations/UpdateBook.gql'
	import book from '@/graphql/queries/Book.gql'

	export default {
		data(){
			return{
				title: '',
				author: '',
				image: '',
				description: '',
				link: '',
				featured: false,
				category: 1,
			}
		},

		apollo: {
			book:{
				query: book,

				variables(){
					if(this.$route && this.$route.params){
						return {
							id: this.$route.params.id
						}
					}
				},

				result({data, loading, networkStatus}) {
					this.title = data.book.title
					this.author = data.book.author
					this.image = data.book.image
					this.description = data.book.description
					this.link = data.book.link
					this.featured = data.book.featured
					this.category = data.book.category.id
				}
			}
		},

		methods:{
			editBook(){
				this.$apollo.mutate({
					mutation: updateBook, //Query

					// Parameter
					variables: {
						id: this.$route.params.id,
						title: this.title,
					    author: this.author,
					    image: this.image,
					    link: this.link,
					    description: this.description,
					    featured: this.featured,
					    category_id: this.category,
					}
				}).then(data => {
					console.log(data)
					this.$router.push(`/books/${this.$route.params.id}`)
				}).catch(error => {
					console.log(error)
				})
			}
		}
	}
	
</script>

<style scoped>
	.form-group {
		margin-bottom: 32px;
	}
	input[type="text"] {
		padding: 10px 14px;
	}
	button {
		padding: 16px;
		color: white;
		background: #027BFF;
		border-radius: 5px;
		font-size: 15px; 
	}
</style>