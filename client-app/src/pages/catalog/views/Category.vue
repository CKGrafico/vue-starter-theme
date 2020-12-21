<template>
  <div id="category">
    <CartSidebar :visible="isCartSideBarOpened"
                 :cart="cart"
                 @onClose="toggleCartSidebar"></CartSidebar>
    <!-- Breadcrumbs -->
    <SfBreadcrumbs
      class="breadcrumbs desktop-only"
      :breadcrumbs="breadcrumbs"></SfBreadcrumbs>
    <!-- Horizontal navigation bar-->
    <div class="navbar section">
      <div class="navbar__aside desktop-only">
        <SfHeading
          :level="3"
          title="Categories"
          class="navbar__title"></SfHeading>
      </div>
      <div class="navbar__main">
        <SfButton
          class="sf-button--text navbar__filters-button"
          data-cy="category-btn_filters"
          aria-label="Filters"
          @click="toggleFilterSidebar">
          <SfIcon
            size="24px"
            color="dark-secondary"
            icon="filter2"
            class="navbar__filters-icon"
            data-cy="category-icon_"></SfIcon>
          Filters
        </SfButton>

        <div class="navbar__sort desktop-only">
          <span class="navbar__label">Sort by:</span>
          <SfComponentSelect v-model="sortBy" class="navbar__select">
            <SfComponentSelectOption
              v-for="option in sortByOptions"
              :key="option.value"
              :value="option.value"
              class="sort-by__option">
              {{ option.label }}
            </SfComponentSelectOption>
          </SfComponentSelect>
        </div>
        <div class="navbar__counter">
          <span class="navbar__label desktop-only">Products found: </span>
          <span class="desktop-only">{{ totalItems }}</span>
          <span class="navbar__label smartphone-only">{{ totalItems }} Items</span>
        </div>
        <div class="navbar__view">
          <span class="navbar__view-label desktop-only">View</span>
          <SfIcon
            data-cy="category-icon_grid-view"
            class="navbar__view-icon"
            color="black"
            icon="tiles"
            size="12px"
            role="button"
            aria-label="Change to grid view"
            aria-pressed="true"
            @click="changeGridViewStyle(true)"></SfIcon>
          <SfIcon
            data-cy="category-icon_list-view"
            class="navbar__view-icon"
            color="black"
            icon="list"
            size="12px"
            role="button"
            aria-label="Change to list view"
            aria-pressed="true"
            @click="changeGridViewStyle(false)"></SfIcon>
        </div>
      </div>
    </div>
    <div class="main section">
      <div class="sidebar desktop-only">
        <SfList>
          <SfListItem
            v-for="cat in categories"
            :key="cat.name">
            <SfMenuItem
              :label="cat.name"
              :link="`/c/${cat.id}`">
            </SfMenuItem>
          </SfListItem>
        </SfList>
      </div>
      <SfLoader :loading="loading" style="top:3em">
        <template #loader>
          <svg
            style="width: 50px; height: 50px; margin: 20px; display:inline-block;"
            version="1.1"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            x="0px"
            y="0px"
            viewBox="0 0 100 100"
            enable-background="new 0 0 100 100"
            xml:space="preserve">
            <path fill="var(--_c-green-primary)" d="M31.6,3.5C5.9,13.6-6.6,42.7,3.5,68.4c10.1,25.7,39.2,38.3,64.9,28.1l-3.1-7.9c-21.3,8.4-45.4-2-53.8-23.3 c-8.4-21.3,2-45.4,23.3-53.8L31.6,3.5z">
              <animateTransform
                attributeName="transform"
                attributeType="XML"
                type="rotate"
                dur="2s"
                from="0 50 50"
                to="360 50 50"
                repeatCount="indefinite"></animateTransform>
            </path>
            <path fill="var(--_c-yellow-primary)" d="M42.3,39.6c5.7-4.3,13.9-3.1,18.1,2.7c4.3,5.7,3.1,13.9-2.7,18.1l4.1,5.5c8.8-6.5,10.6-19,4.1-27.7 c-6.5-8.8-19-10.6-27.7-4.1L42.3,39.6z">
              <animateTransform
                attributeName="transform"
                attributeType="XML"
                type="rotate"
                dur="1s"
                from="0 50 50"
                to="-360 50 50"
                repeatCount="indefinite"></animateTransform>
            </path>
            <path fill="var(--_c-blue-primary)" d="M82,35.7C74.1,18,53.4,10.1,35.7,18S10.1,46.6,18,64.3l7.6-3.4c-6-13.5,0-29.3,13.5-35.3s29.3,0,35.3,13.5 L82,35.7z">
              <animateTransform
                attributeName="transform"
                attributeType="XML"
                type="rotate"
                dur="2s"
                from="0 50 50"
                to="360 50 50"
                repeatCount="indefinite"></animateTransform>
            </path>
          </svg>
        </template>
        <div class="products">
          <transition-group
            v-if="isGridViewStyle"
            appear
            name="products__slide"
            tag="div"
            class="products__grid">
            <SfProductCard
              v-for="(product, i) in products"
              :key="product.slug + '__grid'"
              :style="{ '--index': i }"
              :title="product.name"
              :image="product.imgSrc"
              :regular-price="product.price | list_price"
              :special-price="product.price | special_price"
              :max-rating="5"
              :score-rating="5"
              :is-on-wishlist="false"
              :is-added-to-cart="false"
              :show-add-to-cart-button="true"
              class="products__product-card"
              @click:wishlist="addToWishlist(product)"
              @click:add-to-cart="addToCartInternal(product.id, 1)"></SfProductCard>
          </transition-group>
          <transition-group
            v-else
            appear
            name="products__slide"
            tag="div"
            class="products__list">
            <SfProductCardHorizontal
              v-for="(product, i) in products"
              :key="product.slug + '__list'"
              :style="{ '--index': i }"
              :title="product.name"
              :description="'Test description'"
              :image="product.imgSrc"
              :regular-price="product.price | list_price"
              :special-price="product.price | special_price"
              :max-rating="5"
              :reviews-count="0"
              :score-rating="5"
              :is-on-wishlist="false"
              :is-added-to-cart="false"
              class="products__product-card-horizontal"
              @click:wishlist="addToWishlist(product)"
              @click:add-to-cart="addToCartInternal(product.id, 1)">
              <template #actions>
                <SfButton
                  class="sf-button--text desktop-only"
                  style="margin: 0 0 1rem auto; display: block;"
                  @click="$emit('click:add-to-wishlist')">
                  Save for later
                </SfButton>
                <SfButton
                  class="sf-button--text desktop-only"
                  style="margin: 0 0 0 auto; display: block;"
                  @click="$emit('click:add-to-compare')">
                  Add to compare
                </SfButton>
              </template>
            </SfProductCardHorizontal>
          </transition-group>

          <!-- Pagination -->
          <SfPagination
            v-if="!loading"
            v-show="totalPages > 1"
            data-cy="category-pagination"
            class="products__pagination desktop-only"
            :current="getCurrentPage"
            :total="totalPages"
            :visible="5"
            :has-router="false"
            @click="changeCurrentPage($event)"></SfPagination>

          <div
            v-show="totalPages > 0"
            class="products__show-on-page">
            <span class="products__show-on-page__label">Show on page:</span>
            <SfSelect
              :value="getItemsPerPage"
              class="products__items-per-page"
              @input="changeItemsPerPage($event)">
              <SfSelectOption
                v-for="option in gridViewOptions.pageOptions"
                :key="option"
                :value="option"
                class="products__items-per-page__option">
                {{ option }}
              </SfSelectOption>
            </SfSelect>
          </div>
        </div>
      </SfLoader>
    </div>
  </div>
