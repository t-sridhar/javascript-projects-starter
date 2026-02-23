# TripPlanner — Concept Guide

> This document describes what each feature should do. Read carefully —
> the way a feature is described often hints at how to build it.
>
> If you get stuck, expand the hint sections for guidance.

---

## Flow 1: Country Exploration

### 1.1 Fetching All Countries

The explore page should load all countries from the RestCountries API when the page first loads. While data is loading, the user should see a loading spinner. If the request fails, display a friendly error message instead of leaving the page blank. You only need specific fields from each country — the API supports filtering the response to reduce payload size.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How do you request data from a remote server? What happens if the server is slow or unavailable? How can you show different UI states (loading, success, error) based on the request outcome?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: The `fetch()` API with `async/await`. Wrap in `try/catch` for error handling. Use the API's query parameter `?fields=name,capital,region,...` to request only the data you need. Toggle visibility of loading/error/content elements based on state.
</details>

---

### 1.2 Rendering Country Cards

Each country should be rendered as a card showing: the flag as an image, the country name, capital city, region as a colored badge, population (formatted with K/M suffixes), and area. These cards must be generated entirely in JavaScript from the API data. Each card should link to the country detail page.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You have an array of country objects. You need to transform each one into an HTML string or DOM element. What array method transforms every item? How do you add the resulting HTML to the page efficiently?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.map()` to transform each country object into an HTML string using **template literals** (backtick strings with `${expressions}`). Use `element.innerHTML` or `insertAdjacentHTML` to add the cards to a grid container. For the link, use `href="./country.html?code=${country.cca3}"`.
</details>

---

### 1.3 Formatting Numbers for Display

Raw population numbers like `331002651` are hard to read. Display them as "331M" instead. Similarly, areas should show with a "km²" suffix and thousands separators. Write small utility functions for these conversions and reuse them everywhere.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How can you determine if a number is in the millions vs thousands range? What math operations convert a raw number to its abbreviated form? How do you add thousands separators?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Simple division and comparison — `if (n >= 1_000_000) return (n / 1_000_000).toFixed(1) + 'M'`. Use `Number.toLocaleString()` for thousands separators. Create reusable functions like `formatPopulation()` and `formatArea()` in a utilities module.
</details>

---

### 1.4 Flag Images and Loading

Each country card displays the flag as an image. Flags come from the API response as SVG or PNG URLs. Since there are many countries, loading all flag images at once could be slow. Only images that are currently visible (or about to be visible) should be loaded.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: Is there an HTML attribute that tells the browser to delay loading an image until it's near the viewport? How do you ensure the flag image covers its container without distortion?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: The `loading="lazy"` attribute on `<img>` elements for native lazy loading. Use CSS `object-fit: cover` to ensure flags fill their container without stretching. Set a fixed height on the flag container for consistent card layouts.
</details>

---

## Flow 2: Search & Filtering

### 2.1 Region Filter Pills

A row of clickable buttons (pills) lets the user filter by region: All, Africa, Americas, Asia, Europe, Oceania. Clicking a pill should immediately show only countries from that region. The active pill should have a distinct style. Rather than re-fetching data, filter the in-memory dataset.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You already have all countries loaded. How do you narrow an array to only items matching a condition? How do you track which pill is "active" and style it differently? Rather than adding listeners to each pill individually, is there a more efficient pattern?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.filter()` to keep only countries where `country.region === selectedRegion`. **Event delegation** — one click listener on the pills container, use `event.target.closest()` to find the clicked pill. `classList.add/remove` to toggle the active class. Store the active region in a variable and re-render.
</details>

---

### 2.2 Debounced Search

The search input in the hero section should filter countries by name or capital as the user types. But firing a filter on every single keystroke is wasteful — the system should wait until the user pauses typing before applying the filter. This improves perceived performance.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How can you delay a function call so it only runs after the user stops typing for a short period? What happens to the previous pending call when a new keystroke arrives? What two timer functions work together for this?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **Debouncing** — a function that wraps another function using `setTimeout` and `clearTimeout`. Each keystroke clears the previous timer and starts a new one. Uses **closures** to remember the timeout ID between calls. Apply the debounced function to the `input` event on the search field.
</details>

---

### 2.3 Custom Sort Dropdown

