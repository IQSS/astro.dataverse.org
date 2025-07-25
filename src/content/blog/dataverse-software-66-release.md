---
title: "Dataverse Software 6.6 Release"
description: "Dataverse 6.6 is now available with several important bug fixes and new features."
pubDate: "2025-03-18T00:00:00"
heroImage: "../../assets/dataverse_project_logo.svg"
---

## Release Highlights

Highlights for Dataverse 6.6 include:

- metadata fields can be "display on create" per collection
- ORCIDs linked to accounts
- version notes
- harvesting from DataCite
- citations using Citation Style Language (CSL)
- license metadata enhancements
- metadata fields now support range searches (dates, integers, etc.)
- more accurate search highlighting
- collections can be moved by using the superuser dashboard
- new 3D Objects metadata block
- new Archival metadata block (experimental)
- optionally prevent publishing of datasets without files
- Signposting output now contains links to all dataset metadata export formats
- infrastructure updates (Payara and Solr)

In a recent community call, we talked about many of these highlights if you'd like to watch the [video](https://harvard.zoom.us/rec/share/Ir5CkFHkzoya9b5Nk69rLFUpTyGics3-KGLl9WITSLMy4ezHRsB8CnY22cUNg2g.JPpxrjzHMeCii_zO) (around 22:30).

## Features Added

### Metadata Fields Can Be "Display on Create" Per Collection

Collection administrators can now configure which metadata fields appear during dataset creation through the `displayOnCreate` property, even when fields are not required. This provides greater control over metadata visibility and can help improve metadata completeness.

