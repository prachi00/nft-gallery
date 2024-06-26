<template>
  <div class="min-h-full is-flex is-flex-direction-column">
    <Navbar v-if="isNavbarVisible" />
    <main class="is-flex-grow-1">
      <section class="section">
        <div class="container">
          <div class="columns is-centered">
            <div class="column is-half">
              <Nuxt />
            </div>
          </div>
        </div>
      </section>
    </main>
    <Footer />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator'
import { cryptoWaitReady } from '@polkadot/util-crypto'
import keyring from '@polkadot/ui-keyring'
import isShareMode from '@/utils/isShareMode'
import correctFormat from '@/utils/ss58Format'

@Component<Dashboard>({})
export default class Dashboard extends Vue {
  get chainProperties() {
    return this.$store.getters['chain/getChainProperties']
  }

  get ss58Format(): number {
    return this.chainProperties?.ss58Format
  }

  public async loadKeyring(): Promise<void> {
    const isDevelopment = process.env.VUE_APP_KEYRING === 'true'
    keyring.loadAll({
      ss58Format: correctFormat(this.ss58Format),
      type: 'sr25519',
      isDevelopment,
    })
  }

  public async mountWasmCrypto(): Promise<void> {
    await cryptoWaitReady()
    console.log('wasmCrypto loaded')
    this.loadKeyring()
    this.$store.commit('keyringLoaded') // TODO: See #1376
  }

  public mounted(): void {
    this.mountWasmCrypto()
  }

  get isNavbarVisible() {
    return !isShareMode
  }
}
</script>
