# DevConnect — Concept Guide

> This document describes what each feature should do. Read carefully —
> the way a feature is described often hints at how to build it.
>
> If you get stuck, expand the hint sections for guidance.

---

## Flow 1: Post Feed

### 1.1 Fetching Posts from an API

When the feed page loads, it should request all available posts from an external API. Unlike the previous project where you loaded data from local JSON files, this project uses a real, live API — JSONPlaceholder. Your code needs to make an HTTP request to the API's URL, wait for the response, and then convert that response into a JavaScript array of post objects. Since network requests take time, you need a way to write code that waits for the request to finish before continuing.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The browser has a built-in function for making HTTP requests that returns something you can "wait" on. How can you write code that pauses execution until a network request completes, without blocking the entire page? What happens if the network request fails — how do you handle that gracefully?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `fetch()` to make HTTP requests to `https://jsonplaceholder.typicode.com/posts`. Use `async/await` to pause execution until the response arrives. Call `.json()` on the response to parse the JSON body. Wrap in `try/catch` to handle network errors.
</details>

---

### 1.2 Fetching User Avatars

Each post has a `userId` field, and you need to display the author's avatar next to their post. User avatars come from a different API (RandomUser) than the post data (JSONPlaceholder). Both requests are independent of each other — neither needs the other's result to start. Rather than making one request, waiting for it, then making the second, you should fire both requests at the same time and wait for both to finish together.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: If you have two independent Promises, is there a way to run them simultaneously and wait for both to resolve? What array method transforms one array into another — for example, turning an array of user results into an array of just their avatar URLs?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Promise.all()` to fetch posts and avatars simultaneously — `const [posts, avatars] = await Promise.all([fetchPosts(), fetchAvatars()])`. Use `Array.map()` to extract avatar URLs from the RandomUser API response. Combine users with avatars using the **spread operator** (`{ ...user, avatar: avatarUrl }`).
</details>

---

### 1.3 Rendering Post Cards

Each post should appear as a card showing the author's avatar, name, timestamp, title, a truncated body preview, and action buttons for like, comment, and bookmark. These cards must be generated entirely in JavaScript from the post data — do not write card HTML by hand. You have an array of post objects and need to transform each one into a visible HTML card on the page.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You have an array of objects. You need to transform each object into an HTML string. What array method is designed for transforming every item in an array into something new? How can you build HTML strings that insert variable values (like the post title, author name, avatar URL) into fixed markup?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.map()` to transform each post object into an HTML string. **Template literals** (backtick strings with `${expression}`) to build the HTML. Use `Array.join('')` to combine all the card strings, then set them with `element.innerHTML` or `element.insertAdjacentHTML()`. Use `Array.find()` to look up the author for each post by matching `post.userId` to a user's `id`.
</details>

---

### 1.4 Infinite Scroll

Instead of showing all 100 posts at once or using pagination buttons, the feed should load posts in small batches. When the user scrolls near the bottom of the currently visible posts, the next batch should load automatically. You need a way to detect when a particular element (a "sentinel" at the bottom of the feed) becomes visible in the viewport, without constantly checking its position on every scroll event.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: There is a browser API specifically designed to watch when an element enters or leaves the viewport. It is more efficient than listening to scroll events and manually calculating positions. What do you call the invisible element that sits at the bottom of your content and acts as a trigger point?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **IntersectionObserver** — create one that watches a sentinel `<div>` at the bottom of your posts list. When the sentinel enters the viewport (or gets close, using `rootMargin`), call a function that renders the next batch of posts. Use `Array.slice(start, end)` to extract the next batch from your full array. Track the current count with a variable in a **closure**.
</details>

---

### 1.5 Post Search

