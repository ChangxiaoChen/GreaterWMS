<template>
  <q-page>
    <router-view />
    <q-page-sticky v-show="device === 2" position="bottom-left" :offset="[18, 18]">
      <input id="scannedBarcodes" v-model="barscan" type="text" @input="datachange()" readonly disabled/>
    </q-page-sticky>
    <q-page-sticky v-show="device === 2" position="bottom-right" :offset="[18, 18]">
      <q-fab
        v-model="fab"
        icon="add"
        direction="up"
        color="accent"
        vertical-actions-align="left"
        @click="fabChange()"
      >
        <q-fab-action
          square
          flat
          external-label
          label-position="bottom"
          label-class="text-black"
          :label="$t('scan.scan_locationquery')"
          label-style="background-color:transparent"
          :to="{ name: 'zebra_locationquery' }"
          :style="{
                         'margin-top': fab8.top,
                         'margin-bottom': fab8.bottom,
                         'margin-left': fab8.left,
                         'margin-right': fab8.right,
                         'height': touchheight,
                         'width': touchwidth
              }">
          <q-img src="statics/stock/stocklist.png" />
        </q-fab-action>
        <q-fab-action
          square
          flat
          external-label
          label-position="bottom"
          label-class="text-black"
          :label="$t('scan.scan_goodsquery')"
          label-style="background-color:transparent"
          :to="{ name: 'zebra_goodslist' }"
          :style="{
                         'margin-top': fab7.top,
                         'margin-bottom': fab7.bottom,
                         'margin-left': fab7.left,
                         'margin-right': fab7.right,
                         'height': touchheight,
                         'width': touchwidth
              }">
          <q-img src="statics/goods/goodslist.png" />
        </q-fab-action>
        <q-fab-action
          square
          flat
          external-label
          label-position="bottom"
          label-class="text-black"
          icon="img:statics/stock/cyclecount.png"
          :label="$t('scan.scan_inventory')"
          label-style="background-color:transparent"
          :to="{ name: 'zebra_cyclecount' }"
          :style="{
                         'margin-top': fab6.top,
                         'margin-bottom': fab6.bottom,
                         'margin-left': fab6.left,
                         'margin-right': fab6.right,
                         'height': touchheight,
                         'width': touchwidth
              }">
          <q-img src="statics/stock/cyclecount.png" />
        </q-fab-action>
        <q-fab-action
          square
          flat
          external-label
          label-position="bottom"
          label-class="text-black"
          :label="$t('scan.scan_movetobin')"
          label-style="background-color:transparent"
          :to="{ name: 'zebra_movetobin' }"
          :style="{
                         'margin-top': fab5.top,
                         'margin-bottom': fab5.bottom,
                         'margin-left': fab5.left,
                         'margin-right': fab5.right,
                         'height': touchheight,
                         'width': touchwidth
              }">
          <q-img src="statics/icons/movetobin.png" />
        </q-fab-action>
        <q-fab-action
          square
          flat
          external-label
          label-position="bottom"
          label-class="text-black"
          :label="$t('scan.scan_shipping')"
          label-style="background-color:transparent"
          :to="{ name: 'zebra_shipping' }"
          :style="{
                         'margin-top': fab4.top,
                         'margin-bottom': fab4.bottom,
                         'margin-left': fab4.left,
                         'margin-right': fab4.right,
                         'height': touchheight,
                         'width': touchwidth
              }">
          <q-img src="statics/icons/car.png" />
        </q-fab-action>
        <q-fab-action
          square
          flat
          external-label
          label-position="bottom"
          label-class="text-black"
          :label="$t('scan.scan_picking')"
          label-style="background-color:transparent"
          :to="{ name: 'zebra_picking' }"
          :style="{
                         'margin-top': fab3.top,
                         'margin-bottom': fab3.bottom,
                         'margin-left': fab3.left,
                         'margin-right': fab3.right,
                         'height': touchheight,
                         'width': touchwidth
              }">
          <q-img src="statics/outbound/picked.png" />
        </q-fab-action>
        <q-fab-action
          square
          flat
          external-label
          label-position="bottom"
          label-class="text-black"
          :label="$t('scan.scan_uptobin')"
          label-style="background-color:transparent"
          :to="{ name: 'zebra_uptobin' }"
          :style="{
                         'margin-top': fab2.top,
                         'margin-bottom': fab2.bottom,
                         'margin-left': fab2.left,
                         'margin-right': fab2.right,
                         'height': touchheight,
                         'width': touchwidth
              }">
          <q-img src="statics/inbound/presortstock.png" />
        </q-fab-action>
        <q-fab-action
          square
          flat
          external-label
          label-position="bottom"
          label-class="text-black"
          :label="$t('scan.scan_sorting')"
          label-style="background-color:transparent"
          :to="{ name: 'zebra_sorting' }"
          :style="{
                         'margin-top': fab1.top,
                         'margin-bottom': fab1.bottom,
                         'margin-left': fab1.left,
                         'margin-right': fab1.right,
                         'height': touchheight,
                         'width': touchwidth
              }">
          <q-img src="statics/inbound/preloadstock.png" />
        </q-fab-action>
      </q-fab>
    </q-page-sticky>
  </q-page>
