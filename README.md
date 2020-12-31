# GEOLET

_A simple but highly customizable geolocation plugin for [Leaflet](https://github.com/leaflet/leaflet)_\
_by Ruben Holthuijsen_

__[CLICK HERE FOR A LIVE DEMO](https://rubenholthuijsen.nl/geolet/demo)__ ([mirror](https://rhlt.github.io/leaflet-geolet/demo.html))

This Leaflet plugin adds a __geolocation button__ to your Leaflet based map. After clicking it, a marker will be added to the map at the user's location. A click on that marker opens a popup, the content of which is generated as the user's location changes. All of this is completely customizable, but usable out of the box with just a single line of code.

Current version: 20.12.31 ([changelog](https://github.com/rhlt/leaflet-geolet/blob/main/CHANGELOG.md))\
Requires Leaflet 1.0.0 or above

__[DOWNLOAD geolet.js](https://raw.githubusercontent.com/rhlt/leaflet-geolet/main/geolet.js)__

## How to use

Simply include `geolet.js` in your page, after Leaflet itself. Then, in its most basic form (as in the demo linked above and included as `demo.html`), all you need is this line of code:

`L.geolet({ position: 'bottomleft' }).addTo(map);`

This will add a geolocation button at the bottom left part of the map screen. Here, I'm assuming your Leaflet map object is in a variable named `map`.

### Options

The Geolet control accepts the following options, which can be added to the options object (the part with `{ position: 'bottomleft' }` in the line above):

#### position
_default:_ `'topright'`\
Inherited from [Leaflet's Control class](https://leafletjs.com/reference-1.7.1.html#control)\
This defines where the control is shown. Possible values are `topleft`, `topright`, `bottomleft`, and `bottomright`.

#### title
_default:_ `'Find current location'`\
The value that the `title` attribute of the button element will get. This will be shown as a tooltip when a user hovers over the button.

#### className
_default:_ (none)\
A class name that will be added to the `class` attribute of the button element.

#### activeClassName
_default:_ (none)\
A class name that will be added to the `class` attribute of the button element, when geolocation is active.

#### style
_default:_ `{ display: 'flex', color: '' }`\
An object of style properties that will be applied to the button element. These will be reapplied when geolocation is deactivated. The default `display` value of `flex` is used to center the geolocation icon. The empty `color` property is used to restore the text color to default when geolocation is deactivated.

#### activeStyle
_default:_ `{ display: 'flex', color: '#F00' }`\
An object of style properties that will be applied to the button element when geolocation is activated (i.e., after the user clicks the button).

#### html
_default:_ `'<svg width="16" height="16" viewport="0 0 16 16" style="margin:auto auto"><circle cx="8" cy="8" r="7" style="fill:none;stroke:currentColor;stroke-width:2px"/><circle cx="8" cy="8" r="4" style="fill:currentColor"/></svg>'`\
The content of the button element. The default is a simple, circular SVG icon.

#### geoOptions
_default:_ `{ enableHighAccuracy: true, maximumAge: 30000, timeout: 27000 }`\
An options object that is sent to the browser's `watchPosition` function. The default is what is used [on this MDN page](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API/Using_the_Geolocation_API)

#### marker
_default:_ (none)\
The marker (of type [L.Marker](https://leafletjs.com/reference-1.7.1.html#marker) or something similar) that will be added to the map at the user's location. If this value is empty, a default marker will be generated with a circular icon like the one on the geolocation button. If you don't want a marker at all, set this value to `false`.

#### popup
_default:_ (none)\
The popup (of type [L.Popup](https://leafletjs.com/reference-1.7.1.html#popup) or something similar) that will be bound to the added marker. If this value is empty, a default popup will be generated. If you don't want a popup at all, set this value to `false`.

#### popupContent
_default:_ (none)\
The HTML content that will be shown in the popup when the marker is clicked. This accepts a simply string value, or a callback function of the following form:\
`function (latlng) { return 'Your location: ' + latlng.lat + ', ' + latlng.lng; }`\
The latlng value is passed as an [L.LatLng](https://leafletjs.com/reference-1.7.1.html#latlng) object. The return value will be used as the content of the popup. This function will be called when the user clicks the marker, and (by default) also when the user's location changes while the popup is open. The geolocation control itself is available as the `this` variable.

#### updatePopupWhenOpen
_default:_ `true`\
When this is set to `true` (the default) the popup's content will be updated whenever a user's location changes. This may happen very often, sometimes every second, so this may not always be desired. When this option is `false`, the popup's content will only be generated when it opens (usually when the user clicks the marker), not when it is already open.

#### autoPan
_default:_ `true`\
Whether to automatically pan (and possibly zoom) to the user's location when geolocation is activated and a location is found.

#### minZoom
_default:_ `9`\
If `autoPan` is true, the map view will be zoomed in to this value if the current zoom is less when geolocation is activated and a location is found. It is not used outside `autoPan` and does not restrict the user from zooming out. To keep the zoom level at all times, set this to `false`.


### Attributes

The control object generated by `L.geolet()` has the following publicly available attributes:

#### marker
This contains the `L.Marker` object that will be added to the map when geolocation is activated. It is generated once the control is added to a map. I would recommend against completely replacing this, as doing so will not remove an existing marker from the map.

#### popup
This contains the `L.Popup` object that will be bound to the marker that is added to the map when geolocation is activated. It is generated once the control is added to a map. I would recommend against completely replacing this, as doing so will not remove an existing popup from the map.


### Methods

In addition to the methods inherited from [L.Control](https://leafletjs.com/reference-1.7.1.html#control), the control object generated by `L.geolet()` has the following publicly available methods:

#### isActive()
Returns `true` or `false` depending on if geolocation is currently active. Note that, when active, the marker may not yet be shown on the map if geolocation hasn't found the user's location yet.

#### getLatLng()
Returns an [L.LatLng](https://leafletjs.com/reference-1.7.1.html#latlng) object representing the user's location. Will instead be `null` if no location is available, which may be the case even if geolocation is active, for example when  the user's location hasn't been found yet.

#### activate()
Activates geolocation. The user will be prompted for permission if permission hasn't been granted yet. This is automatically called when the user clicks the button when geolocation is not yet active. If you want to call this manually, note that some browsers (such as Chrome) only allow for geolocation to be activated after a 'user gesture' such as a click or tap, __not__ at page load. Trying to activate at page load will cause an error and will not prompt the user for permission. 

#### deactivate()
Deactivates geolocation and removes the marker from the map. This is automatically called when the user clicks the button when geolocation is active, or when an error occurs.


### Events

The following events will be fired _on the map containing the control_. Use a function like this to listen for an event:

`map.on('geolet_success', function (data) { /* use e.g. data.latlng */ });`

#### geolet_success
Fired whenever a location is found. This may be very often, so it is advisable to do some things (such as moving the map to the found location) only when `first` is true. The `data` object will have the following keys:\
`control`: the control that has fired the event;\
`first`: whether this is the first time since activation that a location has been found _(added in 20.12.31)_;\
`marker`: the marker that has been added to the map;\
`latlng`: the coordinates (as an [L.LatLng](https://leafletjs.com/reference-1.7.1.html#latlng) object) of the user's location;\
`raw`: the raw [GeolocationPosition](https://developer.mozilla.org/en-US/docs/Web/API/GeolocationPosition) object returned by the browser;

#### geolet_error
Fired whenever an error occurs, for example if no permission has been granted to use location data. The `data` object will have the following keys:\
`control`: the control that has fired the event;\
`raw`: the raw [GeolocationPositionError](https://developer.mozilla.org/en-US/docs/Web/API/GeolocationPositionError) object returned by the browser;


### Static methods and attributes

The following static methods and attributes are available on the global `L.Geolet` class:

#### browserSupport
Will be equal to `true` or `false`, depending on whether the browser supports geolocation. This says nothing about whether the user has given permission to use their location. If geolocation is unsupported, the control will be hidden.

#### formatSymbols
An object with symbols that will be used for the `formatLatLng` method (see below). The default values are `{ deg: '&deg;', min: '&#8217;', sec: '&#8221;', N: 'N', E: 'E', S: 'S', W: 'W', space: ' ', comma: ', ' }`. If `formatSymbols` is set to an object with any of these keys, their values will overwrite the defaults.

#### formatLatLng(latLng) / formatLatLng(lat, lng\[, alt\])
Returns an HTML formatted string of the given position, in degrees, minutes, and seconds and with compass directions instead of positive and negative numbers. The symbols used for formatting can be overwritten by setting `formatSymbols`. This method is used to generate the default popup content from the user's location. It accepts everything that [L.latLng](https://leafletjs.com/reference-1.7.1.html#latlng) will accept (including an `L.LatLng` object, an array like `[45, 10]`, an object like `{lat: 45, lng: 10}`, and the latitude/longitude values as separate arguments), although the optional `alt` (altitude) parameter is unused. 
