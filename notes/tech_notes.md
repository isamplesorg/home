# Notes for technical discussions

- When: Fridays, 12:00 ET
- Where: https://arizona.zoom.us/j/84283310669
- Notes: https://hackmd.io/l4vmRR1vSGOJq5xovO2BOg

These notes are stored in github under `home/notes/tech_notes.md`. 
Notes from earlier meetings are at https://docs.google.com/document/d/1GIScduypIrdPi5P62DhvrBCmCw0ypkEJpAM8iQNzCkw/edit?usp=sharing

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