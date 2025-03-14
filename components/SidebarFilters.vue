<template>
  <aside class="filters">
    <div class="filters__close">
      <icon
        name="ic:baseline-cancel"
        size="35px"
        @click="toggleFilterSmallWidth"
      />
    </div>
    <div class="filters__separator filters__separator--ocult"></div>
    <ul class="filters__categories">
      <li
        class="filters__categories-item"
        v-for="item in categories"
        :key="item.name"
        @click="
          toggleCategory(item.name);
          toggleFilterSmallWidth();
        "
      >
        {{ item.name }}
      </li>
    </ul>
    <div class="filters__separator"></div>
    <div class="filters__group" v-for="group in filterGroup" :key="group.title">
      <span @click="toggleList" class="filters__group-title">
        {{ group.title }}
        <template v-if="getSelectedFilterCount(group.title)">
          ({{ getSelectedFilterCount(group.title) }})
        </template>
        <icon name="ic:baseline-keyboard-arrow-down" />
      </span>

      <ul
        ref="groupLists"
        class="filters__group-list"
        :class="{
          'filters__group-list--toggled': toggledGroups[group.title],
          'filters__group-list--color': group.title === 'Color'
        }"
      >
        <li
          v-for="item in group.group"
          :key="item.name || item"
          class="filters__categories-item"
          :class="{
            'filters__categories-item--color': group.title === 'Color'
          }"
          @click="handleItemClick(group.title, item, $event)"
        >
          <template v-if="group.title === 'Color'">
            <span
              class="filters__color-circle"
              :style="{ backgroundColor: item.colorCode }"
            ></span>
            <h4 class="filters__color-name">{{ item.name }}</h4>
          </template>
          <template v-else>
            <div class="filters__label">
              <div class="filters__pseudo-checkbox"></div>
              {{ item }}
            </div>
          </template>
        </li>
      </ul>
      <div class="filters__separator"></div>
    </div>
  </aside>
</template>

<script setup>
import { ref } from 'vue';
import { useFiltersStore } from '@/stores/filtersStore';
import { categories, filterGroup, filterMappings } from '../data/filtersData';

const filtersStore = useFiltersStore();
const toggledGroups = ref({});
// eslint-disable-next-line no-undef
const { toggleFilterSmallWidth } = useFilters();

const getSelectedFilterCount = (groupTitle) => {
  const filterKey = filterMappings[groupTitle];
  return filterKey ? filtersStore.selectedFilters[filterKey].length : 0;
};

const handleItemClick = (groupTitle, item, event) => {
  switch (groupTitle) {
    case 'Shop by Price':
      togglePriceRange(item);
      break;
    case 'Color':
      toggleColor(item.name);
      break;
    case 'Gender':
      toggleGender(item);
      break;
    case 'Kids':
      toggleGenderKid(item);
      break;
    case 'Kids Age':
      toggleKidsAge(item);
      break;
    case 'Sale & Offers':
      toggleSale(item);
      break;
    case 'Brand':
      toggleBrand(item);
      break;
    case 'Sports & Activities':
      toggleActiveType(item);
      break;
    case 'Best for':
      toggleClimate(item);
      break;
    default:
      toggleCategory(item);
      break;
  }

  const pseudoCheckbox = event.currentTarget.querySelector(
    '.filters__pseudo-checkbox'
  );
  if (pseudoCheckbox) {
    pseudoCheckbox.classList.toggle('filters__pseudo-checkbox--check');
  }
};

const toggleList = (e) => {
  const groupTitle = e.target.textContent.trim();
  toggledGroups.value[groupTitle] = !toggledGroups.value[groupTitle];

  const listElement = e.target.nextElementSibling;
  listElement.style.height = toggledGroups.value[groupTitle]
    ? `${listElement.scrollHeight}px`
    : '0';
};

// Toggle functions
const togglePriceRange = (range) => {
  filtersStore.setPriceRangeFilter(range);
};
const toggleGender = (gender) => {
  filtersStore.setGenderFilter(gender);
};
const toggleGenderKid = (kidGender) => {
  filtersStore.setKidGenderFilter(kidGender);
};
const toggleActiveType = (type) => {
  filtersStore.setActivityTypeFilter(type);
};
const toggleClimate = (climate) => {
  filtersStore.setClimateFilter(climate);
};
const toggleKidsAge = (kidAge) => {
  filtersStore.setKidsAgeFilter(kidAge);
};
const toggleBrand = (brand) => {
  filtersStore.setBrandFilter(brand);
};
const toggleCategory = (category) => {
  filtersStore.setCategoryFilter(category);
};
const toggleSale = (sale) => {
  filtersStore.setSaleFilter(sale);
};
const toggleColor = (color) => {
  filtersStore.setColorFilter(color);
};
</script>

<style lang="scss">
.filters {
  position: sticky;
  top: calc(3rem + 2rem + 4.5rem);
  overflow-y: scroll;
  width: 360px;
  height: 80vh;
  margin-left: 2rem;
  padding: 1rem;
  background-color: $color-white;

  @media (max-width: 1780px) {
    margin-left: 0.5rem;
  }

  @media (max-width: 960px) {
    z-index: 9999;
    margin: 0;
    position: fixed;
    top: 0;
    left: 0;
    height: 100vh;
    width: 100vw;
    padding: 1.5rem;
  }

  &__label {
    display: flex;
  }

  &__close {
    position: absolute;
    top: 1rem;
    right: 1rem;
    display: none;
    padding: 0.5rem;

    @media (max-width: 960px) {
      display: block;
    }
  }

  &__separator {
    margin: 1rem 0;
    display: block;
    width: 100%;
    height: 1px;
    background-color: $color-fog-gray;

    &--ocult {
      @media (max-width: 960px) {
        display: none;
      }
    }
  }

  &__categories {
    margin-top: 1rem;
    margin-bottom: 3rem;

    &-item {
      margin: 1rem 0;
      cursor: pointer;

      &:hover {
        color: $color-dark-gray;
      }

      &--color {
        display: flex;
        flex-direction: column;
        align-items: center;
        width: 25%;
      }
    }
  }

  &__group {
    margin: 1rem 0;

    &-title {
      display: flex;
      justify-content: space-between;
      font-weight: 300;
      cursor: pointer;
    }

    &-list {
      margin: 0;
      height: 0;
      overflow-y: hidden;
      transition: height 0.3s ease-in-out;

      &--toggled {
        margin: 0.5rem 0;
      }

      &--color {
        display: flex;
        align-items: center;
        justify-content: start;
        flex-wrap: wrap;
        gap: 1rem;

        @media (max-width: 960px) {
          justify-content: center;
        }
      }
    }

    &-item {
      margin: 0.5rem 0;

      &:hover {
        color: $color-dark-gray;
        cursor: pointer;
      }

      &--color {
        display: flex;
        align-items: center;
        flex-direction: column;
      }
    }
  }
}

.filters__color-circle {
  display: block;
  height: 30px;
  width: 30px;
  border-radius: 50%;
  border: 0.5px solid $color-fog-gray;
}

.filters__color-name {
  font-size: 0.8rem;
  font-weight: 300;
  margin: 0.5rem 0;
}

.filters__pseudo-checkbox {
  max-height: 20px;
  min-width: 20px;
  border: 1px solid $color-fog-gray;
  border-radius: 0.3rem;
  margin-right: 0.5rem;

  &--check {
    position: relative;
    display: inline-block;
    background-color: $color-black;

    &::before {
      content: '\2713';
      position: absolute;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
      font-size: 13px;
      color: $color-white;
      border: none;
    }
  }
}
</style>