</template>

<script>
import { getauth } from 'boot/axios_request'
import { LocalStorage, Platform, Screen } from 'quasar'

var sendCommandResults = 'false'

function sendCommand (extraName, extraValue) {
  var broadcastExtras = {}
  broadcastExtras[extraName] = extraValue
  broadcastExtras.SEND_RESULT = sendCommandResults
  window.plugins.intentShim.sendBroadcast({
    action: 'com.symbol.datawedge.api.ACTION',
    extras: broadcastExtras
  },
  function () { },
  function () { }
  )
}

function unregisterBroadcastReceiver () {
  window.plugins.intentShim.unregisterBroadcastReceiver()
}
function commandReceived (commandText) {
  console.log('commandReceived', commandText)
}
function enumerateScanners (enumeratedScanners) {
  // eslint-disable-next-line no-unused-vars
  var humanReadableScannerList = ''
  for (var i = 0; i < enumeratedScanners.length; i++) {
    console.log('Scanner found: name= ' + enumeratedScanners[i].SCANNER_NAME + ', id=' + enumeratedScanners[i].SCANNER_INDEX + ', connected=' + enumeratedScanners[i].SCANNER_CONNECTION_STATE)
    humanReadableScannerList += enumeratedScanners[i].SCANNER_NAME
    if (i < enumeratedScanners.length - 1) { humanReadableScannerList += ', ' }
  }
}
function activeProfile (theActiveProfile) {
  console.log('activeProfile', theActiveProfile)
}
function barcodeScanned (scanData, timeOfScan) {
  var scannedData = scanData.extras['com.symbol.datawedge.data_string']
  console.log('scaned Data:' + scannedData)
  document.getElementById('scannedBarcodes').value = ''
  document.getElementById('scannedBarcodes').value = scannedData
  document.getElementById('scannedBarcodes').dispatchEvent(new Event('input'))
}

