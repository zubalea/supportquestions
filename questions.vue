<script>
import { call, get, sync } from 'vuex-pathify'

export default {
  name: 'DashboardCoreAppBar',

  props: {
    value: {
      type: Boolean,
      default: false,
    },
  },

  data: () => ({
    notifications: [
      'You have 3 new reports',
      'WAF Service has been enabled',
      'There is 1 new RIF',
      'A new chart has been added to the Global Dashboard',
      'Happy Birthday',
    ],
    profile: [
      { title: 'Profile' },
      { title: 'Change Password', dialog: 'chgPwdDialog' },
      { title: 'Settings' },
      { divider: true },
      { title: 'Log out', link: '/logout' },
    ],
    dashEmitList: [
      { title: 'Copy', emitname: 'copyDash' },
      { title: 'Edit', emitname: 'editDashTitle' },
      { title: 'Refresh', emitname: 'refreshDash' },
      { title: 'Print', emitname: 'printDash' },
    ],
    // name: 'Vue.js',
    searchQuery: '',
    open: [1, 2],
    search: null,
    caseSensitive: false,
    chgPwdDialog: false,
    oldPwd: '',
    newPwd: '',
    chgPwdError: null,
  }),

  computed: {
    currentUser: sync('auth/currentUser'),
    iconType: sync('dashboard/iconType'),
    // // filter() {
    // //   return this.caseSensitive
    // //     ? (item, search, textKey) => item[textKey].indexOf(search) > -1
    // //     : undefined
    // // },
    breakpoint: get('dashboard/breakpoint'),
    drawer: sync('settings/drawer'),
    widgetData: get('dashboard/widgetData'),

    // ...get('dashboard', ['widgetData']),
    filteredWidgets() {
      if (this.searchQuery) {
        return this.widgetData.map((element) => {
          return {
            ...element,
            widgets: element.widgets.filter(
              (widget) =>
                widget.title
                  .toLowerCase()
                  .indexOf(this.searchQuery.toLowerCase()) !== -1
            ),
          }
        })
      } else {
        return this.widgetData
      }
    },
  },
  watch: {
    chgPwdDialog(val) {
      if (val) {
        this.chgPwdError = null
        this.$refs.pwdReset && this.$refs.pwdReset.reset()
        this.$refs.observer && this.$refs.observer.reset()
      }
    },
  },
  created() {
    console.log('created WIDGET fetchdata')
    this.$store.dispatch('dashboard/fetchWidgetData')
  },
  methods: {
    dotsmenu(emitname) {
      switch (emitname) {
        case 'copyDash':
          console.log('start of copy: ')
          break
        case 'editDashTitle':
          console.log('start of edit: ')
          break
        case 'refreshDash':
          console.log('start of refresh: ')
          break
        case 'printDash':
          console.log('start of print: ')
          break
      }
    },
    whoami: call('users/whoami'),
    setPassword: call('users/setPassword'),
    addWidget: call('dashboard/addWidget'),

    async addWidgetScroll(subitem) {
      await this.addWidget(subitem)
      document.getElementById('dashboard').scrollIntoView(false)
    },
    // addWidgetScroll: async (subitem) => {
    //   await this.addWidget(subitem)
    //   document.getElementById('dashboard').scrollIntoView(true)
    // },
    // alertme: (title) => {
    //   return alert(title)
    // },
    async doChgPwd() {
      console.log('doChgPwd()')
      this.chgPwdError = null
      try {
        await this.setPassword({
          username: this.currentUser.username,
          oldPassword: this.oldPwd,
          newPassword: this.newPwd,
        })
        this.chgPwdDialog = false
      } catch (error) {
        console.log(error.response)
        if (error.response.status === 403)
          error.response.data.msg = 'session_expired'
        this.chgPwdError = error
        console.log(error.response.data)
        // alert(error.response.status)
      }
    },
  },
}
</script>

