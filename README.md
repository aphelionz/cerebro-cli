# Cerebro (cerebro-cli)

> A novel recruiting tool using GitHub events.

Finding capable developers is challenging. This tool starts with a simple
heuristic - the ability to get a pull request (PR) merged given a sufficient
amount of feedback, and filters from there. _Credit to
[@sequoia](https://github.com/sequoia) for this heuristic._

The flow is currently as follows:

1. Listen to the public GitHub events firehose for pull request
_merge_ events on PRs that have a specified number of comments.
2. Discard PRs made by bot users
3. For each PR:
    1. Check if the language is your target language
    2. Check if the author of the PR is looking for a job

## Table of Contents

- [Prerequisites](#prerequisites)
- [Usage](#usage)
- [Contributing](#contributing)
- [Future Work](#future-work)
- [Maintainers](#maintainers)
- [License](#license)

### Prerequisites

- [Node.js](https://nodejs.org). The [nvm](https://nvm.sh) tool works well for
this.
- Optional, but highly recommended: A [GH personal token] with default
permissions
- Optional: [Docker] and [Docker Compose].

[GH personal token]: https://github.com/settings/tokens
[Docker]: https://docker.io
[Docker Compose]: https://docs.docker.com/compose/

## Usage

Cerebro can be run in a number of different ways, always configured by environment
variables.

### Configuration

The following environment variables are available:

- `LANGUAGES`: **Required.** Comma separated list of the target languages
you're looking for
- `GH_TOKEN`: **Not required but highly recommended.** Your GitHub personal
authentication token.
- `COMMENT_THRESHOLD`: _optional, default 3_. Show PRs with review comments
greater than or equal to this number
- `SHOW_NON_HIREABLE`: _optional, default false_. Show applicants that are not
explicitly marked as hireable.
- `CHANGESET_THRESHOLD`: _optional, default 5432_. Only match PRs that have a
total changeset (additions + subtractions) under this number.

### Using `npx`

You can skip the whole installation process altogether and just run Cerebro
using `npx`

```bash
GH_TOKEN=[insert GH token here] \
LANGUAGES=Solidity,Rust \
npx cerebro-cli
```

### Using Docker and Docker Compose

With `docker`:

```bash
docker run -ti \
  -e GH_TOKEN=XXXXX \
  -e LANGUAGES=c++,javascript \
  aphelionz/cerebro-cli:v0.2.1
```

Or in your `docker-compose` file:

```yaml
services:
  cerebro:
    image: aphelionz/cerebro-cli:v0.2.1
    environment:
      GH_TOKEN: XXXXX
      LANGUAGES: rust,solidity
```

### Prometheus

By default, the app will expose Prometheus-compatible metrics on port 9100.
These include all of the normal default nodejs metrics, as well as some custom
metrics for Cerebro:

```prometheus
# HELP unique_events_processed Number of unique events processed by Cerebro
# TYPE unique_events_processed counter
unique_events_processed{app="cerebro"} 8811

# HELP suitable_pull_requests_found Number of suitable pull requests by Cerebro
# TYPE suitable_pull_requests_found counter
suitable_pull_requests_found{app="cerebro"} 6

# HELP candidates_found Count of candidates found by Cerebro so far
# TYPE candidates_found counter
candidates_found{app="cerebro"} 0
```

## Contributing

Issues and PRs accepted. More info coming soon.

### Contributing to the manual

The manual is in mdBook format. To contribute you'll need `cargo`, `mdbook`, and
`mdbook-mermaid`.

### Installing from Source

```bash
git clone https://github.com/aphelionz/cerebro
cd cerebro
npm install
```

Then run `npm start` with the aforementioned environment variables
to run, test, and develop!

### Why doesn't this use GraphQL?

GitHub API v4 does not support listening to the public event timeline.

### Future Work

1. Better bot detection
    1. Bot detection really happens in two places, here and in the use of `review_comments`
2. English proficiency
    1. Really needs a manual overview until we find / create a good enough tool
    for this
    2. Ideally would be any proficiency in language
3. "Looking for a job" false negatives, and false positives too
    1. `hireable` is either null (false) or true. However null is the default
    because GH jobs is opt-in. So we only make a note of this for now.
4. IPFS + OrbitDB integration? Or at least _some_ database
5. Readline and raw stdin integration to make a proper UI (or just make an
API + website)
6. Environment variable validation
    1. Is it possible to get the full list of supported GH languages?

## Maintainers

[@aphelionz](https://github.com/aphelionz)

## License

AGPL © 2020 Mark Henderson