A sort dropdown lets users reorder countries by name (A-Z, Z-A), population, or area. This must NOT use a native `<select>` element — build a custom dropdown with a trigger button, a hidden list, and click handlers. The dropdown should open/close on click, close when clicking outside, and show the selected option.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The dropdown has three states: closed, open, and an item selected. How do you toggle visibility of the list? How do you detect clicks outside the dropdown to close it? How do you update the display text and a hidden value when an item is clicked?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: A hidden `<input>` stores the selected value. A `<button>` triggers open/close. A `<ul>` with `hidden` attribute holds options. Toggle the `hidden` attribute on click. Use `document.addEventListener('click', ...)` to detect outside clicks. Use `event.stopPropagation()` on the trigger to prevent the outside-click handler from firing immediately. `element.closest()` to check if the click target is inside the dropdown wrapper.
</details>

---

### 2.4 Sorting the Data

When the sort option changes, rearrange the filtered dataset. Sorting by name requires alphabetical comparison, while population and area use numeric comparison. The sort should work in combination with the active region filter and search query.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How do you rearrange an array based on a comparison? For alphabetical sorting, what built-in string method compares two strings in locale-aware order? How do you reverse the sort direction?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.sort()` with a custom comparator. For names: `a.name.localeCompare(b.name)`. For numbers: `b.population - a.population` (descending). Use a `switch` statement or object lookup to pick the right comparator based on the selected sort value. Always work on a copy with the **spread operator** `[...array]` to avoid mutating the original.
</details>

---

### 2.5 Pagination with Load More

Don't show all 250 countries at once. Display 12 at a time with a "Load More" button. Each click appends the next batch. The button should disappear when all countries are displayed. The count display should update (e.g., "Showing 24 of 195 countries").

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need to track how many countries are currently displayed. Each "Load More" click increases that count. How do you extract a portion of an array from a start index to an end index?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.slice(0, displayedCount)` for initial render, then `Array.slice(displayedCount, displayedCount + batchSize)` for the next batch. Use `insertAdjacentHTML('beforeend', ...)` to append new cards without replacing existing ones. Show/hide the button based on `displayedCount < total`.
</details>

---

## Flow 3: Country Details & Wishlist

### 3.1 Reading the Country Code from the URL

When the user clicks a country card, they navigate to `country.html?code=USA`. The detail page needs to read this code from the URL and use it to fetch the country's full data. This allows users to bookmark or share direct links to countries.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: After the `?` in a URL, there's a standard format for key-value pairs. How does JavaScript provide access to these values from the current page URL?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **URL query parameters**. Read them with `new URLSearchParams(window.location.search).get('code')`. If the code is missing or invalid, show an error message instead of crashing.
</details>

---

### 3.2 Fetching and Displaying Country Details

