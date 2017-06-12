<template>
    <v-text-field
      :class="classname"
      :id="id"
      :label="placeholder"
      @focus = "geolocate()"
      @blur="onBlur()"
      @change="onChange"
      @keypress="onKeyPress"
    ></v-text-field>
</template>

<script>
    export default {
        name: 'VueGoogleAutocomplete',

        props: {
          id: {
            type: String,
            required: true
          },

          classname: String,

          placeholder: {
            type: String,
            default: 'Start typing'
          },

          types: {
            type: String,
            default: 'address'
          },

          country: {
            type: String,
            default: null
          },

          enableGeolocation: {
            type: Boolean,
            default: false
          }
        },

        data: function () {
            return {
                /**
                 * The Autocomplete object.
                 *
                 * @type {Autocomplete}
                 * @link https://developers.google.com/maps/documentation/javascript/reference#Autocomplete
                 */
                autocomplete: null,

                /**
                 * Autocomplete input text
                 * @type {String}
                 */
                autocompleteText: '',
            }
        },

        mounted: function() {
          const options = {
            types: [this.types]
          };

          if (this.country) {
            options.componentRestrictions = {
              country: this.country
            };
          }

          var input = document.getElementById(this.id);
          input.placeholder = "";
          this.autocomplete = new google.maps.places.Autocomplete(
                input,
                options
            );

          this.autocomplete.addListener('place_changed', () => {

                let place = this.autocomplete.getPlace();

                if (!place.geometry) {
                  // User entered the name of a Place that was not suggested and
                  // pressed the Enter key, or the Place Details request failed.
                  this.$emit('no-results-found', place);
                  return;
                }

                let addressComponents = {
                    street_number: 'short_name',
                    route: 'long_name',
                    locality: 'long_name',
                    administrative_area_level_1: 'short_name',
                    country: 'long_name',
                    postal_code: 'short_name'
                };

                let returnData = {};

                if (place.address_components !== undefined) {
                    // Get each component of the address from the place details
                    for (let i = 0; i < place.address_components.length; i++) {
                      let addressType = place.address_components[i].types[0];

                      if (addressComponents[addressType]) {
                        let val = place.address_components[i][addressComponents[addressType]];
                            returnData[addressType] = val;
                      }
                    }

                    returnData['latitude'] = place.geometry.location.lat();
                    returnData['longitude'] = place.geometry.location.lng();

                    // return returnData object and PlaceResult object
                    this.$emit('placechanged', returnData, place);
                }
           });
        },

        methods: {
            /**
             * When the input gets focus
             */
            onFocus() {
              this.geolocate();
              this.$emit('focus');
            },

            /**
             * When the input loses focus
             */
            onBlur() {
              this.$emit('blur');
            },

            /**
             * When the input got changed
             */
            onChange() {
              this.$emit('change', this.autocompleteText);
            },

            /**
             * When a key gets pressed
             * @param  {Event} event A keypress event
             */
            onKeyPress(event) {
              this.$emit('keypress', event);
            },

            // Bias the autocomplete object to the user's geographical location,
            // as supplied by the browser's 'navigator.geolocation' object.
            geolocate() {
                if (this.enableGeolocation) {
                    if (navigator.geolocation) {
                      navigator.geolocation.getCurrentPosition(position => {
                        let geolocation = {
                          lat: position.coords.latitude,
                          lng: position.coords.longitude
                        };
                        let circle = new google.maps.Circle({
                          center: geolocation,
                          radius: position.coords.accuracy
                        });
                        this.autocomplete.setBounds(circle.getBounds());
                      });
                    }
                }
            }
        }
    }
</script>
