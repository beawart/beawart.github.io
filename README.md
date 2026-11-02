# PlanSurf

PlanSurf is a home for a growing collection of focused tools for everyday life.
The landing page lists each app, its availability, and a link to launch it.

## Apps

- **Budget** — available at [beawart.github.io/budget](https://beawart.github.io/budget/).
- **Property** — coming soon.
- **DailyTodo** — coming soon.

## Local development

Install Ruby and Bundler, then run:

```sh
bundle install
./tools/run.sh
```

To build and check the production site:

```sh
./tools/test.sh
```

## Adding an app

Add a card to `index.html` in the app collection. Link available apps to their
public URL; keep apps that are not ready marked as coming soon until they can
be launched.
