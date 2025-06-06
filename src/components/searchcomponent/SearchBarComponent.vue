<template>
  <div class="search-bar">
    <input
      type="text"
      v-model="searchQuery"
      @input="handleSearch"
      placeholder="Buscar un instrumento"
      class="search-input"
    />
    <ul v-if="filteredInstruments.length > 0" class="list-result">
      <li v-for="instrument in filteredInstruments" :key="instrument.id" @click="selectInstrument(instrument)">
        {{ instrument.name }} - Último: ${{ formatCurrency(instrument.last) }}
      </li>
    </ul>
  </div>
</template>

<script>
import { useInstrumentStore } from '../../stores/useInstrumentStore';
import { computed, ref, onMounted } from 'vue';

export default {
  name: 'SearchBarComponent',
  setup() {
    const instrumentStore = useInstrumentStore();
    const searchQuery = ref('');

    // Cargo los instrumentos cuando el componente sea montado
    onMounted(() => {
      instrumentStore.fetchInstruments();
    });

    // filtro instrumentos según el query de búsqueda
    const filteredInstruments = computed(() => {
      if (!searchQuery.value) {
        return [];
      }
      return instrumentStore.instruments.filter((instrument) =>
        instrument.name.toLowerCase().includes(searchQuery.value.toLowerCase())
      );
    });

    const selectInstrument = (instrument) => {
      instrumentStore.setSelectedInstrument(instrument);
      searchQuery.value = ''; // Limpia la búsqueda después de seleccionar un instrumento
    };

    const formatCurrency = (value) => {
      if (value === null || value === undefined) {
        return '-';
      }
      return `$${Number(value).toFixed(2)}`;
    };

    const handleSearch = () => {
      console.log('Realizando búsqueda:', searchQuery.value);
    };

    return {
      searchQuery,
      filteredInstruments,
      selectInstrument,
      formatCurrency,
      handleSearch,
    };
  },
};
</script>

<style scoped>
.search-bar {
  padding: 5px;
  background-color: #333;
  border-radius: 4px;
  display: flex;
  align-items: center;
  flex-direction: column;
  position: relative;
}

.search-input {
  width: 100%;
  padding: 8px;
  font-size: 16px;
  border: none;
  border-radius: 4px;
  outline: none;
  color: #fff;
  background-color: #555;
}

.search-input::placeholder {
  color: #aaa;
}

.list-result {
  display: inline-block;
  position: absolute;
  top: 54px;
  width: 100%;
  text-align: center;
  background: var(--vt-c-black-mute);
  padding: 15px 0;
  border-radius: 8px;
}

.list-result li {
  list-style-type: none;
  cursor: pointer;
  color: white;
}

.list-result li:hover {
  background-color: #444;
}
</style>
