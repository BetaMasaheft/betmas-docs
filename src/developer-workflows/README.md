# Developer Workflows


## How to migrate BetaMasaHeft to a new server


### Set up a virtual machine

Tje configuration files are as follows: [Nginx](https://github.com/BetaMasaheft/jinntec/tree/main/nginx), and  [eXist-db](https://github.com/BetaMasaheft/jinntec/blob/main/etc/conf.xml).

Ideally, all the software needed is installed as Docker containers.

### TBD: Load balancer service

With HA Proxy.


### Nginx

We need a custom made image, with these [configuration files](https://github.com/BetaMasaheft/jinntec/tree/main/nginx). See [https://hub.docker.com/search?q=nginx](https://hub.docker.com/search?q=nginx) for a base image.


### eXist db

Pull the image and run the container:

```bash
sudo docker pull ghcr.io/betamasaheft/betamasaheft:release-expanded

sudo docker run -dit -p 8080:8080 -p 8443:8443 --name betamasa ghcr.io/betamasaheft/betamasaheft:release-expanded
```

  > We have to make sure that the image contains the `conf.xml` located at [https://github.com/BetaMasaheft/jinntec/blob/main/etc/conf.xml](https://github.com/BetaMasaheft/jinntec/blob/main/etc/conf.xml).


### CollateX service

```bash
sudo docker pull ghcr.io/betamasaheft/collatex-service

sudo docker run -dit -p 8080:8080 -p 8443:8443 --name collatex-service docker pull ghcr.io/betamasaheft/collatex-service
```

### IIIF service

```bash
sudo docker pull iipsrv/iipsrv

sudo docker run -it -p 9000:9000 -p 8080:80 -v /home/images/:/images iipsrv/iipsrv
```

### SPARQL service

The current workflow for providing the data for this service is as follows (the future workflow is below):

* The service picks the latest archive from the folder for backup of the eXist DB.

* The service converts the TEI files to RDF/XML files.

* The service converts the RDF/XML files to Turtle files.

* The service restores the database of the SPARQL endpoint.


The data for this service is periodically updated as follows:

* The service picks the latest archive from the Docker volume for backup of the eXist container.

* The service converts the TEI files to Turtle files.

* The service restores the database of the SPARQL endpoint.


### TBD: Counter service


### TBD: Expansion service


### TBD: Testing service

Consider [betmas-e2e](https://github.com/BetaMasaheft/betmas-e2e).


### Backup and restore service

Such service is provided by Komodo, see [Backup and Restore](https://komo.do/docs/setup/backup).



### =======================

### Loading the data

#### The XML data

#### The images

#### The RDF data


<!-- This is no longer true -->
Most data is on GitHub, except expanded data, lists and Dillmann

### Loading application

 * Take the apps from latest production. there might have been changes
   * Use eXide Application/Download app for the following items:
	 * /db/apps/BetMas
	 * /db/apps/BetMasApi
	 * /db/apps/BetMasService
	 * /db/apps/BetMasWeb
	 * /db/apps/DillmannData
	 * /db/apps/EthioStudies
	 * /db/apps/alpheiosannotations
	 * /db/apps/gez-en
	 * /db/apps/guidelines
	 * /db/apps/lists
	 * /db/apps/parser
   * Extract and place into [BetMas](https://github.com/BetaMasaheft/BetMas)
   * Rebase fixing commits over it: [BetMas#exist-6.x](https://github.com/BetaMasaheft/BetMas/tree/exist-6.x)
 * Deploy the apps
     * Tuttle
 	 * BetMas
	 * BetMasApi
	 * BetMasService
	 * BetMasWeb
	 * BetMasInit
	 * EthioStudies
	 * alpheiosannotations
	 * DillmannData
	 * Dillmann
	 * Schema
	 * guidelines (data)
	 * guidelinesApp
	 * lists
	 * parser

 * Update app url
   * edit /db/apps/BetMasWeb/modules/loc.xqm to read the correct app url
 * Register RestXQ stuff:
   * call `/db/apps/BetMasService/modules/registerRESTXQ.xql`
   * http://116.202.114.60:8081/exist/apps/BetMasService/modules/registerRESTXQ.xql

### Loading data

Take and deploy from GitHub:

 * [authority-files](https://github.com/BetaMasaheft/authority-files)
 * [corpora](https://github.com/BetaMasaheft/corpora)
 * [institutions](https://github.com/BetaMasaheft/institutions)
 * [manuscripts](https://github.com/BetaMasaheft/manuscripts)
 * [narratives](https://github.com/BetaMasaheft/narrative)
 * [persons](https://github.com/BetaMasaheft/persons)
 * [places](https://github.com/BetaMasaheft/places)
 * [studies](https://github.com/BetaMasaheft/studies)
 * [works](https://github.com/BetaMasaheft/works)

**Chojnacki does not seem to be used in the end!** VERIFY THO


### Install additional things

* At least https://iipimage.sourceforge.io/ is used in production now to host IIIF images

### Loading expanded content

Expanded content is too large to download through the normal way. Instead, it needs to be downloaded through exide. This XQuery script can do that:

```xquery
xquery version "3.1";

let $file-system-target-base-directory :=
    '/media/add/expanded-data-dump'
let $source-collection := '/db/apps/expanded'
for $doc in collection($source-collection)
let $target :=
    (: Put the files into a corresponding directory in the file system :)
    concat($file-system-target-base-directory, replace(base-uri($doc), '/', '\\'))
return
    file:serialize($doc, $target, ("omit-xml-declaration=yes", "indent=yes", "expand-xinclude=false"))
```

After that, rsync it to local:

```
scp -r bmadmin@betamasaheft2.aai.uni-hamburg.de:/media/add/expanded-data-dump/expanded-data-dump .
```

Finally deploy it:

```
deploy-expanded.sh
```

### Indexing

Touch /db/apps/expanded/collection.xconf

## Permissions

Fix the permissons for everything in `/db/apps/lists`. They need to be world-writable.


## To analyze what is below ================================================

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

#### Running the container locally

```bash
docker run -d \
  -p 8080:8080 \
  -e APP_URL=http://localhost:8080/exist/apps/BetMasWeb \
  --name betmas \
  ghcr.io/betamasaheft/betamasaheft:6.4.0-manuscript-expanded
```

The application will be available at [http://localhost:8080/exist/apps/BetMasWeb](http://localhost:8080/exist/apps/BetMasWeb).


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


## Aggregated indexes (or registers) generated from data

The term `register` is here used in the sense of the German word `Register`, which is a aggregation of two or more indexes. Example of an aggregated index: [Register der Fundorte fuer Telemann Werkverzeichnis](https://adwmainz.pages.gitlab.rlp.net/nfdi4culture/cdmd/telemann-indexes/peritext/registers/institutions/index.html).

The BetaMasaHeft web application geatures the follwing combined indexes:

* **Manuscripts**, which presents the manuscriots by holding institutions, [https://betamasaheft.eu/manuscripts/browse](https://betamasaheft.eu/manuscripts/browse).

* 
