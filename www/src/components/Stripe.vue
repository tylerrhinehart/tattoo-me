<!--  -->
<!-- WHEN A PERSON PRESSES THE $ BUTTON IT SETS THE TATTOO OBJECT IN THE STATE AT THIS.$STORE.STATE.CONFIRM -->
<!--  -->
<template>
	<div class="stripe">
		<!-- <h1>Please give us your payment details:</h1>
		<card class='stripe-card' :class='{ complete }' stripe='pk_test_cr5DjQkjunl2TGMOTUIRhzk7' :options='stripeOptions' @change='complete = $event.complete'
		/>
		<button class='pay-with-stripe' @click='pay' :disabled='!complete'>Pay with credit card</button> -->
		<div class="container-fluid">
			<!-- <div class="row">
				<div class="col-xs-12">
					<h2 class="header">Complete purchase of HD design below</h2>
				</div>
			</div> -->
			<div class="row">
				<div class="col-xs-12">
					<form>
						<label>
							<input name="cardholder-name" class="field is-empty" placeholder="Jane Doe" />
							<span><span>Name</span></span>
						</label>
						<label>
							<input class="field is-empty" autocomplete="tel" type="tel" placeholder="(123) 456-7890" />
							<span><span>Phone number</span></span>
						</label>
						<label>
							<div id="card-element" class="field is-empty"></div>
							<span><span>Credit or debit card</span></span>
						</label>
						<button type="submit">Pay ${{tattoo.price}}</button>
						<div class="outcome">
							<div class="error" role="alert"></div>
							<div class="success">
								Success! Your Stripe token is <span class="token"></span>
							</div>
						</div>
					</form>
				</div>
			</div>
			<div class="row">
				<div class="col-xs-12">
					<img class="img-responsive" :src="tattoo.url" alt="your image">
				</div>
			</div>
		</div>
	</div>

</template>

<script>
	// import { stripeKey, stripeOptions } from './stripeConfig.json'
	import { Card, createToken } from 'vue-stripe-elements'
	import router from "../router"

	export default {
		name: 'stripe',
		data() {
			return {
				complete: false,
				stripeOptions: {
					// see https://stripe.com/docs/stripe.js#element-options for details
				}
			}
		},

		computed: {
			tattoo() {
				return this.$store.state.confirm
			}
		},

		components: { Card },
		mounted() {
			var stripe = Stripe('pk_test_cr5DjQkjunl2TGMOTUIRhzk7');
			var elements = stripe.elements();
			var _this = this;
			var card = elements.create('card', {
				iconStyle: 'solid',
				style: {
					base: {
						iconColor: 'white',
						color: 'white',
						lineHeight: '36px',
						fontWeight: 300,
						fontFamily: '"Helvetica Neue", Helvetica, sans-serif',
						fontSize: '19px',

						'::placeholder': {
							color: 'white',
						},
					},
					invalid: {
						iconColor: '#e85746',
						color: '#e85746',
					}
				},
				classes: {
					focus: 'is-focused',
					empty: 'is-empty',
				},
			});
			card.mount('#card-element');

			var inputs = document.querySelectorAll('input.field');
			Array.prototype.forEach.call(inputs, function (input) {
				input.addEventListener('focus', function () {
					input.classList.add('is-focused');
				});
				input.addEventListener('blur', function () {
					input.classList.remove('is-focused');
				});
				input.addEventListener('keyup', function () {
					if (input.value.length === 0) {
						input.classList.add('is-empty');
					} else {
						input.classList.remove('is-empty');
					}
				});
			});
			function setOutcome(result) {
				var successElement = document.querySelector('.success');
				var errorElement = document.querySelector('.error');
				successElement.classList.remove('visible');
				errorElement.classList.remove('visible');

				if (result.token) {
					// Use the token to create a charge or a customer
					// https://stripe.com/docs/charges
					_this.$store.dispatch('purchaseTattoo', { token: result.token, tattooId: _this.tattoo._id })
					// successElement.querySelector('.token').textContent = result.token.id;
					// successElement.classList.add('visible');
					swal({
						title: "Purchased!",
						text: "Great!  Your payment has been received.  You will receive two emails.  One email will contain your receipt and the other email will contain a link to view/download a high resolution image of the tattoo you have purchased.",
						icon: "success",
						button: true
					});
					router.push({ name: "Home" })
				} else if (result.error) {
					errorElement.textContent = result.error.message;
					errorElement.classList.add('visible');

					swal({
						title: "Woops",
						text: "Pay your bills before getting a Tattoo.  Your payment has been declined.",
						icon: "warning",
						button: true
					});
					// router.push({ name: "Fail" })
				}
			}

			card.on('change', function (event) {
				setOutcome(event);
			});

			document.querySelector('form').addEventListener('submit', function (e) {
				e.preventDefault();
				var form = document.querySelector('form');
				var extraDetails = {
					name: form.querySelector('input[name=cardholder-name]').value,
				};
				stripe.createToken(card, extraDetails).then(setOutcome);
			});
		},
		methods: {
			pay() {
				// createToken returns a Promise which resolves in a result object with
				// either a token or an error key.
				// See https://stripe.com/docs/api#tokens for the token object.
				// See https://stripe.com/docs/api#errors for the error object.
				// More general https://stripe.com/docs/stripe.js#stripe-create-token.
				createToken().then(data => {
					if (data.token)
						this.$store.dispatch('purchaseTattoo', { token: data.token, tattooId: 'tattoo._id' })
					else
						console.log(data)
				})
			}
		}
	}

