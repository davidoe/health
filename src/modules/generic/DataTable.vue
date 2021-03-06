<!--
	- @copyright Copyright (c) 2019 Florian Steffens <flost-dev@mailbox.org>
	-
	- @author Florian Steffens
	-
	- @license GNU AGPL version 3 or any later version
	-
	- This program is free software: you can redistribute it and/or modify
	- it under the terms of the GNU Affero General Public License as
	- published by the Free Software Foundation, either version 3 of the
	- License, or (at your option) any later version.
	-
	- This program is distributed in the hope that it will be useful,
	- but WITHOUT ANY WARRANTY; without even the implied warranty of
	- MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
	- GNU Affero General Public License for more details.
	-
	- You should have received a copy of the GNU Affero General Public License
	- along with this program. If not, see <http://www.gnu.org/licenses/>.
	-
	-->

<template>
	<div class="datatable">
		<Table
			:header="header"
			:data="dataForTable"
			:loading="loading"
			:entity-name="entityName"
			:group-by="groupBy"
			@addItem="addItem"
			@updateItem="updateItem"
			@deleteItem="deleteItem" />
	</div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'
import moment from '@nextcloud/moment'
import Table from '../../ces/Table'

export default {
	name: 'DataTable',
	components: {
		Table,
	},
	props: {
		contextFilter: {
			type: Object,
			default: null,
		},
		entityName: {
			type: String,
			default: t('health', 'item', {}),
		},
		header: {
			type: Array,
			default: function() { return [] },
		},
		groupBy: {
			type: Object,
			default: function() {
				return {
					day: false,
					week: false,
					year: false,
					dateColumnsId: 'datetime',
				}
			},
		},
	},
	data: function() {
		return {
			loading: true,
		}
	},
	computed: {
		...mapState(['activePersonId', 'moduleData']),
		...mapGetters(['person']),
		dataForTable: function() {
			const data = []
			this.datasets.forEach(d => {
				const tmp = JSON.parse(d.data)
				tmp.id = d.id
				data.push(tmp)
			})
			return data
		},
		datasets: function() {
			if (this.moduleData[this.contextFilter.module] && this.moduleData[this.contextFilter.module][this.contextFilter.type]) {
				return this.moduleData[this.contextFilter.module][this.contextFilter.type]
			} else {
				return []
			}
		},
	},
	watch: {
		activePersonId: function() {
			console.debug('person changed, load new datasets')
			this.loadDatasets()
		},
	},
	mounted() {
		this.loadDatasets()
	},
	methods: {
		addItem: function(item) {
			// console.debug('new item', item)
			item.personId = this.person.id

			const cesRequest = {}
			cesRequest.contextFilter = this.contextFilter
			cesRequest.contextFilter.type = 'datasets'
			cesRequest.entityData = item

			this.$store.dispatch('cesRequest', cesRequest).then(newItem => {
				console.debug('saved item', newItem)
				this.loadDatasets()
			})
		},
		updateItem: function(item) {
			// console.debug('update item', item)
			const cesRequest = {}
			cesRequest.contextFilter = this.contextFilter
			cesRequest.contextFilter.type = 'datasets'
			cesRequest.entityFilter = {
				id: item.id,
			}
			cesRequest.entityData = item.data

			this.$store.dispatch('cesRequest', cesRequest).then(result => {
				console.debug('item updated', result)
				this.loadDatasets()
			})
		},
		deleteItem: function(id) {
			// const entityId = this.datasets[id].id
			const entityId = id
			console.debug('delete item with id', entityId)

			const cesRequest = {}
			cesRequest.contextFilter = this.contextFilter
			cesRequest.contextFilter.type = 'datasets'
			cesRequest.entityFilter = {
				id: entityId,
				remove: true,
			}

			this.$store.dispatch('cesRequest', cesRequest).then(result => {
				console.debug('item deleted', result)
				this.loadDatasets()
			})
		},
		loadDatasets: function() {
			const cesRequest = {}
			cesRequest.contextFilter = this.contextFilter
			cesRequest.contextFilter.type = 'datasets'
			cesRequest.entityFilter = {
				personId: this.person.id,
			}
			this.$store.dispatch('cesRequest', cesRequest).then(result => {
				// console.debug('before sort', result)
				result.sort(this.sortDatasets)
				// console.debug('after sort', result)
				const data = {
					module: this.contextFilter.module,
					type: this.contextFilter.type,
				}
				data.data = result
				this.$store.dispatch('updateModuleData', data)
				this.loading = false
			})
		},
		sortDatasets: function(a, b) {
			const dataA = JSON.parse(a.data)
			const dataB = JSON.parse(b.data)
			let dA = 0
			if ('datetime' in dataA) {
				dA = dataA.datetime
			} else if ('datetimeStart' in dataA) {
				dA = dataA.datetimeStart
			} else {
				dA = 0
			}

			let dB = 0
			if ('datetime' in dataB) {
				dB = dataB.datetime
			} else if ('datetimeStart' in dataB) {
				dB = dataB.datetimeStart
			} else {
				dB = 0
			}

			let r = 0
			if (moment(dA) > moment(dB)) {
				r = -1
			} else if (moment(dA) < moment(dB)) {
				r = 1
			}

			// console.debug('compare', { a: dA, b: dB, comp: r })

			return r
		},
	},
}
</script>