</template>

<script>
import Vue from 'vue';
import { useRoute } from "vue-router";
import {
  // SfSidebar,
  SfButton,
  SfList,
  SfIcon,
  SfComponentSelect,
  SfComponentSelectOption,
  SfHeading,
  SfMenuItem,
  // SfFilter,
  SfProductCard,
  SfProductCardHorizontal,
  SfPagination,
  //SfAccordion,
  //SfAccordionItem,
  SfSelect,
  SfBreadcrumbs,
  SfLoader
  // SfColor,
  // SfProperty
} from '@storefront-ui/vue';
import { ref, watch, computed, onMounted, watchEffect} from '@vue/composition-api';
import { useCart } from '@libs/cart';
import CartSidebar from '@libs/cart/components/CartSidebar.vue'
import { useProducts, useCategories } from '@libs/catalog';
import { ProductType } from '@core/api/graphql/types';

export default {
  components: {
    CartSidebar,
    SfButton,
    // SfSidebar,
    SfIcon,
    SfComponentSelect,
    SfList,
    // SfFilter,
    SfProductCard,
    SfProductCardHorizontal,
    SfPagination,
    SfMenuItem,
    //SfAccordion,
    SfSelect,
    SfBreadcrumbs,
    SfLoader,
    // SfColor,
    SfHeading

    // SfProperty
  },
  props: {
    catId: {
      type: String,
      "default": ''
    }
  },
  transition: 'fade',

  setup(props, context) {

    const th = {};
    const uiState = {};
    const { isOnCart } = {} ;
    const { addToWishlist } = {};
    const { result, search } = {};

    const sortBy = 'default';


    const { fetchProducts, products, total, loading } = useProducts();
    const { fetchCategories, categories } = useCategories();
    const { cart, loadMyCart, addToCart } = useCart();

    // Refs
    const isGridView = ref(false);
    const itemsPerPage = ref('20');
    const currentPage = ref(1);
    const isCartSideBarOpened = ref(false);

    // Computed
    const categoryTree = computed(() => []);
    const breadcrumbs = computed(() => []);
    const sortByOptions = computed(() => [{
      value: 'default',
      label: 'Default sorting'
    }, {
      value: 'price-asc',
      label: 'Price sorting (ASC)'
    }]);
    const facets = computed(() => ['color', 'size']);


    const gridViewOptions = {
      pageOptions: ['10', '20', '50']
    };

    const isGridViewStyle = computed(() => isGridView.value);

    const totalItems = computed(() =>  total.value);

    const getCurrentPage = computed(() => currentPage.value);
    const getItemsPerPage = computed(() => itemsPerPage.value);
    const totalPages = computed(() => Math.ceil(total.value / itemsPerPage.value));
    const activeCategory = computed(() => {
      if (!categories.value) {
        return '';
      }
      return categories.value[0]?.name;
    });

    watchEffect(() => {
      //console.log(loading.value);
    })

    onMounted(async () => {
      await fetchCategories(10, 1);
      await fetchProducts(Number(itemsPerPage.value), currentPage.value);
      await loadMyCart();
    })

    //const route = useRoute();

    watch(() => props.catId,
      async catId => {
        await fetchProducts(Number(itemsPerPage.value), currentPage.value, catId);
      }
    )
    // const activeCategory = computed(() => {
    //   const items = categoryTree.value.items;

    //   if (!items) {
    //     return '';
    //   }

    //   const category = items.find(({ isCurrent, items }) => isCurrent || items.find(({ isCurrent }) => isCurrent));

    //   return category?.label || items[0].label;
    // });

    //await search();

    // const { changeFilters, isFacetColor } = useUiHelpers();

    const toggleFilterSidebar = () => { console.log("toggleFilterSidebar"); };
    const toggleCartSidebar = () => { isCartSideBarOpened.value = !isCartSideBarOpened.value };

    const changeGridViewStyle = (isGrid) => isGridView.value = isGrid;

    const addToCartInternal = async (productId, qty) => {
      await addToCart(productId, qty);
      toggleCartSidebar();
    };

    // Change items per page event
    const changeItemsPerPage = async (newItemsPerPage) => {
      itemsPerPage.value = newItemsPerPage;
      currentPage.value = 1;
      await fetchProducts(Number(itemsPerPage.value), currentPage.value);
    };

    // Change current page event
    const changeCurrentPage = async (newCurrentPage) => {
      currentPage.value = Number(newCurrentPage);
      await fetchProducts(Number(itemsPerPage.value), currentPage.value);
    };

    const changeSorting = () => { console.log("changeSorting"); };

    // const selectedFilters = ref({});

    //const isFilterSelected = (facet, option) => (selectedFilters.value[facet.id] || []).includes(option.id);

    // const selectFilter = (facet, option) => {
    //   if (!selectedFilters.value[facet.id]) {
    //     Vue.set(selectedFilters.value, facet.id, []);
    //   }

    //   if (selectedFilters.value[facet.id].find(f => f === option.id)) {
    //     selectedFilters.value[facet.id] = selectedFilters.value[facet.id].filter(f => f !== option.id);
    //     return;
    //   }

    //   selectedFilters.value[facet.id].push(option.id);
    // };

    // const clearFilters = () => {
    //   toggleFilterSidebar();
    //   selectedFilters.value = {};
    //   changeFilters(selectedFilters.value);
    // };

    // const applyFilters = () => {
    //   toggleFilterSidebar();
    //   changeFilters(selectedFilters.value);
    // };

    return {
      ...uiState,
      th,
      products,
      cart,
      addToCartInternal,
      toggleCartSidebar,
      isCartSideBarOpened,
      categories,
      activeCategory,
      loading,
      gridViewOptions,
      isGridViewStyle,
      totalItems,
      getCurrentPage,
      getItemsPerPage,
      totalPages,
      sortBy,
      sortByOptions,
      facets,
      breadcrumbs,
      toggleFilterSidebar,
      changeGridViewStyle,
      changeItemsPerPage,
      changeCurrentPage,
      changeSorting
    };
  }
};
</script>
<style lang="scss" scoped>
@import "~@storefront-ui/vue/styles";
#category {
  box-sizing: border-box;
  @include for-desktop {
    max-width: 1240px;
    margin: 0 auto;
  }
}
.main {
  &.section {
    padding: var(--spacer-xs);
    @include for-desktop {
      padding: 0;
    }
  }
}
.breadcrumbs {
  padding: var(--spacer-base) var(--spacer-base) var(--spacer-base)
    var(--spacer-sm);
}
.navbar {
  position: relative;
  display: flex;
  border: 1px solid var(--c-light);
  border-width: 0 0 1px 0;
  @include for-desktop {
    border-width: 1px 0 1px 0;
  }
  &.section {
    padding: var(--spacer-sm);
    @include for-desktop {
      padding: 0;
    }
  }
  &__aside,
  &__main {
    display: flex;
    align-items: center;
    padding: var(--spacer-sm) 0;
  }
  &__aside {
    flex: 0 0 15%;
    padding: var(--spacer-sm) var(--spacer-sm);
    border: 1px solid var(--c-light);
    border-width: 0 1px 0 0;
  }
  &__main {
    flex: 1;
    padding: 0;
    @include for-desktop {
      padding: var(--spacer-xs) var(--spacer-xl);
    }
  }
  &__title {
    --heading-title-font-weight: var(--font-weight--semibold);
    --heading-title-font-size: var(--font-size--xl);
  }
  &__filters-icon {
    margin: 0 var(--spacer-xs) 0 0;
  }
  &__filters-button {
    display: flex;
    align-items: center;
    --button-font-size: var(--font-size--base);
    --button-text-decoration: none;
    --button-color: var(--c-link);
    --button-font-weight: var(--font-weight--normal);
    @include for-mobile {
      order: 1;
    }
    svg {
      fill: var(--c-text-muted);
      transition: fill 150ms ease;
    }
    &:hover {
      svg {
        fill: var(--c-primary);
      }
    }
  }
  &__label {
    font-family: var(--font-family--secondary);
    font-weight: var(--font-weight--normal);
    color: var(--c-link);
    margin: 0 var(--spacer-2xs) 0 0;
  }
  &__select {
    --component-select-width: 220px;
    --component-select-padding: 0;
    --component-select-selected-padding: 0 var(--spacer-lg) 0 var(--spacer-2xs);
    --component-select-margin: 0;
    --component-select-error-message-height: 0;
  }
  &__sort {
    display: flex;
    align-items: center;
    margin: 0 auto 0 var(--spacer-2xl);
  }
  &__counter {
    font-family: var(--font-family--secondary);
    margin: auto;
    @include for-desktop {
      margin: auto 0 auto auto;
    }
  }
  &__view {
    display: flex;
    align-items: center;
    margin: 0 var(--spacer-xl);
    @include for-desktop {
      margin: 0 0 0 var(--spacer-2xl);
    }
    &-icon {
      cursor: pointer;
      margin: 0 var(--spacer-base) 0 0;
      &:last-child {
        margin: 0;
      }
    }
    &-label {
      margin: 0 var(--spacer-sm) 0 0;
      font: var(--font-weight--normal) var(--font-size--base) / 1.6
        var(--font-family--secondary);
      text-decoration: none;
      color: var(--c-link);
    }
  }
}
.sort-by {
  --component-select-dropdown-z-index: 1;
  flex: unset;
  width: 11.875rem;
}
.main {
  display: flex;
}
.sidebar {
  flex: 0 0 15%;
  padding: var(--spacer-sm);
  border: 1px solid var(--c-light);
  border-width: 0 1px 0 0;
}
.sidebar-filters {
  --sidebar-title-display: none;
  --sidebar-top-padding: 0;
  @include for-desktop {
    --sidebar-content-padding: 0 var(--spacer-xl);
    --sidebar-bottom-padding: 0 var(--spacer-xl);
  }
}
.list {
  --menu-item-font-size: var(--font-size--sm);
  &__item {
    &:not(:last-of-type) {
      --list-item-margin: 0 0 var(--spacer-sm) 0;
    }
  }
}
.products {
  box-sizing: border-box;
  flex: 1;
  margin: 0;
  max-width: 100%;
  &__grid,
  &__list {
    display: flex;
    flex-wrap: wrap;
  }
  &__grid {
    justify-content: space-between;
  }
  &__product-card {
    --product-card-max-width: 50%;
    --product-card-title-margin: var(--spacer-base) 0 0 0;
    flex: 1 1 50%;
    @include for-desktop {
      --product-card-max-width: 25%;
    }
  }
  &__product-card-horizontal {
    flex: 0 0 100%;
  }
  &__slide-enter {
    opacity: 0;
    transform: scale(0.5);
  }
  &__slide-enter-active {
    transition: all 0.2s ease;
    transition-delay: calc(0.1s * var(--index));
  }
  @include for-desktop {
    margin: var(--spacer-sm) 0 0 var(--spacer-sm);
    &__pagination {
      display: flex;
      justify-content: flex-start;
      margin: var(--spacer-xl) 0 0 0;
    }
    &__product-card-horizontal {
      margin: var(--spacer-lg) 0;
    }
    &__product-card {
      flex: 1 1 25%;
    }
    &__list {
      margin: 0 0 0 var(--spacer-sm);
    }
  }
  &__show-on-page {
    display: flex;
    justify-content: flex-end;
    align-items: baseline;
    &__label {
      font-family: var(--font-family--secondary);
      font-size: var(--font-size--sm);
    }
  }
}
.filters {
  &__title {
    --heading-title-font-size: var(--font-size--xl);
    margin: var(--spacer-xl) 0 var(--spacer-base) 0;
    &:first-child {
      margin: calc(var(--spacer-xl) + var(--spacer-base)) 0 var(--spacer-xs) 0;
    }
  }
  &__colors {
    display: flex;
  }
  &__color {
    margin: var(--spacer-xs) var(--spacer-xs) var(--spacer-xs) 0;
  }
  &__chosen {
    color: var(--c-text-muted);
    font-weight: var(--font-weight--normal);
    font-family: var(--font-family--secondary);
    position: absolute;
    right: var(--spacer-xl);
  }
  &__item {
    --radio-container-padding: 0 var(--spacer-sm) 0 var(--spacer-xl);
    --radio-background: transparent;
    --filter-label-color: var(--c-secondary-variant);
    --filter-count-color: var(--c-secondary-variant);
    --checkbox-padding: 0 var(--spacer-sm) 0 var(--spacer-xl);
    padding: var(--spacer-sm) 0;
    border-bottom: 1px solid var(--c-light);
    &:last-child {
      border-bottom: 0;
    }
    @include for-desktop {
      --checkbox-padding: 0;
      margin: var(--spacer-sm) 0;
      border: 0;
      padding: 0;
    }
  }
  &__accordion-item {
    --accordion-item-content-padding: 0;
    position: relative;
    left: 50%;
    right: 50%;
    margin-left: -50vw;
    margin-right: -50vw;
    width: 100vw;
  }
  &__buttons {
    margin: var(--spacer-sm) 0;
  }
  &__button-clear {
    --button-background: var(--c-light);
    --button-color: var(--c-dark-variant);
    margin: var(--spacer-xs) 0 0 0;
  }
}

//Customization
.sf-heading__title{
  font-family: var(--font-family--primary);
  font-size: 8em;
}
</style>