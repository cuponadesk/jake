<!--Customer: allows customers to place orders for cakes. If they select an existing Cake Config, the must select Frosting Filling and Flavor.
Either way, they must select a size and a style, as well as supply a name, phone number, and pickup time -->
<template>
  <div class="order-display">
    <!-- the cake-display shows basic info for the currently selected cake. Defaults to showing the custom
    cake option's basic info if no cake is selected-->
    <div class="cakedisplay">
      <!-- we do ternary statements here - if nothing's selected in drop-down, the cakedisplay defaults to the custom cake info -->
      <h3>
        {{
          isSomethingSelected
            ? selected.cakeItemConfigName
            : this.$store.state.availableCakeConfigsBE[0].cakeConfigName
        }}
      </h3>
      <img
        v-bind:src="[
          isSomethingSelected
            ? selected.cakeItemConfigURL
            : this.$store.state.availableCakeConfigsBE[0].cakeConfigUrl,
        ]"
      /><br />
      <p>
        {{
          isSomethingSelected
            ? selected.cakeItemConfigDescription
            : this.$store.state.availableCakeConfigsBE[0].cakeConfigDescription
        }}
      </p>
    </div>

    <form class="order-form" @submit.prevent="addCakeToCart">
      <!-- <p>Your Cake:</p> -->

      <label for="cake selection">* Select your Cake TYPE:</label><br />
      <!-- the v-model sets the select to whatever the selected object is in the page's data. If null, it's blank.-->
      <select
        name="cake selection"
        v-model="selected"
        v-on:change="selectCakeConfig"
      >
        <!-- sets text for options to all cake names in standardCakeConfigs. value is the cake's cake_id-->
        <option
          v-for="cakeConfig in $store.state.availableCakeConfigsBE"
          v-bind:key="cakeConfig.cakeConfigID"
          v-bind:value="{
            cakeItemConfigName: cakeConfig.cakeConfigName,
            cakeItemConfigID: cakeConfig.cakeConfigID,
            cakeItemFrostingID: cakeConfig.cakeConfigFrostingID,
            cakeItemFillingID: cakeConfig.cakeConfigFillingID,
            cakeItemFlavorID: cakeConfig.cakeConfigFlavorID,
            cakeItemConfigURL: cakeConfig.cakeConfigUrl,
            cakeItemConfigDescription: cakeConfig.cakeConfigDescription,
          }"
        >
          {{ cakeConfig.cakeConfigName }}
        </option></select
      ><br />
      <!-- Sets the size for the cake to order-->
      <label for="cake size">* Select your Cake SIZE:</label><br />
      <select name="cake size" v-model="standardCakeOrderJSON.cakeItemSizeID">
        <option
          v-for="size in $store.state.availableCakeSizesBE"
          v-bind:key="size.sizeID"
          v-bind:value="size.sizeID"
        >
          {{ size.sizeDescription }}
        </option></select
      ><br />
      <!-- Sets the style for the cake to order-->
      <label for="standard cake style">* Select your Cake STYLE:</label><br />
      <select
        name="standard cake style"
        v-model="standardCakeOrderJSON.cakeItemStyleID"
      >
        <option
          v-for="style in $store.state.availableCakeStylesBE"
          v-bind:key="style.styleID"
          v-bind:value="style.styleID"
        >
          {{ style.styleName }}
        </option></select
      ><br />

      <!-- Custom Cake section hidden when standard config not selected -->
      <section class="custom-cake-options" v-show="showCustomOptions">
        <!-- Sets the flavor for the cake to order-->
        <label for="custom cake flavor">* Select your Cake FLAVOR:</label><br />
        <select
          name="custom cake flavor"
          v-model="standardCakeOrderJSON.cakeItemFlavorID"
        >
          <option
            v-for="flavor in $store.state.availableFlavorsBE"
            v-bind:key="flavor.flavorID"
            v-bind:value="flavor.flavorID"
          >
            {{ flavor.flavorName }}
          </option></select
        ><br /><br />
        <!-- Sets the frosting for the cake to order-->
        <label for="custom cake frosting">Select your Cake FROSTING:</label><br />
        <select
          name="custom cake frosting"
          v-model="standardCakeOrderJSON.cakeItemFrostingID"
        >
          <option
            v-for="frosting in $store.state.availableFrostingsBE"
            v-bind:key="frosting.frostingID"
            v-bind:value="frosting.frostingID"
          >
            {{ frosting.frostingName }}
          </option></select
        ><br /><br />
        <!-- Sets the filling for the cake to order-->
        <label for="custom cake filling">Select your Cake FILLING:</label><br />
        <select
          name="custom cake filling"
          v-model="standardCakeOrderJSON.cakeItemFillingID"
        >
          <option
            v-for="filling in $store.state.availableFillingsBE"
            v-bind:key="filling.fillingID"
            v-bind:value="filling.fillingID"
          >
            {{ filling.fillingName }}
          </option></select
        ><br /><br />
      </section>
      <!-- End of Custom Cake section -->

      <!-- right now price in lable is hard coded - need to get that from store data which needs to come from back end -->
      <label for="optional message"
        >Custom Message (Optional: $1.50 extra)</label
      ><br />
      <input
        name="message"
        type="text"
        placeholder="Optional Message"
        v-model="standardCakeOrderJSON.cakeItemMessage"
      /><br />

      <div name="extras" class="select extras">
        <p>Add Extras - pick as many as you'd like!</p>
        <div
          name="extra-checkbox"
          v-for="(extra, index) in $store.state.availableExtrasBE"
          v-bind:key="extra.extraID"
        >
          <input
            type="checkbox"
            :name="extra.extraName"
            :id="index"
            :value="extra.extraID"
            v-on:change="extraCheck"
          />
          <label :for="extra.extraName">{{ extra.extraName }}</label>
        </div>
      </div>

      <p class="price-display">
        Your Order's Total is:
        <span class="calculated-price">$ {{ itemPrice }} </span>
      </p>

      <!-- Hide submit button if required fields are not completed -->
      <section v-if="cakeValidated">
        <input type="submit" value="Add Cake To Cart!" />
      </section>

      <section v-else>
        <!-- note: we need to make this so it's not too wide for mobile.-->
        <p>(*) Required Fields in Order to Add to Cart!
        </p>
      </section>
    </form>
  </div>