<template>
  <v-app-bar id="app-bar" elevate-on-scroll app height="75" color="white">
    <div id="progressbar"></div>
    <!--
    ****************
    Hamburger and Title
    ****************
    -->
    <v-btn
      class="mr-3"
      elevation="1"
      fab
      small
      @click="
        $vuetify.breakpoint.smAndDown
          ? setDrawer(!drawer)
          : $emit('input', !value)
      "
    >
      <v-icon v-if="value">
        mdi-view-quilt
      </v-icon>

      <v-icon v-else>
        mdi-menu
      </v-icon>
    </v-btn>
    <v-toolbar-title
      class="hidden-sm-and-down font-weight-light"
      v-text="$t($route.params.slug)"
    />

    <!--
    ****************
    Widget Button and List
    ****************
    -->

    <!-- <div dense>
      <v-btn small class="mr-0 pl-0 pr-0">
        <v-icon small>
          mdi-filter
        </v-icon>
      </v-btn>
      <v-btn small class="mr-0 pl-0 pr-0" @click="enableEdit">
        <v-icon small>
          mdi-pencil
        </v-icon>
      </v-btn>
    </div> -->
    <div class="ml-5 pl-5">
      <v-btn x-small class="ma-0 pt-4 pb-4">
        <v-icon small>
          mdi-filter
        </v-icon>
      </v-btn>
      <v-menu
        :close-on-content-click="false"
        :nudge-width="200"
        offset-x
        :max-height="400"
      >
        <template v-slot:activator="{ on, attrs }">
          <v-btn v-bind="attrs" x-small class="ma-0 pt-4 pb-4" v-on="on">
            <v-icon small>
              mdi-plus
            </v-icon>
          </v-btn>
        </template>
        <v-card class="mt-0 mb-0">
          <v-text-field
            v-model="searchQuery"
            solo
            dense
            label="search"
            hide-details
          >
            <v-icon slot="append">mdi-magnify</v-icon>
          </v-text-field>
          <v-list>
            <v-list-group
              v-for="widget in filteredWidgets"
              :key="widget.service"
              no-action
            >
              <template v-slot:activator>
                <v-list-item-content>
                  <v-list-item-title
                    v-text="widget.service"
                  ></v-list-item-title>
                </v-list-item-content>
              </template>

              <v-list-item
                v-for="subItem in widget.widgets"
                :key="subItem.title"
                class="pl-5"
                @click="addWidgetScroll(subItem)"
              >
                <v-list-item-icon>
                  <v-icon v-text="iconType[subItem.type]"></v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title
                    class="pl-5"
                    v-text="subItem.title"
                  ></v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-group>
          </v-list>
        </v-card>
      </v-menu>
      <v-menu :close-on-content-click="false" offset-x>
        <template v-slot:activator="{ on, attrs }">
          <v-btn v-bind="attrs" x-small class="ml-5 pt-4 pb-4" v-on="on">
            <v-icon small>
              mdi-dots-vertical
            </v-icon>
          </v-btn>
        </template>
        <v-card class="mt-0 mb-0">
          <v-list>
            <v-list-item
              v-for="(item, index) in dashEmitList"
              :key="index"
              style="min-height: 30px;"
              color="blue"
              @click="dotsmenu(item.emitname)"
            >
              <v-list-item-title>{{ item.title }}</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-card>
      </v-menu>
    </div>

    <v-spacer />

    <!--
    ****************
    Search Bar and Button
    ****************
    -->
    <v-text-field
      :label="$t('search')"
      color="secondary"
      hide-details
      style="max-width: 165px;"
    >
      <template v-if="$vuetify.breakpoint.mdAndUp" v-slot:append-outer>
        <v-btn class="mt-n2" elevation="1" fab small>
          <v-icon>mdi-magnify</v-icon>
        </v-btn>
      </template>
    </v-text-field>

    <!--
    ****************
    Notification
    ****************
    -->
    <v-menu
      bottom
      left
      offset-y
      origin="top right"
      transition="scale-transition"
    >
      <template v-slot:activator="{ attrs, on }">
        <v-btn class="ml-2" min-width="0" text v-bind="attrs" v-on="on">
          <v-badge color="red" overlap bordered>
            <template v-slot:badge>
              <span>5</span>
            </template>

            <v-icon>mdi-bell</v-icon>
          </v-badge>
        </v-btn>
      </template>

      <v-list :tile="false" nav>
        <div>
          <!-- <AppBarItem v-for="(n, i) in notifications" :key="`item-${i}`">
            <v-list-item-title v-text="n" />
          </AppBarItem> -->
          <base-app-bar-item v-for="(n, i) in notifications" :key="`item-${i}`"
            ><v-list-item-title v-text="n" />
          </base-app-bar-item>
        </div>
      </v-list>
    </v-menu>

    <!--
    ****************
    User Profile and Logout
    ****************
    -->
    <v-menu
      bottom
      left
      min-width="200"
      offset-y
      origin="top right"
      transition="scale-transition"
    >
      <template v-slot:activator="{ attrs, on }">
        <v-btn class="ml-2" min-width="0" text v-bind="attrs" v-on="on">
          <v-icon>mdi-account</v-icon>
        </v-btn>
      </template>

      <v-list :tile="false" flat nav>
        <template v-for="(p, i) in profile">
          <v-divider v-if="p.divider" :key="`divider-${i}`" class="mb-2 mt-2" />
          <base-app-bar-Item
            v-else
            :key="`item-${i}`"
            :to="p.link"
            @click.native="$data[p.dialog] = true"
          >
            <v-list-item-title v-text="p.title" />
          </base-app-bar-Item>
          <!-- <AppBarItem v-else :key="`item-${i}`" :to="p.link">
            <v-list-item-title v-text="p.title" />
          </AppBarItem>
           -->
        </template>
      </v-list>
    </v-menu>
    <template>
      <!-- <v-row justify="center"> -->
      <v-dialog v-model="chgPwdDialog" max-width="500px">
        <!-- eslint-disable-next-line vue/no-unused-vars -->
        <ValidationObserver ref="observer" v-slot="{ invalid }">
          <v-form ref="pwdReset" @submit.prevent="doChgPwd">
            <base-material-card
              color="cgibeet"
              light
              max-width="100%"
              class="px-5 py-3"
            >
              <template v-slot:heading>
                <div class="text-center">
                  <h1 class="display-2 font-weight-bold mb-2 white--text">
                    {{ $t('change_password') }}
                  </h1>
                </div>
              </template>

              <v-card-text class="text-center">
                <ValidationProvider
                  v-slot="{ errors }"
                  name="old_password"
                  rules="required"
                >
                  <v-text-field
                    v-model="oldPwd"
                    color="cgibeet2"
                    :label="$t('pwdReset_current_password')"
                    type="password"
                    :error-messages="errors"
                    prepend-icon="mdi-account"
                    autofocus
                  />
                </ValidationProvider>
                <ValidationProvider
                  v-slot="{ errors }"
                  name="new_password"
                  :rules="{
                    min: 8,
                    required: true,
                    regex: {
                      arg1: '(?!^\\d+$)^.+$',
                      arg2: $t('field_not_all_numeric'),
                    },
                  }"
                >
                  <v-text-field
                    v-model="newPwd"
                    :label="$t('pwdReset_new_password')"
                    type="password"
                    class="mb-8 mt-4"
                    color="cgibeet2"
                    :error-messages="errors"
                    prepend-icon="mdi-lock-outline"
                    autocomplete="on"
                  />
                </ValidationProvider>
                <BaseButton
                  :disabled="invalid"
                  type="submit"
                  color="cgiice3"
                  raised
                  light
                  >{{ $t('change') }}
                </BaseButton>
                <BaseButton
                  color="cgiice3"
                  raised
                  light
                  @click="chgPwdDialog = false"
                  >{{ $t('cancel') }}
                </BaseButton>
                <p
                  v-if="chgPwdError"
                  type="warning"
                  dense
                  class="mt-5 error--text"
                >
                  {{ $t(`api-` + chgPwdError.response.data.msg) }}
                </p>
              </v-card-text>
            </base-material-card>
          </v-form>
        </ValidationObserver>
      </v-dialog>
      <!-- </v-row> -->
    </template>
  </v-app-bar>
</template>
