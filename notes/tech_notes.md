# Notes for technical discussions

- When: Fridays, 12:00 ET
- Where: https://arizona.zoom.us/j/84283310669
- Notes: https://hackmd.io/SLE_8TwcSJu5fTeqwkdXng

These notes are stored in github under `home/notes/tech_notes.md`. 
Notes from earlier meetings are at https://docs.google.com/document/d/1GIScduypIrdPi5P62DhvrBCmCw0ypkEJpAM8iQNzCkw/edit?usp=sharing

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