<template>
  <q-page padding>
    <div class="subcontent">
      <div class="q-ma-sm row justify-center">
        <q-btn class="bg-secondary text-accent" icon="navigate_before" @click="onPrev">Forrige</q-btn>
        <q-btn class="bg-secondary text-accent" icon-right="today" @click="onToday">I dag</q-btn>
        <q-btn class="bg-secondary text-accent" icon-right="navigate_next" @click="onNext">Næste</q-btn>

        <q-select v-model="dateHeader" label="date-header" outlined dense options-dense :options="['stacked', 'inline', 'inverted']" class="button" style="min-width: 160px;" />
        <q-select v-model="dateAlign" label="date-align" outlined dense options-dense :options="['center', 'left', 'right']" class="button" style="min-width: 160px;" />
        <q-select v-model="weekdayAlign" label="weekday-align" outlined dense options-dense :options="['center', 'left', 'right']" class="button" style="min-width: 160px;" />
      </div>

      <div class="q-ma-sm row justify-center">
        <router-link to="/">
          <q-btn class="bg-secondary text-accent">Index Page</q-btn>
        </router-link>
        <router-link to="/ExcelReaderPage">
          <q-btn class="bg-secondary text-accent">Excel Reader Page</q-btn>
        </router-link>
      </div>

      <div class="row justify-center">
        <div style="display: flex; max-width: 1600px; width: 100%; height: 800px;">
          <q-calendar-scheduler
            ref="calendar"
            v-model="selectedDate"
            :model-resources="resources"
            :model="shifts"
            view="week"
            short-weekday-label
            :date-header="dateHeader"
            :weekday-align="weekdayAlign"
            :date-align="dateAlign"
            locale="da-dk"
            :weekdays="[1, 2, 3, 4, 5, 6, 0]"
            animated
            bordered
            @change="onChange"
            @moved="onMoved"
            @click-date="onClickDate"
            @click-day-resource="onClickDayResource"
            @click-resource="onClickResource"
            @click-head-resources="onClickHeadResources"
            @click-head-day="onClickHeadDay"
          >
            <template #day="{ scope }">
              <template v-for="(shift, index) in getEvents(scope.timestamp.date, scope.resource.id)" :key="index">
                <q-badge style="width: 100%; cursor: pointer; height: 16px; max-height: 16px">
                  <span class="ellipsis">{{ shift.title }}</span>
                </q-badge>
              </template>
            </template>
          </q-calendar-scheduler>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script>
import { QCalendarScheduler, today } from '@quasar/quasar-ui-qcalendar'
import '@quasar/quasar-ui-qcalendar/src/QCalendarVariables.sass'
import '@quasar/quasar-ui-qcalendar/src/QCalendarTransitions.sass'
import '@quasar/quasar-ui-qcalendar/src/QCalendarScheduler.sass'
import axios from 'axios'
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'SchedulerAlignment',
  components: {
    QCalendarScheduler
  },
  data() {
    return {
      selectedDate: today(),
      dateAlign: 'center',
      weekdayAlign: 'center',
      dateHeader: 'stacked',
      resources: [],
      shifts: []
    }
  },
  methods: {
    async fetchEmployees() {
      try {
        const response = await axios.get('https://warehouse-schedule.azurewebsites.net/api/users/employee')
        this.resources = response.data.map(employee => ({
          id: employee.id,
          label: `${employee.firstName} ${employee.lastName}`
        }))
      } catch (error) {
        console.error('Error fetching employees:', error)
      }
    },
    async fetchShifts() {
      try {
        const response = await axios.get('https://warehouse-schedule.azurewebsites.net/api/shifts')
        const shiftsByDateAndEmployee = {}

        response.data.forEach(shift => {
          const date = shift.date
          const employeeId = shift.employeeId
          if (!shiftsByDateAndEmployee[date]) {
            shiftsByDateAndEmployee[date] = {}
          }
          if (!shiftsByDateAndEmployee[date][employeeId]) {
            shiftsByDateAndEmployee[date][employeeId] = shift
          }
        })

        this.shifts = Object.values(shiftsByDateAndEmployee).flatMap(dateShifts =>
          Object.values(dateShifts).map(shift => {
            const start = `${shift.date}T${shift.shiftStart}:00`
            const end = `${shift.date}T${shift.shiftEnd}:00`
            const title = `Shift from ${shift.shiftStart} to ${shift.shiftEnd}`
            return {
              start,
              end,
              resource: shift.employeeId,
              title
            }
          })
        )
      } catch (error) {
        console.error('Error fetching shifts:', error)
      }
    },
    getEvents(date, resourceId) {
      return this.shifts.filter(shift => shift.start.startsWith(date) && shift.resource === resourceId)
    },
    onToday() {
      this.$refs.calendar.moveToToday()
    },
    onPrev() {
      this.$refs.calendar.prev()
    },
    onNext() {
      this.$refs.calendar.next()
    },
    onMoved(data) {
      console.log('onMoved', data)
    },
    onChange(data) {
      console.log('onChange', data)
    },
    onClickDate(data) {
      console.log('onClickDate', data)
    },
    onClickDayResource(data) {
      console.log('onClickDayResource', data)
    },
    onClickResource(data) {
      console.log('onClickResource', data)
    },
    onClickHeadResources(data) {
      console.log('onClickHeadResources', data)
    },
    onClickHeadDay(data) {
      console.log('onClickHeadDay', data)
    }
  },
  mounted() {
    this.fetchEmployees()
    this.fetchShifts()
  }
})
</script>

<style scoped>
.my-calendar .q-calendar-scheduler__resource {
  background-color: #f5f5f5;
  border-right: 1px solid #e0e0e0;
}
.my-calendar .q-calendar__header {
  background-color: #3f51b5;
  color: white;
}
.my-calendar .q-calendar__day--today {
  background-color: #e0e0e0;
}
.my-calendar .q-calendar__interval {
  border-bottom: 1px solid #e0e0e0;
}
.my-calendar .q-calendar__interval--current {
  background-color: #e0f7fa;
}
.my-calendar .q-calendar__interval--highlight {
  background-color: #fff8e1;
}
</style>
