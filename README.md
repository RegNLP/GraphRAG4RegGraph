
# RegGraph 

This system models regulatory documents as a graph in Neo4j, with nodes representing entities (e.g., documents, passages) and relationships capturing their interconnections (e.g., citations, hierarchy).

## [Link for dump](https://drive.google.com/drive/folders/1WCG1ESVVLI5XDWZgkm9ZmHVkdZat6Q3h?usp=sharing) 


## Nodes

### 1. **Document**
- Represents a regulatory document.
- **Properties**:
  - `id`: Unique identifier.
  - `title`: Title of the document.
  - `version`: Version number.
  - `date`: Publication or revision date.

### 2. **Passage**
- Represents a section or paragraph within a document.
- **Properties**:
  - `UID`: Unique identifier.
  - `passageID`: Hierarchical ID (e.g., "1.2.3").
  - `text`: Text content.

### 3. **Obligation**
- Represents obligations extracted from passages.
- **Properties**:
  - `UID`: Unique identifier.
  - `heading`: Short heading.
  - `description`: Detailed description.

### 4. **NamedEntity**
- Represents named entities found in passages.
- **Properties**:
  - `UID`: Unique identifier.
  - `term`: Entity name.
  - `description`: Additional details.

### 5. **DefinedTerm**
- Represents defined terms in passages.
- **Properties**:
  - `UID`: Unique identifier.
  - `term`: The term being defined.
  - `description`: Definition of the term.

### 6. **Outsource**
- Represents an external reference linked to a passage.
- **Properties**:
  - `reference_text`: The reference description.

---

## Relationships

### 1. **CONTAINS**
- **Source**: `Document`
- **Target**: `Passage`
- **Description**: Links a document to its passages.

### 2. **PARENT_OF**
- **Source**: `Passage`
- **Target**: `Passage`
- **Description**: Represents hierarchical structure between passages.

### 3. **HAS_OBLIGATION**
- **Source**: `Passage`
- **Target**: `Obligation`
- **Description**: Links passages to their obligations.

### 4. **HAS_NAMED_ENTITY**
- **Source**: `Passage`
- **Target**: `NamedEntity`
- **Description**: Links passages to named entities.

### 5. **HAS_DEFINED_TERM**
- **Source**: `Passage`
- **Target**: `DefinedTerm`
- **Description**: Links passages to defined terms.

### 6. **CITES**
- **Source**: `Passage`
- **Target**: `Passage`
- **Description**: Indicates that one passage cites another.
- **Properties**:
  - `reference_text`: Citation details.

### 7. **CITED_BY**
- **Source**: `Passage`
- **Target**: `Passage`
- **Description**: Indicates that one passage is cited by another.
- **Properties**:
  - `reference_text`: Citation details.

### 8. **OUTSOURCE**
- **Source**: `Passage`
- **Target**: `Outsource`
- **Description**: Links passages to external references.

### 9. **HAS_SHARED_DEFINED_TERM**
- **Source**: `DefinedTerm`
- **Target**: `DefinedTerm`
- **Description**: Links duplicate defined terms.

### 10. **HAS_SHARED_NAMED_ENTITY**
- **Source**: `NamedEntity`
- **Target**: `NamedEntity`
- **Description**: Links duplicate named entities.

---

## Example Cypher Queries