The search input in the hero section should filter posts as the user types. Posts should be filtered by whether the search term appears in their title or body. However, searching on every single keystroke is wasteful — if the user types "javascript" quickly, you don't need to filter for "j", then "ja", then "jav", etc. The search should wait until the user pauses typing before performing the filter.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How can you delay a function call so it only runs after the user stops typing for a brief moment? What happens to the previously scheduled call when a new keystroke arrives? Once you have the search term, what array method keeps only the items that match a condition?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **Debouncing** — wrap your search function with `setTimeout` and `clearTimeout` so it only runs after a pause (e.g., 300ms). This pattern uses **closures** to remember the timeout ID between calls. Use `Array.filter()` to keep only posts where `post.title` or `post.body` includes the search term. Convert both to lowercase with `String.toLowerCase()` for case-insensitive matching.
</details>

---

### 1.6 Sorting Posts

Users should be able to sort the feed by "Latest" (newest first), "Most Commented," or "Alphabetical." When the sort option changes, the same filtered dataset should rearrange itself and re-render. The comparison logic is different for each sort option.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How do you rearrange an array based on comparing its items? The comparison logic changes depending on which sort option is selected. How can you store multiple comparison strategies and pick the right one dynamically? For alphabetical comparison, the browser has a built-in method that handles locale-aware string comparison.
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.sort()` with a custom comparator function. For date sorting: compare `Date` objects by subtraction. For numeric sorting: `(a, b) => b.commentCount - a.commentCount`. For alphabetical: `a.title.localeCompare(b.title)`. Consider using an **object** keyed by sort type to store comparator functions and look them up dynamically with bracket notation.
</details>

---

### 1.7 Trending Tags

The sidebar should display popular "tags" extracted from post titles. These are not pre-defined — your code should analyze all post titles, break them into individual words, count how many posts each word appears in, filter out short or common words, and display the top results as clickable pill-shaped buttons. Clicking a tag should filter the feed to show only posts containing that word.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How do you split a sentence into individual words? What data structure is perfect for storing key-value pairs where the key is a word and the value is its count? How do you sort entries by their count value and take only the top results? For ignoring common words like "the", "and", "with" — what collection type lets you efficiently check membership?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Use a **regex** like `str.match(/[a-z]+/g)` to extract words from titles. Use a **Map** to count word occurrences — `map.set(word, (map.get(word) || 0) + 1)`. Use a **Set** of stop words for fast exclusion. Convert the Map to an array with `[...map.entries()]`, then `Array.sort()` by count descending and `Array.slice(0, N)` for the top tags.
</details>

---

### 1.8 Relative Timestamps

Posts should display timestamps in a human-readable relative format: "just now", "5m ago", "2h ago", "3d ago". Since JSONPlaceholder does not include timestamps, you will need to generate simulated dates for each post. The formatting logic should compare the post's date to the current time and choose the appropriate unit.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: If you subtract one Date from another in JavaScript, what do you get? How do you convert that raw difference into minutes, hours, or days? What kind of conditional logic picks the right format — "m" if under 60 minutes, "h" if under 24 hours, etc.?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `new Date()` for the current time. Subtract dates to get milliseconds: `now - postDate`. Divide by 1000 for seconds, by 60 for minutes, by 60 again for hours, by 24 for days. Use a chain of **if/else** statements or **conditional (ternary) operators** to pick the right format string. Use **template literals** to insert the number: `` `${minutes}m ago` ``.
</details>

---

## Flow 2: Post Detail & Comments

### 2.1 Reading Post ID from URL

When a user clicks on a post card in the feed, they navigate to `post.html?id=5`. The post detail page needs to read the `id` value from the URL to know which post to load. This value lives in the URL's query string — the part after the `?`.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The browser provides an API specifically for parsing query strings. How do you access the current page's URL? Once you have a parser for the query string, how do you retrieve a specific parameter by its key name?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **URLSearchParams** — create one with `new URLSearchParams(window.location.search)`. Call `.get('id')` to retrieve the value. Convert to a number with `Number()`. Always check if the value exists — if not, show an error message instead of attempting to fetch.
</details>

---

### 2.2 Fetching Post + Comments

The post detail page should fetch both the post content and its comments. These are two independent API calls — one for the post and one for its comments. Since neither depends on the other, you can make both requests at the same time. Once both arrive, render the post in full (with the complete body text, not truncated) and the author's information.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You already learned how to run two fetch calls simultaneously in Flow 1. The same technique works here — one call for the post, one for comments. After both resolve, you also need the author data. Could you fetch that as part of the same batch, or does it depend on knowing the post's `userId` first?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Promise.all()` with `fetch` for the post and comments endpoints simultaneously: `const [post, comments] = await Promise.all([fetchPost(id), fetchComments(id)])`. Then fetch the author separately using `post.userId`. Use `async/await` throughout. Render the full post body — use `String.split('\n')` and `Array.map()` to convert newlines into `<p>` tags.
</details>

