<template>
  <div id="app">
    <header class="header">
      <h1>Calendario laboral {{ year }}</h1>
    </header>

    <main class="main">
      <div class="form-container">
        <label>
          <select class="select" v-model="region">
            <option
              v-for="(region, i) in regions"
              :key="i"
              :value="region.value"
            >
              {{ region.name }}
            </option>
          </select>
        </label>

        <label>
          <select class="select" v-model="year">
            <option
              v-for="(year, i) in years"
              :key="i"
              :value="year"
            >
              {{ year }}
            </option>
          </select>
        </label>
      </div>
      <div class="calendar-container">
        <v-calendar
          v-for="i in 12"
          :key="i"
          ref="calendar"
          class="calendar"
          :attributes="calendarAttr"
          :first-day-of-week="2"
          title-position="right"
          nav-visibility="hidden"
          transition="none"
          disable-page-swipe
        />
      </div>
    </main>
  </div>
</template>

<script>
  import VCalendar from 'v-calendar/lib/components/calendar.umd'

  export default {
    components: {
      VCalendar
    },

    data() {
      return {
        now: new Date(),
        region: 'madrid',
        year: new Date().getFullYear(),
        regions: [
          {value: 'alava', name: 'Alava'},
          {value: 'albacete', name: 'Albacete'},
          {value: 'alicante', name: 'Alicante'},
          {value: 'almeria', name: 'Almeria'},
          {value: 'asturias', name: 'Asturias'},
          {value: 'avila', name: 'Avila'},
          {value: 'badajoz', name: 'Badajoz'},
          {value: 'baleares', name: 'Baleares'},
          {value: 'barcelona', name: 'Barcelona'},
          {value: 'bilbao', name: 'Bilbao'},
          {value: 'burgos', name: 'Burgos'},
          {value: 'caceres', name: 'Caceres'},
          {value: 'cadiz', name: 'Cadiz'},
          {value: 'cantabria', name: 'Cantabria'},
          {value: 'castellon', name: 'Castellon'},
          {value: 'ceuta', name: 'Ceuta'},
          {value: 'ciudad-real', name: 'Ciudad Real'},
          {value: 'cordoba', name: 'Cordoba'},
          {value: 'la-coruna', name: 'La Coruna'},
          {value: 'cuenca', name: 'Cuenca'},
          {value: 'gijon', name: 'Gijon'},
          {value: 'girona', name: 'Girona'},
          {value: 'granada', name: 'Granada'},
          {value: 'guadalajara', name: 'Guadalajara'},
          {value: 'guipuzcoa', name: 'Guipuzcoa'},
          {value: 'huelva', name: 'Huelva'},
          {value: 'huesca', name: 'Huesca'},
          {value: 'jaen', name: 'Jaen'},
          {value: 'leon', name: 'Leon'},
          {value: 'lleida', name: 'Lleida'},
          {value: 'logrono', name: 'Logrono'},
          {value: 'lugo', name: 'Lugo'},
          {value: 'madrid', name: 'Madrid'},
          {value: 'malaga', name: 'Malaga'},
          {value: 'melilla', name: 'Melilla'},
          {value: 'murcia', name: 'Murcia'},
          {value: 'navarra', name: 'Navarra'},
          {value: 'ourense', name: 'Ourense'},
          {value: 'oviedo', name: 'Oviedo'},
          {value: 'palencia', name: 'Palencia'},
          {value: 'palma-de-mallorca', name: 'Palma de Mallorca'},
          {value: 'las-palmas', name: 'Las Palmas'},
          {value: 'pamplona', name: 'Pamplona'},
          {value: 'pontevedra', name: 'Pontevedra'},
          {value: 'la-rioja', name: 'La Rioja'},
          {value: 'salamanca', name: 'Salamanca'},
          {value: 'san-sebastian', name: 'San Sebastian'},
          {value: 'santander', name: 'Santander'},
          {value: 'segovia', name: 'Segovia'},
          {value: 'sevilla', name: 'Sevilla'},
          {value: 'soria', name: 'Soria'},
          {value: 'tarragona', name: 'Tarragona'},
          {value: 'tenerife', name: 'Tenerife'},
          {value: 'teruel', name: 'Teruel'},
          {value: 'toledo', name: 'Toledo'},
          {value: 'valencia', name: 'Valencia'},
          {value: 'valladolid', name: 'Valladolid'},
          {value: 'vitoria', name: 'Vitoria'},
          {value: 'vizcaya', name: 'Vizcaya'},
          {value: 'zamora', name: 'Zamora'},
          {value: 'zaragoza', name: 'Zaragoza'},
        ],
        dates: null,
        baseAttr: [
          {
            key: 'today',
            highlight: {
              color: 'blue',
              fillMode: 'light',
            },
            dates: new Date()
          },
          {
            key: 'weekend',
            highlight: {
              color: 'none',
              fillMode: 'light',
              contentClass: 'bold',
            },
            dates: {
              weekdays: [1, 7],
            },
          }
        ],
        calendarAttr: this.baseAttr,
      };
    },

    mounted() {
      this.initCalendars();
      this.fetchDates(this.region, this.year);
    },

    watch: {
      region(v) {
        this.fetchDates(v, this.year);
      },

      year(v) {
        this.fetchDates(this.region, v);
        this.initCalendars();
      },

      dates(v) {
        this.calendarAttr = [...this.baseAttr];
        v.forEach((d) => {
          let color;
          switch (d.type) {
            case 'local':
              color = 'green';
              break;
            case 'regional':
              color = 'blue';
              break;
            case 'national':
            default:
              color = 'red';
          }
          this.calendarAttr.push({
            key: 'holiday',
            highlight: {
              color,
            },
            dates: new Date(d.date),
            popover: {
              label: d.name,
            },
          });
        })
      },
    },

    computed: {
      currentYear() {
        return this.now.getFullYear();
      },

      years() {
        const arr = Array(this.currentYear - 1998).fill(2000);
        return arr.map((v, i) => v + i);
      }
    },

    methods: {
      initCalendars() {
        this.$refs.calendar.forEach(
          (c, i) => this.moveCalendar(c, i + 1, this.year)
        );
      },

      async moveCalendar(calendarRef, month = 1, year = this.year) {
        await calendarRef.move({month: month, year: year});
      },

      fetchDates(region, year) {
        const that = this;
        fetch(`${process.env.VUE_APP_API_URL}/${region}/${year}`)
          .then(res => res.json())
          .then(function ({data}) {
            that.dates = data;
          })
          .catch(function (err) {
            console.log('failed to fetch page: ', err);
          });
      },
    },
  }
</script>

<style lang="scss">
  body {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
  }

  .bold {
    font-weight: bold;
  }

  .header {
    margin: 80px 0 48px 0;
  }

  .form-container {
    display: flex;
    flex-flow: row wrap;
    justify-content: center;

    > * {
      margin: 0 8px;
    }
  }

  .select {
    padding: 8px 8px;
    -moz-appearance: none;
    -webkit-appearance: none;
    appearance: none;
    box-shadow: 0 1px 0 1px rgba(0, 0, 0, .04);
    border-radius: .5em;
  }

  .calendar-container {
    display: flex;
    flex-flow: row wrap;
    justify-content: center;
    max-width: 1000px;
    margin: 0 auto 80px auto;
  }

  .calendar {
    flex: 0 1 30%;
    margin: 16px;

    .vc-arrows-container {
      display: none;
    }
  }
</style>
