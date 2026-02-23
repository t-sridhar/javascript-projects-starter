# FoodDash — Food Discovery & Ordering App

Build a food discovery and ordering app from scratch using vanilla JavaScript, HTML, and CSS. Browse meals from around the world using TheMealDB API.

## Live Demo

[View the finished product](#) <!-- Link will be updated after deployment -->

Use the live demo to understand how the finished product should look and work. Explore every page, try every feature, and pay attention to the details.

## Setup

1. Navigate to this directory in your terminal
2. Run `npx serve .` to start a local dev server
3. Open the URL shown in your terminal (usually `http://localhost:3000`)

> **Note:** You must use a local server (not open the HTML file directly) because `fetch()` doesn't work with the `file://` protocol.

## Pages

| Page | File | Description |
|------|------|-------------|
| Home | `index.html` | Hero with search bar, featured categories, popular meals |
| Menu | `menu.html` | Category pills, search, sort dropdown, meal grid, load more |
| Meal Detail | `meal.html` | Full meal info, ingredients, instructions, related meals |
| Cart | `cart.html` | Cart items with quantity controls, order summary, checkout |
| Order | `order.html` | Order confirmation with ID, items, and totals |

## API

This project uses [TheMealDB](https://www.themealdb.com/api.php) — a free API with no authentication required.

| Endpoint | URL | Returns |
|----------|-----|---------|
| Categories | `https://www.themealdb.com/api/json/v1/1/categories.php` | All meal categories |
| By Category | `https://www.themealdb.com/api/json/v1/1/filter.php?c=Beef` | Meals in a category |
| Search | `https://www.themealdb.com/api/json/v1/1/search.php?s=chicken` | Search meals by name |
| Details | `https://www.themealdb.com/api/json/v1/1/lookup.php?i=52772` | Full meal details by ID |
| Random | `https://www.themealdb.com/api/json/v1/1/random.php` | A random meal |

## Color Theme

| Token | Value | Usage |
|-------|-------|-------|
| Primary | `#ef4444` | Buttons, badges, active states |
| Primary Dark | `#dc2626` | Hover states, text |
| Primary Light | `#f87171` | Highlights |
| Primary BG | `#fef2f2` | Light backgrounds |
| Accent | `#f97316` | Secondary actions |
| Accent Dark | `#ea580c` | Accent hovers |

## Guide

Open **`CONCEPTS.md`** for feature descriptions and progressive hints.

The hints are organized in 3 levels:
- **Level 1** (visible) — Feature description that hints at what to build
- **Level 2** (expandable) — Thinking questions to guide your approach
- **Level 3** (expandable) — Direct concept names when you're stuck

Try to solve each feature using only Level 1 first!