---

### 2.3 Rendering Comments

Below the post, display all comments as a list. Each comment should show the commenter's name, email, and body text. The comments come as an array from the API. You need to transform each comment object into visible HTML.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You have an array of comment objects and need to produce an HTML card for each one. You used this same transformation pattern when rendering post cards. What array method transforms every item?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.map()` to transform each comment into an HTML string using **template literals**. Use `.join('')` to combine them. Consider extracting the first letter of the email with `String.charAt(0).toUpperCase()` to create a simple initial-based avatar.
</details>

---

### 2.4 Adding a Comment

Below the comments list, provide a form where users can type their name, email, and comment text. When submitted, the form should validate that all fields are filled (and the email looks valid), then send the data to the API. JSONPlaceholder accepts POST requests and returns a simulated response — the comment won't actually be saved, but you should treat the response as if it was. After a successful "post," add the new comment to the top of the list immediately without refreshing the page.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How do you prevent the form from actually navigating to a new page when submitted? For validation, how do you test if a string matches a specific pattern (like an email address with an @ sign and domain)? For the POST request, `fetch` takes a second argument where you can specify the HTTP method and body. After success, how do you insert new HTML at the beginning of an existing list?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `event.preventDefault()` to stop form submission. **Regex** for email validation — e.g., `/^[^\s@]+@[^\s@]+\.[^\s@]+$/`. Use `fetch(url, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(data) })`. After success, use `element.insertAdjacentHTML('afterbegin', newCommentHTML)` for **optimistic UI update** — showing the comment immediately without waiting for a page reload. Reset the form with `form.reset()`.
</details>

---

### 2.5 Post Interactions — Like & Bookmark

The post detail page should have "Like" and "Bookmark" buttons. Clicking "Like" should toggle the heart icon between filled and outlined, and update the like count. Clicking "Bookmark" should do the same for the bookmark icon. These interactions should be remembered — if the user leaves and comes back, a previously liked post should still show as liked. You need a way to track which post IDs have been liked and which have been bookmarked, using a collection that prevents duplicates.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: What data structure automatically prevents duplicate entries? If a user likes post 5 twice, it should toggle off — not add it again. Where can you store this data so it survives page refreshes and browser restarts? That storage mechanism only works with strings — how do you save a collection of unique IDs and load it back?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Use a **Set** to track liked post IDs and a separate **Set** for bookmarked IDs. `Set.has(id)` to check, `Set.add(id)` and `Set.delete(id)` to toggle. Persist to **localStorage** by converting to an array with `Array.from(set)` or `[...set]`, then `JSON.stringify()`. Load back with `new Set(JSON.parse(...))`. Use `element.classList.toggle()` to update the visual state.
</details>

---

### 2.6 Related Posts

