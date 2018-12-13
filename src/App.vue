<template>
  <div>
    <header class="header">
      <div class="container">
        <a class="header__logo-link" href="/" title="Swiss Parliament Councillors">
          <img class="header__logo" src="./assets/logo.png" alt="Switzerland Flag"
            width="32" height="32">
          Swiss Parliament Councillors
        </a>
      </div>
    </header>
    <main class="main">
      <div class="container">
        <section class="section section--councillor">
          <h2 class="heading heading--h2">Historic</h2>
          <div class="filter">
            <h3 class="heading heading--h3">Filter</h3>
            <p class="section__description">You can search using the following filter options:</p>
            <form class="form">
              <div class="form__group">
                <label class="form__label" for="name">Name</label>
                <input class="form__control" id="name" name="name" type="text"
                  v-model="search.name" autofocus>
              </div>
              <div class="form__group">
                <label class="form__label" for="all">Gender</label>
                <div class="form__radio-group">
                  <div class="form__radio">
                    <input class="form__radio-control" id="all" name="gender" type="radio"
                      value="" v-model="search.gender">
                    <label class="form__radio-label" for="all">All</label>
                  </div>
                  <div class="form__radio">
                    <input class="form__radio-control" id="female" name="gender" type="radio"
                      value="F" v-model="search.gender">
                    <label class="form__radio-label" for="female">Female</label>
                  </div>
                  <div class="form__radio">
                    <input class="form__radio-control" id="male" name="gender" type="radio"
                      value="M" v-model="search.gender">
                    <label class="form__radio-label" for="male">Male</label>
                  </div>
              </div>
              </div>
              <div class="form__group">
                <label class="form__label" for="dateOfBirth">Date of Birth</label>
                <input class="form__control" id="dateOfBirth" name="dateOfBirth" type="date"
                  v-model="search.dateOfBirth">
              </div>
              <div class="form__group">
                <label class="form__label" for="dateOfEntry">Date of Entry</label>
                <input class="form__control" id="dateOfEntry" name="dateOfEntry" type="date"
                  v-model="search.dateOfEntry">
              </div>
            </form>
          </div>
          <div class="responsive-table">
            <table class="table table--hover table--striped">
              <thead class="table__header table__header--dark">
                <tr>
                  <th>Id</th>
                  <th>Last Name</th>
                  <th>First Name</th>
                  <th>Gender</th>
                  <th>Date of Birth</th>
                  <th>Date of Death</th>
                  <th>Place of Citizenship</th>
                  <th>Function</th>
                  <th>Date of Entry</th>
                  <th>Date of Leaving</th>
                  <th>Updated</th>
                </tr>
              </thead>
              <tbody class="table__body">
                <Councillor v-for="(item, index) in filteredList" :key="index" :councillor="item" />
              </tbody>
              <tfoot class="table__footer">
                <tr>
                  <td colspan="11">{{ isLoading ? 'Loading...' : 'No records found.' }}</td>
                </tr>
              </tfoot>
            </table>
          </div>
          <Pagination v-show="totalPages" :total="total" :total-pages="totalPages"
            :current-page="currentPage" @pagechanged="onPageChange" />
        </section>
      </div>
    </main>
  </div>
</template>

<script>
import axios from 'axios';
import Councillor from './components/Councillor.vue';
import Pagination from './components/Pagination.vue';

const apiUrl = 'http://ws-old.parlament.ch/councillors/';

export default {
  name: 'app',
  components: {
    Councillor,
    Pagination,
  },
  data() {
    return {
      isLoading: false,
      total: 0,
      totalPages: 1,
      perPage: 10,
      currentPage: 1,
      apiSettings: {
        format: 'json',
        faction: 'V',
        legislativePeriod: 50,
      },
      search: {
        name: '',
        gender: '',
        dateOfBirth: '',
        dateOfEntry: '',
      },
      list: [],
    };
  },
  mounted() {
    const vm = this;

    vm.get();
  },
  computed: {
    filteredList() {
      const vm = this;

      let result = vm.list.filter((item) => {
        const name = vm.search.name.toLowerCase();
        const firstName = item.firstName.toLowerCase();
        const lastName = item.lastName.toLowerCase();
        const fullName = `${firstName} ${lastName}`;
        const fullNameReverse = `${lastName} ${firstName}`;

        return (firstName.includes(name)
          || lastName.includes(name)
          || fullName.includes(name)
          || fullNameReverse.includes(name));
      });

      result = result.filter(item => item.gender.toLowerCase()
        .includes(vm.search.gender.toLowerCase()));

      result = result.filter(item => item.birthDate
        .includes(vm.search.dateOfBirth));

      result = result.filter(item => item.membership.entryDate
        .includes(vm.search.dateOfEntry));

      vm.total = result.length;
      vm.totalPages = vm.total / vm.perPage;

      return result.slice((vm.currentPage - 1) * vm.perPage, vm.currentPage * vm.perPage);
    },
  },
  watch: {
    'search.name': function name() {
      const vm = this;

      vm.currentPage = 1;
    },
    'search.gender': function gender() {
      const vm = this;

      vm.currentPage = 1;
    },
    'search.dateOfBirth': function dateOfBirth() {
      const vm = this;

      vm.currentPage = 1;
    },
    'search.dateOfEntry': function dateOfEntry() {
      const vm = this;

      vm.currentPage = 1;
    },
  },
  methods: {
    onPageChange(page) {
      const vm = this;

      vm.currentPage = page;
    },
    get() {
      const vm = this;

      vm.isLoading = true;

      axios
        .get(`https://cors-anywhere.herokuapp.com/${apiUrl}historic?format=${vm.apiSettings.format}&factionFilter=${vm.apiSettings.faction}&legislativePeriodFromFilter=${vm.apiSettings.legislativePeriod}`)
        .then((response) => {
          if (response.status === 200) {
            const responseData = response.data;

            if (responseData.length > 0) vm.list = responseData;
          }
        })
        .then(() => {
          vm.isLoading = false;
        });
    },
  },
};
</script>

