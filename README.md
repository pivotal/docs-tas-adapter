# Documentation Repository for Application Service Adapter

## Overview
This repo publishes to:

* Staging: TBD
* Production: TBD

## Branches
| Branch | Usage |
|--------|-------|
| main | Development branch; staging URL: **TBD** |
| version-0-1 | Branch that is published to: **TBD** |
| initial-outline-stubs | Currently inflight |

## Publishing Docs

Staging and Publishing:  

- [docworks](https://docworks.vmware.com/) is the main tool for managing docs used by writers.
- [docsdash](https://docsdash.vmware.com/) is a deployment UI which manages the promotion from staging to pre-prod to production. The process below describes how to upload our docs to staging, replacing the publication with the same version.

### Prepare Markdown Files
- Markdown files live in this repo.
- Each page requires an entry in [toc.md](docs/toc.md) for the table of contents.
- Images should live in an `images` directory at the same level and linked with a relative link.

### Create the ZIP File
Starting from the repo root, this will create a new `docs.zip` with no root folder and show its contents.


```sh
git pull ; rm *.zip ; zip -r tap3beta *
```


### Upload the ZIP File to Docworks
- Go to https://docworks.vmware.com/md2docs/publish
- Fill in the fields exactly as below. Repeat this every time - the browser can help to remember form fields.
- Click on upload, and when prompted, enter your VMware AD password (for docsdash)
- If you see invalid path errors in the yellow box on the right there are broken links, but the site will still be published.
- If the toc.md is invalid then the site will not build, but there will be no indication that something is wrong.

### Form Fields

Form fields for beta-1: [VMwarePub.yaml](https://github.com/pivotal/docs-tap/blob/beta-1/VMwarePub.yaml)
Form fields for main (beta-2?): [VMwarePub.yaml](https://github.com/pivotal/docs-tap/blob/main/VMwarePub.yaml)


### In Docsdash

1. Wait about 1 minute for processing to complete after uploading.
2. Go to https://docsdash.vmware.com/deployment-stage
   
   There should be an entry with a blue link which says `Documentation` and points to staging.

### Promoting to Pre-Prod and Prod

**Prerequisite** Needs additional privileges - reach out to Paige Calvert on the docs team [#tanzu-docs](https://vmware.slack.com/archives/C055V2M0H) or ask a writer to do this step for you.

1. Go to Staging publications in docsdash  
  https://docsdash.vmware.com/deployment-stage

2. Select a publication (make sure it's the latest version)

3. Click "Deploy selected to Pre-Prod" and wait for the pop to turn green (refresh if necessary after about 10s)

4. Go to Pre-Prod list  
  https://docsdash.vmware.com/deployment-pre-prod

5. Select a publication

6. Click "Sign off for Release"

7. Wait for your username to show up in the "Signed off by" column

8. Select the publication again

9. Click "Deploy selected to Prod"

### Landing Page and Publications

General information about landing pages:

- Every product has a landing page (Not exactly true: every umbrella product, such as Tanzu Application Platform should have a landing page.)
- The landing page is a container for all the "publications" for a product. Our first publication will be our combined docs for our first Beta, including installation, and user docs.
- Typically there will be a new docs publication for each minor release but not each point release. This version number become part of the URL e.g. Our first release was version `0.1` (see form section above for the current release).
- Some products like [Tanzu Kubernetes Grid](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/index.html) publish separate release notes publications for each point release.
- For comparison see https://docs.vmware.com/en/VMware-Tanzu-Application-Catalog/index.html