Below the comments section, display a few posts by the same author. Fetch all posts by the post's author, exclude the current post, and show up to 3 results as smaller post cards that link to their respective detail pages.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: The API has an endpoint to fetch all posts by a specific user. Once you have them, how do you remove the current post from the list? How do you limit the results to just 3?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Fetch from `/users/:id/posts` using the post's `userId`. Use `Array.filter()` to exclude the current post: `posts.filter(p => p.id !== currentPostId)`. Use `Array.slice(0, 3)` to limit to 3 results. Render them using the same post card function you built for the feed.
</details>

---

## Flow 3: User Profiles & Social

### 3.1 User Profile Page

When a user navigates to `profile.html?id=3`, the page should load that user's profile information from the API and display it: name, username, bio (from the company's catch phrase), company name, location (city), and website. The user ID comes from the URL — the same technique you used for the post detail page.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You already know how to read an ID from the URL query string. The API returns a user object with nested data — the company name is inside a `company` object, and the city is inside an `address` object. How do you safely access a property that might not exist without causing an error?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `URLSearchParams` to read `?id=3` from the URL. Fetch from `/users/:id`. Access nested properties safely with **optional chaining**: `user.company?.name`, `user.address?.city`. This returns `undefined` instead of throwing an error if `company` or `address` is missing.
</details>

---

### 3.2 User's Posts

Below the profile information, display all posts written by this user. Fetch them from the API using the user-specific posts endpoint and render them as post cards — the same cards used on the feed page.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need the user data and the user's posts. These are two independent API calls. Can you fetch them simultaneously? For rendering, you should reuse the same post card creation logic from the feed — how do you share code between pages?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Promise.all()` to fetch the user and their posts at the same time. Use `Array.map()` to render each post with the same `createPostCard` function. If the user has no posts, show a friendly empty state message. Import shared functions across files using `export`/`import` with **ES modules**.
</details>

---

### 3.3 Follow/Unfollow Toggle

The profile page should have a "Follow" button. Clicking it toggles to "Following" (with different styling), and clicking again toggles back to "Follow." The follow state should persist across page loads — if the user follows someone and comes back later, the button should still show "Following." The same collection-of-IDs pattern you used for likes works here.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You need to track which user IDs are followed using a collection that prevents duplicates. You already built this pattern for likes and bookmarks. How can you reuse the same approach? The button's text and CSS class both need to update based on the current state.
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: A **Set** of followed user IDs, persisted to **localStorage** with `JSON.stringify(Array.from(set))`. Toggle with `Set.has()`, `Set.add()`, `Set.delete()`. Update the button with `element.textContent` and `element.classList.add()`/`element.classList.remove()` to swap between "Follow" and "Following" styles.
</details>

---

### 3.4 Explore Users Page

