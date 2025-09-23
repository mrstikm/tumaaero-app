<template>
	<div v-if="!data.data.value.payload.length">Průvodka neexistuje</div>
	<div v-else class="container mx-auto">
		<div>
			<div class="flex justify-between items-center py-2">
				<span><img src="/logo.jpg" alt="Tůma Aeorspace" width="320" height="107" /></span>
				<h1>*{{ route.params.pruvodka.replaceAll('_', '/') }}*</h1>
				<div class="text-3xl text-right">
					<strong>
						Výrobní průvodka <br />
						{{ route.params.pruvodka.replaceAll('_', '/') }}
					</strong>
				</div>
			</div>
			<div class="flex">
				<div class="grow basis-4/6">
					<div class="py-2">
						<table>
							<tbody>
								<tr>
									<th>Podprodukt:</th>
									<td colspan="2">{{ hlavickaP0.PRODUKT }}</td>
								</tr>
								<tr>
									<th>Název:</th>
									<td colspan="2">{{ hlavickaP0.NAZEV }}</td>
								</tr>
								<tr>
									<th>Č. výkresu:</th>
									<td>{{ hlavickaP0.NO_VYKRESU }}</td>
									<td>Počet: {{ hlavickaP0.POCET_KS }}</td>
								</tr>
							</tbody>
						</table>
					</div>
					<div class="py-2">
						<table>
							<tbody>
								<tr>
									<th>Zákazník:</th>
									<td>{{ hlavickaP0.ZAKAZNIK }}</td>
								</tr>
								<tr>
									<th>Obj. zákazníka:</th>
									<td>{{ hlavickaP0.OBJ_ZAKAZNIKA }}</td>
								</tr>
								<tr>
									<th>Zakázka:</th>
									<td>{{ hlavickaP0.ZAKAZKA }}</td>
								</tr>
							</tbody>
						</table>
					</div>
				</div>
				<div class="basis-2/6">
					<h2 class="font-bold text-lg">Dostupné soubory:</h2>
					<ul>
						<li v-for="(priloha, index) in prilohy0G" :key="index">
							<a :href="`${priloha.LINK}`" class="py-1 inline-block underline text-primary" target="_blank">
								{{ priloha.JMENO_SOUBORU }}
							</a>
						</li>
					</ul>
				</div>
			</div>
			<div class="material">
				<table class="w-full">
					<thead>
						<tr>
							<th>Poz.</th>
							<th>Čár. kód artiklu</th>
							<th>Č. artiklu <br />Název</th>
							<th>Sklad</th>
							<th>Množství</th>
							<th>Vydáno</th>
							<th>Množství 1ks</th>
							<th>Č. operace</th>
						</tr>
					</thead>
					<tbody>
						<template v-if="!materialP1.length">
							<tr>
								<td colspan="8">Žádný materiál...</td>
							</tr>
						</template>
						<template v-else v-for="(item, index) in materialP1" :key="index">
							<tr
								:class="[index > 0 ? 'border-t border-primary' : '', { expanded: expandedRows.includes(item) }]"
								class="expandable"
								@click="expandRow(item)">
								<td>{{ index + 1 }}</td>
								<td>*{{ item.CAROVY_KOD_ARTIKLU }}*</td>
								<td>{{ item.NO_ARTIKLU }} <br />{{ item.NAZEV_ARTIKLU }}</td>
								<td>{{ item.NO_SKLADU }}</td>
								<td>{{ item.POTREBNE_MNOZSTVI }}</td>
								<td>{{ getVydaneMnozstvi(item.NO_ARTIKLU, item.NO_SKLADU) }}</td>
								<td>{{ item.POTREBNE_MNOZSTVI / item.POCET_PODPRODUKTU }} {{ item.JEDNOTKA }}</td>
								<td>{{ item.NO_OPERACE }}</td>
							</tr>
							<tr
								:class="{ expanded: expandedRows.includes(item) }"
								v-if="vydejP5.filter((mat) => mat.NO_ARTIKLU === item.NO_ARTIKLU && mat.NO_SKLADU === item.NO_SKLADU)">
								<td style="white-space: nowrap; padding-left: 54px" colspan="8">
									<table class="w-full">
										<thead class="bg-light">
											<tr>
												<th>Datum výdeje</th>
												<th>Č. artiklu</th>
												<th>Sklad</th>
												<th>Č. výdejky</th>
												<th>Množství</th>
												<th>Sér. číslo</th>
												<th>Atest</th>
												<th>Vydal</th>
												<th>Poznámka</th>
											</tr>
										</thead>
										<tbody>
											<tr
												v-for="(material, indexMat) in vydejP5.filter(
													(mat) => mat.NO_ARTIKLU === item.NO_ARTIKLU && mat.NO_SKLADU === item.NO_SKLADU
												)">
												<td>{{ new Date(material.DATUM).toLocaleDateString('cs-CZ') }}</td>
												<td>{{ material.NO_ARTIKLU }}</td>
												<td>{{ material.NO_SKLADU }}</td>
												<td>{{ material.NO_VYDEJKY }}</td>
												<td>{{ material.MNOZSTVI }}</td>
												<td>{{ material.SER_NO }}</td>
												<td>{{ material.ATEST }}</td>
												<td>{{ material.VYDAL }}</td>
												<td>{{ material.POZN }}</td>
											</tr>
										</tbody>
									</table>
								</td>
							</tr>
							<tr :class="{ expanded: expandedRows.includes(item) }" v-else>
								<td colspan="8">Žádné pohyby materiálu ...</td>
							</tr>
						</template>
					</tbody>
				</table>
			</div>
			<div class="operace">
				<table class="w-full">
					<thead>
						<tr>
							<th>Č.op.</th>
							<th>Činnost</th>
							<th>Pracoviště</th>
							<th>Příprava</th>
							<th>Čas/1ks</th>
							<th>Plán ks</th>
							<th>OK ks</th>
							<th>NOK ks</th>
							<th>Podpis</th>
						</tr>
					</thead>
					<tbody>
						<template v-if="!operaceP2.length">
							<tr>
								<td colspan="9">Žádné operace...</td>
							</tr>
						</template>
						<template v-for="(item, index) in operaceP2" :key="index">
							<tr
								:class="[index > 0 ? 'border-t border-primary' : '', { expanded: expandedRows.includes(item) }]"
								class="expandable"
								@click="expandRow(item)">
								<td>{{ item.NO_OPERACE }}</td>
								<td>{{ item.POPIS_OPERACE }}</td>
								<td>{{ item.NO_PRACOVISTE }}</td>
								<td>{{ item.PRIPRAVA }}</td>
								<td>{{ item.NORMA_KS }}</td>
								<td>{{ item.POCET_PODPRODUKTU }}</td>
								<td>
									<!-- {{ getOKks(item.NO_ROZPISU) }} -->
								</td>
								<td></td>
								<td></td>
							</tr>
							<tr class="additional-info" :class="{ expanded: expandedRows.includes(item) }">
								<td colspan="9">
									<table>
										<tbody>
											<tr v-if="mereniPD.filter((mereni) => mereni.NO_OPERACE === item.NO_OPERACE).length">
												<td
													colspan="9"
													v-for="(mereni, index2) in mereniPD.filter(
														(mereni) => mereni.NO_OPERACE === item.NO_OPERACE
													)"
													:key="index2">
													<p class="pl-8">{{ mereni.POCET_MERENI }} - {{ mereni.PLAN_KONTROL }}</p>
													<!-- <p>{{ mereni }}</p> -->
													<!-- <template
														v-for="(mereniSingle, index3) in mereniPD.filter((item) => item.NO_OPERACE === mereni.NO_OPERACE)"
														:key="index">
														{{ mereniSingle }}</template
													> -->
													<!-- <template v-for="(mereniSingle, index3) in extractNumber(mereni.POCET_MERENI)" :key="index3">
														<div>
															<br />
															<hr />
															<p>{{ mereni.PLAN_KONTROL }}</p>
														</div>
													</template> -->
												</td>
											</tr>
										</tbody>
									</table>
									<table>
										<tbody>
											<tr v-if="getRelevantLogging(item).length">
												<td colspan="9">
													<table>
														<tbody>
															<tr
																v-for="(item, index) in getRelevantLogging(item)"
																:key="index"
																class="cipovani">
																<td colspan="2"></td>
																<td>{{ item.NO_PRACOVNIKA }}</td>
																<td>{{ item.JMENO }}</td>
																<td>{{ new Date(item.ZAHAJENO).toLocaleString('cs-CZ') }}</td>
																<td>{{ new Date(item.UKONCENO).toLocaleString('cs-CZ') }}</td>
																<td>OK - {{ item.POCET_KS }}</td>
																<td>NOK - {{ item.ZMETKY }}</td>
																<td></td>
															</tr>
														</tbody>
													</table>
												</td>
											</tr>
										</tbody>
									</table>
								</td>
							</tr>

							<!-- <template v-if="denniPlanyP3">
								<tr>
									<td colspan="1">Denní plány</td>
								</tr>
								<tr v-for="(item, index) in denniPlanyP3" :key="index">
									{{
										item
									}}
								</tr>
							</template> -->
						</template>
					</tbody>
				</table>
			</div>
			<div class="pripravky">
				<table class="w-full">
					<thead>
						<tr>
							<th>Č. přípravku</th>
							<th>Sklad</th>
							<th>Název</th>
							<th>Umístění</th>
							<th>Č. operace</th>
						</tr>
					</thead>
					<tbody>
						<tr v-if="!nastrojeFN.length">
							<td colspan="5">Žádné přípravky / nástroje ...</td>
						</tr>
						<tr v-else v-for="(item, index) in nastrojeFN" :key="index">
							<td>{{ item.NO_NASTROJE }}</td>
							<td>{{ item.NO_SKLADU }}</td>
							<td>{{ item.NAZEV }}</td>
							<td>{{ item.NO_SKLADU }}</td>
							<td>{{ item.NO_OPERACE }}</td>
						</tr>
					</tbody>
				</table>
			</div>
			<div class="neshody">
				<table class="w-full">
					<thead>
						<tr>
							<th>Č. neshody</th>
							<th>Datum</th>
							<th>Popis nápravy</th>
							<th>Popis neshody</th>
							<th>Stav</th>
							<th>Status</th>
						</tr>
					</thead>
					<tbody>
						<tr v-if="!neshodyL7.length">
							<td colspan="2">Žádné neshody ...</td>
						</tr>
						<tr v-else v-for="(item, index) in neshodyL7" :key="index">
							<td>{{ item.NO_NESHODY }}</td>
							<td>{{ new Date(item.ZAPSANO).toLocaleDateString('cs-CZ') }}</td>
							<td>{{ item.POP_NAPRAVY }}</td>
							<td>{{ item.POP_NESHODY }}</td>
							<td>{{ item.STAV }}</td>
							<td>{{ item.STATUS }}</td>
						</tr>
					</tbody>
				</table>
			</div>
		</div>
	</div>
