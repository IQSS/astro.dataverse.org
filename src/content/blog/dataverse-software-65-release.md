---
title: "Dataverse Software 6.5 Release"
description: "Dataverse 6.5 is now available with several important bug fixes and new features."
pubDate: "2024-12-12T00:00:00"
heroImage: "../../assets/dataverse_project_logo.svg"
---

## Release Highlights

Highlights for Dataverse 6.5 include:

- new API endpoints, including editing of collections, Search API file counts, listing of exporters, comparing dataset versions, and auditing data files
- UX improvements, especially Preview URLs
- increased harvesting flexibility
- performance gains
- a [security vulnerability](https://github.com/IQSS/dataverse-security/issues/98) addressed
- many bug fixes
- and more! Please see below.

## Features Added

### Private URL Renamed to Preview URL and Improved

The name of the URL that may be used by dataset administrators to share a draft version of a dataset has been changed from Private URL to Preview URL.

Also, additional information about the creation of Preview URLs has been added to the popup accessed via edit menu of the Dataset Page.

Users of the Anonymous Preview URL will no longer be able to see the name of the Dataverse that the dataset is in but will be able to see the name of the repository.

Any Private URLs created in previous versions of Dataverse will continue to work.

The old "privateUrl" API endpoints for the creation and deletion of Preview (formerly Private) URLs have been deprecated. They will continue to work but please switch to the "previewUrl" equivalents that have been [documented](https://guides.dataverse.org/en/6.5/api/native-api.html#create-a-preview-url-for-a-dataset) in the API Guide.

See also #8184, #8185, #10950, #10961, and #11085.

### Showing Differences Between Dataset Versions is More Scalable

Showing differences between dataset versions, which is done during dataset edit operations and to populate the dataset page versions table, has been made significantly more scalable. See #10814 and #10818.

### Version Differences Details Sorting Added

In order to facilitate the comparison between the draft version and the published version of a dataset, a sort on subfields has been added. See #10969.

### Reindexing After a Role Assignment is Less Memory Intensive

Adding or removing a user from a role on a collection, particularly the root collection, could lead to a significant increase in memory use, resulting in Dataverse itself failing with an out-of-memory condition. Such changes now consume much less memory. A Solr reindexing step is included in the upgrade instructions below. See also #10697 and #10698.

### Longer Custom Questions in Guestbooks

Custom questions in Guestbooks can now be more than 255 characters and the bug causing a silent failure when questions were longer than this limit has been fixed. See also #9492, #10117, #10118.

### PostgreSQL and Flyway Updates

This release bumps the version of PostgreSQL and Flyway used in containers as well as the PostgreSQL JDBC driver used all installations, including classic (non-Docker) installations. PostgreSQL and its driver have been bumped to version 17. Flyway has been bumped to version 10.

PostgreSQL 13 remains the version used with automated testing, leading us to continue to [recommend](https://guides.dataverse.org/en/6.5/installation/prerequisites.html#postgresql) that version for classic installations.

As of Flyway 10, supporting older versions of PostgreSQL no longer requires a paid subscription. While we don't encourage the use of older PostgreSQL versions, this flexibility may benefit some of our long-standing installations in their upgrade paths.

As part of this update, the containerized development environment now uses Postgres 17 instead of 16. Developers must delete their data (`rm -rf docker-dev-volumes`) and start with an empty database (rerun the [quickstart](https://guides.dataverse.org/en/6.5/developers/dev-environment.html#quickstart) in the dev guide), as [explained](https://groups.google.com/g/dataverse-dev/c/ffoNj5UXyzU/m/nE5oGY_sAQAJ) on the dev mailing list.

The Docker compose file used for [evaluations or demos](https://guides.dataverse.org/en/6.4/container/running/demo.html) has been upgraded from Postgres 13 to 17.

See also #10889 and #10912.

### Harvesting "oai_dc" Metadata Prefix When Extended With Specific Namespaces

Some data repositories extend the "oai_dc" metadata prefix with specific namespaces. In this case, harvesting of these datasets into Dataverse was not possible because an XML parsing error was raised.

Harvesting of these datasets has been fixed by excluding tags with namespaces that are not "dc:". That is, only harvesting metadata with the "dc" namespace. See #10837.

### Harvested Dataset PID from Record Header

When harvesting, Dataverse can now use the identifier from the OAI-PMH record header as the persistent id for the harvested dataset.

This will allow harvesting from sources that do not include a persistent id in their oai_dc metadata records, but use valid DOIs or handles as the OAI-PMH record header identifiers.

It is also possible to optionally configure a harvesting client to use this OAI-PMH identifier as the **preferred** choice for the persistent id. See the [Harvesting Clients API](https://guides.dataverse.org/en/6.5/api/native-api.html#create-a-harvesting-client) section of the Guides, #11049 and #10982 for more information.

### Harvested Datasets Can Have Multiple "otherId" Values

When harvesting using the DDI format, datasets can now have multiple "otherId" values. See #10772.

### Multiple Languages in Docker

Documentation has been added to explain how to set up multiple languages (e.g. English and French) in the tutorial for setting up Dataverse in Docker.

See [the tutorial](https://guides.dataverse.org/en/6.5/container/running/demo.html#multiple-languages), #10939, and #10940.

### GlobusBatchLookupSize

An optimization has been added for the Globus upload workflow, with a corresponding new database setting: `:GlobusBatchLookupSize`

See the [Database Settings](https://guides.dataverse.org/en/6.5/installation/config.html#GlobusBatchLookupSize) section of the guides, #10977, and #11040 for more information.

## Bugs Fixed

### Relation Type (Related Publication) and DataCite

The subfield "Relation Type" was added to the field "Related Publication" in Dataverse 6.4 (#10632) but couldn't be used without workarounds described in an [announcement](https://groups.google.com/g/dataverse-community/c/zlRGJtu3x4g/m/GtVZ26uaBQAJ) about the problem. The bug has been fixed and workarounds are no longer required. See #10926 and the announcement above.

### Sort Order for Files

"Newest" and "Oldest" were reversed when sorting files on the dataset landing page. This has been fixed. See #10742 and #11000.

### Guestbook Email Validation

In the Guestbook UI form, the email address is now checked for validity. See #10661 and #11022.

### Updating Files Now Possible When Latest and Only Dataset Version is Deaccessioned

When a dataset was deaccessioned, and was the only previous version, it would cause an error when trying to update the files. This has been fixed. See #9351 and #10901.

### My Data Filter by Username Feature Restored

The superuser-only feature of filtering by a username on the My Data page was not working. Entering a username in the "Results for Username" field now returns data for the desired user. See also #7239 and #10980.

### Better Handling of Parallel Edit/Publish Errors

Improvements have been made in handling the errors when a dataset has been edited in one browser window and an attempt is made to edit or publish it in another. (This practice is discouraged, by the way.) See #10793 and #10794.

### Facets Filter Labels Now Translated Above Search Results

On the main page, it's possible to filter results using search facets. If internationalization (i18n) has been enabled in the Dataverse installation, allowing pages to be displayed in several languages, the facets were correctly translated in the filter column at the left. However, they were not being translated above the search results, remaining in the default language, English. This has been fixed. See #9408 and #10158.

### Unpublished File Bug Fix Related to Deaccessioning

A bug fix was made related to retrieval of the major version of a Dataset when all major versions were deaccessioned. This fixes the incorrect showing of the files as "Unpublished" in the search list even when they are published. In the upgrade instructions below, there is a step to reindex Solr. See also #10947 and #10974.

### Minor DataCiteXML Fix (Useless Null)

A minor bug fix was made to avoid sending a useless ", null" in the DataCiteXML sent to DataCite and in the DataCite export when a dataset has a metadata entry for "Software Name" and no entry for "Software Version". The bug fix will update datasets upon publication. Anyone with existing published datasets with this problem can be fixed by [pushing updated metadata to DataCite for affected datasets](https://guides.dataverse.org/en/6.5/admin/dataverses-datasets.html#update-metadata-for-a-published-dataset-at-the-pid-provider) and [re-exporting the dataset metadata](https://guides.dataverse.org/en/6.5/admin/metadataexport.html#batch-exports-through-the-api). See "Pushing updated metadata to DataCite" in the upgrade instructions below. See also #10919.

### PIDs and Make Data Count Citation Retrieval

Make Data Count (MDC) citation retrieval with the PID settings has been fixed. PID parsing in Dataverse is now case insensitive, improving interaction with services that may change the case of PIDs. Warnings related to managed/excluded PID lists for PID providers have been reduced. See #10708.

### Quirk in Overview Display When Using External Controlled Variables

This bugfix corrects an issue when there are duplicated entries on the metadata page. It is fixed by correcting an IF-clause in metadataFragment.xhtml. See #11005 and #11034.

### Globus "missing properties" Logging Fixed

In previous releases, logging would show Globus-related strings were missing from properties files. This has been fixed. See #11030.

## API Updates

### Editing Collections

A new endpoint (`PUT /api/dataverses/<identifier>`) for updating an existing collection (dataverse) has been added. It uses the same JSON structure as the one used for collection creation. See also [the docs](https://guides.dataverse.org/en/6.5/api/native-api.html#update-a-dataverse-collection), #10904, and #10925.

### fileCount Added to Search API

A new search field called `fileCount` can be searched to discover the number of files per dataset. The upgrade instructions below explain how to update your Solr `schema.xml` file to add the new field and reindex Solr. See also #8941 and #10598.

### List Dataset Metadata Exporters

A list of available dataset metadata exporters can now be retrieved programmatically via API. See [the docs](https://guides.dataverse.org/en/6.5/api/native-api.html#get-export-formats) and #10739.

### Comparing Dataset Versions

An API has been added to compare dataset versions. See [the docs](https://guides.dataverse.org/en/6.5/api/native-api.html#compare-versions-of-a-dataset), #10888, and #10945.

### Audit Data Files

A superuser-only API endpoint has been added to audit datasets with data files where the physical files are missing or the file metadata is missing. See [the docs](https://guides.dataverse.org/en/6.5/api/native-api.html#datafile-audit), #11016, and [#220](https://github.com/IQSS/dataverse.harvard.edu/issues/220).

### Update Collection API Inheritance

The update collection (dataverse) API endpoint has been updated to support an "inherit from parent" configuration for metadata blocks, facets, and input levels.

Previously, not setting these fields meant using a copy of the settings from the parent collection, which could get out of sync. See also [the docs](https://guides.dataverse.org/en/6.5/api/native-api.html#update-a-dataverse-collection), #11018, and #11026.

### isMetadataBlockRoot and isFacetRoot

The JSON payload of the "get collection" endpoint has been extended to include properties isMetadataBlockRoot and isFacetRoot. See also [the docs](https://guides.dataverse.org/en/6.5/api/native-api.html#view-a-dataverse-collection), #11012, and #11013.

### Whitespace Trimming When Loading Metadata Block TSV Files

When loading custom metadata blocks using the `api/admin/datasetfield/load` API endpoint, whitespace can be introduced into field names. Whitespace is now trimmed from the beginning and end of all values read into the API before persisting them. See #10688 and #10696.

### Image URLs from the Search API

As of 6.4 (#10855) `image_url` is being returned from the Search API. The logic has been updated to only show the image if each of the following are true:

1. The data file is not harvested
2. A thumbnail is available for the data file
3. If the data file is restricted, then the caller must have DownloadFile permission for the data file
4. The data file is NOT actively embargoed
5. The data file's retention period has NOT expired

See also #10875 and #10886.

### Metrics API Bug Fixes

Two bugs in the Metrics API have been fixed:

- The /datasets and /datasets/byMonth endpoints could report incorrect values if or when they have been called using the "dataLocation" parameter (which allows getting metrics for local, remote (harvested), or all datasets) as the metrics cache was not storing different values for these cases.

- Metrics endpoints whose calculation relied on finding the latest published dataset version were incorrect if/when the minor version number was > 9.

The upgrade instructions below include a step for clearing the metrics cache.

See also #10379 and #10865.

### API Tokens

An optional query parameter called "returnExpiration" has been added to the `/api/users/token/recreate` endpoint, which, if set to true, returns the expiration time in the response. See [the docs](https://guides.dataverse.org/en/6.5/api/native-api.html#recreate-a-token), #10857 and #10858.

The `/api/users/token` endpoint has been extended to support any auth mechanism for retrieving the token information. Previously this endpoint only accepted an API token to retrieve its information. Now it accepts any authentication mechanism and returns the associated API token information. See #10914 and #10924.

## Settings Added

- `:GlobusBatchLookupSize`

## Backward Incompatible Changes

Generally speaking, see the [API Changelog](https://guides.dataverse.org/en/latest/api/changelog.html) for a list of backward-incompatible API changes.

### List Collections Linked to a Dataset

The API endpoint that returns a list of collections that a dataset has been linked to has been improved to provide a more structured JSON response. See [the docs](https://guides.dataverse.org/en/6.5/admin/dataverses-datasets.html#list-collections-that-are-linked-from-a-dataset), #9650, and #9665.

## Complete List of Changes

For the complete list of code changes in this release, see the [6.5 milestone](https://github.com/IQSS/dataverse/issues?q=milestone%3A6.5+is%3Aclosed) in GitHub.

## Getting Help

For help with upgrading, installing, or general questions please post to the [Dataverse Community Google Group](https://groups.google.com/g/dataverse-community) or email support@dataverse.org.

## Installation

If this is a new installation, please follow our [Installation Guide](https://guides.dataverse.org/en/latest/installation/). Please don't be shy about [asking for help](https://guides.dataverse.org/en/latest/installation/intro.html#getting-help) if you need it!

Once you are in production, we would be delighted to update our [map of Dataverse installations](https://dataverse.org/installations) around the world to include yours! Please [create an issue](https://github.com/IQSS/dataverse-installations/issues) or email us at support@dataverse.org to join the club!

You are also very welcome to join the [Global Dataverse Community Consortium](https://www.gdcc.io/) (GDCC).

## Upgrade Instructions

Upgrading requires a maintenance window and downtime. Please plan accordingly, create backups of your database, etc.

These instructions assume that you've already upgraded through all the 5.x releases and are now running Dataverse 6.4.

0\. These instructions assume that you are upgrading from the immediate previous version. If you are running an earlier version, the only supported way to upgrade is to progress through the upgrades to all the releases in between before attempting the upgrade to this version.

If you are running Payara as a non-root user (and you should be!), **remember not to execute the commands below as root**. By default, Payara runs as the `dataverse` user. In the commands below, we use sudo to run the commands as a non-root user.

Also, we assume that Payara 6 is installed in `/usr/local/payara6`. If not, adjust as needed.

```shell
export PAYARA=/usr/local/payara6
```

(or `setenv PAYARA /usr/local/payara6` if you are using a `csh`-like shell)

1\. List deployed applications

```shell
$PAYARA/bin/asadmin list-applications
```

2\. Undeploy the previous version (should match "list-applications" above)

```shell
$PAYARA/bin/asadmin undeploy dataverse-6.4
```

3\. Stop and start Payara

```shell
sudo service payara stop
sudo service payara start
```

4\. Download and deploy this version

```shell
wget https://github.com/IQSS/dataverse/releases/download/v6.5/dataverse-6.5.war
$PAYARA/bin/asadmin deploy dataverse-6.5.war
```

Note: if you have any trouble deploying, stop Payara, remove the following directories, start Payara, and try to deploy again.

```shell
sudo service payara stop
sudo rm -rf $PAYARA/glassfish/domains/domain1/generated
sudo rm -rf $PAYARA/glassfish/domains/domain1/osgi-cache
sudo rm -rf $PAYARA/glassfish/domains/domain1/lib/databases
```

5\. For installations with internationalization:

Please remember to update translations via [Dataverse language packs](https://github.com/GlobalDataverseCommunityConsortium/dataverse-language-packs).

6\. Restart Payara

```shell
sudo service payara stop
sudo service payara start
```

7\. Update Solr schema.xml file. Start with the standard v6.5 schema.xml, then, if your installation uses any custom or experimental metadata blocks, update it to include the extra fields (step 7a).

Run the commands below as a non-root user.

Stop Solr (usually `sudo service solr stop`, depending on Solr installation/OS, see the [Installation Guide](https://guides.dataverse.org/en/6.5/installation/prerequisites.html#solr-init-script)).

```shell
sudo service solr stop
```

Replace schema.xml

Please note that the path to Solr may differ from the example below.

```shell
wget https://raw.githubusercontent.com/IQSS/dataverse/v6.5/conf/solr/schema.xml
sudo cp schema.xml /usr/local/solr/solr-9.4.1/server/solr/collection1/conf
```

Start Solr (but if you use any custom metadata blocks, perform the next step, 7a first).

```shell
sudo service solr start
```

7a\. For installations with custom or experimental metadata blocks:

Before starting Solr, update the `schema.xml` file to include all the extra metadata fields that your installation uses.

We do this by collecting the output of Dataverse's Solr schema API endpoint (`/api/admin/index/solr/schema`) and piping it to the `update-fields.sh` script which updates the `schema.xml` file supplied as an argument.

The example below assumes the default installation location of Solr, but you can modify the commands as needed.

```shell
wget https://raw.githubusercontent.com/IQSS/dataverse/v6.5/conf/solr/update-fields.sh
chmod +x update-fields.sh
curl "http://localhost:8080/api/admin/index/solr/schema" | sudo ./update-fields.sh /usr/local/solr/solr-9.4.1/server/solr/collection1/conf/schema.xml
```

Now start Solr.

```shell
sudo service solr start
```

8\. Reindex Solr

Below is the simplest way to reindex Solr:

```shell
curl http://localhost:8080/api/admin/index
```

The API above rebuilds the existing index. If you want to be absolutely sure that your index is up-to-date and consistent, you may consider wiping it clean and reindexing everything from scratch (see [the guides](https://guides.dataverse.org/en/latest/admin/solr-search-index.html)). Just note that, depending on the size of your database, a full reindex may take a while and the users will be seeing incomplete search results during that window.

9\. Run reExportAll to update dataset metadata exports

Below is the simple way to reexport all dataset metadata. For more advanced usage, please see [the guides](http://guides.dataverse.org/en/6.4/admin/metadataexport.html#batch-exports-through-the-api).

```shell
curl http://localhost:8080/api/admin/metadata/reExportAll
```

10\. Clear metrics cache

Run the [clearMetricsCache](https://guides.dataverse.org/en/6.5/api/native-api.html#metrics) API endpoint to remove old cached values that may be incorrect.

```shell
curl -X DELETE http://localhost:8080/api/admin/clearMetricsCache
```

11\. Pushing updated metadata to DataCite

(If you don't use DataCite, you can skip this. Also, if you aren't affected by the "useless null" bug described above, you can skip this.)

Entries at DataCite for published datasets can be updated by a superuser using an API call (newly [documented](https://guides.dataverse.org/en/6.5/admin/dataverses-datasets.html#update-metadata-for-all-published-datasets-at-the-pid-provider)):

`curl  -X POST -H 'X-Dataverse-key:<key>' http://localhost:8080/api/datasets/modifyRegistrationPIDMetadataAll`

This will loop through all published datasets (and released files with PIDs). As long as the loop completes, the call will return a 200/OK response. Any PIDs for which the update fails can be found using the following command:

`grep 'Failure for id' server.log`

Failures may occur if PIDs were never registered, or if they were never made findable. Any such cases can be fixed manually in DataCite Fabrica or using the [Reserve a PID](https://guides.dataverse.org/en/6.4/api/native-api.html#reserve-a-pid) API call and the newly documented `/api/datasets/<id>/modifyRegistration` call respectively. See https://guides.dataverse.org/en/6.4/admin/dataverses-datasets.html#send-dataset-metadata-to-pid-provider. Please reach out with any questions.

PIDs can also be updated by a superuser on a per-dataset basis using

`curl -X POST -H 'X-Dataverse-key:<key>' http://localhost:8080/api/datasets/<id>/modifyRegistrationMetadata`