<style lang="scss">
*,
*::before,
*::after {
  box-sizing: border-box;
}

.body {
  margin: 0;
  font-family: 'Open Sans', sans-serif;
}

.text-uppercase {
  text-transform: uppercase;
}

.container {
  padding: 0 1rem;
}

.header {
  display: flex;
  align-items: center;
  height: 75px;
  background-color: #f2f2f2;

  &__logo-link {
    display: flex;
    align-items: center;
    color: #000;
    font-size: 1.25rem;
    font-weight: 700;
    text-decoration: none;
  }

  &__logo {
    margin-right: 1rem;
  }
}

.section {
  &--councillor {
    .heading {
      margin: 0;

      &--h2 {
        display: flex;
        align-items: flex-end;
        height: 50px;
        padding-bottom: .25rem;
        border-bottom: 1px solid #ccc;
      }

      &--h3 {
        margin-bottom: .5rem;
      }
    }
  }

  &__description {
    margin: 0 0 1rem;
  }
}

.filter {
  padding: 1rem .5rem;

  @media (min-width: 768px) {
    padding: 2rem 1rem;
  }
}

.form {
  display: flex;
  flex-wrap: wrap;

  $this: &;

  &__group {
    flex-basis: calc(100% - 2 * .5rem);
    margin: .5rem;

    @media (min-width: 768px) {
      flex-basis: calc(50% - 2 * 1rem);
      margin: 1rem;
    }

    @media (min-width: 992px) {
      flex-basis: calc(25% - 2 * 1rem);
    }
  }

  &__label {
    display: inline-block;
    margin-bottom: .25rem;
    font-weight: 700;
  }

  &__control {
    width: 100%;
    height: calc(1.875rem + 2px);
    padding: .375rem .75rem;
    border: 1px solid #ccc;
    border-radius: 2px;
    background-color: #fff;
    font-family: 'Open Sans', sans-serif;
    font-size: 1rem;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;

    &:focus {
      outline: 1px solid #00c8ff;
      box-shadow: 0 0 5px #00c8ff;
    }
  }

  &__radio-group {
    display: flex;
    align-items: center;
    height: calc(1.875rem + 2px);
  }

  &__radio {
    display: flex;
    align-items: baseline;
    margin-right: 1rem;

    &:last-child {
      margin-right: 0;
    }
  }

  &__radio-control {
    margin: 0 .375rem 0 0;
  }
}

.responsive-table {
  display: flex;
  overflow-x: auto;
  width: 100%;
}

.table {
  width: 100%;
  border-collapse: collapse;
  font-size: .75rem;

  $this: &;

  th,
  td {
    padding: .3rem;
    white-space: nowrap;
  }

  &--hover {
    #{$this}__body tr:hover {
      background-color: rgba(255, 155, 155, .5) !important;
    }

    #{$this}__body tr:focus {
      outline: 0;
      background-color: rgba(0, 200, 255, .5) !important;
    }
  }

  &--striped {
    #{$this}__body tr:nth-of-type(odd) {
      background-color: rgba(0, 0, 0, .05);
    }
  }

  &__header {
    th {
      border-bottom: 2px solid;
      border-color: #ccc;
      text-align: left;
    }

    &--dark th {
      border-color: #fff;
      background-color: #000;
      color: #fff;
    }
  }

  &__body {
    td {
      border-top: 1px solid #ccc;
    }

    &:empty + #{$this}__footer {
      display: table-footer-group;
    }

    & + #{$this}__footer {
      display: none;
    }
  }

  &__footer {
    font-style: italic;
  }
}
</style>
