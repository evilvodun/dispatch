<template>
  <v-dialog v-model="display" max-width="600px">
    <template #activator="{ props: filterProps }">
      <v-badge :model-value="!!numFilters" bordered color="info" :content="numFilters">
        <v-btn color="secondary" v-bind="filterProps"> Filter </v-btn>
      </v-badge>
    </template>
    <v-card>
      <v-card-title>
        <span class="text-h5">Case Filters</span>
      </v-card-title>
      <v-list density="compact">
        <v-list-item>
          <date-window-input v-model="local_reported_at" label="Reported At" />
        </v-list-item>
        <v-list-item>
          <date-window-input v-model="local_closed_at" label="Closed At" />
        </v-list-item>
        <v-list-item>
          <project-combobox v-model="local_project" label="Projects" />
        </v-list-item>
        <v-list-item>
          <case-type-combobox v-model="local_case_type" />
        </v-list-item>
        <v-list-item>
          <case-severity-combobox v-model="local_case_severity" />
        </v-list-item>
        <v-list-item>
          <case-priority-combobox v-model="local_case_priority" />
        </v-list-item>
        <v-list-item>
          <case-status-multi-select v-model="local_status" />
        </v-list-item>
        <v-list-item>
          <tag-type-filter-combobox v-model="local_tag_type" label="Tag Types" />
        </v-list-item>
        <v-list-item>
          <tag-filter-auto-complete v-model="local_tag" label="Tags" />
        </v-list-item>
      </v-list>
      <v-card-actions>
        <v-spacer />
        <v-btn color="info" variant="text" @click="applyFilters()"> Apply Filters </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script setup>
import { ref, computed } from "vue"
import { useStore } from "vuex"
import { sum } from "lodash"

import CasePriorityCombobox from "@/case/priority/CasePriorityCombobox.vue"
import CaseSeverityCombobox from "@/case/severity/CaseSeverityCombobox.vue"
import CaseStatusMultiSelect from "@/case/CaseStatusMultiSelect.vue"
import CaseTypeCombobox from "@/case/type/CaseTypeCombobox.vue"
import DateWindowInput from "@/components/DateWindowInput.vue"
import ProjectCombobox from "@/project/ProjectCombobox.vue"
import TagFilterAutoComplete from "@/tag/TagFilterAutoComplete.vue"
import TagTypeFilterCombobox from "@/tag_type/TagTypeFilterCombobox.vue"

const store = useStore()
const props = defineProps({
  projects: {
    type: Array,
    default: function () {
      return []
    },
  },
})

const display = ref(false)
const local_case_priority = ref([])
const local_case_severity = ref([])
const local_case_type = ref([])
const local_closed_at = ref({})
const local_project = ref(props.projects)
const local_reported_at = ref({})
const local_status = ref([])
const local_tag = ref([])
const local_tag_type = ref([])

const case_priority = computed(
  () => store.state.case_management.table.options.filters.case_priority
)
const case_severity = computed(
  () => store.state.case_management.table.options.filters.case_severity
)
const case_type = computed(() => store.state.case_management.table.options.filters.case_type)
const project = computed(() => store.state.case_management.table.options.filters.project)
const status = computed(() => store.state.case_management.table.options.filters.status)
const tag = computed(() => store.state.case_management.table.options.filters.tag)
const tag_type = computed(() => store.state.case_management.table.options.filters.tag_type)

const numFilters = computed(() => {
  return sum([
    case_priority.value?.length || 0,
    case_severity.value?.length || 0,
    case_type.value?.length || 0,
    project.value?.length || 0,
    status.value?.length || 0,
    tag.value?.length || 0,
    tag_type.value?.length || 0,
  ])
})

const applyFilters = () => {
  const filters = {
    case_priority: local_case_priority.value,
    case_severity: local_case_severity.value,
    case_type: local_case_type.value,
    closed_at: local_closed_at.value,
    project: local_project.value,
    reported_at: local_reported_at.value,
    status: local_status.value,
    tag: local_tag.value,
    tag_type: local_tag_type.value,
  }

  // Commit the mutation to update the filters in the Vuex store
  store.commit("case_management/SET_FILTERS", filters)
  display.value = false
}
</script>
