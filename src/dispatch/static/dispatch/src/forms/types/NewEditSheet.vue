<template>
  <v-form @submit.prevent v-slot="{ isValid }">
    <v-navigation-drawer v-model="showCreateEdit" location="right" width="500">
      <template #prepend>
        <v-list-item lines="two">
          <v-list-item-title v-if="id" class="text-h6"> Edit </v-list-item-title>
          <v-list-item-title v-else class="text-h6"> New </v-list-item-title>
          <v-list-item-subtitle>Form Type</v-list-item-subtitle>

          <template #append>
            <v-btn
              icon
              variant="text"
              color="info"
              :loading="loading"
              :disabled="!isValid.value"
              @click="save()"
            >
              <v-icon>mdi-content-save</v-icon>
            </v-btn>
            <v-btn icon variant="text" color="secondary" @click="closeCreateEdit()">
              <v-icon>mdi-close</v-icon>
            </v-btn>
          </template>
        </v-list-item>
      </template>
      <v-card>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <span class="text-subtitle-2">Details</span>
              </v-col>
              <v-col cols="12">
                <v-text-field
                  v-model="name"
                  label="Name"
                  hint="A name for the form type."
                  clearable
                  required
                  name="Name"
                  :rules="[rules.required]"
                />
              </v-col>
              <v-col cols="12">
                <v-textarea
                  v-model="description"
                  label="Description"
                  hint="A description for the form type."
                  clearable
                  name="Description"
                />
              </v-col>
              <v-col cols="12">
                <v-checkbox
                  v-model="enabled"
                  label="Enabled"
                  hint="Whether this form type is enabled."
                />
              </v-col>
              <v-col cols="12">
                <v-textarea
                  v-model="form_schema"
                  label="Form Schema"
                  hint="The schema defining this form."
                  clearable
                  name="Form Schema"
                />
              </v-col>
              <div v-if="!has_formkit_pro" class="ml-11 text-caption text-grey">
                For more advanced form components, upgrade to
                <a href="https://formkit.com/pro" target="_blank" rel="noopener noreferrer"
                  >FormKit Pro</a
                >
              </div>
            </v-row>
          </v-container>
        </v-card-text>
      </v-card>
    </v-navigation-drawer>
  </v-form>
</template>

<script>
import { required } from "@/util/form"
import { mapFields } from "vuex-map-fields"
import { mapActions } from "vuex"

export default {
  setup() {
    return {
      rules: { required },
    }
  },
  name: "FormsTypeNewEditSheet",

  computed: {
    ...mapFields("forms_type", [
      "selected.name",
      "selected.description",
      "selected.enabled",
      "selected.loading",
      "selected.id",
      "selected.project",
      "selected.form_schema",
      "dialogs.showCreateEdit",
      "has_formkit_pro",
    ]),
    ...mapFields("forms_type", {
      default_incident_cost_type: "selected.default",
    }),
  },

  methods: {
    ...mapActions("forms_type", ["save", "closeCreateEdit"]),
  },

  created() {
    if (this.$route.query.project) {
      this.project = { name: this.$route.query.project }
    }
  },
}
</script>

<style>
.mdi-school {
  color: white !important;
}
</style>
