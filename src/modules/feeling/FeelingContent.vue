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
	<div>
		<h2>
			Feeling <span>for {{ person.name }}</span>
		</h2>
		<div class="datatable">
			<HealthTable :header="header" :data="feelingdata" />
		</div>
	</div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'
import moment from '@nextcloud/moment'
import HealthTable from './../../HealthTable'

export default {
	name: 'FeelingContent',
	filters: {
		formatMyDate: function(v) {
			return moment(v).format('DD.MM.YYYY')
		},
	},
	components: {
		HealthTable,
	},
	data: function() {
		return {
		}
	},
	computed: {
		...mapState(['personData']),
		...mapGetters(['person']),
		header: function() {
			return [
				{
					name: 'Date',
					columnId: 'datetime',
					type: 'datetime',
					show: true,
				},
				{
					name: 'Mood',
					columnId: 'mood',
					type: 'select',
					show: this.person.feelingColumnMood,
					options: [
						{ id: 0, label: t('health', 'Perfect', {}) },
						{ id: 1, label: t('health', 'Fine', {}) },
						{ id: 2, label: t('health', 'Okay', {}) },
						{ id: 3, label: t('health', 'Don\'t know', {}) },
						{ id: 4, label: t('health', 'Don\'t ask me', {}) },
					],
				},
				{
					name: 'Sadness level',
					columnId: 'sadness',
					type: 'select',
					show: this.person.feelingColumnSadness,
					options: [
						{ id: 0, label: t('health', 'None', {}) },
						{ id: 1, label: t('health', 'Low', {}) },
						{ id: 2, label: t('health', 'Medium', {}) },
						{ id: 3, label: t('health', 'High', {}) },
					],
				},
				{
					name: 'Symptoms',
					columnId: 'symptoms',
					show: this.person.feelingColumnSymptoms,
					type: 'multiselect',
					options: [
						{ id: 0, label: t('health', 'Fatigue', {}) },
						{ id: 1, label: t('health', 'No Appetite', {}) },
						{ id: 2, label: t('health', 'Overeating', {}) },
						{ id: 3, label: t('health', 'Repeated thoughts', {}) },
						{ id: 4, label: t('health', 'Unmotivated', {}) },
						{ id: 5, label: t('health', 'Irritable', {}) },
						{ id: 6, label: t('health', 'Lack of Concentration', {}) },
						{ id: 7, label: t('health', 'Anxiety', {}) },
						{ id: 8, label: t('health', 'Isolation self from others', {}) },
						{ id: 9, label: t('health', 'Thoughts of death/sicide', {}) },
						{ id: 10, label: t('health', 'Feeling hopeless', {}) },
						{ id: 11, label: t('health', 'Feeling worthless', {}) },
						{ id: 12, label: t('health', 'Indecisive', {}) },
					],
				},
				{
					name: 'Attacks',
					columnId: 'attacks',
					show: this.person.feelingColumnAttacks,
					type: 'multiselect',
					options: [
						{ id: 0, label: t('health', 'Panic attack', {}) },
						{ id: 1, label: t('health', 'Fit of range', {}) },
						{ id: 2, label: t('health', 'Asthma attack', {}) },
					],
				},
				{
					name: 'Default medication',
					columnId: 'defaultMedication',
					type: 'boolean',
					show: this.person.feelingColumnMedication,
				},
				{
					name: 'Pain',
					columnId: 'pain',
					type: 'select',
					show: this.person.feelingColumnPain,
					options: [
						{ id: 0, label: t('health', 'None', {}) },
						{ id: 1, label: t('health', 'Low', {}) },
						{ id: 2, label: t('health', 'Medium', {}) },
						{ id: 3, label: t('health', 'High', {}) },
					],
				},
			]
		},
		feelingdata: function() {
			return [
				[
					moment().format('YYYY-MM-DDTHH:mm'), // date
					1, // mood
					1, // sadness
					1, // symptoms
					1, // attacks
					true, // medication
					1, // pain
				],
			]
		},
	},
	methods: {

	},
}
</script>
<style lang="scss" scoped>
	.textarea-mission {
		width: 67%;
		min-height: 200px;
	}
	.content-wrapper-health {
		width: 98%;
	}
	.widget {
		border: 1px solid gray;
		border-radius: 4px;
		background-color: #80808026;
		padding: 4px;
		width: 100px;
		margin: 10px;
		float: left;
	}
	.widget h3 {
		margin-top: 5px;
		margin-bottom: 2px;
		font-size: large;
	}
	.widget .date {
		color: gray;
		font-size: 0.8em;
		text-align: right;
	}
	.widget span {
		padding-left: 2px;
		padding-right: 2px;
	}
	.widget .firstNumber {
		font-weight: bold;
		text-align: right;
	}
	.widget .secondNumber {
		text-align: right;
	}
	.clear {
		clear: both;
	}
</style>