Using the code from the URL, fetch the country from the API's alpha endpoint. Display all details: flag, official name, native name, capital, region, subregion, population, area, languages, currencies, and timezones. Layout the flag and info side-by-side on larger screens.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The API response structure nests some data inside objects (languages, currencies). How do you extract values from an object when you don't know the keys in advance? How do you join multiple values into a comma-separated string?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Object.values(country.languages)` to get an array of language names, then `.join(', ')` to make a string. Same for currencies. Use **optional chaining** (`?.`) to safely access nested properties that might not exist: `country.capital?.[0]`. Build the HTML with template literals.
</details>

---

### 3.3 Border Countries

If a country has land borders, display them as clickable chips/badges below the main detail. Each chip shows the border country's flag and name, and links to that country's detail page. The API returns border codes as an array of CCA3 codes, so you need a second API call to get the names and flags.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You have an array of country codes. You need to fetch data for all of them. Can you fetch multiple countries in a single API call? What if a country has no borders — how should the UI handle that?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: The RestCountries API supports `alpha?codes=USA,CAN,MEX` to fetch multiple countries at once. Join the border codes with commas. Use conditional rendering — check `borders.length === 0` to hide the section entirely. Each chip links to `./country.html?code=${borderCode}`.
</details>

---

### 3.4 Wishlist with localStorage

Users can add countries to their wishlist by clicking a heart icon. The wishlist persists across page reloads and browser sessions. On the explore page, each card shows a heart icon that toggles between outlined (not in wishlist) and filled (in wishlist). On the detail page, there's an explicit "Add to Wishlist" / "In Wishlist" button.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need permanent storage that survives page reloads. What browser mechanism stores data as key-value pairs that persists across sessions? Since it only stores strings, how do you save an array of country codes? How do you check if a code already exists in the array?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **localStorage** with `JSON.stringify()` and `JSON.parse()` for arrays. Wrap in `try/catch` for safety. Use `Array.includes()` to check membership. Use `Array.filter()` to remove. Create a state module with functions like `getWishlist()`, `addToWishlist(code)`, `removeFromWishlist(code)`, `isInWishlist(code)`.
</details>

---

### 3.5 Heart Icon Toggle

On country cards, the heart icon should visually toggle between filled and outlined states. Clicking it should add/remove the country from the wishlist, update the icon appearance, and show a brief notification. Since cards are dynamically generated, you cannot attach listeners to each card individually.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The cards are created dynamically by JavaScript. If you attach click listeners at page load, the cards don't exist yet. How can you listen for clicks on elements that are added to the page later? How do you identify which card's heart was clicked?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **Event delegation** — attach one listener on the grid container. Use `event.target.closest('[data-wishlist-code]')` to find the clicked heart button. Read the country code from a `data-*` attribute. Toggle the SVG's `fill` attribute between `"none"` and `"currentColor"`. Use `classList.toggle()` for the active state.
</details>

---

## Flow 4: Compare & Trip Planning

### 4.1 Country Comparison Selectors

The compare page has 3 selector dropdowns (the third is optional). Each is a custom dropdown (not a native `<select>`) with a search input inside. When the user types, the country list filters in real-time. Selecting a country updates the display text and triggers a comparison re-render.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need to reuse the same dropdown pattern three times with different IDs. How do you create a reusable setup function that works with any dropdown given its element IDs? How do you filter the country list as the user types in the search input?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Create a setup function that takes element IDs as parameters. Use `Array.filter()` with `String.includes()` for the search. Maintain a `selectedCodes` array (one per selector). When any selector changes, check if at least 2 are selected and re-render the comparison table. Use the same custom dropdown pattern: hidden input, trigger button, dropdown list with `hidden` attribute.
</details>

---

### 4.2 Comparison Table

When 2 or 3 countries are selected, display a side-by-side comparison table. Rows include: flag, capital, region, subregion, population, area, languages, currencies, and timezones. The table should be horizontally scrollable on small screens. Fetch the full country data for all selected codes in one API call.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need to build an HTML table dynamically. Each row compares one attribute across all selected countries. How do you iterate over a list of attribute definitions and, for each one, render cells for each country?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Define an array of row objects: `[{ label: 'Capital', render: (c) => c.capital?.[0] }, ...]`. Use `Array.map()` to generate `<tr>` elements, and nested `.map()` for `<td>` cells. Fetch data with `alpha?codes=${codes.join(',')}`. Use CSS `overflow-x: auto` on the table container for mobile scrolling.
</details>

---

### 4.3 Trip Planner Form

The planner page has a form where users can: name their trip, select countries from their wishlist (using a custom multi-select dropdown), set start and end dates, and add notes. The country selector should only show countries that are in the wishlist.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: This dropdown allows selecting *multiple* countries (unlike the compare page which selects one per dropdown). How do you toggle selection on/off for each item? How do you display the selected countries as removable tags below the dropdown?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Maintain a `selectedCountryCodes` array. On item click, check if the code is already in the array — if yes, remove it (`Array.filter`); if no, add it (`Array.push`). Re-render both the dropdown items (to show active state) and the tags below. Each tag has a small "x" button that removes it. Use **event delegation** on the tags container for the remove buttons.
</details>

---

### 4.4 Saving and Deleting Trips

When the form is submitted, validate that at least a name and one country are provided. Save the trip to localStorage with a unique ID, name, countries, dates, and notes. Display saved trips as cards below the form. Each card has a delete button that removes the trip after confirmation.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: Each trip needs a unique identifier so you can delete a specific one. How can you generate a unique ID without a server? When deleting, how do you remove a specific item from an array based on its ID?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Generate IDs with `Date.now().toString(36) + Math.random().toString(36).slice(2)`. Store trips as an array in localStorage. Use `Array.unshift()` to add newest first. Use `Array.filter(t => t.id !== targetId)` to remove. After saving/deleting, call `localStorage.setItem()` with the updated array and re-render the trips list.
</details>

---

### 4.5 Trip Cards Display

Each saved trip renders as a card showing: trip name, dates (or "TBD" if not set), countries as colored tags, and notes (if any). The card header has a distinct background color. The delete button shows a trash icon and changes color on hover.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You have an array of trip objects. Each needs to become an HTML card. Some fields are optional (dates, notes) — how do you conditionally include HTML sections? How do you render an array of country names as individual tag elements?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **Template literals** with **ternary operators** for conditional HTML: `${trip.notes ? `<div>${trip.notes}</div>` : ''}`. Use `Array.map()` on `trip.countries` to generate tag elements. Use `.join('')` to combine the tag HTML strings. Attach a `data-delete-trip` attribute with the trip ID for event delegation.
</details>

---

## Cross-Cutting Concepts

### Page Routing

The app has 5 HTML pages. Your entry-point JavaScript file should detect which page is currently loaded and initialize only the relevant code. Each HTML file has a `data-page` attribute on the `<html>` element to help with this.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How do you read a data attribute from an HTML element? How can you map a string value (like "explore") to a specific initialization function without a long if/else chain?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `document.documentElement.dataset.page` to read the current page. Use a `switch` statement or store init functions in an **object** keyed by page name: `pageMap[currentPage]?.()`. The `?.` is **optional chaining** — it safely calls the function only if it exists. Consider **dynamic imports** with `await import('./explore.js')` for code splitting.
</details>

---

### Toast Notifications

The app should show brief, auto-dismissing notification messages (e.g., "Added to wishlist!", "Trip saved") that slide in from the side and disappear after a few seconds. Different types (success, error, info) should have different colors.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need to create an element dynamically, animate it in, wait a few seconds, then animate it out and remove it. How do you trigger a CSS transition from JavaScript? How do you schedule the removal?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Create a `<div>` with the toast HTML, append to a fixed container. Use `requestAnimationFrame()` to add a "visible" class that triggers a CSS transition. `setTimeout()` to auto-dismiss. Listen for `transitionend` event to remove the element from the DOM after the exit animation.
</details>

---

### Custom Dropdowns

Every dropdown in the app (sort, compare selectors, planner country selector) must be custom-built — NO native `<select>` elements. They all follow the same pattern: a trigger button, a dropdown panel with items, click-outside-to-close behavior, and keyboard accessibility.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The pattern is the same for all dropdowns — only the data and callback differ. Can you create a reusable setup function? What parameters would it need (element references, data, callback)?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: A setup function that takes: trigger element, dropdown element, display element, hidden input, and an onSelect callback. Use `element.hasAttribute('hidden')` to check state. `event.stopPropagation()` on the trigger click. A global `document.addEventListener('click', ...)` that closes any open dropdown when clicking outside. Store the active class name and item class name as parameters for reusability.
</details>

---

### Debouncing

Several features (search input, country selector search) use debouncing to avoid excessive updates. Write the debounce utility once and reuse it everywhere.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: A `debounce(fn, delay)` function that returns a new function. Internally, it uses `clearTimeout` and `setTimeout`. The timeout ID is stored in a **closure** variable. Call `fn.apply(this, args)` to preserve context and arguments.
</details>

---

### Event Delegation

Many interactive elements (country cards, wishlist hearts, trip delete buttons, dropdown items) are created dynamically. Rather than attaching individual event listeners, use a single listener on the parent container.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **Event delegation** — attach one listener on a parent that exists at page load. Use `event.target.closest('.some-class')` to find the clicked element. If `closest` returns `null`, the click wasn't on a relevant element — just return. Read `data-*` attributes from the found element to identify which item was clicked.
</details>

---

### Responsive Design

The country card grid adapts: 4 columns on desktop, 3 on tablet landscape, 2 on tablet portrait, 1 on mobile. Use CSS Grid with media queries at 1024px, 768px, and 480px. The compare table should be horizontally scrollable on small screens. Navigation links should condense on mobile.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: CSS `@media (max-width: 768px) { ... }` breakpoints. `grid-template-columns: repeat(4, 1fr)` for the grid, changing the repeat count at each breakpoint. `overflow-x: auto` for the comparison table. Consider hiding the logo text on mobile to save space: `display: none`.
</details>

---

### Data Formatting

Population should display with K/M/B suffixes (1.3M, 450K). Area should include thousands separators and a km² unit. Strings that might be too long should be truncatable. Write small utility functions and import them where needed.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Conditional logic with numeric thresholds: `if (n >= 1_000_000) return (n/1_000_000).toFixed(1) + 'M'`. `Number.toLocaleString()` for thousands separators. A `truncate(str, maxLen)` function using `String.slice()`. The **numeric separator** `1_000_000` is valid JavaScript and improves readability.
</details>

---

### State Management

Wishlist and trip data need to be accessible from multiple pages and persist across sessions. Centralize this state in a dedicated module using the closure pattern with getter/setter functions.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: A factory function that returns an object with methods like `getWishlist()`, `addToWishlist(code)`, `removeFromWishlist(code)`, `isInWishlist(code)`. Internally, each method reads/writes to **localStorage**. Use `JSON.parse` and `JSON.stringify` for serialization. Wrap in `try/catch` for safety. This **closure pattern** keeps the implementation private and exposes a clean API.
</details>
