<template>
  <div class="content">
    <b-field grouped group-multiline>
      <BasicSwitch
        class="is-flex control"
        v-model="vListed"
        label="sort.listed"
        size="is-medium"
      />
    </b-field>
  </div>
</template>

<script lang="ts" >
import { Component, Prop, Vue, Emit } from 'vue-property-decorator'
import { Debounce } from 'vue-debounce-decorator'
import { exist } from './Search/exist'
// import shouldUpdate from '@/utils/shouldUpdate'

@Component({
  components: {
    BasicSwitch: () => import('@/components/shared/form/BasicSwitch.vue'),
  },
})
export default class ToggleLayout extends Vue {
  @Prop(String) public search!: string
  @Prop(String) public type!: string
  @Prop(String) public sortBy!: string
  @Prop(Boolean) public listed!: boolean

  protected isVisible = false
  public mounted(): void {
    exist(this.$route.query.listed, this.updateListed)
  }

  get vListed(): boolean {
    return this.listed
  }

  set vListed(listed: boolean) {
    this.updateListed(listed)
  }
 @Emit('update:listed')
  @Debounce(50)
  updateListed(value: string | boolean): boolean {
    const v = String(value)
    this.replaceUrl(v, 'listed')
    return v === 'true'
  }

    @Debounce(100)
  replaceUrl(value: string, key = 'search'): void {
    this.$router
      .replace({
        name: String(this.$route.name),
        query: {
          ...this.$route.query,
        //   search: this.searchQuery,
          [key]: value,
        },
      })
      .catch(console.warn /*Navigation Duplicate err fix later */)
  }

}
</script>

<style scoped lang="scss">
@import '@/styles/variables';

.card {
  box-shadow: 0px 0px 5px 0.5px $primary;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
