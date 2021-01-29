
<script>
import { subDays, formatISO } from 'date-fns'
export default {
  name: 'AppbarCustomer',
  data() {
    return {
      customername: 'Customer Name 1',
      customercode: 'C1',
      customers: [
        { name: 'Customer Name 1', code: 'C1' },
        { name: 'Customer Name 2', code: 'C2' },
        { name: 'Customer Name 3', code: 'C3' },
      ],
      // defaultcustomer: this.defaultselect.text,
      // customercode: this.defaultselect.value,
      // today: new/*  */ Date(),
      today: new Date(),
      // startdate: formatISO(this.today, { representation: 'date' }),
      // startdate: this.today,
      startdate: null,
      enddate: null,
      datemenu: false,
      customerdialog: false,
      datedialog: false,
      enableSelect: false,
      rangeCodeId: 'l30d',
      // dates: [this.startdate, this.enddate],
      daterange: [
        { code: 'ctm', range: 'Custom', type: 'day', delta: 0 },
        { code: 'l7d', range: 'Last 7 Days', type: 'day', delta: 7 },
        { code: 'l30d', range: 'Last 30 Days', type: 'day', delta: 30 },
        { code: 'lw', range: 'Last week', type: 'week', delta: 1 },
        { code: 'lm', range: 'Last Month', type: 'month', delta: 1 },
        { code: 'mtd', range: 'Month to Date', type: 'month', delta: 0 },
        { code: 'wtd', range: 'Week to Date', type: 'week', delta: 0 },
      ],
    }
  },
  computed: {
    // today() {
    //   return new Date()
    // },
    // selectedText() {
    //   return this.defaultselect.text
    // },
    dates() {
      return [this.startdate, this.enddate]
    },
  },
  created() {
    const defaultRange = this.daterange.find(
      (range) => range.code === this.rangeCodeId
    )
    console.log(defaultRange)
    this.calcStartdate(this.today, defaultRange.type, defaultRange.delta)
    this.calcEnddate('0')
  },
  methods: {
    datecalc() {},
    showAlert() {
      debugger
      alert('it worked')
    },
    updateCustomer(val) {
      this.customername = val
    },
    calcStartdate(date, type, delta) {
      // debugger
      switch (type) {
        case 'day':
          this.startdate = formatISO(subDays(new Date(date), delta), {
            representation: 'date',
          })
          break
        case 'week':
          // placeholder
          break
      }
    },
    calcEnddate(delta) {
      this.enddate = formatISO(this.today, { representation: 'date' })
    },
  },
}
</script>

<template>
  <div justify="center">
    <!-- <v-row justify="center">
      <v-col class="d-flex ma-0 pa-0" cols="12">
        <h4 v-if="!enableSelect"
          >{{ selectedText
          }}<v-icon @click="enableSelect = !enableSelect"
            >mdi-menu-down</v-icon
          ></h4
        >
      </v-col>
    </v-row> -->
    <v-menu
      transition="scale-transition"
      dense
      offset-y
      bottom
      origin="top right"
      max-height="300"
    >
      <template v-slot:activator="{ attrs, on }">
        <v-list-item
          class="text-center"
          style="max-width: 256px; min-height:20px"
          v-bind="attrs"
          v-on="on"
        >
          <v-list-item-content class="pa-0">
            <v-toolbar-title>
              {{ customername }}
              <v-icon v-if="true">$dropdown</v-icon>
            </v-toolbar-title>
          </v-list-item-content>
        </v-list-item>
      </template>

      <v-list>
        <v-list-item
          v-for="customer in customers"
          :key="customer.id"
          @click="updateCustomer(customer.name)"
        >
          <v-list-item-title> {{ customer.name }} </v-list-item-title>
        </v-list-item>
      </v-list>
    </v-menu>
    <v-menu
      v-model="datemenu"
      :close-on-content-click="false"
      left
      height="400px"
      offset-y
      nudge-right="50px"
    >
      <template v-slot:activator="{ attrs, on }">
        <v-list-item
          class="text-center"
          style="max-width: 256px; min-height:20px"
          v-bind="attrs"
          v-on="on"
        >
          <v-list-item-content class="pa-1">
            <v-list-item-subtitle>
              {{ startdate }} to {{ enddate
              }}<v-icon small class="ml-2">mdi-calendar-month</v-icon>
            </v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
      </template>
      <v-card class="mb-0 pb-0">
        <v-card-title class="headline">
          <v-select
            v-model="rangeCodeId"
            label="Date Range"
            :items="daterange"
            item-text="range"
            item-value="code"
            :offset-y="true"
            flat
          ></v-select>
        </v-card-title>
        <v-card-text>
          <v-date-picker
            v-model="dates"
            :readonly="rangeCodeId !== 0"
            range
            no-title
            @change.native="showAlert"
          ></v-date-picker>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>

          <v-btn text @click="datemenu = false">
            Cancel
          </v-btn>
          <v-btn text @click="datemenu = false">
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-menu>
  </div>
</template>
