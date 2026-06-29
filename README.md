# Queue Monitor

Queue Monitor is the foundation for a Laravel queue observability product: a dashboard for understanding asynchronous job health, queue depth, failures, retries, throughput, and latency.

The repository is not yet a working monitor. It currently contains the application shell, queue persistence foundation, TypeScript frontend toolchain, and quality scripts needed to build that product deliberately.

## Product Direction

A finished Queue Monitor should help developers and operators answer operational questions quickly:

- Which jobs are waiting?
- Which jobs failed?
- Which queue is backing up?
- How old is the oldest pending job?
- What exception caused a job to fail?
- Can a failed job be retried or forgotten safely?
- Are queue latency and throughput moving in the wrong direction?

Those capabilities are the intended product direction, not the current feature set.

## Current Repository State

Implemented:

- Laravel 13 application shell.
- Inertia + Vue + TypeScript frontend dependencies.
- Laravel queue configuration.
- Database migrations for users, cache, jobs, job batches, and failed jobs.
- Basic route smoke/unit tests.
- Pint, Larastan/PHPStan, ESLint, Prettier, and vue-tsc scripts.
- GitHub workflow for automatic code-quality fixes.

Not implemented yet:

- queue dashboard
- job list
- failed job explorer
- retry or forget actions
- queue worker status
- throughput metrics
- latency metrics
- queue connection selector
- database models or controllers for queue monitoring
- API endpoints for queue telemetry
- Vue pages/components for queue observability

The visible page at `/` is currently a placeholder page.

## Observability Shape

Laravel queue tables can be inspected manually, but repeated manual inspection does not scale. The first useful version should make queue state visible at a glance and keep potentially destructive actions, such as retrying or forgetting failed jobs, clearly guarded.

The natural product boundary is read-first: queue counts, pending jobs, failed jobs, oldest-job age, throughput, latency, and exception details before any mutation actions.

## What Exists In The Repository

```text
routes/web.php
    Serves the Inertia Welcome page at `/`.

resources/js/pages/Welcome.vue
    Placeholder welcome page currently served at `/`.

resources/js/app.ts
    Minimal Inertia app entry. It currently sets title/progress only.

config/queue.php
    Laravel queue connections: sync, database, beanstalkd, SQS, Redis,
    deferred, background, and failover.

database/migrations/
    Framework migrations, including jobs, job_batches, and failed_jobs.

tests/
    Basic route smoke test and unit test.

phpstan.neon
    Larastan/PHPStan configuration at level 7.
```

## Queue Configuration

The default queue connection is:

```env
QUEUE_CONNECTION=database
```

The queue config includes Laravel's standard drivers:

- `sync`
- `database`
- `beanstalkd`
- `sqs`
- `redis`
- `deferred`
- `background`
- `failover`

The database queue uses the `jobs` table by default. Failed jobs use the `failed_jobs` table with the `database-uuids` driver.

## Stack

| Layer | Tools |
| --- | --- |
| Backend | Laravel 13, PHP 8.3+, Inertia Laravel |
| Frontend | Vue 3, TypeScript, Vite, Tailwind CSS 4 |
| Queue foundation | Laravel queue config and database queue migrations |
| Quality | PHPUnit, Pint, Larastan, ESLint, Prettier, vue-tsc |

## Local Setup

```bash
composer install
npm ci
cp .env.example .env
php artisan key:generate
```

Configure the database connection in `.env` before running migrations.

```bash
php artisan migrate
```

Run the development stack:

```bash
composer dev
```

Or run the pieces separately:

```bash
php artisan serve
npm run dev
```

## Build

The repository defines:

```bash
npm run build
```

and:

```bash
npm run build:ssr
```

Because the queue-monitoring UI is not implemented yet, a successful build only verifies the current application shell.

## Tests And Quality

Run the backend test script:

```bash
composer test
```

The script clears config, runs Pint in check mode, runs PHPStan through `composer types:check`, and then runs PHPUnit.

Run individual checks:

```bash
composer lint:check
composer types:check
npm run lint:check
npm run format:check
npm run types:check
```

Apply formatting fixes:

```bash
composer lint
npm run lint
npm run format
```

Run the combined check script:

```bash
composer ci:check
```

## Suggested First Implementation Milestones

These are not implemented yet, but they are the natural next steps for the repository:

1. Replace the placeholder welcome page with a queue dashboard page.
2. Add read-only endpoints for pending jobs, failed jobs, queue counts, and oldest job age.
3. Add models or query objects around `jobs`, `job_batches`, and `failed_jobs`.
4. Add feature tests for queue data endpoints.
5. Add a Vue table for pending jobs and failed jobs.
6. Add guarded retry/forget actions for failed jobs.
7. Add filtering by connection, queue, status, and time range.
8. Add summary cards for depth, failures, retries, and latency.

## Current Limits

- The project name describes the intended product, not the implemented feature set.
- There is no queue monitor UI yet.
- There are no queue-specific controllers, actions, models, or tests.
- The current Inertia app entry is minimal and should be completed as part of the first frontend implementation.

## Contributing

Contributions should first establish the queue-monitoring contract before polishing UI details.

Good initial contributions:

- define read-only queue metrics
- add tests for jobs/failed-jobs queries
- build a dashboard around database queues
- add retry/forget actions with clear confirmation behavior
- complete the Inertia/Vue entry point if needed by the current toolchain

Before opening a pull request, run:

```bash
composer test
npm run lint:check
npm run format:check
npm run types:check
npm run build
```

## License

Queue Monitor is open-sourced under the MIT license. See `LICENSE`.
