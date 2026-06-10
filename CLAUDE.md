# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

This repo contains a single self-contained static page: [index.html](index.html). It is a marketing/lead-generation landing page for "Meridian Wealth Advisors" (investment strategy consultation). There is no build system, package manager, server, or test suite — everything (HTML, CSS, JS) lives in this one file.

## Development workflow

- **Run/preview**: open `index.html` directly in a browser, or serve the directory with any static file server (e.g. `npx serve` or `python -m http.server`) if `fetch`/CORS behavior needs to be tested.
- **No build, lint, or test commands exist.** Validate changes by opening the page in a browser and checking the relevant section visually/functionally.
- All edits happen in place in `index.html` — there are no separate source files to keep in sync.

## Structure of index.html

The file is organized into three large blocks in this order:

1. **`<style>` block (head)** — all CSS, using custom properties defined on `:root` (`--primary`, `--secondary`, `--accent`, `--background`, `--text`, `--light-bg`, `--border`, `--nav-height`, `--transition`). Reuse these variables rather than hardcoding colors/spacing. Sections are styled with comment headers like `/* ===== Hero ===== */` — follow this convention for new sections. Responsive breakpoints are at 1024px, 900px, 768px, and 480px near the end of the stylesheet.
2. **`<body>` markup** — a single long page composed of sequential `<section>` elements, each with an `id` and `aria-label`, in this order: navbar, hero, why-us, process (timeline), testimonials, lead-magnet, enquiry (form), faq, final-cta, footer. Sections intended to animate in on scroll carry the `fade-in` class.
3. **`<script>` block (end of body)** — a single IIFE containing independent, self-contained behavior modules:
   - Sticky/scrolled navbar state (`scroll` listener toggling `.scrolled`)
   - Mobile nav toggle (`.menu-open` class on `#navbar`)
   - Smooth-scroll for in-page anchor links, offset by navbar height
   - FAQ accordion (single-open, toggles `.open` + inline `max-height`)
   - Scroll-triggered fade-ins via `IntersectionObserver` (with a no-JS/no-IO fallback that immediately reveals all `.fade-in` elements)
   - Enquiry form validation (name/email/phone via regex) and AJAX submission
   - Footer copyright year injection

## Enquiry form

The form (`#enquiryForm`) submits to **FormSubmit** (`https://formsubmit.co/wonderb3rry@hotmail.com`), with the JS rewriting the action to the `/ajax/` endpoint for a fetch-based submission so the page can show inline success/error messages (`#formSuccess` / `#formError`) without a redirect. Hidden fields configure FormSubmit behavior (`_subject`, `_captcha`, `_template`). Client-side validation (name length, email regex, phone regex) runs on blur and again on submit before the request is sent — keep both the hidden FormSubmit config fields and the validation logic in sync if the form fields change.
