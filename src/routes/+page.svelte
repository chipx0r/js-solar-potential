<!--
 Copyright 2023 Google LLC

 Licensed under the Apache License, Version 2.0 (the "License");
 you may not use this file except in compliance with the License.
 You may obtain a copy of the License at

      https://www.apache.org/licenses/LICENSE-2.0

 Unless required by applicable law or agreed to in writing, software
 distributed under the License is distributed on an "AS IS" BASIS,
 WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 See the License for the specific language governing permissions and
 limitations under the License.
 -->

<script lang="ts">
  /* global google */

  import * as GMAPILoader from '@googlemaps/js-api-loader';
  const { Loader } = GMAPILoader;

  import { onMount } from 'svelte';

  import SearchBar from './components/SearchBar.svelte';
  import Sections from './sections/Sections.svelte';

  const googleMapsApiKey = import.meta.env.VITE_GOOGLE_MAPS_API_KEY;
  const defaultPlace = {
    name: 'Prta del Sol 1',
    address: 'Prta del Sol 1, Centro, Madrid, Spain',
  };
  let location: google.maps.LatLng | undefined;
  const zoom = 19;

  // Initialize app.
  let mapElement: HTMLElement;
  let map: google.maps.Map;
  let geometryLibrary: google.maps.GeometryLibrary;
  let mapsLibrary: google.maps.MapsLibrary;
  let placesLibrary: google.maps.PlacesLibrary;
  onMount(async () => {
    // Load the Google Maps libraries.
    const loader = new Loader({ apiKey: googleMapsApiKey });
    const libraries = {
      geometry: loader.importLibrary('geometry'),
      maps: loader.importLibrary('maps'),
      places: loader.importLibrary('places'),
    };
    geometryLibrary = await libraries.geometry;
    mapsLibrary = await libraries.maps;
    placesLibrary = await libraries.places;

    // Get the address information for the default location.
    const geocoder = new google.maps.Geocoder();
    const geocoderResponse = await geocoder.geocode({
      address: defaultPlace.address,
    });
    const geocoderResult = geocoderResponse.results[0];

    // Initialize the map at the desired location.
    location = geocoderResult.geometry.location;
    map = new mapsLibrary.Map(mapElement, {
      center: location,
      zoom: zoom,
      tilt: 0,
      mapTypeId: 'satellite',
      mapTypeControl: false,
      fullscreenControl: false,
      rotateControl: false,
      streetViewControl: false,
      zoomControl: false,
    });
  });
</script>

<!-- Top bar -->
<div class="flex flex-row h-full">
  <!-- Side bar -->
  <aside class="flex-none md:w-1/3 w-1/3 p-2 pt-3 overflow-auto">
    <div class="flex flex-col space-y-2 h-full" style="background: linear-gradient(180deg, rgba(83,123,213,0.55) 0%, rgba(52,163,206,0.6) 35%, rgba(61,163,197,1) 100%);">
      <div>
        <img src="/logo-tunergia.png" style="width: 20%;" alt="" />
      </div>
      <div style="font-size: 28px;font-weight: bold;">
        ¡Empieza a escribir<br>tu historia solar!
      </div>
      <div>Cuenta mas sobre ti</div>
      
    {#if placesLibrary && map}
      <SearchBar bind:location {placesLibrary} {map} initialValue={defaultPlace.name} />
    {/if}

    {#if location}
      <Sections {location} {map} {geometryLibrary} {googleMapsApiKey} />
    {/if} 

      <!-- <div class="pac-card" id="pac-card">
        <div id="pac-container">
           
        </div>
       </div> -->

       <br>

       <div>¿Cuándo consumes tu energía?</div>
       <div>
        <input type="radio" id="constante" name="tipo" value="constante" />
            <label for="constante">Durante todo el día</label>
        <input type="radio" id="diurno" name="tipo" value="diurno" checked />
            <label for="diurno">Por la mañana</label>
        <input type="radio" id="nocturno" name="tipo" value="nocturno" />
            <label for="nocturno">Por la noche</label>
       </div>
       <br>


       <div>Dinos cuánto pagas al mes en tu factura de luz o tu consumo estimado</div>

       <div>

        <div>
          <input type="radio" id="euros" name="euroKWH" value="euros" checked />
              <label for="euros">€/mes</label>
          <input type="radio" id="kWh" name="euroKWH" value="kWh"/>
              <label for="kWh">kWh/mes</label>
        </div>
        <input type="range" id="slider-range" min="0" max="1000"/>
        <br>
        <input type="text" id="tarifa" class="tarifa" value="€50" size="8">
      </div>

      <!--br>
      <div>¿Tienes Coche Electrico?</div>
       <div>
        <input type="radio" id="si" name="sino" value="si" checked />
            <label for="si">Si</label>
        <input type="radio" id="no" name="sino" value="no" />
            <label for="no">NO</label>
       </div-->

       <br>
       <div>¿En qué época del año consumes más?</div>
       <div>
        <input type="radio" id="verano" name="mayorconsumo" value="verano" />
            <label for="verano">Verano</label>
        <input type="radio" id="invierno" name="mayorconsumo" value="invierno" />
            <label for="invierno">Invierno</label>
        <input type="radio" id="constante" name="mayorconsumo" value="constante" checked/>
            <label for="constante">Durante todo el año es igual</label>
       </div>
      
      <div class="grow" />

    </div>
  </aside>
  <!-- Main map -->
  <div bind:this={mapElement} class="w-full" />

</div>
