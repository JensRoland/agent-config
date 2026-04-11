# Coding style

## General principles

Don't over-engineer solutions. Keep things simple and pragmatic, and only add complexity when it's justified by a clear need or benefit.

Assume that we can use the latest stable versions of tools and libraries; prefer modern browser-native APIs to custom JS when possible. Examples:

- Frontend: ES modules, native <dialog>, `popover`s, css variables, `corner-shape`s, `oklch` / `color-mix()` / `relative-color()` colors, customizable selects, private class `#properties`, `Object.hasOwn()`, View Transitions, anchor positioning, accent-color, Set features, etc.
- Python: 3.12+ features: `dataclasses`, `pathlib`, list/dict/set comprehensions, non-capitalized type annotations (`dict` instead of `typing.Dict`), etc.

Do not build features with backwards compatibility in mind unless explicitly stated. The only 'exception' is DB migrations which should be included to upgrade existing data.

Include tests and use linters, type checkers, etc. to ensure code quality and catch bugs early.

You should wrap non-trivial external services in facades to add a bit of loose coupling, especially when those tools might change or be replaced in the future. Examples: logging, analytics, error tracking, auth, transactional email sending, payments processing.

Always cache results of expensive operations or data fetches when possible.

Design for cost efficiency, but keep things simple and be pragmatic. Do use caching and avoid hammering the server or external APIs with unnecessary requests, but don't over-engineer a solution if it's not needed.

All 'destructive' actions (e.g. deleting an item, sending an email, processing a payment) should require user confirmation to prevent accidental actions. Unless specified differently, this should be implemented using a styled confirmation dialog.

Keep the README.md file updated with clear instructions on how to set up the development environment, run the application, and run tests. Include any necessary configuration steps or environment variables. If it takes more than three or four simple steps to get the development environment up and running, you should attempt to simplify the process.

## Developer tooling

Use GitHub Actions for CI/CD.

For serverside JavaScript, use bun or pnpm as the package manager.

For Python, use `uv` as the package manager and `uv run` to run scripts. Use `uv sync` to install dependencies from `pyproject.toml`.

## Frontend scripting & styling

Do not use Tailwind or other bloated CSS frameworks. Use plain CSS or a lightweight utility library if needed.

Do not use React.

Do not use TypeScript. Use plain JavaScript (preferably with JsDoc annotations) and a lightweight frontend framework if needed, such as Solid.js or even HTMX for less interactive sites.

Do implement micro-interactions and animations (e.g. signaling that a button was pressed or a div is loading) to enhance the user experience, but keep them subtle and non-distracting.

Prefer reusable components, especially frontend components (both JS and HTML/CSS-only components). If we have already built a confirmation dialog, dropdown menu, combobox or chip input in one view, we should reuse it when we need a similar component in another view. Keep an updated list of reusable components in the project's CLAUDE.md file.

Design frontend code to load quickly and be responsive. Minimize the amount of JavaScript and CSS that needs to be loaded, and optimize the performance of the code. And prefer optimized formats like AVIF for images. But don't overdo it; loading a nice web font is often worth the extra few hundred milliseconds.

File uploads should always be handled with a (reusable) dropzone component that supports drag-and-drop as well as click-to-upload functionality. Image uploads in particular should support previewing (and optionally cropping/scaling to any or to a locked aspect ratio) the image before confirming the upload.

Form select inputs should be implemented using a reusable combobox component that supports searching and keyboard navigation.

When user- or AI-generated multiline content is displayed, opt for Markdown support.

## Infrastructure, backend, and external services

In languages that support them, use classes to encapsulate and organize backend logic. Don't use 'duck typing' or pass around untyped dicts without a very good reason.

Do not use Supabase, Vercel, or other backend-in-a-box platforms unless explicitly stated. Assume that, depending on actual scale, we will either run on cheap shared hosting or on AWS.

Use low-cost SaaS services when it makes sense, especially for 'non-trivial but solved problems' (like SMS or transactional email sending) and security-sensitive features (like authentication, authorization, and payments processing). This can save time and resources compared to building and maintaining these features in-house.

When using Websockets, always build robust error handling and reconnection logic to ensure a smooth user experience. This includes:

- Detecting connection drops and attempting to reconnect automatically.
- Providing user feedback when the connection is lost and when it is re-established.
- Implementing exponential backoff for reconnection attempts to avoid overwhelming the server.
- If frontend reflection of backend state is updated by a WS message, e.g. "user X is typing.." or "processing job X", refreshing the page should not cause the state to be lost. Upon reconnection, the frontend should fetch the current state from the backend to ensure consistency.

For fast non-cryptographic hashing, use xxh3. For cryptographic hashing, use bcrypt or argon2.

Don't use GraphQL unless explicitly stated. If proposing GraphQL in a plan, be prepared to argue in detail why it's necessary and worth the added complexity compared to e.g. a REST API.

@RTK.md
