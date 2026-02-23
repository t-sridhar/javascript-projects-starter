# FoodDash — Concept Guide

> This document describes what each feature should do. Read carefully —
> the way a feature is described often hints at how to build it.
>
> If you get stuck, expand the hint sections for guidance.

---

## Flow 1: Food Browsing

### 1.1 Fetching Meal Categories

The home page should display a grid of food categories (like Beef, Chicken, Seafood, etc.) with thumbnail images. These categories are not hardcoded — they come from an external API. Your code should request the list of categories when the page loads, and once the response arrives, transform each category object into a visible card element.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How do you request data from a URL and wait for the response? The response comes as text — how do you convert it into JavaScript objects you can work with? What should the user see while waiting for the data to arrive?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: The `fetch()` API returns a **Promise**. Use `async/await` to wait for the response, then `.json()` to parse it. Show a loading spinner before the request and hide it after. Wrap everything in **try/catch** for error handling.
</details>

---

### 1.2 Rendering Category Cards

Each category from the API has a name and a thumbnail URL. For each category, you need to create a card element containing an image and the category name. These cards should be clickable — clicking one should take the user to the menu page filtered by that category.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You have an array of category objects. You need to create a DOM element for each one. What array method transforms each item into something new? How do you pass the category name to the next page so it knows what to load?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.forEach()` or `Array.map()` to iterate over categories. Use `document.createElement()` or `innerHTML` with **template literals** to build card HTML. Pass the category to the next page via **URL query parameters**: `menu.html?category=Beef`.
</details>

---

### 1.3 Fetching Meals by Category

When the menu page loads, it should check the URL for a `category` parameter and then fetch all meals belonging to that category from the API. The meals endpoint returns an array of meal summaries (ID, name, thumbnail).

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How do you read values from the current page's URL? After the `?` in a URL, key-value pairs follow a standard format. JavaScript has a built-in class for parsing these.
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `new URLSearchParams(window.location.search).get('category')` to read the category from the URL. Then use `fetch()` with that value in the API URL. Use **template literals** to build the endpoint string dynamically.
</details>

---

### 1.4 Rendering Meal Cards

Each meal from the API should become a card showing the meal image, name, a generated price, and an "Add to Cart" button. The card's image and title should link to a detail page where the full meal information is displayed.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The API doesn't include prices. How could you generate a consistent price from the meal's ID number so the same meal always shows the same price? What mathematical operations could convert an ID into a reasonable dollar amount?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Using the **modulo operator** (`%`) on the numeric meal ID to generate a price within a range: `8.99 + (parseInt(id) % 15) * 1.5`. Use `Intl.NumberFormat` for currency formatting. Link to `meal.html?id=MEAL_ID`.
</details>

---

### 1.5 Searching Meals

Both the home page hero and the menu page have search inputs. The home page search should navigate to the menu page with the query in the URL. The menu page search should filter meals in real time as the user types — but you should be careful not to make an API request on every single keystroke.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How can you delay a function call so it only runs after the user pauses typing? What happens to the previous pending call when a new keystroke arrives? This prevents excessive API calls.
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **Debouncing** — wrapping a function with `setTimeout` and `clearTimeout` so it only executes after a pause. The home page form uses `event.preventDefault()` on submit and navigates via `window.location.href`. The menu page uses a debounced input handler.
</details>

---

### 1.6 Loading Popular Meals

The home page should show a "Popular Meals" section. Since the API has no "popular" endpoint, you can fetch meals using a common search term (like "chicken") and display the first 8 results. These meal cards should have the same structure as menu page cards, with Add to Cart functionality.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You have an array of meals but only want to show a portion of them. What array method extracts a subset from the beginning of an array without modifying the original?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.slice(0, 8)` to take the first 8 meals. Reuse the same meal card creation function from the menu page. Use `Promise.all()` if loading multiple sections in parallel.
</details>

---

## Flow 2: Menu & Filtering

### 2.1 Reading URL Parameters

The menu page needs to behave differently depending on how the user arrived. If they clicked a category card, the URL has `?category=Beef`. If they searched from the home page, it has `?search=chicken`. Your page initialization should check for these parameters and load the appropriate data.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The URL can contain different parameters. How do you check which parameter exists? What if neither is present — what should the page default to?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `URLSearchParams` to read both `category` and `search` parameters. Use **conditional logic** (if/else) to decide which API endpoint to call. Default to loading a category like "Beef" if no parameter is present.
</details>

---

### 2.2 Category Pill Filtering

