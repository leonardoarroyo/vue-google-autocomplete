# Vuetify Google Autocomplete

A fork of [vue-google-autocomplete](https://github.com/olefirenko/vue-google-autocomplete) which uses `v-text-field` instead of regular `input`.

## Demo

Live demo: [olefirenko.github.io/vue-google-autocomplete](https://olefirenko.github.io/vue-google-autocomplete/)

## Benefits

I have tried to use different Vue Google Autocomplete components, but did not find any that would solve my needs. So below I would like to mention what you can get from this exact component:

* Load more than one autocompletion inputs (I could not achieve this with existing vue components)
* Getting geolocation data (latitude, longitude) for found address object along with other address data (country, city, state, street, house number, zip code). So no need to do additional geocode request on backend side.
* No external dependencies
* You can get access to underlying [PlaceResult object](https://developers.google.com/maps/documentation/javascript/reference#PlaceResult) to get more details about found location.
* You can limit results to specific country or use users geolocation data

## Installation

This component uses Google Maps Places API to get geo suggests for autocompletion, so you have to include the Google Maps Places API in the `<head>` of your HTML:

```html
<!DOCTYPE html>
  <html>
  <head>
    …
    <script src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY_HERE&libraries=places"></script>
  </head>
  <body>
    …
  </body>
</html>
```

To obtain API key please visit the [Google Developer Console](https://console.developers.google.com). The API's that you have to enable in your Google API Manager Dashboard are [Google Maps Geocoding API](https://developers.google.com/maps/documentation/geocoding/start), [Google Places API Web Service](https://developers.google.com/places/web-service/) and [Google Maps Javascript API] (https://developers.google.com/maps/documentation/javascript/).

The easiest way to use Vue Google Autocomplete is to install it from **npm** or **yarn**.

```sh
npm install vue-google-autocomplete --save
```

Or

```sh
yarn add vue-google-autocomplete
```

## Usage

The Vue Google Autocomplete works out of the box by just including it.

```js
import VueGoogleAutocomplete from 'vue-google-autocomplete'

```

In your template you can use this syntax:
```
<vue-google-autocomplete
    id="map"
    classname="form-control"
    placeholder="Start typing"
    v-on:placechanged="getAddressData"
>
</vue-google-autocomplete>
```

### Properties

#### id
Type: `String`

`required` ID for the input container.

#### classname
Type: `String`

Class to the input container.

#### placeholder
Type: `String`
Default: `Start typing`

The input field will get this placeholder text.

#### types
Type: `String`
Default: `address`

Types supported in place autocomplete requests. [More info](https://developers.google.com/places/supported_types#table3)

#### country
Type: `String`
Default: null

Option to restrict the autocomplete search to a particular country. Countries must be passed as a two-character, ISO 3166-1 Alpha-2 compatible country code (i.e. "br", "sg", "fr").

#### enable-geolocation
Type: `Boolean`
Default: `false`

Bias the search towards user current location.

### Events
The component emits next events, which you can listen in your application:

#### placechanged
Gets triggered when the address data got obtained. This data is available on the returned objects:
* `street_number`, `route`, `locality`, `administrative_area_level_1`, `country`, `postal_code`, `latitude`, `longitude`.
* `place` - [PlaceResult object](https://developers.google.com/maps/documentation/javascript/reference#PlaceResult) is available as second parameter.

#### no-results-found
Gets triggered when a user entered the name of a Place that was not suggested and pressed the Enter key, or the Place Details request failed.

#### focus
Gets triggered when the autocomplete input field receives focus.

#### blur
Gets triggered when the autocomplete input field loses focus.

#### change
Gets triggered when the autocomplete input got changed

#### keypress
Gets triggered when a key gets pressed

### Example

Please note that you need to provide what method will listen (`v-on:placechanged`) to an event when the address data is obtained.

```js
<template>
    <div>
        <h2>Your Address</h2>

        <vue-google-autocomplete
            id="map"
            classname="form-control"
            placeholder="Please type your address"
            v-on:placechanged="getAddressData"
            country="sg"
        >
        </vue-google-autocomplete>
    </div>
</template>

<script>
    import VueGoogleAutocomplete from 'vue-google-autocomplete'

    export default {
        components: { VueGoogleAutocomplete },

        data: function () {
            return {
              address: ''
            }
        },

        methods: {
            /**
            * When the location found
            * @param {Object} addressData Data of the found location
            * @param {Object} placeResultData PlaceResult object
            */
            getAddressData: function (addressData, placeResultData) {
                this.address = addressData;
            }
        }
    }
</script>
```