### 1. Retrieve all passages in a document:
```cypher
MATCH (d:Document)-[:CONTAINS]->(p:Passage)
WHERE d.id = 'Document123'
RETURN p; 
```
### 2. Find all obligations for a specific passage:
```cypher
MATCH (p:Passage)-[:HAS_OBLIGATION]->(o:Obligation)
WHERE p.UID = 'Passage123'
RETURN o;
```
### 3. Retrieve mutual citations between passages:
```cypher
MATCH (p1:Passage)-[:CITES]->(p2:Passage),
      (p2)-[:CITES]->(p1)
RETURN p1, p2;
```
### 4. Find duplicate defined terms:
```cypher
MATCH (n:DefinedTerm)
WHERE n.term IS NOT NULL AND n.term <> ""
WITH n.term AS term, COUNT(*) AS count
WHERE count > 1
RETURN term, count
ORDER BY count DESC;
```
### 5. Find external references for a passage:
```cypher
MATCH (p:Passage)-[:OUTSOURCE]->(o:Outsource)
WHERE p.UID = 'Passage123'
RETURN o;
```
### 5. Find external references for a passage:
```cypher
MATCH (p:Passage)-[:OUTSOURCE]->(o:Outsource)
WHERE p.UID = 'Passage123'
RETURN o;
```
### 5. Find external references for a passage:
```cypher
MATCH (p:Passage)-[:OUTSOURCE]->(o:Outsource)
WHERE p.UID = 'Passage123'
RETURN o;
```
### 6. Find all passages with more than one obligation and list their details:
```cypher
MATCH (p:Passage)-[:HAS_OBLIGATION]->(o:Obligation)
WITH p, COUNT(o) AS obligation_count
WHERE obligation_count > 1
MATCH (p)-[:HAS_OBLIGATION]->(o)
RETURN p.UID AS PassageID, obligation_count, COLLECT(o.heading) AS ObligationHeadings;
```
### 7. Retrieve all documents containing passages that cite external references:
```cypher
MATCH (d:Document)-[:CONTAINS]->(p:Passage)-[:OUTSOURCE]->(o:Outsource)
RETURN d.id AS DocumentID, d.title AS Title, COLLECT(DISTINCT o.reference_text) AS ExternalReferences;
```
### 8. Identify clusters of passages that share the same defined term:
```cypher
MATCH (p1:Passage)-[:HAS_DEFINED_TERM]->(t:DefinedTerm)<-[:HAS_DEFINED_TERM]-(p2:Passage)
WHERE id(p1) < id(p2)
RETURN t.term AS Term, COLLECT(DISTINCT p1.UID) AS Passage1, COLLECT(DISTINCT p2.UID) AS Passage2;
```
### 9. Get a hierarchy of passages for a specific document:
```cypher
MATCH (d:Document {id: 'Document123'})-[:CONTAINS]->(p:Passage)
OPTIONAL MATCH (p)-[:PARENT_OF*]->(child:Passage)
RETURN p.passageID AS ParentPassageID, COLLECT(child.passageID) AS ChildPassages
ORDER BY p.passageID;
```
### 10. Count the number of named entities shared between passages:
```cypher
MATCH (p1:Passage)-[:HAS_NAMED_ENTITY]->(n:NamedEntity)<-[:HAS_NAMED_ENTITY]-(p2:Passage)
WHERE id(p1) < id(p2)
RETURN p1.UID AS Passage1, p2.UID AS Passage2, COUNT(n) AS SharedNamedEntities
ORDER BY SharedNamedEntities DESC;
```
### 11. Find passages that cite other passages within the same document:
```cypher
MATCH (d:Document)-[:CONTAINS]->(p1:Passage)-[:CITES]->(p2:Passage)
WHERE (d)-[:CONTAINS]->(p2)
RETURN d.id AS DocumentID, p1.passageID AS CitingPassage, p2.passageID AS CitedPassage;
```
### 12. Retrieve all obligations and named entities linked to passages of a specific document:
```cypher
MATCH (d:Document {id: 'Document123'})-[:CONTAINS]->(p:Passage)
OPTIONAL MATCH (p)-[:HAS_OBLIGATION]->(o:Obligation)
OPTIONAL MATCH (p)-[:HAS_NAMED_ENTITY]->(n:NamedEntity)
RETURN p.passageID AS PassageID, COLLECT(DISTINCT o.heading) AS Obligations, COLLECT(DISTINCT n.term) AS NamedEntities;
```
### 13. Find passages with overlapping citations (citing the same target passage):
```cypher
MATCH (p1:Passage)-[:CITES]->(target:Passage)<-[:CITES]-(p2:Passage)
WHERE id(p1) < id(p2)
RETURN target.UID AS TargetPassage, COLLECT(DISTINCT p1.UID) AS CitingPassages1, COLLECT(DISTINCT p2.UID) AS CitingPassages2;
```
### 14. List all defined terms shared across multiple passages and their corresponding documents:
```cypher
MATCH (t:DefinedTerm)<-[:HAS_DEFINED_TERM]-(p:Passage)<-[:CONTAINS]-(d:Document)
WITH t, COUNT(DISTINCT p) AS PassageCount, COLLECT(DISTINCT d.title) AS Documents
WHERE PassageCount > 1
RETURN t.term AS Term, PassageCount, Documents
ORDER BY PassageCount DESC;
```
### 15. Find the most frequently cited passages across all documents:
```cypher
MATCH (target:Passage)<-[:CITES]-(p:Passage)
RETURN target.UID AS TargetPassage, COUNT(p) AS CitationCount
ORDER BY CitationCount DESC
LIMIT 10;
```

# Document

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
`CrossReferenceData.csv` include connection between documents based on the reference text. (Manually, Tuba)


