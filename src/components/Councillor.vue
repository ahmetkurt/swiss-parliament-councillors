<template>
  <tr tabindex="0">
    <td>{{ councillor.id }}</td>
    <td>{{ councillor.lastName }}</td>
    <td>{{ councillor.firstName }}</td>
    <td class="text-uppercase">{{ councillor.gender }}</td>
    <td>{{ councillor.birthDate | dateLocaleString }}</td>
    <td>{{ councillor.dateOfDeath | dateLocaleString }}</td>
    <td>{{ councillor.placeOfCitizenship }}</td>
    <td class="text-uppercase">{{ councillor.function }}</td>
    <td>{{ councillor.membership.entryDate | dateLocaleString }}</td>
    <td>{{ councillor.membership.leavingDate | dateLocaleString }}</td>
    <td>{{ councillor.updated | dateLocaleString }}</td>
  </tr>
</template>

<script>
export default {
  name: 'Councillor',
  props: {
    councillor: Object,
  },
  filters: {
    dateLocaleString(date) {
      return date ? new Date(date).toLocaleString(document.documentElement.lang, { year: 'numeric', month: '2-digit', day: '2-digit' }) : '-';
    },
  },
};
</script>
