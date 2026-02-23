# DevConnect — Developer Social Platform

Build a developer social platform from scratch using vanilla JavaScript, HTML, and CSS.

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
| Feed | `index.html` | Post feed with search, sort, trending tags, infinite scroll |
| Post Detail | `post.html` | Full post with comments, like/bookmark, related posts |
| Profile | `profile.html` | User profile, stats, follow toggle, user's posts |
| Explore | `explore.html` | Developer directory with search |
| Create Post | `create.html` | Post creation form with live preview |

## APIs

This project uses **real external APIs** — no local JSON files needed.

| API | Base URL | What It Provides |
|-----|----------|------------------|
| JSONPlaceholder | `https://jsonplaceholder.typicode.com` | Posts, users, and comments |
| RandomUser | `https://randomuser.me/api/` | Avatar images for users |

### Endpoints You Will Use

| Endpoint | Description |
|----------|-------------|
| `GET /posts` | Fetch all 100 posts |
| `GET /posts/:id` | Fetch a single post by ID |
| `GET /posts/:id/comments` | Fetch comments for a post |
| `GET /users` | Fetch all 10 users |
| `GET /users/:id` | Fetch a single user by ID |
| `GET /users/:id/posts` | Fetch all posts by a user |
| `POST /posts` | Create a new post (simulated — returns mock response) |
| `GET /api/?results=10&seed=devconnect` | Fetch 10 random user avatars (RandomUser) |

## Guide

Open **`CONCEPTS.md`** for feature descriptions and progressive hints.

The hints are organized in 3 levels:
- **Level 1** (visible) — Feature description that hints at what to build
- **Level 2** (expandable) — Thinking questions to guide your approach
- **Level 3** (expandable) — Direct concept names when you're stuck

Try to solve each feature using only Level 1 first!
