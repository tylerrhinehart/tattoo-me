<template>
	<div class="profile">
		<div v-if="info.accountType == 'regular'">
			<md-tabs md-fixed>
				<md-tab id="purchased" md-label="Purchased">
					<div class="row">
						<hr>
						<h2>Your Purchased Tattoos</h2>
						<hr>
						<div v-masonry transition-duration="0.3s" item-selector=".item" class="tattoo">
							<div v-masonry-tile class="item" v-for="item in purchased">
								<purchased :cardProp="item"></purchased>
							</div>
						</div>
					</div>
				</md-tab>
				<md-tab id="info" md-label="Info">
					<div class="row">
						<div class="col-xs-12">
							<h2>
								{{info.name}}
							</h2>
						</div>
						<div class="col-xs-12">
							<h2>
								{{info.email}}
							</h2>
						</div>
						<div class="col-xs-12">
							<button @click="logout()" class="btn btn-default">Logout</button>
						</div>
					</div>
				</md-tab>
			</md-tabs>
		</div>
		<div v-else>
			<md-tabs md-fixed>
				<md-tab id="purchased" md-label="Purchased">
					<div class="row">
						<hr>
						<h2>Your Purchased Tattoos</h2>
						<hr>
						<div v-masonry transition-duration="0.3s" item-selector=".item" class="tattoo">
							<div v-masonry-tile class="item" v-for="item in purchased">
								<purchased :cardProp="item"></purchased>
							</div>
						</div>
					</div>
				</md-tab>
				<md-tab id="uploaded" md-label="Uploaded">
					<div class="row">
						<div class="col-xs-12">
							<hr>
							<h1>My Gallery</h1>
						</div>
					</div>
					<hr>
					<div v-masonry transition-duration="0.3s" item-selector=".item" class="tattoo">
						<div v-masonry-tile class="item" v-for="item in blocks">
							<artistgallery :cardProp="item"></artistgallery>
						</div>
					</div>
				</md-tab>
				<md-tab id="info" md-label="Info">
					<div class="row">
						<div v-if="info.url">
							<div class="col-xs-12">
								<img :src="info.url" alt="profile picture">
							</div>
						</div>
						<div v-else>
							<div class="col-xs-12">
								<button class="btn btn-default">
										<h3>Upload an Artist Image</h3>
									</button>
							</div>
						</div>
						<div class="col-xs-12">
							<h2>
								{{info.name}}
							</h2>
						</div>
						<div class="col-xs-12">
							<h2>
								{{info.email}}
							</h2>
						</div>
						<div class="col-xs-12">
							<button @click="logout()" class="btn btn-default">Logout</button>
						</div>
					</div>
				</md-tab>
			</md-tabs>
		</div>
	</div>
</template>

<script>
	import Artistgallery from './ArtistGallery'
	import Purchased from './Purchased'
	export default {
		name: 'profile',
		data() {
			return {
			}
		},
		methods: {
			logout() {
				return this.$store.dispatch('logout')
			}
		},
		computed: {
			info() {
				return this.$store.state.userInfo
			},
			blocks() {
				return this.$store.state.tattoos
			},
			purchased() {
				return this.$store.state.purchased
			}
		},
		components: {
			Artistgallery,
			Purchased
		},
		mounted() {
			this.$store.commit('resetTattoos')
			this.$store.dispatch('getArtistGallery')
			this.$store.dispatch('getPurchased')
		}
	}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
	.md-tab-header-container {
		font-size: 8rem;
	}

	h2 {
		color: white;
	}

	h1 {
		color: white;
	}

	button {
		margin-top: 2rem;
	}

	.profile {
		padding-bottom: 15rem;
	}

	.tattoo {
		width: 90%;
		margin: 0 auto;
	}

	.item {
		margin: 0 1rem 0 1rem;
		width: 45%;
	}
</style>