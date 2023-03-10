<template>
	<tr v-if="row" :class="{ selected }">
		<td><NcCheckboxRadioSwitch :checked="selected" @update:checked="v => $emit('update-row-selection', { rowId: row.id, value: v })" /></td>
		<td v-for="col in columns" :key="col.id">
			<TableCellProgress v-if="col.type === 'number' && col.subtype === 'progress'"
				:column="col"
				:row-id="row.id"
				:value="parseInt(getCellValue(col.id))" />
			<TableCellLink v-else-if="col.type === 'text' && col.subtype === 'link'"
				:column="col"
				:row-id="row.id"
				:value="getCellValue(col.id)" />
			<TableCellNumber v-else-if="col.type === 'number' && !col.subtype"
				:column="col"
				:row-id="row.id"
				:value="getCellValue(col.id)" />
			<TableCellStars v-else-if="col.type === 'number' && col.subtype === 'stars'"
				:column="col"
				:row-id="row.id"
				:value="getCellValue(col.id)" />
			<TableCellYesNo v-else-if="col.type === 'selection' && col.subtype === 'check'"
				:column="col"
				:row-id="row.id"
				:value="getCellValue(col.id) === 'true'" />
			<TableCellDateTime v-else-if="col.type === 'datetime'"
				:column="col"
				:row-id="row.id"
				:value="getCellValue(col.id)" />
			<TableCellTextLine v-else-if="col.type === 'text' && col.subtype === 'line'"
				:column="col"
				:row-id="row.id"
				:value="getCellValue(col.id)" />
			<TableCellHtml v-else
				:value="getCellValue(col.id)"
				:row-id="row.id"
				:column="col" />
		</td>
		<td>
			<NcButton type="primary" @click="$emit('edit-row', row.id)">
				<template #icon>
					<Pencil :size="20" />
				</template>
			</NcButton>
		</td>
	</tr>
</template>

<script>
import { NcCheckboxRadioSwitch, NcButton } from '@nextcloud/vue'
import Pencil from 'vue-material-design-icons/Pencil.vue'
import TableCellHtml from './TableCellHtml.vue'
import TableCellProgress from './TableCellProgress.vue'
import TableCellLink from './TableCellLink.vue'
import TableCellNumber from './TableCellNumber.vue'
import TableCellStars from './TableCellStars.vue'
import TableCellYesNo from './TableCellYesNo.vue'
import TableCellDateTime from './TableCellDateTime.vue'
import TableCellTextLine from './TableCellTextLine.vue'

export default {
	name: 'TableRow',
	components: {
		TableCellYesNo,
		TableCellStars,
		TableCellNumber,
		TableCellLink,
		TableCellProgress,
		TableCellHtml,
		NcButton,
		Pencil,
		NcCheckboxRadioSwitch,
		TableCellDateTime,
		TableCellTextLine,
	},
	props: {
		row: {
			type: Object,
			default: () => {},
		},
		columns: {
			type: Array,
			default: () => [],
		},
		selected: {
			type: Boolean,
			default: false,
		},
	},
	computed: {
		getSelection: {
			get: () => { return this.selected },
			set: () => { alert('updating selection') },
		},
	},
	methods: {
		getCellValue(columnId) {
			if (!this.row) {
				return null
			}

			// lets see if we have a value
			const cell = this.row.data.find(item => item.columnId === columnId)

			// if no value is given, try to get the default value from the column definition
			if (!cell) {
				const column = this.columns.filter(column => column.id === columnId)[0]
				return column[column.type + 'Default']
			}
			return cell.value
		},
		truncate(text) {
			if (text.length >= 400) {
				return text.substring(0, 400) + '...'
			} else {
				return text
			}
		},
	},
}
</script>

<style scoped>

tr.selected {
  background-color: var(--color-primary-light) !important;
}

</style>
