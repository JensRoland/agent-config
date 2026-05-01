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

## Code organization

The Reader Axiom — *code is read more often than it is written, so optimize for the reader* — held when humans were the primary readers. With AI agents now doing most of the reading, the optimization target shifts. Agents ingest large spans at once, follow types and contracts more easily than narrative naming, and pay a real cost for jumping between many small files. Optimize for them.

- **Vertical slices, not horizontal layers.** All logic for a single entity — type, validator, serializer, repository, service, factory, DI wiring — lives in one file. Don't split a feature across `models/`, `serializers/`, `services/`, `repositories/` just because that's the conventional layout.
- **One file per concept, up to about ten thousand lines.** Big enough to swallow a whole entity-shaped slice; small enough that a million-token context window inhales it without breaking stride. Don't manufacture a second file because the first crossed some legacy size threshold.
- **Machine-checkable seams, not pretty ones.** Spend the design effort on contracts, types, and tests at module boundaries — not on cosmetic naming or decorative formatting.
- **No splitting for splitting's sake.** A 200-line function that does one thing top-to-bottom is fine. Don't extract `_helper_for_step_3` just to push the parent under some line count.
- **One canonical implementation per utility.** Date formatters, slugifiers, retry wrappers — exactly one version per codebase. If you find yourself writing a second, find and use the first.
- **Default to no comments.** Names and types do the explaining. Add a comment only when the *why* is non-obvious (a hidden constraint, a workaround for a specific bug, a subtle invariant). Don't explain *what* the code does.

### Architectural roadmap

Every CLAUDE.md (or AGENTS.md) should open with a concise high-level architectural roadmap so an agent landing in the repo cold knows the shape of the codebase: which entities live where, how the vertical slices are organized, what shared infrastructure exists, and which non-obvious constraints apply. Keep it tight — a paragraph or a short list per major area, not a wiki dump.

If the codebase follows the vertical-slice approach above, say so explicitly, and point at the `refactor` skill so agents know which guidance to apply when restructuring code.

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

## Supply chain security

### Minimum release age

To defend against supply chain attacks, configure a minimum release age of 7 days for package managers that support it. This prevents installing freshly-published (and potentially malicious) package versions.

- **npm** (v11.10.0+): Set `min-release-age=7` in `.npmrc`
- **pnpm** (v10.16+): Set `minimumReleaseAge: 10080` in `pnpm-workspace.yaml` (value in minutes)
- **bun** (v1.3+): Set `minimumReleaseAge = 604800` under `[install]` in `bunfig.toml` (value in seconds)
- **uv**: Set `exclude-newer = "7 days"` under `[tool.uv]` in `pyproject.toml`

If the project uses an older version of one of these tools, upgrade to a version that supports this feature.

### Pin GitHub Actions by SHA

When adding an external GitHub Actions step (e.g. `actions/checkout`, `actions/setup-node`, `docker/login-action`, `pnpm/action-setup`, etc.), always use the latest major version but pin it by commit SHA rather than a mutable version tag. Format: `uses: actions/checkout@<full-sha> # v6`. This prevents a compromised or force-pushed tag from injecting malicious code into CI.

If an action is already pinned by SHA, don't upgrade it unless specifically asked.

@RTK.md
