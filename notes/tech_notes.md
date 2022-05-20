# Notes for technical discussions

- When: Fridays, 12:00 ET
- Where: https://arizona.zoom.us/j/84283310669
- Notes: https://hackmd.io/SLE_8TwcSJu5fTeqwkdXng

These notes are stored in github under [`home/notes/tech_notes.md`](https://github.com/isamplesorg/home/blob/master/notes/tech_notes.md). 
Notes from earlier meetings are at https://docs.google.com/document/d/1GIScduypIrdPi5P62DhvrBCmCw0ypkEJpAM8iQNzCkw/edit?usp=sharing


## 2022-05-20

Identifier minting
- [`/isb_lib/identifiers`](https://github.com/isamplesorg/isamples_inabox/tree/develop/isb_lib/identifiers)
- Currently supports (future) IGSN, DOI. ARKs support straight forward.

Record landing pages
- Provide a landing page for each identifier on iSC or iSB
- Machine readable with record content embedded as json-ld
- Human readable rendered client side

Vocabularies

- Three top level vocabularies (taxonomies)
    - [Specimen type](https://isamplesorg.github.io/models/generated/vocabularies/specimenType.html)
    - [Material type](https://isamplesorg.github.io/models/generated/vocabularies/materialType.html)
    - [Sampled feature](https://isamplesorg.github.io/models/generated/vocabularies/sampledFeature.html)
- Mapping existing records was done earlier, though was incomplete is some areas
- Options for mapping:
    - Records are manually annotated with vocab term (not scalable)
    - Controlled terms in source align with vocab term (limited existing vocabs)
    - Automated mapping from record content (requires training, varying precision)
    - Semi-automated (automated suggestion, approval / correction provides feedback to training for progressive improvement)
- Vocabulary extension adding (possibly domain specific) terms increasing specificity for high level taxonomy, i.e. adding narrower terms.
    - Additional terms may be gleaned from corpus by vaious clustering approaches.

- Overlap with TDWG / Darwin Core activities
  -[materialSampleType Brainstorming Activity](https://docs.google.com/spreadsheets/d/1Q2Bj9uhCD0HWEgm_hGe3CeMleMIFYMzYqWXgtgblYOU/edit#gid=0)


## 2022-04-15

Hackathon / technical docs:
- Setting up an iSB
- Documentation for onboarding 

### WebUI

- Cesium3D view now on iSC
    - https://hyde.cyverse.org/isamples_central/ui/
- Feedback needed
    - Add issues to https://github.com/isamplesorg/isamples_webui/issues

- Option to save state to a database, and retrieve that information as the current view (query and 3d view). This would enable easy sharing and gathering metrics
- 

### Identifier Minting

A use case to be supported by iSamples is the minting (creation) of identifiers to be associated with physical samples.

- > O12: Researcher needs to manage field assigned identifiers. Difficulty is reconciling identifiers that describe the context and the information from someone describing the samples in a bag. People have different references in the field, and then need to reconcile is this trench two the same trench 2?
- > C1: Researchers want to be able to get sample IDs and record sample metadata early in a project.

States of an identifier:

1. Identifier is created with no information 
2. Identifier is associated with uncurated information
3. Identified information has been validated and stored in repo
4. Identified information is published (generally available)
5. Identified information is deprecated

Information required to mint an identifier (properties of the identifier):

- who
- when
- allocating agent (the system that the research isusing to mint the identifier)
- other?
- intended use of the identifier (what is it identifying)

Users minting identifiers should be authenticated, this will provide the "who".

Different kinds of identifier for different purposes. e.g.: IGSN for physical samples; ORCID for people; ROR for organizations; ARK or DOI for media (or anything else)

SESAR - request hold on identifier range. Need a flag on the identifiers to indicate "unregistered" / record hasn't been added to a collection.

Given a freshly minted identifier, is the existence of the identifier recorded by the allocating agent? Or by the requestor?

Microservice to generate an opaque string. A stateful minter ensures uniqueness across minting. A stateless minter may take the last identifier minted and return the next.

What about the 50000 identifiers I didn't use in the field? Can they be returned?

ROR and ORCID are centralized minters. 

Perhaps mint a single "prefix" identifier and the researcher can add to the prefix to gather unique identifiers.

Biodegradable identifiers? Option to extend expiration of unassigned identifiers.







## 2022-04-08

### Backend

- Performance improvements for harvesting and ingest
- Loading and reindexing processmore performant for changes to index and / or core data model

### WebUI

- Cesium 3d view streaming updates
- A couple more optimizations to use:
    - geohash for proximity 
    - pre-compute elevations for terrain display

### Vocabularies, narrower terms

Three vocabularies:
- Sampled Feature
- Specimen Type
- Material Type

Identify narrower terms for each vocabulary to assist with discovery. e.g. SESAR has extensive lists of somewhat controlled terms. Can we normalize those terms and extend the vocabularies with terms as appropriate?

For OpenContext, can do manual alignment from Getty and British Museum (if available). These would feed into MaterialType and SpecimenType. SampledFeature is more challenging.

GEOME locality field can be helpful for broad feature descriptions. Habitat can be controlled for some teams (e.g. ENVO terms). Perhaps group by the team id and evaluate the terms. Habitat and microhabitat may be useful for SampledFeature descriptions. MIXSv6 env_broad_scale terms. 


### Adding content to iSamples

Investigating light weight options for adding content to iSamples. One approach may be to use sources like GitHub repo (consistent layout based on a template), and aggregate content from those GH sources to an iSB instance. Validation, identifier allocation could be done at the repository level. Basic workflow would be something like:

1. Create repository from template
2. Edit descriptive info
3. Add content
    - Records as spreadsheets / CSV tables
    - Images stored in repo or (especially for large media) separately using an online service such as flickr, Dropbox, Filebase, etc. Content needs to be accessible via URL, ideally with identifier assigned. Figshare and zenodo. Check SESAR related materials field for common links to external resources.
4. Validation scripts are applied against content 
5. Notify iSB maintainer of content avaiability
6. iSB syncs content from GH repo
7. Content becomes available through iSC

Challenges include:
- identifier minting. Perhaps use iSB as an authoritative source (new API)
- curation process? (can be partially automated) Perhaps add a label to records that indicate they have been reviewed by a curator. e.g. SESAR hs 3 methods for registration: single record, batch file (curator reviewed, e.g. ownership, incorrect labelling), web services. GEOME content is self managed, sometimes a datamanger may examine. 
    



## 2022-04-01

iSamples UI and backend updates
- Adding Cesium 3D view to search UI
- Dump to sqlite
- General performance improvements


Clustering work and potential vocabulary suggestions
- limited by resources
- about an hour for operation on HPC
- Need to look at the clustering results for GEOME and Smithsonian to see what additional terms are being identified in the source records to assist with the assignment of controlled vocabulary terms or perhaps expand the vocabularies with narrower terms.
    - Finish BERT
    - Clustering on progression of vocabularies 
    - Look at more detailed clustering


Metadata model
- Some adjustment for closer alignment with [prov](https://www.w3.org/TR/prov-primer/) and [geojson-ld](http://www.opengis.net/doc/IS/eo-geojson/1.0). See [GeoJSON feature draft implementation](https://github.com/isamplesorg/metadata/blob/GeoJSON-schema/iSamplesGeoJSONSchemaBasic0.3.json) of iSamples 0.3 schema.
- Action of taking a sample is a type of `prov:Activity`
- Curation activity is a type of `prov:Activity`
- Should curated location (i.e. the current location of the specimen) be a property of the sample or of the curation activity? If 'curatedLocation' is a property of Curation, and Curation is viewed as a collection of events ('curation history'), then previous locations of sample will be tracked...
- Similarly for sample access constraints.  Access constraints are bound to the current sample location, so should be with curationLocation (where ever that lands)


```plantuml

class Temporal
note bottom: Includes prov\nstartedAtTime and\nendedAtTime

class Spatial
note bottom: Spatial is an instance\nof a geojson Feature

class Protocol
note bottom: Defined sampling methodology

class Initiative
note bottom: Context for\nsampling activity

class "prov:Agent" as Agent

abstract class "prov:Entity" as Entity
abstract class "prov:Activity" as Activity

PhysicalEntity -|> Entity
SamplingActivity -|> Activity
CurationActivity -|> Activity

class PhysicalEntity {
	Identifier: id
	String: label
	String: description
	String[]: keywords
	Concept: specimenType
	Concept: materialType
	SamplingActivity: wasGeneratedBy
	CurationActivity[]: curationActivity
	Concept[]: classification
	curationLocation
	accessConstraints
}

class SamplingActivity {
	Identifier: id
	Agent: wasAssociatedWith
	PhysicalEntity: used
	Spatial: where
	Temporal: when
	Concept: sampledFeature
	Protocol: procedure
	Initiative: initiative
}

class CurationActivity {
	Identifier: id
	Temporal: when
	PhysicalEntity: used
	Agent: curator	
	String: description
}

PhysicalEntity --> SamplingActivity : wasGeneratedBy
SamplingActivity -- Temporal
SamplingActivity -- Agent
CurationActivity -- Agent
SamplingActivity -- Spatial
PhysicalEntity -- CurationActivity
SamplingActivity -- Protocol
SamplingActivity -- Initiative
```

Note -- the current model has identifier for the 'digital specimen', i.e. the metadata record about the sample, and a separate identifier for the physical object (e.g. an IGSN). In the example here, 'wasGeneratedBy' is technically about the thing identified by @id--i.e. the metadata record, not the sample. 

Incomplete draft of a physical sample in json-ld:
```json
{
	"@context":{
		"@vocab":"https://isamples.org/schema/",
		"ORCID":"https://orcid.org/",
		"geojson": "https://purl.org/geojson/vocab#",
		"specimen": "https://w3id.org/isample/vocabulary/specimentype/0.9/",
		"material": "https://w3id.org/isample/vocabulary/materialtype/0.9/",
		"sample": "https://w3id.org/isample/vocabulary/sampledfeature/0.9/"
	},
	"@id": "sample-001",
	"@type":"PhysicalEntity",
	"specimenType": {"@id":"specimen:liquidorgas"},
	"materialType": {"@id":"material:gaesousmaterial"},
	"wasGeneratedBy":{
		"@type":"SamplingActivity",
		"@id":"https://igsn.org/FAKE0001",
		"wasAssociatedWith": {
			"@id":"ORCID:0000-002-6513-4996"
		},
		"used":{
			"@id":"https://www.wikidata.org/wiki/Q2"
		},
		"sampledFeature":{"@id":"sample:atmosphere"},
		"startedAtTime":"2021-01-01T12:03:01.000Z",
		"endedAtTime":"2021-01-01T12:03:01.000Z",
		"where":{
			"@type": "geojson:Feature",
			"geometry":{
				"@type":"geojson:Point",
				"coordinates": [
					-149.82845306396484,
					-17.49164892251183
				]			
			},
			"properties":{
				"title":"Oriti",
				"description":"The porch of Oriti"
			}
		}
	},
	"curationActivity": [
		{
			"@type":"CurationActivity",
			"startedAtTime": "2021-03-01",
			"wasAssociatedWith": {"@id":"ORCID:0000-0002-6513-4996"}
		}
	]
}
```
## 2022-03-11

Vocabulary work:

* Steve adjusting comments for various vocabularies, will update rdfs:comment to skos:definition for definitions. 
* Hong has been working on deriving vocabularies from the content
    * SESAR descripton field, end up with about 72,000 unique descriptions (after removing punctuations, etc). Basically 72,000 sets of keywords
    * TODO: Download the records and evaluate the clustering within high level vocabulary


DataCite:

* Need to start setting up testing with IGSN minting with the new Datacite infrastructure
    * Rory, Cody Ross, Matt at DataCite
        * TODO: setup a call with Rory, Cody and Matt
    * IGSN metadata mapping will store admin type info and link sample metadata, very similar to the current approach for IGSN.
    * IGSN metadata recommendation document: https://docs.google.com/document/d/1iEGZNaIqx4bMKFRrpcv4ljoCGv_pssUmk_YEgA8RTT8/edit
    * Complications for fee model support - fees need to be paid for IGSN minting, how to manage this?
* For identifier minting - could have an option for minting IGSNs if account info is available, or ARKs in other cases
    * ARKs could be minted by iSamples Central as a virtual org

Strabospot:
* Support minting IGSNs (and possibly ARKs)
* Operates offline, and syncs with a server when back online
* Similar to FAIMS field data collection (originally for archaeology)


## 2022-02-25

- Update on Web UI
    - https://hyde.cyverse.org/isamples_central/ui
    - Table column selection
    - Date range selection
    - TODO:
        - Add a badge for identifying which box is being viewed
        - Add a mechanism for exporting selected records
        - Hide facets that have a zero count
        - Show vocabularies as a hierachy

- Clustering
    - made the code more efficient, now run 60k records takes 10 mins and less than 9G of RAM. Code pushed to github.
    - next step is to use selected fields and a larger samples (10% of total records) 
    - also look into [Orange](https://orangedatamining.com/)

- LinkML and core model
    - Draft of generated documentation: https://isamplesorg.github.io/metadata/, https://isamplesorg.github.io/metadata/
    - CVs may be published in e.g.,https://journals.sfu.ca/ijg/index.php/journal/index

- Web UI release. What needs to be done at the project level before more widely sharing the web ui?
    - URLs
    - Feedback
    - Monitoring
    - Documentation
        - Model
        - API
    - Add branding, links to project and github (logo, favico, title)
    - Add a gmail address for handling feedback
    - Any other metrics to capture?

## 2022-02-18

Agenda:

- Update on web ui
    - table view available
    - help for search terms
    - add an icon for toggling the columns to display
    - see if react table sorting can be hooked into the facet search
    - date facet range
        - perhaps set the min and max range of dates from the range available in the current search range
        - might be able to use a stats query for these values
    
- Procedures for deploying the web ui (version, release, deploy)
    - When we are ready to release, we should tag and push everywhere

- Clustering work
    - birch algorithm enables segmenting the clustering, which in turn facilitate parallelization
    - remove the admin metadat from the text input to the clustering
    - need to experiment more with threshold settings
    - Examine SESAR record for example, which appear to cluster into only a couple of groups. Examine those records with the clustering tools to see if there are additional terms that can be added to the existing vocabularies.
    - Perhaps REU support for students to assist with the clustering

- Goals for linkml use
    - making sure that controlled vocabularies are extensible
    - consider making the enums as separate .yaml files which can be imported into the core model
    - Provide linkml templates for users of the iboxes 
        - A simple, and likely common extension will be to expand a controlled vocabulary (e.g. materialType) with additional terms. The new terms could be added to the vocabulary and the vocabulary used to drive aspect of the UI (so the new term could be available in the facet hierarchy) and also to drive the indexing rules (e.g. so the indexer recognizes the new term as being narrower than the parent)
    - Use the vocabulary terms to drive UI elements (indicate broader - narrower than)
    - Also use the vocabulary hierarchy in the indexing process to ensure that a broader term also includes the narrower records
    - TODO: what is the best practice for storing the hierarchy of terms in the indexed fields of solr. One option is to include term expansion in the facet field (i.e. include the leaf value and all it's parents to the root). There are techniques for labelling a hierarchy to facilitate efficient discovery. 