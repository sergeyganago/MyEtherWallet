<template>
  <div class="mobile-interface-address">
    <address-qrcode-modal ref="qrcode" :address="account.address" />
    <div class="wrap">
      <div class="top-block">
        <div class="blockie-container">
          <blockie
            :address="address"
            :size="8"
            :scale="16"
            class="blockie-image"
          />
        </div>
        <div class="address">{{ address }}</div>
        <input
          ref="copyAddress"
          :value="address"
          class="hidden-input"
          autocomplete="off"
        />
        <div class="address-end">
          {{
            address !== null && address !== ''
              ? address.substring(address.length - 4, address.length)
              : ''
          }}
        </div>
        <div class="buttons-container">
          <button @click="openQrcode">
            <img alt src="~@/assets/images/icons/qr-code-white.svg" />
            <div class="floating-barcode">
              <div class="barcode-image"></div>
            </div>
          </button>
          <button @click="print">
            <img alt src="~@/assets/images/icons/printer-white.svg" />
          </button>
          <button @click="copy">
            <img alt src="~@/assets/images/icons/copy.svg" />
          </button>
        </div>
      </div>
      <div v-if="hasMultipleAddr" class="bottom-block">
        <button @click="switchAddr">{{ $t('interface.change-addr') }}</button>
      </div>
    </div>
    <!-- .wrap -->
  </div>
</template>

<script>
import AddressQrcodeModal from '@/components/AddressQrcodeModal';
import Blockie from '@/components/Blockie';
import { mapState } from 'vuex';
import { Toast } from '@/helpers';
import {
  KEYSTORE,
  PRIV_KEY,
  MEW_CONNECT,
  WEB3_WALLET
} from '@/wallets/bip44/walletTypes';

export default {
  components: {
    blockie: Blockie,
    'address-qrcode-modal': AddressQrcodeModal
  },
  props: {
    address: {
      type: String,
      default: ''
    },
    print: {
      type: Function,
      default: function () {}
    },
    switchAddr: {
      type: Function,
      default: function () {}
    }
  },
  data() {
    return {
      hasMultipleAddr: false
    };
  },
  computed: {
    ...mapState('main', ['account'])
  },
  mounted() {
    if (this.account.address !== null) {
      if (
        this.account.identifier !== KEYSTORE &&
        this.account.identifier !== PRIV_KEY &&
        this.account.identifier !== MEW_CONNECT &&
        this.account.identifier !== WEB3_WALLET
      ) {
        this.hasMultipleAddr = true;
      } else {
        this.hasMultipleAddr = false;
      }
    }
  },
  methods: {
    copy() {
      this.$refs.copyAddress.select();
      document.execCommand('copy');
      Toast.responseHandler(this.$t('common.copied'), Toast.INFO);
    },
    openQrcode() {
      this.$refs.qrcode.$refs.addressQrcode.show();
    }
  }
};
</script>

<style lang="scss" scoped>
@import 'MobileInterfaceAddress.scss';
</style>