The menu page should show clickable "pill" buttons for each food category. When a pill is clicked, it should visually highlight as active and reload the meal grid with meals from that category. Only one pill should be active at a time.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: Rather than attaching a click listener to every individual pill, is there a more efficient pattern? If you put one listener on the parent container, can you figure out which pill was clicked?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **Event delegation** — attach a single listener on the pill container, use `event.target.closest('.category-pill')` to find the clicked pill. Use `classList.remove()` on all pills and `classList.add()` on the clicked one. Store the category in a `data-category` attribute.
</details>

---

### 2.3 Custom Sort Dropdown

The sort control should NOT use a native `<select>` element. Instead, build a custom dropdown using a button trigger, a hidden `<ul>`, and JavaScript to open/close it. Clicking the trigger toggles the dropdown. Clicking an option selects it, updates the trigger text, and closes the dropdown. Clicking outside should also close it.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need a button that toggles visibility of a list. When an option is clicked, you need to update multiple things: a hidden input, the display text, and visual active state. How do you detect clicks outside a specific element?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Toggle the dropdown with `element.removeAttribute('hidden')` / `element.setAttribute('hidden', '')`. Use `event.stopPropagation()` on the trigger click. Add a `document.addEventListener('click')` that closes the dropdown when the click is outside the wrapper. Use `element.closest()` to check containment.
</details>

---

### 2.4 Sorting Meals