The Explore page shows a directory of all developers. Fetch all users and their avatars, then display each as a user card showing avatar, name, username, company, post count, and a "View Profile" link. The user data and avatar data come from two different APIs, so fetch them simultaneously.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: To show post counts per user, you also need the posts data. That is a third API call — but it is independent of the others. Can you run all the fetches at once? Once you have posts, how do you count how many belong to each user?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Promise.all()` to fetch users, avatars, and posts simultaneously. Count posts per user with a **Map**: iterate posts with `forEach`, using `map.set(userId, (map.get(userId) || 0) + 1)`. Combine users with avatars and post counts using `Array.map()` and the **spread operator**. Render cards with **template literals**.
</details>

---

### 3.5 Search Users

The Explore page should have a search input that filters the user cards by name or username as the user types. Like the post search, it should wait until the user pauses typing before filtering to avoid unnecessary work.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: This is the same debounce + filter pattern you used for post search. What changes? The fields you search are `name` and `username` instead of `title` and `body`. Can you use the same debounce function for both the post search and the user search?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Reuse your **debounce** utility function. Use `Array.filter()` to match against `user.name` and `user.username`. For flexible matching, create a **RegExp** from the search term: `new RegExp(searchTerm, 'i')` for case-insensitive matching. Escape special regex characters in user input with `String.replace(/[.*+?^${}()|[\]\\]/g, '\\$&')`.
</details>

---

## Flow 4: Content Creation

### 4.1 Create Post Form

The Create Post page should have a form with a title input and a body textarea. Below each field, display a live character count that updates as the user types. The form should validate on submission: the title must be at least 5 characters and the body at least 20. Display error messages below each field if validation fails.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: How do you react to every keystroke the user makes in an input field? What event fires on each character change? For character count, the string's `length` property gives you the count. For validation, you need to check conditions and display appropriate messages — what happens when the user starts typing after seeing an error?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: The `input` event on text fields — it fires on every change (typing, pasting, deleting). Access the value with `event.target.value` or `element.value`. Use `String.length` for character count. Update a count element with `element.textContent`. For validation, use `String.trim().length` to ignore whitespace. Show errors with `element.textContent = 'Error message'` and clear them when the user starts fixing.
</details>

---

### 4.2 Live Preview

As the user types in the title and body fields, a preview card on the side (or below) should update in real time, showing exactly how their post will look. When the fields are empty, show placeholder text. This provides immediate visual feedback without any button clicks.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You are already listening to the `input` event for character counts. In that same listener, you can also update the preview. Where should the preview text come from — the input's current value. What should the preview show when the input is empty?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: In your `input` event listener, set `previewElement.textContent = inputElement.value || 'Placeholder text...'`. The `||` operator provides a fallback when the value is an empty string (which is falsy). This is a simple form of **real-time DOM manipulation** — reading from one element and writing to another on every input event.
</details>

---

### 4.3 Form Submission

When the user submits the form (and validation passes), send the post data to the API using a POST request. JSONPlaceholder accepts the request and returns a simulated response with an assigned ID. During the request, disable the submit button and change its text to "Publishing..." to prevent double-submission. On success, show a notification and redirect the user back to the feed. On failure, re-enable the button and show an error.

<details>
<summary><strong>Hint Level 2</strong></summary>

Think about: You already used `fetch` with POST for adding a comment. The same approach works here. How do you disable a button? How do you navigate the user to a different page with JavaScript? Should the redirect happen immediately or after a short delay so the user can see the success message?
</details>

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `fetch(url, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(data) })` for the POST request. `button.disabled = true` to prevent double-clicks. `window.location.href = './index.html'` for redirect. Use `setTimeout` to delay the redirect so the success toast is visible. Wrap in `try/catch` — in the `catch` block, re-enable the button.
</details>

---

## Cross-Cutting Concepts

### Page Routing

The app has 5 HTML pages. Your entry-point JavaScript file (`app.js`) should detect which page is currently loaded and initialize only the relevant code. Each page's `<body>` element has a `data-page` attribute (e.g., `data-page="feed"`) to identify it. Read this attribute and call the correct initialization function.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `document.body.dataset.page` to read the current page name. Store page-init functions in an **object** keyed by page name — e.g., `{ feed: initFeed, post: initPost, ... }`. Call the right one: `pageMap[currentPage]?.()`. The `?.` is **optional chaining** — it prevents an error if the page name is not in the object.
</details>

---

### Toast Notifications

The app should show brief, auto-dismissing notification messages (e.g., "Post liked!", "Comment posted!", "Failed to load data") that slide in from a corner and disappear after a few seconds. Different types of messages (success, error, info) should have different colors.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `document.createElement('div')` to create the toast element dynamically. Append it to a fixed-position container. Use CSS transitions or `@keyframes` for slide-in and slide-out animations. `setTimeout` to auto-remove after 3 seconds. Pass a `type` parameter to set the CSS class: `toast.className = 'toast toast--' + type`.
</details>

---

### Error Handling

Every API call can fail — the server might be down, the network could drop, or the user might request an ID that does not exist. Every `fetch` call should handle errors gracefully: show a user-friendly message and never leave the user staring at a blank page or an unhandled console error.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `try/catch` blocks around every `await fetch(...)`. Check `response.ok` before calling `.json()` — if the status is not 2xx, throw an error. In the `catch` block, show a toast notification or render an error message in the DOM. Always hide loading indicators in both the success and error paths (consider using `finally`).
</details>

---

### localStorage for Interactions

Likes, bookmarks, and follows all need to persist across page loads and browser sessions. Each is a collection of unique IDs. You need a consistent pattern for saving and loading these collections from browser storage, keeping in mind that browser storage only works with strings.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: Use a **Set** for each collection (liked post IDs, bookmarked post IDs, followed user IDs). **localStorage** only stores strings, so convert with `JSON.stringify(Array.from(set))` when saving and `new Set(JSON.parse(stored))` when loading. Create reusable `getStorage(key, default)` and `setStorage(key, value)` wrapper functions. Wrap in `try/catch` because localStorage can be disabled or full.
</details>

---

### State Management

Multiple parts of the app need access to shared data — likes, bookmarks, follows. Rather than scattering variables across files or using globals, create a single state manager with controlled access. External code should not be able to directly modify the internal state — only through defined methods like `toggleLike(id)`, `isLiked(id)`, etc.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: The **closure pattern** — a factory function that declares private variables (the Sets) and returns an object with public methods (`isLiked`, `toggleLike`, `getLikeCount`, etc.). The private variables are captured by the returned methods and persist in memory, but cannot be accessed directly from outside. This is sometimes called the **module pattern** or **revealing module pattern**.
</details>

---

### Formatting Utilities

You will need several formatting functions used across multiple pages: converting timestamps to relative time ("5m ago"), truncating long text with an ellipsis, converting strings to title case. Write these as small, reusable utility functions in one file and import them wherever needed.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: For relative time: **Date math** — subtract dates, divide by 1000/60/60/24 for seconds/minutes/hours/days. For truncation: `String.slice(0, maxLength)` plus `'...'` when the string exceeds the limit. For title case: `String.split(' ')` then `Array.map()` each word, capitalizing with `String.charAt(0).toUpperCase() + String.slice(1)`, then `Array.join(' ')`.
</details>

---

### Suggested Users Sidebar

The feed page should have a sidebar showing a few "suggested developers" to follow. Select a subset of users (e.g., the first 5) and render them as compact cards with avatar, name, username, and a follow button. The follow button should work the same way as on the profile page — toggling between "Follow" and "Following" and persisting the state.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `Array.slice(0, 5)` to pick a subset of users. Render with `Array.map()` and **template literals**. Reuse the same follow state (Set in localStorage) that the profile page uses. Attach click handlers using **event delegation** — one listener on the parent container, check `event.target.closest('[data-action="follow"]')` to identify the clicked button.
</details>

---

### Event Delegation

Many parts of the app have dynamically generated elements — post cards, comment cards, tag pills, follow buttons. Since these elements are created after the page loads, you cannot attach listeners to them during initialization. Instead, attach a single listener on a parent element that already exists, and use the event's target to determine which child element was clicked.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: **Event delegation** — attach one `click` listener on the container element. Use `event.target.closest('.post-card')` or `event.target.closest('[data-action]')` to find the relevant ancestor element. Read data attributes with `element.dataset.postId` or `element.dataset.action` to determine what was clicked and what to do.
</details>

---

### Custom Events

When one part of the app changes state (e.g., a post is liked from a card in the feed), other parts might need to know (e.g., the stats sidebar should update the like count). Rather than tightly coupling these components, you can use the browser's built-in event system to broadcast state changes.

<details>
<summary><strong>Hint Level 3</strong></summary>

Look into: `document.dispatchEvent(new CustomEvent('stateChange', { detail: { type: 'like', postId: 5 } }))` to broadcast. `document.addEventListener('stateChange', handler)` to listen. The `detail` property carries custom data. This is the **observer pattern** — components subscribe to events without knowing who publishes them.
</details>
