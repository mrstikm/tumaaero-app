<template>
	<div class="flex items-center justify-center h-screen">
		<div>
			<div class="container">
				<h1 class="text-center">Načtěte čárový kód průvodky</h1>
				<form @submit.prevent="submit" class="flex flex-col gap-2">
					<div class="relative">
						<input class="text-center border border-black p-2 w-full" type="text" v-model="code" placeholder="1/1234567/0" />
						<div class="absolute right-0 top-0 p-2" @click="openScanner">
							<svg
								fill="#000000"
								height="24px"
								width="24px"
								version="1.1"
								id="Capa_1"
								xmlns="http://www.w3.org/2000/svg"
								xmlns:xlink="http://www.w3.org/1999/xlink"
								viewBox="0 0 487 487"
								xml:space="preserve">
								<g>
									<g>
										<path
											d="M308.1,277.95c0,35.7-28.9,64.6-64.6,64.6s-64.6-28.9-64.6-64.6s28.9-64.6,64.6-64.6S308.1,242.25,308.1,277.95z
			 M440.3,116.05c25.8,0,46.7,20.9,46.7,46.7v122.4v103.8c0,27.5-22.3,49.8-49.8,49.8H49.8c-27.5,0-49.8-22.3-49.8-49.8v-103.9
			v-122.3l0,0c0-25.8,20.9-46.7,46.7-46.7h93.4l4.4-18.6c6.7-28.8,32.4-49.2,62-49.2h74.1c29.6,0,55.3,20.4,62,49.2l4.3,18.6H440.3z
			 M97.4,183.45c0-12.9-10.5-23.4-23.4-23.4c-13,0-23.5,10.5-23.5,23.4s10.5,23.4,23.4,23.4C86.9,206.95,97.4,196.45,97.4,183.45z
			 M358.7,277.95c0-63.6-51.6-115.2-115.2-115.2s-115.2,51.6-115.2,115.2s51.6,115.2,115.2,115.2S358.7,341.55,358.7,277.95z" />
									</g>
								</g>
							</svg>
						</div>
					</div>
					<button class="p-2 bg-primary text-white hover:bg-hover" type="submit">Odeslat</button>
				</form>
				<div
					v-if="scannerOpen"
					class="flex justify-center absolute top-0 left-0 w-full h-full bg-black bg-opacity-50 p-20 flex items-center justify-center">
					<div class="bg-white p-4">
						<ClientOnly>
							<StreamBarcodeReader class="reader" @decode="onDecode" />
						</ClientOnly>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>
<script setup>
	import { StreamBarcodeReader } from 'vue-barcode-reader'

	const code = ref(null)
	const scannerOpen = ref(false)

	const onDecode = (codeScanned) => {
		scannerOpen.value = false
		code.value = codeScanned
	}
	const openScanner = () => {
		scannerOpen.value = true
	}
	const submit = () => {
		code.value = code.value.replaceAll('/', '_')
		navigateTo(`/pruvodka/${code.value}`)
	}
</script>
<style scoped>
	.reader {
		max-width: 480px;
	}
	a {
		display: inline-block;
	}
	.btn {
		border: 1px solid #000;
	}
</style>