export default {
  name: 'Pagezebra_scanbase',
  data () {
    return {
      openid: '',
      login_name: '',
      authin: '0',
      pathname: 'cyclecount/',
      separator: 'cell',
      loading: false,
      device: LocalStorage.getItem('device'),
      device_name: LocalStorage.getItem('device_name'),
      width: '',
      height: '',
      scroll_height: '',
      table_list: [],
      goods_code_label: this.$t('stock.view_stocklist.goods_code'),
      goods_qty_label: this.$t('stock.view_stocklist.onhand_stock'),
      move_qty_label: '移库数量',
      thumbStyle: {
        right: '4px',
        borderRadius: '5px',
        backgroundColor: '#E0E0E0',
        width: '5px',
        opacity: 0.75
      },
      barStyle: {
        right: '2px',
        borderRadius: '9px',
        backgroundColor: '#EEEEEE',
        width: '9px',
        opacity: 0.2
      },
      fab: true,
      touchheight: ((Screen.width - 50) / 5) + '' + 'px',
      touchwidth: ((Screen.width - 50) / 5) + '' + 'px',
      fab1: {
        top: '',
        bottom: '',
        left: '',
        right: ''
      },
      fab2: {
        top: '',
        bottom: '',
        left: '',
        right: ''
      },
      fab3: {
        top: '',
        bottom: '',
        left: '',
        right: ''
      },
      fab4: {
        top: '',
        bottom: '',
        left: '',
        right: ''
      },
      fab5: {
        top: '',
        bottom: '',
        left: '',
        right: ''
      },
      fab6: {
        top: '',
        bottom: '',
        left: '',
        right: ''
      },
      fab7: {
        top: '',
        bottom: '',
        left: '',
        right: ''
      },
      fab8: {
        top: '',
        bottom: '',
        left: '',
        right: ''
      },
      frombin: '',
      tobin: '',
      barscan: '',
      bin_scan: '',
      goods_scan: ''
    }
  },
  methods: {
    fabChange () {
      var _this = this
      LocalStorage.set('fab', _this.fab)
    },
    datachange () {
      var _this = this
      if (LocalStorage.has('auth')) {
        getauth('scanner/?bar_code=' + _this.barscan, {
        }).then(res => {
          if (res.results[0].mode === 'BINSET') {
            _this.bin_scan = res.results[0].code
            _this.goods_scan = ''
          } else if (res.results[0].mode === 'GOODS') {
            _this.goods_scan = res.results[0].code
          }
        }).catch(err => {
          _this.$q.notify({
            message: err.detail,
            icon: 'close',
            color: 'negative'
          })
        })
      }
    },
    scanEvents () {
      var _this = this
      document.addEventListener('deviceready', _this.onDeviceReady, false)
    },
    onDeviceReady () {
      var _this = this
      _this.receivedEvent('deviceready')
      _this.registerBroadcastReceiver()
      _this.determineVersion()
    },
    onPause: function () {
      unregisterBroadcastReceiver()
    },
    onResume () {
      var _this = this
      _this.registerBroadcastReceiver()
    },
    receivedEvent (id) {
      console.log('Received Event: ' + id)
    },
    startSoftTrigger () {
      sendCommand('com.symbol.datawedge.api.SOFT_SCAN_TRIGGER', 'START_SCANNING')
    },
    stopSoftTrigger () {
      sendCommand('com.symbol.datawedge.api.SOFT_SCAN_TRIGGER', 'STOP_SCANNING')
    },
    determineVersion () {
      sendCommand('com.symbol.datawedge.api.GET_VERSION_INFO', '')
    },
    setDecoders () {
      //  Set the new configuration
      var profileConfig = {
        PROFILE_NAME: 'wms',
        PROFILE_ENABLED: 'true',
        CONFIG_MODE: 'UPDATE',
        PLUGIN_CONFIG: {
          PLUGIN_NAME: 'BARCODE',
          PARAM_LIST: {
            // "current-device-id": this.selectedScannerId,
            scanner_selection: 'auto'
          }
        }
      }
      sendCommand('com.symbol.datawedge.api.SET_CONFIG', profileConfig)
    },
    registerBroadcastReceiver () {
      var _this = this
      window.plugins.intentShim.registerBroadcastReceiver({
        filterActions: [
          'com.greaterwms.app.ACTION',
          'com.symbol.datawedge.api.RESULT_ACTION'
        ],
        filterCategories: [
          'android.intent.category.DEFAULT'
        ]
      },
      function (intent) {
        // eslint-disable-next-line no-prototype-builtins
        if (intent.extras.hasOwnProperty('RESULT_INFO')) {
          var commandResult = intent.extras.RESULT + ' (' +
              intent.extras.COMMAND.substring(intent.extras.COMMAND.lastIndexOf('.') + 1, intent.extras.COMMAND.length) + ')'// + JSON.stringify(intent.extras.RESULT_INFO);
          commandReceived(commandResult.toLowerCase())
        }
        // eslint-disable-next-line no-prototype-builtins
        if (intent.extras.hasOwnProperty('com.symbol.datawedge.api.RESULT_GET_VERSION_INFO')) {
          //  The version has been returned (DW 6.3 or higher).  Includes the DW version along with other subsystem versions e.g MX
          var versionInfo = intent.extras['com.symbol.datawedge.api.RESULT_GET_VERSION_INFO']
          console.log('Version Info: ' + JSON.stringify(versionInfo))
          var datawedgeVersion = versionInfo.DATAWEDGE
          datawedgeVersion = datawedgeVersion.padStart(5, '0')
          console.log('Datawedge version: ' + datawedgeVersion)

          //  Fire events sequentially so the application can gracefully degrade the functionality available on earlier DW versions
          if (datawedgeVersion >= '006.3') { _this.datawedge63() }
          if (datawedgeVersion >= '006.4') { _this.datawedge64() }
          if (datawedgeVersion >= '006.5') { _this.datawedge65() }
          // eslint-disable-next-line no-prototype-builtins
        } else if (intent.extras.hasOwnProperty('com.symbol.datawedge.api.RESULT_ENUMERATE_SCANNERS')) {
          console.log(11111)
          //  Return from our request to enumerate the available scanners
          var enumeratedScannersObj = intent.extras['com.symbol.datawedge.api.RESULT_ENUMERATE_SCANNERS']
          console.log(22222)
          enumerateScanners(enumeratedScannersObj)
          // eslint-disable-next-line no-prototype-builtins
        } else if (intent.extras.hasOwnProperty('com.symbol.datawedge.api.RESULT_GET_ACTIVE_PROFILE')) {
          console.log(33333)
          //  Return from our request to obtain the active profile
          var activeProfileObj = intent.extras['com.symbol.datawedge.api.RESULT_GET_ACTIVE_PROFILE']
          console.log(44444)
          activeProfile(activeProfileObj)
          console.log(66666)
          // eslint-disable-next-line no-prototype-builtins
        } else if (!intent.extras.hasOwnProperty('RESULT_INFO')) {
          //  A barcode has been scanned
          console.log(55555)
          barcodeScanned(intent, new Date().toLocaleString())
        }
      }
      )
    },
    datawedge63 () {
      console.log('Datawedge 6.3 APIs are available')
      sendCommand('com.symbol.datawedge.api.CREATE_PROFILE', 'wms')
      sendCommand('com.symbol.datawedge.api.GET_ACTIVE_PROFILE', '')
      sendCommand('com.symbol.datawedge.api.ENUMERATE_SCANNERS', '')
    },
    datawedge64 () {
      console.log('Datawedge 6.4 APIs are available')
      var profileConfig = {
        PROFILE_NAME: 'wms',
        PROFILE_ENABLED: 'true',
        CONFIG_MODE: 'UPDATE',
        PLUGIN_CONFIG: {
          PLUGIN_NAME: 'BARCODE',
          RESET_CONFIG: 'true',
          PARAM_LIST: {}
        },
        APP_LIST: [{
          PACKAGE_NAME: 'org.greaterwms.app',
          ACTIVITY_LIST: ['*']
        }]
      }
      sendCommand('com.symbol.datawedge.api.SET_CONFIG', profileConfig)
      var profileConfig2 = {
        PROFILE_NAME: 'wms',
        PROFILE_ENABLED: 'true',
        CONFIG_MODE: 'UPDATE',
        PLUGIN_CONFIG: {
          PLUGIN_NAME: 'INTENT',
          RESET_CONFIG: 'true',
          PARAM_LIST: {
            intent_output_enabled: 'true',
            intent_action: 'com.zebra.cordovademo.ACTION',
            intent_delivery: '2'
          }
        }
      }
      sendCommand('com.symbol.datawedge.api.SET_CONFIG', profileConfig2)
      //  Give some time for the profile to settle then query its value
      setTimeout(function () {
        sendCommand('com.symbol.datawedge.api.GET_ACTIVE_PROFILE', '')
      }, 1000)
    },
    datawedge65 () {
      console.log('Datawedge 6.5 APIs are available')
      sendCommandResults = 'true'
    }
  },
  created () {
    var _this = this
    if (LocalStorage.has('openid')) {
      _this.openid = LocalStorage.getItem('openid')
    } else {
      _this.openid = ''
      LocalStorage.set('openid', '')
    }
    if (LocalStorage.has('login_name')) {
      _this.login_name = LocalStorage.getItem('login_name')
    } else {
      _this.login_name = ''
      LocalStorage.set('login_name', '')
    }
    if (LocalStorage.has('auth')) {
      _this.authin = '1'
    } else {
      _this.authin = '0'
    }
  },
  mounted () {
    var _this = this
    if (Platform.is.electron) {
      _this.height = String(Screen.height) + 'px'
    } else if (Platform.is.cordova) {
      _this.device_name = LocalStorage.getItem('device_name')
      if (LocalStorage.getItem('device') === 2) {
        window.plugins.insomnia.keepAwake()
        if (LocalStorage.getItem('device_name') === 'Urovo' || LocalStorage.getItem('device_name') === 'Zebra Technologies') {
          _this.fab1.top = '0px'
          _this.fab1.bottom = (0 - ((Screen.width - 50) / 5)) + '' + 'px'
          _this.fab1.left = (((Screen.width - 50) / 6) - (Screen.width / 12 * 10)) + '' + 'px'
          _this.fab1.right = '0px'
          _this.fab2.top = '0px'
          _this.fab2.bottom = (0 - ((Screen.width - 50) / 5)) + '' + 'px'
          _this.fab2.left = ((((Screen.width - 50) / 6) - (Screen.width / 12 * 10)) / 2) + '' + 'px'
          _this.fab2.right = '0px'
          _this.fab3.top = '0px'
          _this.fab3.bottom = '0px'
          _this.fab3.left = '-0px'
          _this.fab3.right = '0px'
          _this.fab4.top = ((Screen.width - 50) / 5) + '' + 'px'
          _this.fab4.bottom = (0 - ((Screen.width - 50) / 5)) + '' + 'px'
          _this.fab4.left = (((Screen.width - 50) / 6) - (Screen.width / 12 * 10)) + '' + 'px'
          _this.fab4.right = '0px'
          _this.fab5.top = '0px'
          _this.fab5.bottom = (0 - ((Screen.width - 50) / 5)) + '' + 'px'
          _this.fab5.left = ((((Screen.width - 50) / 6) - (Screen.width / 12 * 10)) / 2) + '' + 'px'
          _this.fab5.right = '0px'
          _this.fab6.top = '0px'
          _this.fab6.bottom = '0px'
          _this.fab6.left = '0px'
          _this.fab6.right = '0px'
          _this.fab7.top = ((Screen.width - 50) / 5) + '' + 'px'
          _this.fab7.bottom = (0 - ((Screen.width - 50) / 5)) + '' + 'px'
          _this.fab7.left = (((Screen.width - 50) / 6) - (Screen.width / 12 * 10)) + '' + 'px'
          _this.fab7.right = '0px'
          _this.fab8.top = '0px'
          _this.fab8.bottom = ((Screen.width - 50) / 8) + '' + 'px'
          _this.fab8.left = ((((Screen.width - 50) / 6) - (Screen.width / 12 * 10)) / 2) + '' + 'px'
          _this.fab8.right = '0px'
        }
      }
    } else {
      _this.height = Screen.height + '' + 'px'
    }
    _this.barscan = ''
    _this.bin_scan = ''
    _this.goods_scan = ''
    _this.scanEvents()
  },
  beforeDestroy () {
    var _this = this
    LocalStorage.remove('fab')
    window.removeEventListener('deviceready', _this.onDeviceReady, false)
  }
}
</script>
