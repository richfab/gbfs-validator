<script setup>
import { computed, defineProps } from 'vue'
import SubResult from './SubResult.vue'

const props = defineProps({
  isValidating: {
    type: Boolean,
    required: true
  },
  result: {
    type: [Object, Error, Boolean],
    required: true
  }
})

const errorsCountFormated = computed(() => {
  return new Intl.NumberFormat().format(props.result.summary.errorsCount)
})
</script>

<template>
  <div class="result">
    <div v-if="isValidating" class="validating">Validating...</div>
    <div v-else>
      <div v-if="result.summary">
        <h3 class="mt-4 mb-4">Result</h3>

        <b-alert
          v-if="result.summary.versionUnimplemented"
          variant="danger"
          show
        >
          Sorry, this version is not yet implemented or not detectable !
        </b-alert>
        <div v-else>
          <b-alert variant="info" show>
            Detected version <b>{{ result.summary.version.detected }} </b> and
            validate with
            <a
              :href="`https://github.com/NABSA/gbfs/blob/v${result.summary.version.validated}/gbfs.md`"
              ><b>{{ result.summary.version.validated }}</b></a
            >
          </b-alert>

          <div>
            <b-alert v-if="result.summary.hasErrors" variant="danger" show>
              Invalid GBFS feed <br />
              <b>{{ errorsCountFormated }} errors</b>
            </b-alert>
            <b-alert v-else variant="success" show>Valid !</b-alert>
          </div>
          <h4 class="mt-3 mb-3">Detail per files</h4>
          <div v-for="file in result.files" :key="file.filename">
            <SubResult :file="file" />
          </div>
        </div>
      </div>
      <b-alert v-else-if="result" variant="danger" show>{{ result }}</b-alert>
    </div>
  </div>
</template>

<style scoped>
.result {
  margin-bottom: 80px;
}

.validating {
  text-align: center;
  margin: 50px 0;
  font-size: 2rem;
}

.info {
  margin: 30px 0;
}
</style>
