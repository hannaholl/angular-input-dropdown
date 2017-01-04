# Angular input dropdown
Angular input dropdown is an angular directive for creating an input field with a stylable dropdown list. The list can be filtered or updated as the user types to show a list based on the user's input.

If used in a form and `input-required` is set to true, the input field will validate only when an item from the dropdown list has been selected.

## Demo
A demo can be viewed <a href="https://embed.plnkr.co/4HZ0CS/preview">here</a>.   
The demo code can also be found in /demo.

## Installation
You download the files manually or install angular input dropdown with bower:
```
bower install angular-input-dropdown
```

## Getting started
#### 1. Include the CSS and JS files in your project
```html
<link rel='stylesheet' href='inputDropdownStyles.css' />
<script src="inputDropdown.js"></script>
```

#### 2. Create an angular app and add `inputDropdown` as a dependecy
```js
var app = angular.module('myApp', ['inputDropdown']);
```

#### 3. Create a controller to handle the data for the input
```js
app.controller('InputDropdownController', [function() {
  this.selectedDropdownItem = null;
  this.dropdownItems = ['dropdown 1', 'dropdown 2', 'dropdown 3'];
}]);
```

#### 4. Add the directive to your html
Use the attribute `default-dropdown-items` to pass in an array of items to show in the dropdown and `selected-item` to bind the item the user selects to your controller.
```html
<div ng-controller="InputDropdownController as dropdownCtrl">
  <input-dropdown
    selected-item="dropdownCtrl.selectedDropdownItem"
    default-dropdown-items="dropdownCtrl.dropdownItems">
  </input-dropdown>
</div>
```
This creates an `<input>` element that will show a list with 'dropdown 1', 'dropdown 2' and 'dropdown 3' when it gains focus.
When the user selects an item the input field will be updated to match the selection and `selectedDropdownItem` in the controller will be updated with the user's selection.

## Styling
Some basics styles are included in the `inputDropdownStyles.css` file. To overwrite them or add more styles you can uses these CSS selectors.
```css
.input-dropdown {
  /* div wrapper for the whole directive, can be used to set the width for the input and dropdown list */
}

.input-dropdown input[type='text'] {
  /* Style the input field */
}

.input-dropdown ul {
  /* Style the dropdown list */
}

.input-dropdown ul > li {
  /* Style the dropdown list item */
}

.input-dropdown ul > li.active {
  /* Style the dropdown list item when active.
    The active class is added to an item on mouse over or when it's selected with the arrow keys. */
}
```

## Usage

### Attributes
These attributes are available to set on the `<input-dropdown>` directive.

* `input-placeholder`: String. Sets the placeholder attribute on the input element
* `input-class-name`: String. Sets the classname for the input element - e.g. form-control for bootstrap
* `input-name`: String. Sets the name attribute on the input element
* `input-required`: Boolean. Sets ng-required on the input element. If true the input will validate when the user has made a selection from the dropdown list.
* `selected-item`: Object, *required*. Used in the controller to read the user's selection. Can be set to null when created or equal to an item in the dropdown array to set the initial value for the input. Will update when the user selects an item from the list.
* `default-dropdown-items`: Array. An array of strings or object to show in the dropdown list. If objects, each object must have a property `readableName` which is what will show in the dropdown. If not set there will be no default list when the input field is empty and you'll need to pass a list based on the users input in the `filter-list-method`.
* `filter-list-method`: Function. Called when user types in the input field. Must return a promise that resolves with an array to use as a new dropdown list. Must be set with a `userInput` paramater: `filter-list-method="methodToCall(userInput)"`.
* `item-selected-method`: Function. Called when user selects an item from the dropdown. Must be set with an `item` paramater: `item-selected-method="methodToCall(item)"`.
* `allow-custom-input`: Boolean. Set to true to let user enter their own value instead of forcing an item from the dropdown list to be selected.

### Custom user input
By default the field will only validate once the user has selected a value from the dropdown list. If you don't want this behaviour set the `allow-custom-input` attribute to true. This will allow the user to enter any value into the field and  set the field to be valid even if no item from the list was selected. This could be useful if you want to show the user a list of suggestions, but still want to let them submit a custom value too.

#### Example
```html
<input-dropdown
  allow-custom-input="true">
</input-dropdown>
```

### Dropdown with objects
Use objects in the dropdown list if you need more data than just strings. Each object needs to have a property `readableName`, this is what will be displayed in the dropdown.

#### Example
```js
// Inside a controller

this.selectedCountry = null;
this.defaultDropdownObjects = [
  {
    readableName: 'China',
    countryCode: 'CH',
    id: 0
  },
  {
    readableName: 'Sweden',
    countryCode: 'SE',
    id: 1
  },
  {
    readableName: 'United Kingdom',
    countryCode: 'UK',
    id: 2
  },
  {
    readableName: 'United States',
    countryCode: 'US',
    id: 3
  }
];
```
```html
<input-dropdown
  selected-item="myController.selectedCountry"
  default-dropdown-items="myController.defaultDropdownObjects">
</input-dropdown>
```
This will show a list with the country names (China, Sweden, United Kingdom, United States) and when the user clicks one of them, `selectedCountry` in the controller will be set to the correspoding object (ie `{readableName: 'United States', countryCode: 'US', id: 3 }`).

### Update dropdown
To filter or update the dropdown list as the user types into the field, pass a method to the directive in the `filter-list-method` attributes like this:
```html
<input-dropdown filter-list-method="methodToCall(userInput)"></input-dropdown>
```
This method should return a promise with a new array to use as a dropdown. For example it can be used to show the user live search results as they type.

#### Example
```js
// Inside a controller

this.filterDropdown = function(userInput) {
  // Search for cities based on the user's input
  return $http.get('/cities/search/' + userInput)
    .then(function(request) {
      var cities = request.data;
      var dropdownArray = cities.map(function(cityObject) {
        var dropdownObject = angular.copy(cityObject);
        
        // Add readableName property to display item in dropdown list.
        dropdownObject.readableName = cityObject.name + ' in ' + cityObject.country;
        
        return dropdownObject;
      });
      return dropdownArray;
    });
};
```
```html
<input-dropdown filter-list-method="myController.filterDropdown(userInput)"></input-dropdown>
```


## License
MIT
