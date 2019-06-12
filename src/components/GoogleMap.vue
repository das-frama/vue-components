<template>
  <div :id="name" class="google-map"></div>
</template>

<script>
export default {
  props: {
    name: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      map: null,
      marker: null
    };
  },
  computed: {
    isMapScriptLoaded: {
      get() {
        if (window.isMapScriptLoaded === undefined) {
          window.isMapScriptLoaded = false;
        }
        return window.isMapScriptLoaded;
      },
      set(newValue) {
        window.isMapScriptLoaded = newValue;
      }
    }
  },
  mounted() {
    if (!this.isMapScriptLoaded) {
      // Добавить script тег к dom элементу.
      let googleMapsScript = document.createElement("script");
      googleMapsScript.onload = this.initMap;
      googleMapsScript.async = true;
      googleMapsScript.src =
        "https://maps.googleapis.com/maps/api/js?key=AIzaSyA-KDF-3FdNn_I1yEe6qPD5GIFAMezr6JA";
      document.body.appendChild(googleMapsScript);
      this.isMapScriptLoaded = true;
    } else {
      this.initMap();
    }
  },
  methods: {
    initMap() {
      /* eslint-disable */
      const options = {
        zoom: 16,
        center: new google.maps.LatLng(57.605939, 39.819588),
        disableDefaultUI: true,
        gestureHandling: "cooperative",
        scaleControl: true,
        styles: [
          {
            featureType: "all",
            stylers: [
              {
                saturation: 0
              },
              {
                hue: "#e7ecf0"
              }
            ]
          },
          {
            featureType: "road",
            stylers: [
              {
                saturation: -70
              }
            ]
          },
          {
            featureType: "transit",
            stylers: [
              {
                visibility: "off"
              }
            ]
          },
          {
            featureType: "poi",
            stylers: [
              {
                visibility: "off"
              }
            ]
          },
          {
            featureType: "water",
            stylers: [
              {
                visibility: "simplified"
              },
              {
                saturation: -60
              }
            ]
          }
        ]
      };
      this.map = new google.maps.Map(this.$el, options);
      this.marker = new google.maps.Marker({
        position: { lat: 57.605939, lng: 39.819588 },
        map: this.map
      });
      /* eslint-enable */
    }
  }
};
</script>
