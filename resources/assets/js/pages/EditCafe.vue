<style scoped lang="scss">
  @import '~@/abstracts/_variables.scss';

  div#new-cafe-page{
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: white;
    z-index: 99999;
    overflow: auto;

    img#back{
      float: right;
      margin-top: 20px;
      margin-right: 20px;
    }

    .centered{
      margin: auto;
    }

    h2.page-title{
      color: #342C0C;
      font-size: 36px;
      font-weight: 900;
      font-family: "Lato", sans-serif;
      margin-top: 60px;
    }

    label.form-label{
      font-family: "Lato", sans-serif;
      text-transform: uppercase;
      font-weight: bold;
      color: black;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    input[type="text"].form-input{
      border: 1px solid #BABABA;
      border-radius: 3px;

      &.invalid{
        border: 1px solid #D0021B;
      }
    }

    div.validation{
      color: #D0021B;
      font-family: "Lato", sans-serif;
      font-size: 14px;
      margin-top: -15px;
      margin-bottom: 15px;
    }

    div.location-type{
      text-align: center;
      font-family: "Lato", sans-serif;
      font-size: 16px;
      color: $secondary-color;
      border-bottom: 1px solid $secondary-color;
      border-top: 1px solid $secondary-color;
      border-left: 1px solid $secondary-color;
      border-right: 1px solid $secondary-color;
      width: 25%;
      display: inline-block;
      height: 55px;
      line-height: 55px;
      cursor: pointer;
      margin-bottom: 5px;

      &.active{
        color: white;
        background-color: $secondary-color;
      }

      &.roaster{
        border-top-left-radius: 3px;
        border-bottom-left-radius: 3px;
        border-right: 0px;
      }

      &.cafe{
        border-top-right-radius: 3px;
        border-bottom-right-radius: 3px;
      }
    }

    div.brew-method{
      font-size: 16px;
      color: #666666;
      font-family: "Lato", sans-serif;
      border-radius: 4px;
      background-color: #F9F9FA;
      width: 150px;
      height: 57px;
      float: left;
      margin-right: 10px;
      margin-bottom: 10px;
      padding: 5px;
      cursor: pointer;
      position: relative;

      &.active{
        color: white;
        background-color: $secondary-color;
      }

      div.brew-method-container{
        position: absolute;
        top: 50%;
        transform: translateY(-50%);

        img.brew-method-icon{
          display: inline-block;
          margin-right: 10px;
          margin-left: 5px;
          width: 20px;
          max-height: 30px;
        }

        span.brew-method-name{
          display: inline-block;
          width: calc( 100% - 40px);
          vertical-align: middle;
        }
      }
    }

    div.company-selection-container{
      position: relative;

      div.company-autocomplete-container{
        border-radius: 3px;
        border: 1px solid #BABABA;
        background-color: white;
        margin-top: -17px;
        width: 80%;
        position: absolute;
        z-index: 9999;

        div.company-autocomplete{
          cursor: pointer;
          padding-left: 12px;
          padding-right: 12px;
          padding-top: 8px;
          padding-bottom: 8px;

          span.company-name{
            display: block;
            color: #0D223F;
            font-size: 16px;
            font-family: "Lato", sans-serif;
            font-weight: bold;
          }

          span.company-locations{
            display: block;
            font-size: 14px;
            color: #676767;
            font-family: "Lato", sans-serif;
          }

          &:hover{
            background-color: #F2F2F2;
          }
        }

        div.new-company{
          cursor: pointer;
          padding-left: 12px;
          padding-right: 12px;
          padding-top: 8px;
          padding-bottom: 8px;
          font-family: "Lato", sans-serif;
          color: #054E7A;
          font-style: italic;

          &:hover{
            background-color: #F2F2F2;
          }
        }
      }
    }


    a.edit-location-button{
      display: block;
      text-align: center;
      height: 50px;
      color: white;
      border-radius: 3px;
      font-size: 18px;
      font-family: "Lato", sans-serif;
      background-color: #A7BE4D;
      line-height: 50px;
      margin-bottom: 10px;
    }

    a.delete-location{
      color: #D0021B;
      font-size: 14px;
      text-decoration: underline;
      display: inline-block;
      margin-bottom: 50px;
    }
  }

  /* Small only */
  @media screen and (max-width: 39.9375em) {
    div#new-cafe-page{
      div.location-type{
        width: 50%;
      }
    }
  }