When the user selects a sort option (like "Name: A-Z"), the meal grid should re-render with meals in the new order. The sorting happens client-side on the already-fetched array of meals. After sorting, you need to clear the grid and re-build it from the sorted data.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How do you rearrange an array based on a comparison between items? The sort works on meal names (strings). How do you compare two strings alphabetically?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.sort()` with a custom comparator. For alphabetical sorting, use `String.localeCompare()`: `(a, b) => a.strMeal.localeCompare(b.strMeal)`. Sort a **copy** of the array (use spread `[...meals]`) to avoid mutating the original. Clear the grid with `innerHTML = ''` before re-rendering.
</details>

---

### 2.5 Load More / Pagination

Don't show all meals at once. Display 12 meals initially and show a "Load More" button. Each click appends 12 more meals to the grid. When all meals have been shown, hide the button.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need to track how many meals are currently displayed. Each "Load More" click should extract the next batch from the array starting where you left off. What array method gives you a slice without modifying the original?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Keep a `displayedCount` variable. Use `Array.slice(displayedCount, displayedCount + 12)` for each batch. **Append** new cards to the grid (don't clear it). Compare `displayedCount` to the total array length to decide if the button should be visible.
</details>

---

### 2.6 Updating the URL Without Reloading

When the user clicks a category pill on the menu page, the URL should update to reflect the new category — but the page should NOT fully reload. This makes the URL shareable and supports the browser back button.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The browser has an API for changing the URL in the address bar without triggering a page navigation. Which method adds a new history entry vs. replacing the current one?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `window.history.replaceState(null, '', newUrl)` to update the URL without reloading. Build the new URL with the category parameter: `./menu.html?category=${categoryName}`.
</details>

---

## Flow 3: Meal Details

### 3.1 Fetching Meal by ID

The meal detail page reads a meal ID from the URL and fetches the full meal object. The full object contains much more data than the category listing — including instructions, ingredients, measures, area of origin, tags, and a YouTube link.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The lookup endpoint returns an object with many properties. Some are null or empty strings. How do you safely access deeply nested data that might not exist? What should you show if the API returns no meal?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `URLSearchParams` to read the `id` parameter. `fetch()` the lookup endpoint. Check if the result is null and show an error state. Use **optional chaining** (`?.`) and **nullish coalescing** (`??`) for safe access to potentially missing fields.
</details>

---

### 3.2 Rendering Meal Detail

The full detail view should show the meal image prominently, along with the title, category badge, area of origin badge, a generated price, and an "Add to Cart" button. The layout should be two-column on desktop (image left, info right) and stack on mobile.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You're building a large chunk of HTML from JavaScript. Template literals with multiple lines and embedded expressions work well here. How do you conditionally include a section (like the YouTube link) only when the data exists?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **Template literals** with conditional expressions using the **ternary operator**: `${meal.strYoutube ? '<a href="...">Watch</a>' : ''}`. Use `element.innerHTML` to set the full detail HTML at once. CSS Grid for the two-column layout.
</details>

---

### 3.3 Parsing Ingredients

TheMealDB stores ingredients in a peculiar way: `strIngredient1` through `strIngredient20` and `strMeasure1` through `strMeasure20`. You need to loop through these numbered properties and build a clean list of ingredient-measure pairs, stopping when you hit an empty ingredient.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The property names follow a pattern with a changing number. How can you dynamically construct a property name from a base string and a number? What kind of loop lets you iterate from 1 to 20?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: A `for` loop from 1 to 20. Access properties dynamically with **bracket notation**: `meal['strIngredient' + i]` or with template literals `meal[\`strIngredient${i}\`]`. Check if each ingredient is truthy and has content after `.trim()`. Push valid pairs into an array.
</details>

---

### 3.4 Related Meals

Below the meal detail, show a "You Might Also Like" section with meals from the same category. Fetch meals by the current meal's category, filter out the current meal, and display the first 4 as cards.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need to remove one specific item from an array (the current meal). What array method creates a new array with items that pass a test? How do you compare meal IDs to exclude the right one?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.filter()` to exclude the current meal: `meals.filter(m => m.idMeal !== currentId)`. Then `Array.slice(0, 4)` for the first 4. Reuse your meal card component. If the category is missing or no related meals exist, hide the section entirely.
</details>

---

### 3.5 Adding to Cart from Detail Page

The meal detail page has an "Add to Cart" button. Clicking it should add the meal to the cart state and show a confirmation toast. The cart badge in the header should update immediately to reflect the new count.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The cart data needs to persist across page navigations. You need to store the meal's ID, name, price, and thumbnail. How do you update a UI element (the badge) that exists in the header on every page?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Call your cart state's `addItem()` method with a meal object. Query the DOM for the cart badge element and update its `textContent`. Use `removeAttribute('hidden')` to show the badge. The toast is a dynamically created element that auto-removes after a timeout.
</details>

---

## Flow 4: Cart & Ordering

### 4.1 Closure-Based Cart State

The cart needs a central state manager. Rather than scattering cart data in global variables, create a function that returns an object of methods (addItem, removeItem, updateQuantity, getItems, getTotal, getCount, clear) while keeping the actual items array private.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: A function can define local variables, then return an object whose methods reference those variables. Even after the function returns, the methods still have access to those variables. What is this pattern called?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: The **closure pattern**. A factory function `createCartState()` defines `let items = []` and returns `{ getItems: () => [...items], addItem: (meal) => { ... }, ... }`. The returned methods "close over" the `items` variable, keeping it private. Export a singleton instance.
</details>

---

### 4.2 LocalStorage Sync

The cart must survive page reloads. Every time the cart changes (add, remove, update quantity, clear), the new state should be saved. When the cart state initializes, it should load existing data.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The browser has a storage mechanism that persists data across page loads. It only stores strings, not objects. How do you convert an array of objects to a string for storage, and back again on retrieval? What if the stored data is corrupted?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **localStorage** with `JSON.stringify()` for saving and `JSON.parse()` for loading. Wrap both in **try/catch** blocks — `JSON.parse()` throws on invalid data. Initialize the items array from localStorage in the factory function: `let items = load() || []`.
</details>

---

### 4.3 Rendering Cart Items

The cart page should display each item as a row with a thumbnail image, title, per-unit price, quantity controls (plus/minus buttons), a line total, and a remove button. If the cart is empty, show a friendly empty state with a link to browse the menu.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need to create HTML for each cart item. The quantity buttons and remove button all need to know which item they affect. How do you store item identification data on a button element so click handlers can read it?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Use `data-*` attributes on buttons: `data-item-id="${item.id}" data-action="increase"`. Use **event delegation** on the parent container — one click handler checks `event.target.closest('[data-action]')` to find the action and item ID.
</details>

---

### 4.4 Quantity Controls & Order Totals

The plus button should increase quantity by 1. The minus button should decrease by 1, but when quantity reaches 1, another minus should remove the item. The order summary (subtotal, tax, total) should update after every change. Tax is calculated at 8% of the subtotal.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: After any quantity change, you need to recalculate the total across all items. Each item's contribution is price times quantity. What array method combines all items into a single sum?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.reduce()` to sum up `item.price * item.quantity` for all items. After updating quantities, re-render the entire cart (clear and rebuild) to keep DOM and state in sync. Use `Math.max(1, newQty)` to prevent quantities below 1, or remove the item.
</details>

---

### 4.5 Checkout Flow

The checkout button should save the order data (items, subtotal, tax, total, a generated order ID) to localStorage, clear the cart, and navigate to the order confirmation page. The order page reads this data and displays it.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need to pass a complex data structure (the order) from the cart page to the order page. Since these are separate page loads, the data must survive navigation. Which browser storage mechanism should you use? How do you generate a unique order ID?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Save with `localStorage.setItem('orderData', JSON.stringify(order))`. Generate an order ID using `Date.now().toString(36)` combined with `Math.random().toString(36)` for uniqueness. Navigate with `window.location.href = './order.html'`. On the order page, read and display the data, then optionally clear it from storage.
</details>

---

### 4.6 Order Confirmation

The order page should display the order ID, date, a list of ordered items with thumbnails, and a price breakdown. If there is no order data in storage (e.g., user navigated directly), show an empty state. Include buttons to order again or return home.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How do you format a date object into a human-readable string? The order items need to be rendered as a list with images and totals — similar to cart items but without quantity controls.
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `new Date(isoString).toLocaleDateString()` with formatting options for a readable date. Use `Array.map()` with template literals to build the items HTML. `Intl.NumberFormat` for consistent currency formatting across the page.
</details>

---

## Cross-Cutting Concepts

### Page Routing

The app has 5 HTML pages. Your entry-point JavaScript file should detect which page is currently loaded and initialize only the relevant code. The `<html>` element has a `data-page` attribute to help with this.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need to read an attribute from the document element and then run different code depending on its value. What statement is designed for branching on multiple possible values?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `document.documentElement.dataset.page` to read the current page. Use a `switch` statement to call the right initialization function. For code splitting, use **dynamic imports**: `const { initHomePage } = await import('./home.js')`. This loads each page module only when needed.
</details>

---

### Toast Notifications

The app should show brief, auto-dismissing messages (e.g., "Added to cart!", "Item removed") that slide in from the side and disappear after a few seconds. Support different types: success (green), error (red), info (blue).

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need to dynamically create an element, style it based on type, add it to a container, and then automatically remove it after a delay. What functions handle timed operations?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Create a toast `<div>` dynamically with `document.createElement()`. Use CSS **@keyframes** animations for slide-in/slide-out. `setTimeout()` to auto-remove after 3 seconds. Add a close button for manual dismissal. Use a fixed-position container in the DOM.
</details>

---

### Debouncing

The search input should not fire an API call on every keystroke. Instead, wait until the user pauses typing for 300ms before making the request. This pattern is used whenever you need to throttle rapid-fire events.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: Each new keystroke should cancel the previous pending call and start a new timer. What two timer functions work together for this? Where do you store the timer ID so it persists between calls?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: A **debounce** function that uses a **closure** to remember the `timerId`. It calls `clearTimeout(timerId)` then `timerId = setTimeout(fn, delay)` on each invocation. Export it as a reusable utility.
</details>

---

### Event Delegation

Many elements in this app are created dynamically (meal cards, cart items, category pills). Attaching individual click handlers to each one is wasteful and error-prone. Instead, attach a single listener to a parent container and use the event to determine which child was clicked.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: When you click a child element, the click event "bubbles up" to the parent. On the parent's listener, how do you figure out which specific child (or grandchild) was the actual click target?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `event.target.closest('.some-class')` to find the nearest ancestor matching a selector. This works even if the click landed on a nested element (like an SVG inside a button). **Event delegation** — one listener on the parent instead of N listeners on children.
</details>

---

### Custom Dropdowns

The sort dropdown must be built entirely with HTML, CSS, and JavaScript — no native `<select>` elements. This means managing open/close state, click-outside-to-close behavior, keyboard accessibility, and active item tracking manually.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The dropdown is a `<ul>` that toggles between visible and hidden. You need three event listeners: one on the trigger button, one on the dropdown items, and one on the document to close when clicking outside. How do you prevent the trigger click from immediately triggering the document click?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Use `element.setAttribute('hidden', '')` and `element.removeAttribute('hidden')` to toggle visibility. `event.stopPropagation()` on the trigger prevents the document listener from firing. `event.target.closest('.wrapper')` in the document listener checks if the click was inside the dropdown.
</details>

---

### Responsive Design

The app should work on all screen sizes. The meal grid should go from 4 columns on desktop to 3 on tablet, 2 on small tablet, and 1 on mobile. The cart layout should switch from side-by-side to stacked on smaller screens.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: CSS provides a way to apply different styles based on the browser window width. What rule lets you define breakpoints? How does CSS Grid's `grid-template-columns` make responsive layouts straightforward?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `@media (max-width: 1024px)`, `@media (max-width: 768px)`, and `@media (max-width: 480px)` for breakpoint-based overrides. CSS Grid with `repeat(N, 1fr)` for the column count. Change `grid-template-columns` at each breakpoint. Use CSS **custom properties** (variables) so you can adjust font sizes globally at breakpoints.
</details>

---

### Formatting & Display

Currency should always display as "$12.49" format. Text that might be too long (like meal titles in cards) should be truncated with an ellipsis. Dates should be human-readable.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' })` for currency. CSS `-webkit-line-clamp` for multi-line text truncation. `Date.toLocaleDateString()` with formatting options for dates. Write small utility functions and reuse them everywhere.
</details>

---

### Error Handling

Every API call can fail (network issues, API down, invalid IDs). Every place that fetches data should handle failure gracefully — show a user-friendly error message, never leave the page blank or show only a spinner forever.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `try/catch` blocks around every `await fetch()` call. Check `response.ok` before parsing. Show toast notifications for errors. Have fallback empty states in the HTML that can be shown when data loading fails.
</details>
