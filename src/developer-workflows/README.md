# Developer Workflows


## Setting up a virtual machine for the eXist web applications

For setting up Nginx, one has to use these [configuration files](https://github.com/BetaMasaheft/jinntec/tree/main/nginx), and for setting up eXist-db, one has to use this [configuration file](https://github.com/BetaMasaheft/jinntec/blob/main/etc/conf.xml).


## Docker deployments

Docker is used to bake an image of BetMas in two stages. First, an `expansion` routine is used to
transform data: references are resolved into absolute references, etc. In a second stage, the
application is installed to the latest version in this repository.


### Building the Docker Image

The Docker image is automatically built and pushed to GitHub Container Registry (GHCR) via CI when:
- Code is pushed to the `master` or `main` branch
- The workflow is manually triggered via GitHub Actions

The image tag format is `{EXISTDB_VERSION}-manuscript-expanded`, where `EXISTDB_VERSION` matches the eXist-db version in the base image (default: `6.4.0`).


#### Local Build

To build the image locally, you'll need a GitHub Personal Access Token (PAT) with access to the private `expanded` repository:

```bash
# Build with BuildKit and secret for private repo access
# The EXISTDB_VERSION build arg defaults to 6.4.0
DOCKER_BUILDKIT=1 docker build \
  --build-arg EXISTDB_VERSION=6.4.0 \
  --secret id=github_token,env=GITHUB_TOKEN \
  -t ghcr.io/betamasaheft/betamasaheft:6.4.0-manuscript-expanded \
  .
```

To build with a different eXist-db version:
```bash
DOCKER_BUILDKIT=1 docker build \
  --build-arg EXISTDB_VERSION=6.5.0 \
  --secret id=github_token,env=GITHUB_TOKEN \
  -t ghcr.io/betamasaheft/betamasaheft:6.5.0-manuscript-expanded \
  .
```

Set the `GITHUB_TOKEN` environment variable with your PAT:
```bash
export GITHUB_TOKEN=your_github_pat_token
```


#### Multi-Architecture Build

The CI builds multi-architecture images (linux/amd64, linux/arm64). To build locally for multiple architectures:

```bash
# Create a buildx builder instance
docker buildx create --use --name multiarch

# Build and push for multiple platforms
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  --build-arg EXISTDB_VERSION=6.4.0 \
  --secret id=github_token,env=GITHUB_TOKEN \
  -t ghcr.io/betamasaheft/betamasaheft:6.4.0-manuscript-expanded \
  --push \
  .
```


#### Pulling the Image

Pull the pre-built image from GHCR:
```bash
docker pull ghcr.io/betamasaheft/betamasaheft:6.4.0-manuscript-expanded
```


#### Running the container locally

```bash
docker run -d \
  -p 8080:8080 \
  -e APP_URL=http://localhost:8080/exist/apps/BetMasWeb \
  --name betmas \
  ghcr.io/betamasaheft/betamasaheft:6.4.0-manuscript-expanded
```

The application will be available at [http://localhost:8080/exist/apps/BetMasWeb](http://localhost:8080/exist/apps/BetMasWeb).

#### Running the container on the production server

```bash
docker run -dit -p 8080:8080 ghcr.io/betamasaheft/betamasaheft:release-expanded
```


## CI Build Process

The GitHub Actions workflow (`.github/workflows/docker-build.yml`) handles:

- Multi-architecture builds (amd64 and arm64)

- Authentication with private repositories

- Pushing to GitHub Container Registry

- Build caching for faster subsequent builds

Images are published to: `ghcr.io/betamasaheft/betamasaheft:<version>`


## Code Formatting with Prettier

This project uses [Prettier](https://prettier.io/) to maintain consistent code formatting for both XQuery and JavaScript files. Formatting is automatically checked in CI on every push and pull request.

### Supported File Types

- **XQuery files**: `.xq`, `.xql`, `.xqm`, `.xquery` (using [prettier-plugin-xquery](https://github.com/DrRataplan/prettier-plugin-xquery))
- **JavaScript files**: `.js`

### Local Usage

Install dependencies:
```bash
npm install
```

Check formatting:
```bash
npm run format:check
```

Auto-format files:
```bash
npm run format:write
```

Check only for errors (suppresses formatting warnings):
```bash
npm run format:check:errors
```

### CI Integration

The GitHub Actions workflow (`.github/workflows/format-check.yml`) automatically runs Prettier checks on:
- All pushes to `master`/`main` branches
- All pull requests targeting `master`/`main` branches

The CI will fail if files are not properly formatted, ensuring code style consistency across the project.


### Configuration

- Configuration file: `.prettierrc.js`
- Ignored files: `.prettierignore`
- Uses tabs for indentation
- Print width: 120 characters

Note: Some XQuery files using eXist-db specific update syntax (e.g., `update insert`, `update value`) are excluded from formatting checks due to parser limitations. These are listed in `.prettierignore`.


## Indexes generated from data

* Persons;

* Places;

* Titles / Colophon / Supplications;

* Calendar;

* Decorations;

* Bindings;

* Additions;

* Keywords;

* Paratexts.
