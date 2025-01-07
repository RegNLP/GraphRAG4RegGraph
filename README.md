# GraphRAG

`OriginalDocuments` -> 40 regulatory documents

`StructuredDocuments` -> 40 document 
 - `StructuredDocuments/txt` -> only hierarchical info structured (Manually - Tuba)
 -  `StructuredDocuments/json` -> txt file converted to json format with 
	Sample
```json
[
  {
    "ID": "fa81ab52-b6d6-444c-a3e5-ec220f333a38",
    "DocumentID": 23,
    "PassageID": "1.1",
    "Passage": "Introduction"
  }
]
```

`DocumentMap` includes ID and file names map

`CombinedRegulatoryDocumentsV2.json` includes all structured json files. Can be used backbone of the knowledge graph.

`AnnotatedDocuments` for each document for each passages data annotated by ADGM
  - "Obligations" -> Labelled from Barry using GPT models
  - "NamedEntities" -> Labelled from Barry using standard keyword match, entities are static
  - "DefinedTerms"  -> Labelled from Barry using standard keyword match, terms are static

    Sample (Format has slightly difference between documents, but logis is same)
```json
[
    {
      "ID": "74a9598f-2f07-49bd-b099-a955157b71ea",
      "PassageID": "3.1.",
      "Passage": "Two main groups of statute law have effect in the ADGM: (i) the 47 modified English statutes scheduled to the Application Regulations; and (ii) other enactments passed by ADGM. The first group includes statutes that make modifications to common law rules and other standalone commercial statutes. The second group comprises key ADGM enactments for commercial activity which are primarily based on precedent statutes from the UK and are up to date, fitting snugly with the common law. For the first group, the Application Regulations specify exactly which statutory modifications of common law rules have effect in the ADGM\u2019s legal system. Future statutory modifications passed in the UK will not have effect in ADGM. The underlying common law precedent is to be referred to disregarding them, unless another ADGM enactment has expressly stated that they shall apply in ADGM.  Existing statutory modifications have effect in ADGM only to the extent that they are included in the 47 English statutes scheduled to the Application Regulations or are included in another ADGM enactment. Key statutory modifications and other general English commercial statutes relevant to modern business have been included in the ADGM\u2019s body of statute law and were selected as a result of a review of key English commercial statutes and were publicly consulted upon.\n",
      "Obligations": [
            "Obligation:1 \"Comply with Modified English Statutes**: Familiarize with and adhere to the 47 modified English statutes scheduled to the Application Regulations that are applicable within the ADGM.\"",
            "Obligation:2 \"Follow ADGM Enactments**: Understand and comply with enactments passed by the ADGM that are tailored to its legal framework and regulate commercial activity.\"",
            "Obligation:3 \"Disregard Future UK Statutory Modifications**: Do not automatically apply future UK statutory modifications to ADGM operations unless an ADGM enactment specifies their applicability.\"",
            "Obligation:4 \"Consult the Application Regulations**: Refer to the Application Regulations to determine the effect of statutory modifications within the ADGM.\"",
            "Obligation:5 \"Stay Informed on Legal Updates**: Regularly monitor for any changes or updates to the ADGM's legal framework, including new enactments or amendments to existing laws.\"",
            "Obligation:6 \"Seek Legal Advice**: Consult with legal experts or compliance professionals to ensure a thorough understanding of ADGM regulations and their implications for your business.\"",
            "Obligation:7 \"Implement Compliance Measures**: Establish and maintain appropriate compliance measures, policies, and procedures, including staff training and regular audits.\"",
            "Obligation:8 \"Maintain Records and Reporting**: Keep accurate records and fulfill any reporting requirements as mandated by ADGM regulations and enactments.\"",
            "Obligation:9 \"Address Industry-Specific Regulations**: Identify and adhere to any additional industry-specific regulations and compliance requirements relevant to your business.\"",
            "Obligation:10 \"Refer to Common Law Precedent**: In situations without specific statutory guidance within the ADGM, refer to the underlying common law precedent to inform business practices.\""
                    ],
      "NamedEntities": {
            "group": "collection of agents (people, organizations, software agents, etc.) that are considered as a unit",
            "statute law": "law enacted by a legislature",
            "law": "rule recognized by some community as regulating the behavior of its members and that it may enforce through the imposition of penalties"
                    },
      "DefinedTerms": {
            "ADGM": "Means the Abu Dhabi Global Market."
                    }
                }
    
]
```
