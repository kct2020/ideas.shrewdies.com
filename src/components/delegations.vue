<template>
<q-expansion-item dense dense-toggle expand-separator icon="redeem" header-class="text-primary" :label="heading" title="Click to view full delegation information">
  <q-scroll-area style="height:400px">
  <q-list dense>
    <q-item v-for="dele in delegations" :key="dele.id" label="Delegations">
      <q-item-section avatar class="gt-xs">
        <router-link :to="returnAccountLink(dele.delegatee)"><q-avatar size="sm"><q-img :src="getHiveAvatarUrl(dele.delegatee)" /></q-avatar></router-link>
      </q-item-section>
      <q-item-section>
        <router-link :to="returnAccountLink(dele.delegatee)">{{ dele.delegatee }}</router-link>
      </q-item-section>
      <q-item-section>
        {{ tidyNumber(vestToHive(dele.vesting_shares)) }} {{ token }}
      </q-item-section>
      <q-item-section>
        {{ timeDelta(dele.min_delegation_time) }}
      </q-item-section>
      <q-item-section v-if="username === loggedInUser">
        <q-btn flat color="orange" icon="edit" title="Modify delegation" @click="to = dele.delegatee; amountVests = parseFloat(dele.vesting_shares.split(' ')[0]); delegationDialogVisible = true" />
      </q-item-section>
    </q-item>
    <q-item>
      <q-item-section>
        <q-btn flat icon="search" label="Show more delegators" color="primary" @click="getDelegations()" v-if="delegations.length > 99" :disable="loadMoreButtonDisabled"/>
        <q-btn :disable="loggedInUser !== username" icon="add" color="primary" flat label="Delegate your Hive Power" @click="to = ''; amountVests = 0.000000; delegationDialogVisible = true"/>
      </q-item-section>
    </q-item>
  </q-list>
  <q-dialog v-model="delegationDialogVisible">
    <delegationDialog :username="username" :toModify="to" :delegations="delegations" tokenName="HIVE" :amountVestsSuggest="amountVests" />
  </q-dialog>
  </q-scroll-area>
</q-expansion-item>
</template>
<script>
import moment from 'moment'
export default {
  name: 'delegations',
  props: ['username'],
  data () {
    return {
      delegations: [],
      error: null,
      token: 'HP',
      to: '',
      loadMoreButtonDisabled: false,
      delegationDialogVisible: false,
      amountVests: null
    }
  },
  components: {
    delegationDialog: () => import('components/delegationDialog.vue')
  },
  computed: {
    globalProps: function () { return this.$store.state.hive.globalProps },
    loggedInUser: function () { return this.$store.state.hive.user.username },
    account: function () { return this.$store.state.hive.accounts[this.username] },
    heading: function () {
      var incomingMsg = ''
      if (this.account.received_vesting_shares.split(' ')[0] !== '0.000000') { incomingMsg = ', ' + this.tidyNumber(this.vestToHive(this.account.received_vesting_shares.split(' ')[0])) + ' HP of incoming delegations' }
      if (this.delegations && this.delegations.length > 0) {
        return this.tidyNumber(this.vestToHive(this.account.delegated_vesting_shares.split(' ')[0])) + ' ' + this.token + ' delegated from ' + this.username + '' + incomingMsg
      } else if (this.delegations) {
        return 'No outgoing delegations' + incomingMsg
      } else {
        return 'Loading Delegations ..'
      }
    }
  },
  methods: {
    getDelegations () {
      var last = ''
      var limit = 100
      if (this.delegations.length > 0) { last = this.delegations[this.delegations.length - 1].delegatee }
      this.$hive.api.callAsync('condenser_api.get_vesting_delegations', [this.username, last, limit])
        .then(response => {
          if ((last !== '' & response.length === 1) || (response.length < limit)) { this.loadMoreButtonDisabled = true }
          if (last !== '') { response.shift() }
          this.delegations.push.apply(this.delegations, response)
        })
        .error(err => { this.error = err.cause.data })
    },
    timeDelta (timestamp) {
      var now = moment.utc()
      var stamp = moment.utc(timestamp)
      var diff = stamp.diff(now, 'minutes')
      return moment.duration(diff, 'minutes').humanize(true)
    },
    vestToHive (vests) { if (this.globalProps) { return this.$hive.formatter.vestToHive(vests, this.globalProps.total_vesting_shares, this.globalProps.total_vesting_fund_hive).toFixed(3) } else { return null } },
    getHiveAvatarUrl (user) { return 'https://images.hive.blog/u/' + user + '/avatar' },
    onlyUnique (value, index, self) { return self.indexOf(value) === index },
    tidyNumber (x) {
      var parts = x.toString().split('.')
      parts[0] = parts[0].replace(/\B(?=(\d{3})+(?!\d))/g, ',')
      return parts.join('.')
    },
    returnAccountLink (account) { return '/@' + account }
  },
  mounted () {
    if (this.globalProps.empty) { this.$store.dispatch('hive/getGlobalProps') }
    this.getDelegations()
  }
}
</script>