Currently this feature can only be configured [via API](https://guides.dataverse.org/en/6.6/api/native-api.html#update-collection-input-levels), but a UI implementation is planned in #11221. See #10476, #11224, and #11312.

### ORCIDs Linked to Accounts

Dataverse now includes improved integration with ORCID, supported through a grant to GDCC from the ([ORCID Global Participation Fund](https://info.orcid.org/global-participation-fund-announces-fourth-round-of-awardees/)).

Specifically, Dataverse users can now link their Dataverse account with their ORCID profile. Previously, this was only available to users who logged in with ORCID. Once linked, Dataverse will automatically prepopulate their ORCID to their author metadata when they create a dataset.

This functionality leverages Dataverse's existing support for login via ORCID, but can be turned on independently of it. If ORCID login is enabled, the user's ORCID will automatically be added to their profile. If the user has logged in via some other mechanism, they are able to click a button to initiate a similar authentication process in which the user must login to their ORCID account and approve the connection.

Feedback from installations that enable this functionality is requested and we expect that updates can be made in the next Dataverse release.

See the [User Guide](http://guides.dataverse.org/en/6.6/user/account.html#linking-orcid-with-your-account-profile), [Installation Guide](http://guides.dataverse.org/en/6.6/installation/orcid.html), #7284, and #11222.

### Version Notes

Dataverse now supports the option of adding a version note before or during the publication of a dataset. These notes can be used, for example, to indicate why a version was created or how it differs from the prior version. Whether this feature is enabled is controlled by the flag `dataverse.feature.enable-version-note`. Version notes are shown in the user interface (in the dataset page version table), indexed (as `versionNote`), available via the API, and have been added to the JSON, DDI, DataCite, and OAI-ORE exports.

With the addition of this feature, work has been done to clean-up and rename fields that have been used for specifying the reason for deaccessioning a dataset and providing an optional link to a non-Dataverse location where the dataset still can be found. The former was listed in some JSON-based API calls and exports as "versionNote" and is now "deaccessionNote", while the latter was referred to as "archiveNote" and is now "deaccessionLink".

Further, some database consolidation has been done to combine the deaccessionlink and archivenote fields, which appear to have both been used for the same purpose. The deaccessionlink database field is older and also was not displayed in the current UI. Going forward, only the deaccessionlink column exists.

See the [User Guide](https://guides.dataverse.org/en/6.6/user/dataset-management.html#data-provenance), [API Guide](https://guides.dataverse.org/en/6.6/api/native-api.html#dataset-version-notes) #8431, and #11068.

### OAI-PMH Harvesting from DataCite

DataCite maintains an OAI server (<https://oai.datacite.org/oai>) that serves records for every DOI they have registered. There's been a lot of interest in the community in being able to harvest from them. This way, it will be possible to harvest metadata from institution X even if the institution X does not maintain an OAI server of their own, if they happen to register their DOIs with DataCite. One extra element of this harvesting model that makes it especially powerful and flexible is the DataCite's concept of a "dynamic OAI set": a harvester is not limited to harvesting the pre-defined set of ALL the records registered by the institution X, but can instead harvest virtually any arbitrary subset thereof; any query that the DataCite search API understands can be used as an OAI set. The feature is already in use at Harvard Dataverse, as a beta version patch.

For various reasons, in order to take advantage of this feature harvesting clients must be created using the `/api/harvest/clients` API. Once configured however, harvests can be run from the Harvesting Clients control panel in the UI.

DataCite-harvesting clients must be configured with 2 new feature flags, `useListRecords` and `useOaiIdentifiersAsPids` (added in Dataverse 6.5). Note that these features may be of use when harvesting from other sources, not just from DataCite.

See the [Admin Guide](http://guides.dataverse.org/en/6.6/admin/harvestclients.html#harvesting-from-datacite), [API Guide](http://guides.dataverse.org/en/6.6/api/native-api.html#harvesting-from-datacite), #10909, and #11011.

### Citations Using Citation Style Language (CSL)

This release adds support for generating citations in any of the standard independent formats specified using the [Citation Style Language](https://citationstyles.org).

The CSL formats are available to copy/paste if you click "Cite Dataset" and then "View Styled Citations" on the dataset page. An API call to retrieve a dataset citation in EndNote, RIS, BibTeX, and CSLJson format has also been added. The first three have been available as downloads from the UI (CSLJson is not) but have not been directly accessible via API until now. The CSLJson format is new to Dataverse and can be used with open source libraries to generate all of the other CSL-style citations.

Admins can use a new `dataverse.csl.common-styles` setting to highlight commonly used styles. Common styles are listed in the pop-up, while others can be found by type-ahead search in a list of 1000+ options.

See the [User Guide](http://guides.dataverse.org/en/6.6/user/find-use-data.html#cite-data), [Settings](http://guides.dataverse.org/en/6.6/installation/config.html#dataverse-csl-common-styles), [API Guide](http://guides.dataverse.org/en/6.6/api/native-api.html#get-citation-in-other-formats), and #11163.

### License Metadata Enhancements

- Added new fields to licenses: rightsIdentifier, rightsIdentifierScheme, schemeUri, languageCode. See JSON files under [Adding Licenses](https://guides.dataverse.org/en/6.6/installation/config.html#adding-licenses) in the guides
- Updated DataCite metadata export to include rightsIdentifier, rightsIdentifierScheme, and schemeUri consistent with the DataCite 4.5 schema and examples
- Enhanced metadata exports to include all new license fields
- Existing licenses from the example set included with Dataverse will be automatically updated with new fields
- Existing API calls support the new optional fields

See below for upgrade instructions. See also #10883 and #11232.

### Range Search

This release enhances how numerical and date fields are indexed in Solr. Previously, all fields were indexed as English text (`text_en`), but with this update:

- Integer fields are indexed as `plong`
- Float fields are indexed as `pdouble`
- Date fields are indexed as `date_range` (`solr.DateRangeField`)

This change enables range queries when searching from both the UI and the API, such as `dateOfDeposit:[2000-01-01 TO 2014-12-31]` or `targetSampleActualSize:[25 TO 50]`. See below for a full list of fields that now support range search.

Additionally, search result highlighting is now more accurate, ensuring that only fields relevant to the query are highlighted in search results. If the query is specifically limited to certain fields, the highlighting is now limited to those fields as well. See #10887.

Specifically, the following fields were updated:

- coverage.Depth
- coverage.ObjectCount
- coverage.ObjectDensity
- coverage.Redshift.MaximumValue
- coverage.Redshift.MinimumValue
- coverage.RedshiftValue
- coverage.SkyFraction
- coverage.Spectral.CentralWavelength
- coverage.Spectral.MaximumWavelength
- coverage.Spectral.MinimumWavelength
- coverage.Temporal.StartTime
- coverage.Temporal.StopTime
- dateOfCollectionEnd
- dateOfCollectionStart
- dateOfDeposit
- distributionDate
- dsDescriptionDate
- journalPubDate
- productionDate
- resolution.Redshift
- targetSampleActualSize
- timePeriodCoveredEnd
- timePeriodCoveredStart

### New 3D Objects Metadata Block

A new metadata block has been added for describing 3D object data. You can download it from the [guides](https://guides.dataverse.org/en/6.6/user/appendix.html). See also #11120 and #11167.

All new Dataverse installations will receive this metadata block by default. We recommend adding it by following the upgrade instructions below.

### New Archival Metadata Block (Experimental)

An experimental "Archival" metadata block has been added, [downloadable](https://guides.dataverse.org/en/6.6/user/appendix.html) from the User Guide. The purpose of the metadata block is to enable repositories to register metadata relating to the potential archiving of the dataset at a depositor archive, whether that being your own institutional archive or an external archive, i.e. a historical archive. Feedback is welcome! See also #10626.

### Prevent Publishing of Datasets Without Files

Datasets without files can be optionally prevented from being published through a new "requireFilesToPublishDataset" boolean defined at the collection level. This boolean can be set only via API and only by a superuser. See [Change Collection Attributes](https://guides.dataverse.org/en/6.6/api/native-api.html#change-collection-attributes). If the boolean is not set, the parent collection is consulted. If you do not set the boolean, the existing behavior of datasets being able to be published without files will continue. Superusers can still publish datasets whether or not the boolean is set. See #10981 and #10994.

### Metadata Source Facet Can Now Differentiate Between Harvested Sources

The behavior of the feature flag `index-harvested-metadata-source` and the "Metadata Source" facet, which were added and updated, respectively, in [Dataverse 6.3](https://github.com/IQSS/dataverse/releases/tag/v6.3) (through pull requests #10464 and #10651), have been updated. A new field called "Source Name" has been added to harvesting clients.

Before Dataverse 6.3, all harvested content (datasets and files) appeared together under "Harvested" under the "Metadata Source" facet. This is still the behavior of Dataverse out of the box. Since Dataverse 6.3, enabling the `index-harvested-metadata-source` feature flag (and reindexing) resulted in harvested content appearing under the nickname for whatever harvesting client was used to bring in the content. This meant that instead of having all harvested content lumped together under "Harvested", content would appear under "client1", "client2", etc.

With this release, enabling the `index-harvested-metadata-source` feature flag, populating a new field for harvesting clients called "Source Name" ("sourceName" in the [API](https://dataverse-guide--11217.org.readthedocs.build/en/11217/api/native-api.html#create-a-harvesting-client)), and reindexing (see upgrade instructions below) results in the source name appearing under the "Metadata Source" facet rather than the harvesting client nickname. This gives you more control over the name that appears under the "Metadata Source" facet and allows you to reuse the same source name to group harvested content from various harvesting clients under the same name if you wish.

Previously, `index-harvested-metadata-source` was not documented in the guides, but now you can find information about it under [Feature Flags](https://guides.dataverse.org/en/6.6/installation/config.html#feature-flags). See also #10217 and #11217.

### Globus Framework Improvements

The improvements and optimizations in this release build on top of the earlier work (such as #10781). They are based on the experience gained at IQSS as part of the production rollout of the Large Data Storage services that utilizes Globus.

The changes in this release focus on improving Globus _downloads_, i.e., transfers from Dataverse-linked Globus volumes to users' Globus collections. Most importantly, the mechanism of "Asynchronous Task Monitoring", first introduced in #10781 for _uploads_, has been extended to handle downloads as well. This generally makes downloads more reliable, specifically in how Dataverse manages temporary access rules granted to users, minimizing the risk of consequent downloads failing because of stale access rules left in place.

Multiple other improvements have been made making the underlying Globus framework more reliable and robust.

See `globus-use-experimental-async-framework` under [Feature Flags](https://guides.dataverse.org/en/6.6/installation/config.html#feature-flags) and [dataverse.files.globus-monitoring-server](https://guides.dataverse.org/en/6.6/installation/config.html#dataverse-files-globus-monitoring-server) in the Installation Guide, #11057, and #11125.

### OIDC Bearer Tokens

The release extends the OIDC API auth mechanism, available through feature flag `api-bearer-auth`, to properly handle cases where `BearerTokenAuthMechanism` successfully validates the token but cannot identify any Dataverse user because there is no account associated with the token.

To register a new user who has authenticated via an OIDC provider, a new endpoint has been implemented (`/users/register`). A feature flag named `api-bearer-auth-provide-missing-claims` has been implemented to allow sending missing user claims in the request JSON. This is useful when the identity provider does not supply the necessary claims. However, this flag will only be considered if the `api-bearer-auth` feature flag is enabled. If the latter is not enabled, the `api-bearer-auth-provide-missing-claims` flag will be ignored.

A feature flag named `api-bearer-auth-handle-tos-acceptance-in-idp` has been implemented. When enabled, it specifies that Terms of Service acceptance is managed by the identity provider, eliminating the need to explicitly include the acceptance in the user registration request JSON.

See [the guides](https://guides.dataverse.org/en/6.6/api/auth.html#bearer-tokens), #10959, and #10972.

### Signposting Output Now Contains Links to All Dataset Metadata Export Formats

When Signposting was added in Dataverse 5.14 (#8981), it provided links only for the `schema.org` metadata export format.

The output of HEAD, GET, and the Signposting "linkset" API have all been updated to include links to all available dataset metadata export formats, including any external exporters, such as Croissant, that have been enabled.

This provides a lightweight machine-readable way to first retrieve a list of links, such as via a HTTP HEAD request, to each available metadata export format and then follow up with a request for the export format of interest.

In addition, the content type for the `schema.org` dataset metadata export format has been corrected. It was `application/json` and now it is `application/ld+json`.

See also [the guides](https://guides.dataverse.org/en/6.6/api/native-api.html#retrieve-signposting-information), #10542 and #11045.

### Dataset Types Can Be Linked to Metadata Blocks

Metadata blocks, such as (e.g. "CodeMeta") can now be linked to dataset types (e.g. "software") using new superuser APIs.

This will have the following effects for the APIs used by the [new Dataverse UI](https://github.com/IQSS/dataverse-frontend):

- The list of fields shown when creating a dataset will include fields marked as "displayoncreate" (in the tsv/database) for metadata blocks (e.g. "CodeMeta") that are linked to the dataset type (e.g. "software") that is passed to the API.
- The metadata blocks shown when editing a dataset will include metadata blocks (e.g. "CodeMeta") that are linked to the dataset type (e.g. "software") that is passed to the API.

Mostly in order to write automated tests for the above, a [displayOnCreate](https://guides.dataverse.org/en/6.6/api/native-api.html#set-displayoncreate-for-a-dataset-field) API endpoint has been added.

For more information, see the guides ([overview](https://guides.dataverse.org/en/6.6/user/dataset-management.html#dataset-types), [new APIs](https://guides.dataverse.org/en/6.6/api/native-api.html#link-dataset-type-with-metadata-blocks)), #10519 and #11001.

### Other Features

- In addition to the API [Move a Dataverse Collection](https://guides.dataverse.org/en/6.6/admin/dataverses-datasets.html#move-a-dataverse-collection), it is now possible for a Dataverse administrator to move a collection using the Dataverse dashboard. See #10304 and #11150.
- The Preview URL popup and [related documentation](https://guides.dataverse.org/en/6.6/user/dataset-management.html#preview-url-to-review-unpublished-dataset) have been updated to give more information about anonymous access, including the names of the dataset fields that will be withheld from the Anonymous Preview URL user and to suggest how to review the URL before releasing it. See also #11159 and #11164.
- [ROR](https://ror.org) (Research Organization Registry) has been added as an Author Identifier Type for when the author is an organization rather than a person. Like ORCID, ROR will appear in the "Datacite" metadata export format. See #11075 and #11118.
- The publisher value of harvested datasets is now attributed to the dataset's distributor instead of its producer. This improves the citation associated with these datasets, but the change affects only newly harvested datasets. See "Upgrade Instructions" below on how to re-harvest. For more information, see [the guides](http://guides.dataverse.org/en/6.6/admin/harvestclients.html#harvesting-client-changelog), #8739, and #9013.
- A new harvest status differentiates between a complete harvest with errors ("completed with failures") and without errors ("completed"). Also, harvest status labels are now internationalized. See #9294 and #11017.
- The OAI-ORE exporter can now export metadata containing nested compound fields or compound fields within compound fields. See #10809 and #11190.
- It is now possible to edit a custom role with the same alias. See #8808 and #10612.
- The [Metadata Customization](https://guides.dataverse.org/en/6.6/admin/metadatacustomization.html#controlledvocabulary-enumerated-properties) documentation has been updated to explain how to implement a boolean fieldtype (look for "boolean"). See #7961 and #11064.
- The version of Stata files is now detected during S3 direct upload (as it was for normal uploads), allowing ingest of Stata 14 and 15 files that have been uploaded directly. See [the guides](https://guides.dataverse.org/en/6.6/developers/big-data-support.html#features-that-are-disabled-if-s3-direct-upload-is-enabled) #10108, and #11054.
- It is now possible to populate the "Keyword" metadata field from an [OntoPortal](https://ontoportal.org) service. The code has been shared to the GDCC [dataverse-external-vocab-support](https://github.com/gdcc/dataverse-external-vocab-support#scripts-in-production) GitHub repository. See #11258.
- Support for legacy configuration of a PermaLink PID provider, such as using the :Protocol,:Authority, and :Shoulder settings, has been fixed. See #10516 and #10521.
- On the home page for each guide (User Guide, etc.) there was an overwhelming amount of information in the form of a deeply nested table of contents. The depth of the table of contents has been reduced to two levels, making the home page for each guide more readable. Compare the User Guide for [6.5](https://guides.dataverse.org/en/6.5/user/index.html) vs. [6.6](https://guides.dataverse.org/en/6.6/user/index.html) and see #11166.
- For compliance with GDPR and other privacy regulations, advice on adding a cookie consent popup has been added to the guides. See the new [cookie consent](https://guides.dataverse.org/en/6.6/installation/config.html#adding-cookie-consent-for-gdpr-etc) section and #10320.
- A new file has been added to import the French Open License to Dataverse: licenseEtalab-2.0.json. You can download it from [the guides](http://guides.dataverse.org/en/6.6/installation/config.html#adding-licenses). This license, which is compatible with the Creative Commons license, is recommended by the French government for open documents. See #9301, #9302, and #11302.
- The API that lists versions of a dataset now features an optional `excludeMetadataBlocks` parameter, which defaults to "false" for backward compatibility. For a dataset with a large number of versions and/or metadataBlocks, having the metadata blocks included can dramatically increase the volume of the output. See also [the guides](https://guides.dataverse.org/en/6.6/api/native-api.html#list-versions-of-a-dataset), #10171, and #10778.
- Deeply nested metadata fields are not supported but the code used to generate the Solr schema has been adjusted to support them. See #11136.
- The [tutorial](https://guides.dataverse.org/en/6.6/container/running/demo.html) on running Dataverse in Docker has been updated to explain how to configure the root collection using a JSON file (#10541 and #11201) and now uses the Permalink PID provider instead of the FAKE DOI Provider (#11107 and #11108).
- Payara application server has been upgraded to version 6.2025.2. See #11126 and #11128.
- Solr has been upgraded to version 9.8.0. See #10713.
- For testing purposes, the FAKE PID provider can now be used with [file PIDs enabled](https://guides.dataverse.org/en/6.6/installation/config.html#filepidsenabled). (The FAKE provider is not recommended for any production use.) See #10979.

## Bugs Fixed

- A bug which causes users of the Anonymous Review URL to have some metadata of published datasets withheld has been fixed. See #11202 and #11164.
- A bug that caused ORCIDs starting with "https://orcid.org/" entered as author identifier to be ignored when creating the DataCite metadata has been fixed. This primarily affected users of the ORCID external vocabulary script; for the manual entry form, we used to recommend not using the URL form. The display of authorIdentifier, when not using any external vocabulary scripts, has been improved so that either the plain identifier (e.g. "0000-0002-1825-0097") or its URL form (e.g. "https://orcid.org/0000-0002-1825-0097") will result in valid links in the display (for identifier types that have a URL form). The URL form is now [recommended](http://guides.dataverse.org/en/6.6/user/dataset-management.html#adding-a-new-dataset) when doing manual entry. See #11242 and #11242.
- Multiple small issues with the formatting of PIDs in the DDI exporters, and EndNote and BibTeX citation formats have been addressed. These should improve the ability to import Dataverse citations into reference managers and fix potential issues harvesting datasets using PermaLinks. See #10768, #10769, #11165, and #10790.
- On the Advanced Search page, the metadata fields are now displayed in the correct order as defined in the TSV file via the displayOrder value, making the order the same as when you view or edit metadata. Note that fields that are not defined in the TSV file, like the "Persistent ID" and "Publication Date", will be displayed at the end. See #11272 and #11279.
- Bugs that caused 1) guestbook questions to appear along with terms of use/terms of access in the request access dialog when no guestbook was configured, and 2) terms of access to not be shown when using the per-file request access/download menu items have been fixed. Text related to configuring the choice to have guestbooks appear when file access is requested or when files are downloaded has been updated to make it clearer that this affects only datasets where guestbooks have been configured. See #11203.
- The file page version table now shows whether a file has been replaced. See #11142 and #11145.
- We fixed an issue where draft versions of datasets were sorted using the release timestamp of their most recent major version. This caused newer drafts to appear incorrectly alongside their corresponding major version, instead of at the top, when sorted by "newest first". Sorting now uses the last update timestamp when sorting draft datasets. The sorting behavior of published major and minor dataset versions is unchanged. There is no need to reindex datasets because Solr is being upgraded (see "Upgrade Instructions"), which will result in an empty database that will be reindexed. See #11178.
- Some external controlled vocabulary scripts/configurations, when used on a metadata field that is single-valued, could result in indexing failure for the dataset, e.g. when the script tried to index both the identifier and name of the identified entity for indexing. Dataverse has been updated to correctly indicate the need for a multi-valued Solr field in these cases in the call to `/api/admin/index/solr/schema`. Configuring the Solr schema and running the update-fields.sh script as usually recommended when using custom metadata blocks (see "Upgrade Instructions") will resolve the issue. See [the guides](https://guides.dataverse.org/en/6.6/admin/metadatacustomization.html#using-external-vocabulary-services), #11095, and #11096.
- The OpenAIRE metadata export format can now correctly process one or multiple productionPlaces as geolocation. See #9546 and #11194
- We fixed a bug that caused adding free-form provenance to a file to fail. See #11145.
- A bug has been fixed which could cause publication of datasets to fail in cases where they were not assigned a DOI at creation. See #11234 and #11236.
- When users request access to files, the people who have permission to grant access received an email with a link that didn't work due to a trailing period (full stop) right next to the link, e.g. `https://demo.dataverse.org/permissions-manage-files.xhtml?id=9.` A space has been added to fix this. See #10384 and #11115.
- Harvesting clients now use the correct granularity while re-running a partial harvest, using the `from` parameter. The correct granularity comes from the `Identify` verb request. See #11020 and #11038.
- Access requests were missing on the File Permission page after upgrading from Dataverse 6.0. This has been corrected with a database update script. See #10714 and #11061.
- When a dataset has a long running lock, including when it is "in review", Dataverse will now slow the page refresh rate over time. See #11264 and #11269.
- The `/api/info/metrics/files/monthly` API call had a bug that resulted in files being counted each time they were published in a new version if those publication events occurred in different months. This resulted in an over-count. The `/api/info/metrics/files` and `/api/info/metrics/files/toMonth` API calls had a bug that resulted in files that were published but no longer in the latest published version as of the specified date (now, or the date entered in the `/toMonth` variant). This resulted in an under-count. See #11189.
- DatasetFieldTypes in MetadataBlock response that are also a child of another DatasetFieldType were being returned twice. The child DatasetFieldType was included in the "fields" object as well as in the "childFields" of its parent DatasetFieldType. This fix suppresses the standalone object so only one instance of the DatasetFieldType is returned (in the "childFields" of its parent). This fix changes the JSON output of the API `/api/dataverses/{dataverseAlias}/metadatablocks` (see "Backward Incompatible Changes", below). See #10472 and #11066.
- A bug that caused replacing files via API when file PIDs were enabled to fail has been fixed. See #10975 and #10979.
- The [:CustomDatasetSummaryFields](https://guides.dataverse.org/en/6.6/installation/config.html#customdatasetsummaryfields) setting now allows spaces along with a comma separating field names. In addition, a bug that caused license information to be hidden if there are no values for any of the custom fields specified has been fixed. See #11228 and #11229.
- Dataverse 6.5 introduced a bug which causes search to fail for non-superusers in multiple groups when the `AVOID_EXPENSIVE_SOLR_JOIN` feature flag is set to true. This release fixes the bug. See #11133 and #11134.
- We fixed a bug with My Data where listing collections for a user with only rights on harvested collections would result in a server error response. See #11083.
- Minor styling fixes for the Related Publication field and fields using ORCID or ROR have been made. See #11053, #10964, and #11106.
- In the Search API, files were displaying DRAFT version instead of latest released version under `dataset_citation`. See #10735 and #11051.
- Unnecessary Solr documents were being created when a file was added or deleted from a draft dataset. These documents could accumulate and potentially impact performance. There is no action to take because this release includes a new Solr version, which will start with an empty database. See #11113 and #11114.
- When using the API to update a collection, omitting optional fields such as `inputLevels`, `facetIds`, or `metadataBlockNames` caused data to be deleted. The fix no longer deletes data for these fields. Two new flags have been added to the `metadataBlocks` JSON object to signal the deletion of the data: `inheritMetadataBlocksFromParent: true` and `inheritFacetsFromParent: true`. See [the guides](https://guides.dataverse.org/en/6.6/api/native-api.html#update-a-dataverse-collection), #11130, and #11144.

## API Updates

### Search API Returns Additional Fields for Files

Added new fields to search results type=files

For Files:

- restricted: boolean
- canDownloadFile: boolean (from file user permission)
- categories: array of string "categories" would be similar to what it is in metadata api.

For tabular files:

- tabularTags: array of string for example, `{"tabularTags" : ["Event", "Genomics", "Geospatial"]}`
- variables: number/int shows how many variables we have for the tabular file
- observations: number/int shows how many observations for the tabular file

See #11027 and #11097.

### Backend Support for Collection Featured Items

CRUD endpoints for Collection Featured Items have been implemented. In particular, the following endpoints have been implemented:

- Create a feature item (POST `/api/dataverses/<dataverse_id>/featuredItems`)
- Update a feature item (PUT `/api/dataverseFeaturedItems/<item_id>`)
- Delete a feature item (DELETE `/api/dataverseFeaturedItems/<item_id>`)
- List all featured items in a collection (GET `/api/dataverses/<dataverse_id>/featuredItems`)
- Delete all featured items in a collection (DELETE `/api/dataverses/<dataverse_id>/featuredItems`)
- Update all featured items in a collection (PUT `/api/dataverses/<dataverse_id>/featuredItems`)

See also the "Settings Added" section, #10943 and #11124.

### Other API Updates

- Multiple files can be deleted from a dataset at once. See the [the guides](https://guides.dataverse.org/en/6.6/api/native-api.html#delete-files-from-a-dataset) and #11230.
- An API has been added to get the "classic" download count from a dataset with an optional `includeMDC` parameter (for Make Data Count). See [the guides](http://guides.dataverse.org/en/6.6/api/native-api.html#get-the-download-count-of-a-dataset), #11244 and #11282.
- An API has been added that lists the collections that the user has access to via the permission passed. See [the guides](http://guides.dataverse.org/en/6.6/api/native-api.html#list-dataverse-collections-a-user-can-act-on-based-on-their-permissions), #6467, and #10906.
- An API has been added to get dataset versions including a summary of differences between consecutive versions where available. See [the docs](https://guides.dataverse.org/en/6.6/api/native-api.html#get-versions-of-a-dataset-with-summary-of-changes), #10888, and #10945.
- An API has been added to list of versions of a data file showing any changes that affected the file with each version. See [the guides](https://guides.dataverse.org/en/6.6/api/native-api.html#get-json-representation-of-a-file-s-versions), #11198 and #11237.
- The Search API has a new [parameter](https://guides.dataverse.org/en/6.6/api/search.html#parameters) called `show_type_counts`. If you set it to true, it will return `total_count_per_object_type` for the types dataverse, dataset, and files (#11065 and #11082) even if the search result for any given type is 0 (#11127 and #11138).
- CRUD operations for external tools are now available for superusers from non-localhost. See [the guides](https://guides.dataverse.org/en/6.6/admin/external-tools.html#managing-external-tools), #10930 and #11079.
- A new API endpoint has been added that allows a global role to be updated. See [the guides](https://guides.dataverse.org/en/6.6/api/native-api.html#update-global-role) and #10612.
- An API has been added to send feedback to the collection, dataset, or data file's contacts. If necessary, you can [rate limit](https://guides.dataverse.org/en/6.6/installation/config.html#rate-limiting) the `CheckRateLimitForDatasetFeedbackCommand` and configure the new [:ContactFeedbackMessageSizeLimit](https://guides.dataverse.org/en/6.6/installation/config.html#contactfeedbackmessagesizelimit) database setting. See [the guides](http://guides.dataverse.org/en/6.6/api/native-api.html#send-feedback-to-contact-s), #11129, and #11162.
- /api/metadatablocks is no longer returning duplicated metadata properties and does not omit metadata properties when called. See "Backward Incompatible Changes" below and #10764.
- A new query param, `returnChildCount`, has been added to the getDataverse endpoint (`/api/dataverses/{id}`) for optionally retrieving the child count, which represents the number of collections, datasets, or files within the collection (direct children only). See also #11255 and #11259.

## End-Of-Life (EOL) Announcements

### PostgreSQL 13 reaches EOL on 13 November 2025

Per <https://www.postgresql.org/support/versioning/> PostgreSQL 13 reaches EOL on 13 November 2025. Our first step toward moving off version 13 was to [switch](https://github.com/gdcc/dataverse-ansible/commit/8ebbd84ad2cf3903b8f995f0d34578250f4223ff) our testing to version 16, as we've [noted](https://guides.dataverse.org/en/6.6/installation/prerequisites.html#postgresql) in the guides. You are encouraged to start planning your upgrade and may want to review the [Dataverse 5.4 release notes](https://github.com/IQSS/dataverse/releases/tag/v5.4) as the upgrade process (e.g. `pg_dumpall`, etc.) will likely be similar. If you notice any bumps along the way, please let us know!

Dataverse developers [using Docker](https://guides.dataverse.org/en/6.6/container/dev-usage.html) have been using PostgreSQL 17 since Dataverse 6.5 (#10912). (Developers not using Docker who are still on PostgreSQL 13 are encouraged to upgrade.) Older or newer versions should work, within reason.

See also #11212 and #11215.

## Security

### SameSite Cookie Attribute

The SameSite cookie attribute is defined in an upcoming revision to [RFC 6265](https://datatracker.ietf.org/doc/html/rfc6265) (HTTP State Management Mechanism) called [6265bis](https://datatracker.ietf.org/doc/html/draft-ietf-httpbis-rfc6265bis-19>) ("bis" meaning "repeated"). The possible values are "None", "Lax", and "Strict".

"If no SameSite attribute is set, the cookie is treated as Lax by default" by browsers according to [MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#controlling_third-party_cookies_with_samesite). This was the previous behavior of Dataverse, to not set the SameSite attribute.

New Dataverse installations now explicitly set to the SameSite cookie attribute to "Lax" out of the box through the installer (in the case of a "classic" installation) or through an updated base image (in the case of a Docker installation). Classic installations should follow the upgrade instructions below to bring their installation up to date with the behavior for new installations. Docker installations will automatically get the updated base image.

While you are welcome to experiment with "Strict", which is intended to help prevent Cross-Site Request Forgery (CSRF) attacks, as described in the RFC proposal and an OWASP [cheatsheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#samesite-cookie-attribute), our testing so far indicates that some functionality, such as OIDC login, seems to be incompatible with "Strict".

You should avoid the use of "None" as it is less secure than "Lax". See also [the guides](https://guides.dataverse.org/en/6.6/installation/config.html#samesite-cookie-attribute), https://github.com/IQSS/dataverse-security/issues/27, #11210, and the upgrade instructions below.

## Settings Added

- dataverse.feature.enable-version-note
- dataverse.csl.common-styles
- dataverse.files.featured-items.image-maxsize - It sets the maximum allowed size of the image that can be added to a featured item.
- dataverse.files.featured-items.image-uploads - It specifies the name of the subdirectory for saving featured item images within the docroot directory.
- dataverse.feature.api-bearer-auth-provide-missing-claims
- dataverse.feature.api-bearer-auth-handle-tos-acceptance-in-idp
- :ContactFeedbackMessageSizeLimit

## Backward Incompatible Changes

Generally speaking, see the [API Changelog](https://guides.dataverse.org/en/latest/api/changelog.html) for a list of backward-incompatible API changes.

- /api/metadatablocks is no longer returning duplicated metadata properties and does not omit metadata properties when called. See #10764.
- The JSON response of API call `/api/dataverses/{dataverseAlias}/metadatablocks` will no longer include the DatasetFieldTypes in "fields" if they are children of another DatasetFieldType. The child DatasetFieldType will only be included in the "childFields" of its parent DatasetFieldType. See #10472 and #11066.
- `versionNote` has been renamed to `deaccessionNote`. `archiveNote` has been renamed to `deaccessionLink`. See #11068.
- The [Show Role](https://guides.dataverse.org/en/6.6/api/native-api.html#show-role) API endpoint was returning 401 Unauthorized when a permission check failed. This has been corrected to return 403 Forbidden instead. That is, the API token is known to be good (401 otherwise) but the user lacks permission (403 is now sent). See also the [API Changelog](https://guides.dataverse.org/en/6.6/11116/api/changelog.html), #10340, and #11116.
- Changes to PID formatting occur in the DDI/DDI Html export formats and the EndNote and BibTex citation formats. These changes correct errors and improve conformance with best practices but could break parsing of these formats. See #10768, #10769, #11165, and #10790.

## Complete List of Changes

For the complete list of code changes in this release, see the [6.6 milestone](https://github.com/IQSS/dataverse/issues?q=milestone%3A6.6+is%3Aclosed) in GitHub.

## Getting Help

For help with upgrading, installing, or general questions please post to the [Dataverse Community Google Group](https://groups.google.com/g/dataverse-community) or email support@dataverse.org.

## Installation

If this is a new installation, please follow our [Installation Guide](https://guides.dataverse.org/en/latest/installation/). Please don't be shy about [asking for help](https://guides.dataverse.org/en/latest/installation/intro.html#getting-help) if you need it!

Once you are in production, we would be delighted to update our [map of Dataverse installations](https://dataverse.org/installations) around the world to include yours! Please [create an issue](https://github.com/IQSS/dataverse-installations/issues) or email us at support@dataverse.org to join the club!

You are also very welcome to join the [Global Dataverse Community Consortium](https://www.gdcc.io/) (GDCC).

## Upgrade Instructions

Upgrading requires a maintenance window and downtime. Please plan accordingly, create backups of your database, etc.

These instructions assume that you've already upgraded through all the 5.x releases and are now running Dataverse 6.5.

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
$PAYARA/bin/asadmin undeploy dataverse-6.5
```

3\. Stop Payara

```shell
sudo service payara stop
```

4\. Upgrade to Payara 6.2025.2

The steps below reuse your existing domain directory with the new distribution of Payara. You may also want to review the Payara upgrade instructions as it could be helpful during any troubleshooting:
[Payara Release Notes](https://docs.payara.fish/community/docs/6.2025.2/Release%20Notes/Release%20Notes%206.2025.2.html).
We also recommend you ensure you followed all update instructions from the past releases regarding Payara.
(The most recent Payara update was for [v6.3](https://github.com/IQSS/dataverse/releases/tag/v6.3).)

Move the current Payara directory out of the way:

```shell
mv $PAYARA $PAYARA.6.2024.6
```

Download the new Payara version 6.2025.2 (from https://www.payara.fish/downloads/payara-platform-community-edition/ or https://nexus.payara.fish/repository/payara-community/fish/payara/distributions/payara/6.2025.2/payara-6.2025.2.zip), and unzip it in its place:

```shell
cd /usr/local
unzip payara-6.2025.2.zip
```

Replace the brand new `payara/glassfish/domains/domain1` with your old, preserved domain1:

```shell
mv payara6/glassfish/domains/domain1 payara6/glassfish/domains/domain1_DIST
mv payara6.6.2024.6/glassfish/domains/domain1 payara6/glassfish/domains/
```

5\. Download and deploy this version

```shell
wget https://github.com/IQSS/dataverse/releases/download/v6.6/dataverse-6.6.war
$PAYARA/bin/asadmin deploy dataverse-6.6.war
```

Note: if you have any trouble deploying, stop Payara, remove the following directories, start Payara, and try to deploy again.

```shell
sudo service payara stop
sudo rm -rf $PAYARA/glassfish/domains/domain1/generated
sudo rm -rf $PAYARA/glassfish/domains/domain1/osgi-cache
sudo rm -rf $PAYARA/glassfish/domains/domain1/lib/databases
sudo service payara start
```

6\. For installations with internationalization or text customizations:

Please remember to update translations via [Dataverse language packs](https://github.com/GlobalDataverseCommunityConsortium/dataverse-language-packs).

If you have text customizations you can get the latest English files from <https://github.com/IQSS/dataverse/tree/v6.6/src/main/java/propertyFiles>.

7\. Decide to enable (or not) the `index-harvested-metadata-source` feature flag

Decide whether or not to enable the `dataverse.feature.index-harvested-metadata-source` feature flag described above, in [the guides](https://guides.dataverse.org/en/6.6/installation/config.html#feature-flags), #10217 and #11217. The reason to decide now is that reindexing is required and the next steps involve restarting Payara and upgrading Solr, which will result in a fresh index.

8\. Configure SameSite

To bring your Dataverse installation in line with new installations, as described above and in [the guides](https://guides.dataverse.org/en/6.6/installation/config.html#samesite-cookie-attribute), we recommend running the following commands:

```
./asadmin set server-config.network-config.protocols.protocol.http-listener-1.http.cookie-same-site-value=Lax

./asadmin set server-config.network-config.protocols.protocol.http-listener-1.http.cookie-same-site-enabled=true
```

Please note that "None" is less secure than "Lax" and should be avoided. You can test the setting by inspecting headers with curl, looking at the JSESSIONID cookie for "SameSite=Lax" (yes, it's expected to be repeated, probably due to a bug in Payara) like this:

```
% curl -s -I http://localhost:8080 | grep JSESSIONID
Set-Cookie: JSESSIONID=6574324d75aebeb86dc96ecb3bb0; Path=/;SameSite=Lax;SameSite=Lax
```

Before making the changes above, SameSite attribute should be absent, like this:

```
% curl -s -I http://localhost:8080 | grep JSESSIONID
Set-Cookie: JSESSIONID=6574324d75aebeb86dc96ecb3bb0; Path=/
```

8\. Restart Payara

```shell
sudo service payara stop
sudo service payara start
```

9\. Update metadata blocks

These changes reflect incremental improvements made to the handling of core metadata fields.

Expect the loading of the citation block to take several seconds because of its size (especially due to the number of languages).

```shell
wget https://raw.githubusercontent.com/IQSS/dataverse/v6.6/scripts/api/data/metadatablocks/citation.tsv

curl http://localhost:8080/api/admin/datasetfield/load -H "Content-type: text/tab-separated-values" -X POST --upload-file citation.tsv
```

The 3D Objects metadata block is included in all new installations of Dataverse so we recommend adding it.

```shell
wget https://raw.githubusercontent.com/IQSS/dataverse/v6.6/scripts/api/data/metadatablocks/3d_objects.tsv

curl http://localhost:8080/api/admin/datasetfield/load -H "Content-type: text/tab-separated-values" -X POST --upload-file 3d_objects.tsv
```

10\. Upgrade Solr

Solr 9.8.0 is now the version recommended in our Installation Guide and used with automated testing. Additionally, due to the new range search support feature and the addition of fields (e.g. versionNote, fileRestricted, canDownloadFile, variableCount, and observations), the default `schema.xml` files has changed so you must upgrade.

Install Solr 9.8.0 following the [instructions](https://guides.dataverse.org/en/6.6/installation/prerequisites.html#solr) from the Installation Guide.

The instructions in the guide suggest to use the config files from the installer zip bundle. When upgrading an existing instance, it may be easier to download them from the source tree:

```shell
wget https://raw.githubusercontent.com/IQSS/dataverse/v6.6/conf/solr/solrconfig.xml
wget https://raw.githubusercontent.com/IQSS/dataverse/v6.6/conf/solr/schema.xml
cp solrconfig.xml schema.xml /usr/local/solr/solr-9.8.0/server/solr/collection1/conf
```

10a\. For installations with additional metadata blocks or external controlled vocabulary scripts, update fields

- Stop Solr instance (usually `service solr stop`, depending on Solr installation/OS, see the [Installation Guide](https://guides.dataverse.org/en/6.6/installation/prerequisites.html#solr-init-script)).

- Run the `update-fields.sh` script that we supply, as in the example below (modify the command lines as needed to reflect the correct path of your Solr installation):

```shell
wget https://raw.githubusercontent.com/IQSS/dataverse/v6.6/conf/solr/update-fields.sh
chmod +x update-fields.sh
curl "http://localhost:8080/api/admin/index/solr/schema" | ./update-fields.sh /usr/local/solr/solr-9.8.0/server/solr/collection1/conf/schema.xml
```

- Start Solr instance (usually `service solr start` depending on Solr/OS).

11\. Reindex Solr

```shell
curl http://localhost:8080/api/admin/index
```

12\. Run reExportAll to update dataset metadata exports

For existing published datasets, additional license metadata will not be available from DataCite or in metadata exports until

- the dataset is republished or
- the /api/admin/metadata/{id}/reExportDataset is run for the dataset or
- the /api/datasets/{id}/modifyRegistrationMetadata API is run for the dataset or
- the global version of these API calls (/api/admin/metadata/reExportAll, /api/datasets/modifyRegistrationPIDMetadataAll) are used.

For this reason, we recommend reexporting all dataset metadata. For more advanced usage, please see [the guides](http://guides.dataverse.org/en/6.6/admin/metadataexport.html#batch-exports-through-the-api).

```shell
curl http://localhost:8080/api/admin/metadata/reExportAll
```

13\. (Optional) Re-harvest datasets

The publisher value of harvested datasets is now attributed to the dataset's distributor instead of its producer. For more information, see [the guides](http://guides.dataverse.org/en/6.6/admin/harvestclients.html#harvesting-client-changelog), #8739, and #9013.

This improves the citation associated with these datasets, but the change only affects newly harvested datasets.

If you would like to pick up this change for existing harvested datasets, you should re-harvest them. This can be accomplished by deleting and re-adding each harvesting client, followed by a harvesting run. You may want to use [harvesting client APIs](https://guides.dataverse.org/en/6.6/api/native-api.html#managing-harvesting-clients) to save (serialize), add, and remove clients.