</style>

<template>
  <transition name="scale-in-center">
    <div id="new-cafe-page">
      <router-link :to="{ name: 'cafes' }">
        <img src="/img/close-modal.svg" id="back"/>
      </router-link>

      <div class="grid-container">
        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <h2 class="page-title">Edit Cafe</h2>
          </div>
        </div>
        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered company-selection-container">
            <label class="form-label">Company Name</label>
            <input type="text" class="form-input" v-model="companyName" v-on:keyup="searchCompanies()" v-bind:class="{'invalid' : !validations.companyName.is_valid }"/>
            <div class="validation" v-show="!validations.companyName.is_valid">{{ validations.companyName.text }}</div>
            <input type="hidden" v-model="companyID"/>
            <div class="company-autocomplete-container" v-show="companyName.length > 0 && showAutocomplete">
              <div class="company-autocomplete" v-for="companyResult in companyResults" v-on:click="selectCompany( companyResult )">
                <span class="company-name">{{ companyResult.name }}</span>
                <span class="company-locations">{{ companyResult.cafes_count }} location<span v-if="companyResult.cafes_count > 1">s</span></span>
              </div>
              <div class="new-company" v-on:click="addNewCompany()">
                Add new company called "{{ companyName }}"
              </div>
            </div>
          </div>
        </div>
        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <label class="form-label">Website</label>
            <input type="text" class="form-input" v-model="website" v-bind="{ 'invalid' : !validations.website.is_valid }"/>
            <div class="validation" v-show="!validations.website.is_valid">{{ validations.website.text }}</div>
          </div>
        </div>
        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <label class="form-label">Location Type</label>
          </div>
        </div>
        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <div class="location-type roaster" v-bind:class="{ 'active': companyType == 'roaster' }" v-on:click="setCompanyType('roaster')">
              Roaster
            </div><div class="location-type cafe" v-bind:class="{ 'active': companyType == 'cafe' }" v-on:click="setCompanyType('cafe')">
              Cafe
            </div>
          </div>
        </div>

        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <label class="form-label">Brew Methods Available</label>
          </div>
        </div>

        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <div class="brew-method" v-on:click="toggleSelectedBrewMethod( method.id )" v-for="method in brewMethods" v-bind:class="{'active': brewMethodsSelected.indexOf( method.id ) >= 0 }">
              <div class="brew-method-container">
                <img v-bind:src="method.icon+'.svg'" class="brew-method-icon"/> <span class="brew-method-name">{{ method.method }}</span>
              </div>
            </div>
          </div>
        </div>

        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <label class="form-label">Location Name</label>
            <input type="text" class="form-input" v-model="locationName"/>
          </div>
        </div>

        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <label class="form-label">Street Address</label>
            <input type="text" v-model="addressSearch" id="street-address" placeholder="Street Address" class="form-input" v-bind:class="{'invalid' : !validations.address.is_valid }"/>
            <input type="hidden" v-model="address"/>
            <div class="validation" v-show="!validations.address.is_valid">{{ validations.address.text }}</div>
          </div>
        </div>
        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <label class="form-label">City</label>
            <input type="text" class="form-input" v-model="city" v-bind:class="{'invalid' : !validations.city.is_valid }"/>
            <div class="validation" v-show="!validations.city.is_valid">{{ validations.city.text }}</div>
          </div>
        </div>
        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <div class="grid-x grid-padding-x">
              <div class="large-6 medium-6 small-12 cell">
                <label class="form-label">State</label>
                <select v-model="state" v-bind:class="{'invalid' : !validations.state.is_valid }">
                  <option value=""></option>
                  <option value="AL">Alabama</option>
                  <option value="AK">Alaska</option>
                  <option value="AZ">Arizona</option>
                  <option value="AR">Arkansas</option>
                  <option value="CA">California</option>
                  <option value="CO">Colorado</option>
                  <option value="CT">Connecticut</option>
                  <option value="DE">Delaware</option>
                  <option value="DC">District Of Columbia</option>
                  <option value="FL">Florida</option>
                  <option value="GA">Georgia</option>
                  <option value="HI">Hawaii</option>
                  <option value="ID">Idaho</option>
                  <option value="IL">Illinois</option>
                  <option value="IN">Indiana</option>
                  <option value="IA">Iowa</option>
                  <option value="KS">Kansas</option>
                  <option value="KY">Kentucky</option>
                  <option value="LA">Louisiana</option>
                  <option value="ME">Maine</option>
                  <option value="MD">Maryland</option>
                  <option value="MA">Massachusetts</option>
                  <option value="MI">Michigan</option>
                  <option value="MN">Minnesota</option>
                  <option value="MS">Mississippi</option>
                  <option value="MO">Missouri</option>
                  <option value="MT">Montana</option>
                  <option value="NE">Nebraska</option>
                  <option value="NV">Nevada</option>
                  <option value="NH">New Hampshire</option>
                  <option value="NJ">New Jersey</option>
                  <option value="NM">New Mexico</option>
                  <option value="NY">New York</option>
                  <option value="NC">North Carolina</option>
                  <option value="ND">North Dakota</option>
                  <option value="OH">Ohio</option>
                  <option value="OK">Oklahoma</option>
                  <option value="OR">Oregon</option>
                  <option value="PA">Pennsylvania</option>
                  <option value="RI">Rhode Island</option>
                  <option value="SC">South Carolina</option>
                  <option value="SD">South Dakota</option>
                  <option value="TN">Tennessee</option>
                  <option value="TX">Texas</option>
                  <option value="UT">Utah</option>
                  <option value="VT">Vermont</option>
                  <option value="VA">Virginia</option>
                  <option value="WA">Washington</option>
                  <option value="WV">West Virginia</option>
                  <option value="WI">Wisconsin</option>
                  <option value="WY">Wyoming</option>
                </select>
                <div class="validation" v-show="!validations.state.is_valid">{{ validations.state.text }}</div>
              </div>
              <div class="large-6 medium-6 small-12 cell">
                <label class="form-label">Zip Code</label>
                <input type="text" class="form-input" v-model="zip" v-bind:class="{'invalid' : !validations.zip.is_valid }"/>
                <div class="validation" v-show="!validations.zip.is_valid">{{ validations.zip.text }}</div>
              </div>
            </div>
          </div>
        </div>
        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <a class="edit-location-button" v-on:click="submitEditCafe()">Update Cafe</a>
          </div>
        </div>
        <div class="grid-x grid-padding-x">
          <div class="large-8 medium-9 small-12 cell centered">
            <a class="delete-location" v-on:click="deleteCafe()">Delete Cafe</a>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  /*
    Imports the Event Bus to pass events on tag updates
  */
  import { EventBus } from '../event-bus.js';

  /*
    Imports lodash for debouncing
  */
  import _ from 'lodash';

  /*
    Imports the config for the API URL for searching companies.
  */
  import { ROAST_CONFIG } from '../config.js';

  export default {
    /*
      Defines the data used by the page
    */
    data(){
      return {
        companyResults: [],
        showAutocomplete: true,

        companyName: '',
        companyID: '',
        newCompany: false,
        companyType: 'roaster',
        website: '',

        locationName: '',
        addressSearch: '',
        address: '',
        city: '',
        state: '',
        zip: '',
        lat: '',
        lng: '',
        brewMethodsSelected: [],

        validations: {
          companyName: {
            is_valid: true,
            text: ''
          },
          website: {
            is_valid: true,
            text: ''
          },
          address: {
            is_valid: true,
            text: ''
          },
          city: {
            is_valid: true,
            text: ''
          },
          state: {
            is_valid: true,
            text: ''
          },
          zip: {
            is_valid: true,
            text: ''
          }
        }
      }
    },


    /*
      Sync the tags to send to the server for the new cafe.
    */
    mounted(){
      this.autocomplete = document.getElementById('street-address');
      let googleMapsAutocomplete = new google.maps.places.Autocomplete( this.autocomplete );

      googleMapsAutocomplete.addListener( 'place_changed', function(){
        let place = googleMapsAutocomplete.getPlace();

        let addressBuilderStreetNumber = '';
        let addressBuilderRoute = '';

        for (var i = 0; i < place.address_components.length; i++) {
          let type = place.address_components[i].types[0];
          switch( type ){
            case 'street_number':
              addressBuilderStreetNumber = place.address_components[i].short_name;
            break;
            case 'route':
              addressBuilderRoute = place.address_components[i].short_name;
            break;
            case 'locality':
              this.city = place.address_components[i].long_name;
            break;
            case 'administrative_area_level_1':
              this.state = place.address_components[i].short_name;
            break;
            case 'postal_code':
              this.zip = place.address_components[i].short_name;
            break;
          }
        }

        this.address = addressBuilderStreetNumber+' '+addressBuilderRoute;
        this.lat = place.geometry.location.lat();
        this.lng = place.geometry.location.lng();

      }.bind(this));
    },

    /*
      When the component is created, add a location
    */
    created(){
      this.$store.dispatch( 'loadCafeEdit', {
        id: this.$route.params.id
      });
    },

    /*
      Loads the Vuex data we need such as brew methods
      and add cafe status.
    */
    computed: {
      brewMethods(){
        return this.$store.getters.getBrewMethods;
      },
      editCafeStatus(){
        return this.$store.getters.getCafeEditStatus;
      },
      editCafeLoadStatus(){
        return this.$store.getters.getCafeEditLoadStatus;
      },
      editCafe(){
        return this.$store.getters.getCafeEdit;
      },
      deleteCafeStatus(){
        return this.$store.getters.getCafeDeletedStatus;
      }
    },

    /*
      Defines what we need to watch on the page.
    */
    watch: {
      'editCafeStatus': function(){
        if( this.editCafeStatus == 2 ){
          this.$router.push({ name: 'cafe', params: { id: this.$route.params.id }});
        }
      },
      'editCafeLoadStatus': function(){
        if( this.editCafeLoadStatus == 2 ){
          this.populateForm();
        }
      },
      'deleteCafeStatus': function(){
        if( this.deleteCafeStatus == 2 ){
          this.$router.push({ name: 'cafes' });

          EventBus.$emit('show-success', {
            notification: 'Cafe deleted successfully!'
          });
        }
      }
    },

    /*
      Defines the methods used by the page
    */
    methods: {
      /*
        Sets the type of the company as either roaster or cafe.
      */
      setCompanyType( type ){
        this.companyType = type;
      },

      /*
        Toggles the selected brew method
      */
      toggleSelectedBrewMethod( id ){
        if( this.brewMethodsSelected.indexOf( id ) >= 0 ){
          this.brewMethodsSelected.splice( this.brewMethodsSelected.indexOf( id ), 1 );
        }else{
          this.brewMethodsSelected.push( id );
        }
      },

      /*
        Searches the API route for companies
      */
      searchCompanies: _.debounce( function(e) {
        if( this.companyName.length > 1){
          this.showAutocomplete = true;
          axios.get( ROAST_CONFIG.API_URL + '/companies/search' , {
            params: {
              search: this.companyName
            }
          }).then( function( response ){
            this.companyResults = response.data.companies;
          }.bind(this));
        }
      }, 300),

      /*
        Method populates the form with the data we need.
      */
      populateForm(){
        this.companyName    = this.editCafe.company.name;
        this.companyID      = this.editCafe.company.id;
        this.newCompany     = false;
        this.companyType    = this.editCafe.company.roaster == 1 ? 'roaster' : 'cafe';
        this.website        = this.editCafe.company.website;

        this.locationName   = this.editCafe.location_name;
        this.address        = this.editCafe.address;
        this.city           = this.editCafe.city;
        this.state          = this.editCafe.state;
        this.zip            = this.editCafe.zip;
        this.lat            = this.editCafe.latitude;
        this.lng            = this.editCafe.longitude;

        for( let i = 0; i < this.editCafe.brew_methods.length; i++ ){
          this.brewMethodsSelected.push( this.editCafe.brew_methods[i].id );
        }

        this.addressSearch = this.address;

        this.showAutocomplete = false;
      },

      /*
        Submits edits for a cafe
      */
      submitEditCafe(){
        if( this.validateEditCafe() ){
          this.$store.dispatch( 'editCafe', {
            id: this.editCafe.id,
            company_name: this.companyName,
            company_id: this.companyID,
            company_type: this.companyType,
            website: this.website,
            location_name: this.locationName,
            address: this.address,
            city: this.city,
            state: this.state,
            zip: this.zip,
            lat: this.lat,
            lng: this.lng,
            brew_methods: this.brewMethodsSelected
  				});
        }
      },

      /*
        Adds a new company
      */
      addNewCompany(){
        this.showAutocomplete = false;
        this.newCompany = true;
        this.companyResults = [];
      },

      /*
        Selects an existing company
      */
      selectCompany( company ){
        this.showAutocomplete = false;
        this.companyName = company.name;
        this.companyID = company.id;
        this.newCompany = false;
        this.companyResults = [];
      },

      /*
        Validates cafe edits
      */
      validateEditCafe(){
        let validNewCafeForm = true;

        /*
          Ensure a name has been entered
        */
        if( this.companyName.trim() == '' ){
          validNewCafeForm = false;
          this.validations.companyName.is_valid = false;
          this.validations.companyName.text = 'Please enter a name for the company.';
        }else{
          this.validations.companyName.is_valid = true;
          this.validations.companyName.text = '';
        }

        /*
          If a website has been entered, ensure the URL is valid
        */
        if( this.website.trim != '' && !this.website.match(/((([A-Za-z]{3,9}:(?:\/\/)?)(?:[\-;:&=\+\$,\w]+@)?[A-Za-z0-9\.\-]+|(?:www\.|[\-;:&=\+\$,\w]+@)[A-Za-z0-9\.\-]+)((?:\/[\+~%\/\.\w\-_]*)?\??(?:[\-\+=&;%@\.\w_]*)#?(?:[\.\!\/\\\w]*))?)/ ) ){
          validNewCafeForm = false;
          this.validations.website.is_valid = false;
          this.validations.website.text = 'Please enter a valid URL for the website.';
        }else{
          this.validations.website.is_valid = true;
          this.validations.website.text = '';
        }

        /*
          Ensure an address has been entered
        */
        if( this.address.trim() == '' ){
          validNewCafeForm = false;
          this.validations.address.is_valid = false;
          this.validations.address.text = 'Please enter an address for the new cafe.';
        }else{
          this.validations.address.is_valid = true;
          this.validations.address.text = '';
        }

        /*
          Ensure a city has been entered
        */
        if( this.city.trim() == '' ){
          validNewCafeForm = false;
          this.validations.city.is_valid = false;
          this.validations.city.text = 'Please enter a city for the new cafe.';
        }else{
          this.validations.city.is_valid = true;
          this.validations.city.text = '';
        }

        /*
          Ensure a state has been entered
        */
        if( this.state.trim() == '' ){
          validNewCafeForm = false;
          this.validations.state.is_valid = false;
          this.validations.state.text = 'Please enter a state for the new cafe.';
        }else{
          this.validations.state.is_valid = true;
          this.validations.state.text = '';
        }

        /*
          Ensure a zip has been entered
        */
        if( this.zip.trim() == '' || !this.zip.match(/(^\d{5}$)/) ){
          validNewCafeForm = false;
          this.validations.zip.is_valid = false;
          this.validations.zip.text = 'Please enter a valid zip code for the new cafe.';
        }else{
          this.validations.zip.is_valid = true;
          this.validations.zip.text = '';
        }

        return validNewCafeForm;
      },

      /*
        Deletes a cafe
      */
      deleteCafe(){
        if( confirm( 'Are you sure you want to delete this cafe?' ) ){
          this.$store.dispatch( 'deleteCafe', {
            cafe_id: this.editCafe.id
          } );
        }
      },

      /*
        Clears the form.
      */
      clearForm(){
        this.companyResults       = [];
        this.companyName          = '';
        this.companyID            = '';
        this.newCompany           = false;
        this.companyType          = 'roaster';
        this.website              = '';
        this.locationName         = '';
        this.address              = '';
        this.city                 = '';
        this.state                = '';
        this.zip                  = '';
        this.brewMethodsSelected  = [];


        this.validations = {
          companyName: {
            is_valid: true,
            text: ''
          },
          website: {
            is_valid: true,
            text: ''
          },
          address: {
            is_valid: true,
            text: ''
          },
          city: {
            is_valid: true,
            text: ''
          },
          state: {
            is_valid: true,
            text: ''
          },
          zip: {
            is_valid: true,
            text: ''
          }
        };
      }
    }
  }
</script>