</template>
<script setup>
	const route = useRoute()
	const expandedRows = ref([])

	const expandRow = (item) => {
		if (expandedRows.value.includes(item)) {
			expandedRows.value = expandedRows.value.filter((i) => i !== item)
		} else {
			expandedRows.value.push(item)
		}
	}

	const extractNumber = (text) => {
		const match = text.match(/\d+/)
		return match ? parseInt(match[0], 10) : 0
	}

	const stringPruvodka = route.params.pruvodka.split('_')
	const noSkupiny = stringPruvodka[0]
	const noPlanu = stringPruvodka[1]
	const noPolozky = stringPruvodka[2]

	const hlavickaP0 = ref(null)
	const materialP1 = ref(null)
	const operaceP2 = ref(null)
	const nastrojeFN = ref(null)
	const mereniPD = ref(null)
	const prilohy0G = ref(null)
	const vydejP5 = ref(null)
	const cipovaniP3 = ref(null)
	// const denniPlanyP3 = ref(null)
	const neshodyL7 = ref(null)

	const data = await useAsyncData('data', () =>
		$fetch('https://control.tuma-aerospace.cz/sendPost2/PXQ72SBErpZST9nbB98EZMRRhAFvpC', {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json',
				Accept: 'application/json',
				charset: 'utf-8',
			},
			body: {
				id: 'PXQ72SBErpZST9nbB98EZMRRhAFvpC',
				typ: 0,
				head: `typUdalosti=50;idUzivatele=null;noSkupiny=${noSkupiny};noPlanu=${noPlanu};noPolozky=${noPolozky};`,
				items: [],
			},
		})
	)
	console.log(data.data.value.payload)

	hlavickaP0.value = data.data.value.payload?.find((item) => item.TYP === 'HLAVICKA P0')
	materialP1.value = data.data.value.payload?.filter((item) => item.TYP === 'MATERIAL P1')
	operaceP2.value = data.data.value.payload?.filter((item) => item.TYP === 'OPERACE P2')
	nastrojeFN.value = data.data.value.payload?.filter((item) => item.TYP === 'NASTROJE FN')
	mereniPD.value = data.data.value.payload?.filter((item) => item.TYP === 'MERENI PD')
	prilohy0G.value = data.data.value.payload?.filter((item) => item.TYP === 'PRILOHY 0G')
	materialP1.value.sort((a, b) => a.NO_OPERACE - b.NO_OPERACE)
	vydejP5.value = data.data.value.payload?.filter((item) => item.TYP === 'MATERIAL VYDEJ P5')
	cipovaniP3.value = data.data.value.payload?.filter((item) => item.TYP === 'OPERACE CIPOVANI P3')
	// denniPlanyP3.value = data.data.value.payload?.filter((item) => item.TYP === 'DENNI PLANY P3')
	neshodyL7.value = data.data.value.payload?.filter((item) => item.TYP === 'NESHODY L7')

	const getVydaneMnozstvi = (NO_ARTIKLU, NO_SKLADU) => {
		const vydej = vydejP5.value.filter((item) => item.NO_ARTIKLU === NO_ARTIKLU && item.NO_SKLADU === NO_SKLADU)
		return vydej.reduce((acc, item) => acc + Math.abs(item.MNOZSTVI), 0)
	}

	const getRelevantLogging = (item) => {
		return cipovaniP3.value.filter((op) => op.NO_PLANU === item.NO_PLANU && op.NO_ROZPISU === item.NO_ROZPISU)
	}

	const getOKks = (NO_ROZPISU) => {
		const cipovaní = cipovaniP3.value.filter((item) => item.NO_ROZPISU === NO_ROZPISU)
	}
</script>
<style scoped>
	th {
		text-align: left;
	}
	thead {
		background-color: lightblue;
	}
	thead.bg-light {
		background-color: lightgray;
	}
	th,
	td {
		padding: 0.875rem 0.5rem;
	}
	.cipovani {
		background-color: antiquewhite;
	}
	tr.expandable {
		position: relative;
		&::after {
			content: '';
			display: block;
			width: 12px;
			height: 12px;
			border: 2px solid black;
			border-style: none solid solid none;
			position: absolute;
			top: 50%;
			right: 12px;
			transform: rotate(45deg) translate(-5px, -5px);
			transition: all 0.15s ease-in-out;
		}
		&.expanded {
			&::after {
				transform: rotate(-135deg);
			}
		}
		& + *:not(.expanded) {
			display: none;
		}
	}
	tr {
		table,
		tbody {
			width: 100%;
		}
		td:has(table) {
			padding: 0;
		}
	}
</style>