</template>

<script>
import CustomerService from "../services/CustomerService.js";

export default {
  data() {
    return {
      //this object is used to drive the cake-display logic
      selected: {},
      //this JSON is used to store data when making cake selections before sending off to the store.
      standardCakeOrderJSON: {
        cakeItemStyleID: 1,
        cakeItemSizeID: 1,
        cakeItemFlavorID: 1,
        cakeItemFrostingID: 1,
        cakeItemFillingID: 1,
        cakeItemMessage: "",
        cakeItemExtras: [],
        cakeItemPrice: 0.0,
        cakeItemConfigID: null,
      },
    };
  },

  name: "order-cake",
  /**
   * the big kahuna created lifecycle hook that sets all available cake components.
   */
  created() {
    CustomerService.getAvailableConfigs().then((response) => {
      this.$store.commit("SET_AVAILABLE_CAKE_CONFIG_ARRAY", response.data);
    });
    CustomerService.getAvailableSizes().then((response) => {
      this.$store.commit("SET_AVAILABLE_SIZES_ARRAY", response.data);
    });

    CustomerService.getAvailableStyles().then((response) => {
      this.$store.commit("SET_AVAILABLE_STYLES_ARRAY", response.data);
    });

    CustomerService.getAvailableFlavors().then((response) => {
      this.$store.commit("SET_AVAILABLE_FLAVORS_ARRAY", response.data);
    });

    CustomerService.getAvailableFrostings().then((response) => {
      this.$store.commit("SET_AVAILABLE_FROSTINGS_ARRAY", response.data);
    });

    CustomerService.getAvailableFillings().then((response) => {
      this.$store.commit("SET_AVAILABLE_FILLINGS_ARRAY", response.data);
    });

    CustomerService.getAvailableExtras().then((response) => {
      this.$store.commit("SET_AVAILABLE_EXTRAS_ARRAY", response.data);
    });
  },

  computed: {
    /**
     * computed function that returns the price of the chosen size. Used to compute itemPrice.
     */
    sizePrice() {
      let price = 0.0;
      if (this.standardCakeOrderJSON.cakeItemSizeID) {
        let sizeElement = this.$store.state.availableCakeSizesBE.find(
          (element) =>
            element.sizeID === this.standardCakeOrderJSON.cakeItemSizeID
        );
        price += sizeElement.priceMod;
      }
      return price;
    },

    /**
     * computed function that returns the price of the chosen style. Used to compute itemPrice.
     */
    stylePrice() {
      let price = 0.0;
      if (this.standardCakeOrderJSON.cakeItemStyleID) {
        let styleElement = this.$store.state.availableCakeStylesBE.find(
          (element) =>
            element.styleID === this.standardCakeOrderJSON.cakeItemStyleID
        );
        price += styleElement.priceMod;
      }
      return price;
    },

    /**
     * computed function that returns the price of the chosen flavor. Used to compute itemPrice.
     */
    flavorPrice() {
      let price = 0.0;
      if (this.standardCakeOrderJSON.cakeItemFlavorID) {
        let flavorElement = this.$store.state.availableFlavorsBE.find(
          (element) =>
            element.flavorID === this.standardCakeOrderJSON.cakeItemFlavorID
        );
        price += flavorElement.priceMod;
      }
      return price;
    },

    /**
     * computed function that returns the price of the chosen frosting. Used to compute itemPrice.
     */
    frostingPrice() {
      let price = 0.0;
      if (this.standardCakeOrderJSON.cakeItemFrostingID) {
        let frostingElement = this.$store.state.availableFrostingsBE.find(
          (element) =>
            element.frostingID === this.standardCakeOrderJSON.cakeItemFrostingID
        );
        price += frostingElement.priceMod;
      }
      return price;
    },

    /**
     * computed function that returns the price of the chosen filling. Used to compute itemPrice.
     */
    fillingPrice() {
      let price = 0.0;
      if (this.standardCakeOrderJSON.cakeItemFillingID) {
        let fillingElement = this.$store.state.availableFillingsBE.find(
          (element) =>
            element.fillingID === this.standardCakeOrderJSON.cakeItemFillingID
        );
        price += fillingElement.priceMod;
      }
      return price;
    },

    /**
     * computed function that returns the price for having a message. Used to compute itemPrice.
     */
    messagePrice() {
      let price = 0.0;
      if (this.standardCakeOrderJSON.cakeItemMessage) {
        price += this.$store.state.messagePrice;
      }
      return price;
    },

    /**
     * computed function that returns the sum for having the selected extras. Used to compute itemPrice.
     */
    extrasPrice() {
      let price = 0.0;
      if (this.standardCakeOrderJSON.cakeItemExtras.length > 0) {
        this.standardCakeOrderJSON.cakeItemExtras.forEach(
          (element) => (price += element.priceMod)
        );
      }
      return price;
    },

    /**
     * computed function that returns the price for the current cake. Adds together the pricing for selected
     * size, style, flavor, frosting, filling, and optional extras, as well as adding in charge for custom message
     * if customer has written a custom message.
     */
    itemPrice() {
      let price =
        this.sizePrice +
        this.stylePrice +
        this.flavorPrice +
        this.frostingPrice +
        this.fillingPrice +
        this.messagePrice +
        this.extrasPrice;

      return price.toFixed(2);
    },

    /**
     * ensure that the chosen cake can be ordered - a cake MUST have a flavor, size, and style. 1 is always
     * the id of our null rows in our database.
     */
    cakeValidated() {
      if (
        this.standardCakeOrderJSON.cakeItemSizeID != 1 &&
        this.standardCakeOrderJSON.cakeItemFlavorID != 1 &&
        this.standardCakeOrderJSON.cakeItemStyleID != 1
      ) {
        return true;
      } else {
        return false;
      }
    },

    /**
     * function that checks to see if a customer has selected a cake type. If they have, it displays the basic
     * info for that cake in the cake-display at top of Component. if they haven't, displays the basic info
     * for a custom cake in cake-display.
     */
    isSomethingSelected() {
      if (this.selected.cakeItemConfigID > 0) {
        return true;
      } else {
        return false;
      }
    },

    /**
     * function that returns a boolean on whether or not the component should show the selection options for
     * filling, flavor, and frosting. Customers can only pick these fields if they've chosen a custom cake.
     */
    showCustomOptions() {
      if (this.selected.cakeItemConfigID === 1) {
        return true;
      } else {
        return false;
      }
    },
  },
  methods: {
    /**
     * method that places the relevant extra JSON in the standardCakeOrderJSON's cakeItemExtras array if the extra's
     * box is checked. if the box is unchecked, it removes that extra's JSON from the array.
     */
    extraCheck(event) {
      if (event.target.checked) {
        this.standardCakeOrderJSON.cakeItemExtras.push(
          this.$store.state.availableExtrasBE[event.target.id]
        );
      } else {
        this.standardCakeOrderJSON.cakeItemExtras = this.standardCakeOrderJSON.cakeItemExtras.filter(
          (element) => element.extraID != event.target.value
        );
      }
    },

    /**used to preseelect items in the standardCakeOrderJSON depending on the cake type selected in
     * "Select your cake".
     */
    selectCakeConfig() {
      this.standardCakeOrderJSON.cakeItemConfigID = this.selected.cakeItemConfigID;
      this.standardCakeOrderJSON.cakeItemFlavorID = this.selected.cakeItemFlavorID;
      this.standardCakeOrderJSON.cakeItemFrostingID = this.selected.cakeItemFrostingID;
      this.standardCakeOrderJSON.cakeItemFillingID = this.selected.cakeItemFillingID;
    },

    /**
     * Takes the currently built standardCakeOrderJSON and puts its info into the store's cakeItemToOrder object.
     * Sets the cakeItemToOrder object's cakeItemTempOrderID to the store's cakeItemNumberForOrder,
     * increases cakeItemNumberForOrder by one,  sets the price for the cakeItemToOrder object, then moves it to
     * the currentActiveOrder object's itemsInOrder array and blanks the cakeItemToOrder object.
     * Drives the shopping cart functionality.
     */
    addCakeToCart() {
      this.$store.commit("MAKE_CAKE_ITEM", this.standardCakeOrderJSON);
      this.$store.commit("SET_CAKE_ITEM_PRICE", this.itemPrice);
      this.$store.commit(
        "ADD_CAKE_ITEM_TO_ACTIVE_ORDER",
        this.$store.state.cakeItemToOrder
      );
      this.$router.push("/shoppingcart");
    },
  },
};
</script>

<style>
</style>