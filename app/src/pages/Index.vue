<template>
  <q-page class="q-pa-md">
    <q-stepper
      v-model="step"
      vertical
      color="primary"
      animated
      style="opacity:0.9"
    >
      <q-step
        v-for="(data, index) in stepData"
        v-bind:key="index"
        :name="index"
        :done="step > index"
        :title="data.title"
        :caption="data.caption"
        :icon="data.icon"
      >
        <q-markdown :src="data.body" />
        <q-stepper-navigation>
          <q-btn @click="step = index+1" color="primary" label="Continue" />
          <q-btn v-if="index > 0" flat @click="step = index-1" color="primary" label="Back" class="q-ml-sm" />
        </q-stepper-navigation>
      </q-step>

      <q-step
        :name="stepData.length"
        title="Download your charter"
        icon="folder"
        active-icon="folder"
      >
        <q-markdown>
# Download the charter documents.
        </q-markdown>
        <q-btn color="teal" label="here" icon="cloud_download"></q-btn>
        <q-stepper-navigation>
          <q-btn color="primary" label="Finish"/>
          <q-btn flat @click="step = stepData.length-1" color="primary" label="Back" class="q-ml-sm" />
        </q-stepper-navigation>
      </q-step>
    </q-stepper>
  </q-page>
</template>

<style>
</style>

<script>
import frontMatter from 'front-matter'
import rawStepData from '../../../content/sample'

function normalizeStepData (raw) {
  const includesFrontMater = frontMatter.test(raw)
  const { attributes, body } = frontMatter(raw)

  if (!includesFrontMater) {
    return {
      title: 'Error: missing front-matter',
      icon: 'warning',
      body: `
::: danger
This .md file seems to be missing it's front-matter header.
:::
      `
    }
  }

  if (!attributes.title) {
    return {
      title: 'Error: missing title',
      icon: 'warning',
      body: `
::: danger
The **title** attribute is missing from the front-matter header, which is required.
:::
      `
    }
  }

  return {
    title: attributes.title,
    caption: attributes.caption || '',
    icon: attributes.icon || 'notes',
    body
  }
}

export default {
  name: 'PageIndex',
  data () {
    return {
      step: 0,
      stepData: rawStepData.map(normalizeStepData)
    }
  }
}
</script>
