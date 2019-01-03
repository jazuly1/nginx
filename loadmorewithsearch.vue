<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="card card-default">
		  <form @submit.prevent="search">
		  	<input v-model="term" type="search">
				<button class="btn btn-success" type="submit">Search</button>
		  </form>

			<div v-show="SearchDiv">
				<div>Selected: <strong>{{ results }}</strong></div>
				<div class="card-header"><h3>Artist</h3></div>
					<div class="card-body no-padding">
						<table v-if="results.data" class="table table-responsive table-hover">
							<tr>
								<th>Name</th>
								<th>Gender</th>
								<th>Added</th>
								<th>Updated</th>
							</tr>
							<tbody>
								<tr v-for="(result, index) in results.data">
									<td>{{ result.artist_name }}</td>
									<td>{{ result.gender }}</td>
									<td>{{ result.created_at }}</td>
									<td>{{ result.updated_at }}</td>
								</tr>
							</tbody>
						</table>
					</div>
					<div v-if="results.next_page_url" class="card-footer">
						<button @click.prevent="paginatesearch(results.next_page_url)" type="button" class="btn btn-primary btn-block" :disabled="disabled == 1 ? true : false">
							<i v-show="loading" class="fa fa-spinner fa-spin"></i> Load More
						</button>
					</div>
			</div>

				<div v-show="IndexDiv">
					<div class="card-header"><h3>Artist</h3></div>
					<div class="card-body no-padding">
						<table v-if="artists.data" class="table table-responsive table-hover">
							<tr>
								<th>Name</th>
								<th>Gender</th>
								<th>Added</th>
								<th>Updated</th>
							</tr>
							<tbody>
								<tr v-for="(artist, index) in artists.data">
									<td>{{ artist.artist_name }}</td>
									<td>{{ artist.gender }}</td>
									<td>{{ artist.created_at }}</td>
									<td>{{ artist.updated_at }}</td>
								</tr>
							</tbody>
						</table>
					</div>
					<div v-if="artists.next_page_url" class="card-footer">
						<button @click.prevent="paginate(artists.next_page_url)" type="button" class="btn btn-primary btn-block" :disabled="disabled == 1 ? true : false">
							<i v-show="loading" class="fa fa-spinner fa-spin"></i> Load More
						</button>
					</div>
				</div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'ArtistIndex',
    data() {
      return {
				term:'',
				disabled: 0,
				artists: {},
				results:[],
				loading: false,
				noResults: false,
				SearchDiv: false,
				IndexDiv: true
      }
		},
    mounted() {
			this.paginate()
    },
    methods: {
			paginate(url = '') {
				this.loading = true;
				this.disabled = 1;
				axios.get(url ? url : 'api/artist')
				.then(response => {
					this.loading = false;
					this.disabled = 0;
					if (! this.artists.data) {
						this.artists = response.data
					}
					else {
						this.artists.data.push(...response.data.data)
						this.artists.next_page_url = response.data.next_page_url
					}
				})
      	.catch(e => console.error(e))
			},
			search() {
				let formData = new FormData();
				formData.append('term', this.term);
				axios.post('/api/artist/search/', formData)
				.then((response) => {
					this.SearchDiv = true;
					this.IndexDiv = false;
					this.results = response.data;
					this.noResults = this.results.length === 0;
					console.log(this.term)
				});
			},
			paginatesearch(url = '') {
				this.loading = true;
				this.disabled = 1;
				axios.get(url ? url : '/api/artist/search/')
				.then(response => {
					this.loading = false;
					this.disabled = 0;
					if (! this.results.data) {
						this.results = response.data
					}
					else {
						this.results.data.push(...response.data.data)
						this.results.next_page_url = response.data.next_page_url
					}
				})
      	.catch(e => console.error(e))
	  	},
		}
  }
</script>