</script>

<style scoped>
	.img-responsive {
		margin: 0 auto;
		padding-top: 5rem;
		max-width: 70vw;
		min-width: 50vw
	}
	/* .stripe-card {
		width: 300px;
		border: 1px solid grey;
	}

	.stripe-card.complete {
		border-color: green;
	} */

	* {
		font-family: 'Helvetica Neue', Helvetica, sans-serif;
		font-size: 19px;
		font-variant: normal;
		padding: 0;
		margin: 0;
	}

	html {
		height: 100%;
	}

	body {
		/* background: #424770; */
		background: #000000;
		display: flex;
		align-items: center;
		min-height: 100%;
	}

	form {
		width: 480px;
		margin: 20px auto;
	}

	label {
		height: 35px;
		position: relative;
		color: white;
		display: block;
		margin-top: 30px;
		margin-bottom: 20px;
	}

	label>span {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		font-weight: 300;
		line-height: 32px;
		color: white;
		border-bottom: 1px solid #586A82;
		transition: border-bottom-color 200ms ease-in-out;
		cursor: text;
		pointer-events: none;
	}

	label>span span {
		position: absolute;
		top: 0;
		left: 0;
		transform-origin: 0% 50%;
		transition: transform 200ms ease-in-out;
		cursor: text;
	}

	label .field.is-focused+span span,
	label .field:not(.is-empty)+span span {
		transform: scale(0.68) translateY(-36px);
		cursor: default;
	}

	label .field.is-focused+span {
		border-bottom-color: #34D08C;
	}

	.field {
		background: transparent;
		font-weight: 300;
		border: 0;
		color: white;
		outline: none;
		cursor: text;
		display: block;
		width: 100%;
		line-height: 32px;
		padding-bottom: 3px;
		transition: opacity 200ms ease-in-out;
	}

	.field::-webkit-input-placeholder {
		color: #3D3535;
	}

	.field::-moz-placeholder {
		color: #3D3535;
	}
	/* IE doesn't show placeholders when empty+focused */

	.field:-ms-input-placeholder {
		color: #3D3535;
	}

	.field.is-empty:not(.is-focused) {
		opacity: 0;
	}

	button {
		float: left;
		display: block;
		background: #34D08C;
		color: white;
		border-radius: 2px;
		border: 0;
		margin-top: 20px;
		font-size: 19px;
		font-weight: 400;
		width: 100%;
		height: 47px;
		line-height: 45px;
		outline: none;
	}

	button:focus {
		background: #24B47E;
	}

	button:active {
		background: #159570;
	}

	.outcome {
		float: left;
		width: 100%;
		padding-top: 8px;
		min-height: 20px;
		text-align: center;
	}

	.success,
	.error {
		display: none;
		font-size: 15px;
	}

	.success.visible,
	.error.visible {
		display: inline;
	}

	.error {
		color: #E4584C;
	}

	.success {
		color: #34D08C;
	}

	.success .token {
		font-weight: 500;
		font-size: 15px;
	}
</style>