# Decisions API
The Decisions API enables users to build and retrieve a decision flow by linking statistical models in combination with deterministic logic from business rules for the purpose of integrating them into an operational process. 

This API provides the following features:

* Offer integration to occur by adding treatments within the decision flow, which enables users to return a collection of best offers to callers. 
* Ability for users to build a sequential set of models accessed from Model Repository API, rule sets from the Business Rules API, treatment groups from the Treatment Definition API, and conditioning logic. 

The models, rules sets, treatment groups, branches, and conditions are linked together via a common term list. After a decision flow is constructed, the Model Publish API can be used to publish the decision flows code to the SAS Micro Analytic Service for transactional web service execution, or to CAS destinations for batch processes.


Example processes that can be authored with the Decisions API would be loan approval, promotion offering, inventory purchasing, sales attribution, and many more.


Here is an example of a Credit Approval Decision for a small regional bank:

* Rule set - Validate State of Customer
* if stateIsValid = 'YES'
  * Model - Credit Scoring
  * Rule set - Credit Approval Rules
* else
  * Rule set - Auto Decline
  * Treatment group - Counter offers


#### Why Use this API?

This API enables users to build and retrieve decision making processes that can be published to transactional or batch targets.

## API Request Examples Grouped by Object Type

<details>
<summary>Decisions</summary>

* [Get API resource links](#top-level-api)
* [Create a decision](#CreateDecision)
* [Create a decision with treatments](#CreateDecisionTreatments)
* [Create a decision with branches](#CreateDecisionBranches)
* [Update a decision](#UpdateDecision)
* [Get a decision](#GetDecision)
* [Get a decision summary](#GetDecisionSummary)
* [Delete a decision](#DeleteDecision)
* [Get a collection of decisions](#GetCollectionDecisions)
* [Get the generated code for the current editable version of a decision](#GetGeneratedCodeCurrentEditableVersionDecision)
* [Create a decision version](#CreateDecisionVersion)
* [Get a specific version of a decision](#GetSpecificVersionDecision)
* [Delete a decision revision](#DeleteDecisionRevision)
* [Get all versions for a decision](#GetAllVersionsDecision)
* [Get the last modified date and time for a decision version](#GettheLastModifiedDateTimeDecisionVersion)
* [Get the generated code for a specific version of a decision](#GetGeneratedCodeSpecificVersionDecision)
</details>

<details>
<summary>Code Files</summary>

* [Create a code file](#CreateCodeFile)
* [Get a code file](#GetCodeFile)
* [Get a code file summary](#GetCodeFileSummary)
* [Delete a code file](#DeleteCodeFile)
* [Get the collection of code files](#GetCollectionCodeFiles)
</details>

<details>
<summary>See Also</summary>

* [Decisions API documentation](https://developer.sas.com/apis/rest/DecisionManagement/#decisions)
* [Decision Management REST API Examples](https://documentation.sas.com/?cdcId=edmcdc&cdcVersion=5.4&docsetId=edmresttut&docsetTarget=titlepage.htm&locale=en)
</details>


#### <a name='top-level-api'>Get API Resource Links</a>

Here is an example of a request that returns a list of the top-level resource links for the Decisions API.

```json
{
  "GET": "/decisions",
  "headers":{
		"Accept":"application/vnd.sas.api+json"
  }
}
```

`Response:`
```json
{
   "links": [
       {
          "method":"GET",
          "rel":"decisions",
          "href":"/decisions/flows",
          "uri":"/decisions/flows",
          "type":"application/vnd.sas.collection",
          "itemType":"application/vnd.sas.summary"
       },
       {
          "method":"POST",
          "rel":"createDecision",
          "href":"/decisions/flows",
          "uri":"/decisions/flows",
          "type":"application/vnd.sas.decision",
          "responseType":"application/vnd.sas.decision"
       },
       {
          "method":"GET",
          "rel":"codeFiles",
          "href":"/decisions/codeFiles",
          "uri":"/decisions/codeFiles",
          "type":"application/vnd.sas.collection",
          "itemType":"application/vnd.sas.summary"
       },
       {
          "method":"POST",
          "rel":"createCodeFile",
          "href":"/decisions/codeFiles",
          "uri":"/decisions/codeFiles",
          "type":"application/vnd.sas.decision.code.file",
          "responseType":"application/vnd.sas.decision.code.file"
       }
   ],
   "version":2
}
```

<br>


#### <a name='CreateDecision'>Create a Decision</a>

Here is an example of creating a decision.

```json
{
	"POST": "/decisions/flows/",
	"headers":{
		"Accept":"application/vnd.sas.decision+json",
		"Content-Type":"application/vnd.sas.decision+json"
	},
	"body":{
	   "name": "CreditOffer",
	   "signature": [
	      {
	         "name": "CustIncome",
	         "direction": "inOut",
	         "dataType": "decimal"
	      },
	      {
	         "name": "EM_PROBABILITY",
	         "direction": "output",
	         "dataType": "decimal"
	      },
	      {
	         "name": "EM_SEGMENT",
	         "direction": "input",
	         "dataType": "decimal"
	      },
	      {
	         "name": "YOJ",
	         "direction": "inOut",
	         "dataType": "decimal"
	      },
	      {
	         "name": "DELINQ",
	         "direction": "inOut",
	         "dataType": "decimal"
	      },
	      {
	         "name": "origin",
	         "direction": "input",
	         "dataType": "string",
	         "defaultValue" : "WEB"
	      },
	      {
	         "name": "CreditAmount",
	         "direction": "output",
	         "dataType": "integer"
	      },
	      {
	         "name": "customerId",
	         "direction": "output",
	         "dataType": "string"
	      },
	      {
	         "name": "hasBadTransactionFlag",
	         "direction": "output",
	         "dataType": "integer"
	      },
	      {
	         "name": "transactions",
	         "direction": "input",
	         "dataType": "dataGrid",
	         "dataGridExtension": [
                 {
                     "name": "type",
                     "dataType": "string",
                     "length": 100
                 },
                 {
                     "name": "amount",
                     "dataType": "decimal"
                 },
                 {
                     "name": "isBad",
                     "dataType": "integer"
                 }
             ]
	      }
	   ],
	   "subjectId": {
		   "name":"customerId"	   
		},
	   "subjectLevel": "customer",		   
	   "flow": {
	      "steps": [
	         {
	             "type": "application/vnd.sas.decision.step.ruleset",
	             "ruleset": {
	                 "name": "Transaction Check",
	                 "id": "e73870fa-1d40-44d1-99b9-2157a084adcc",
	                 "versionId": "55555555-f66c-4090-89ed-331c3743eaf2"
	              },
	              "mappingDataGridName": "transactions",
	              "mappings": [
	                  {
	                      "stepTermName": "YOJ",
	                      "direction": "input",
	                      "targetDecisionTermName": "YOJ"
	                  },
	                  {
                          "targetDataGridTermName": "type",
                          "direction": "input",
                          "stepTermName": "transType"
                      },
                      {
                          "targetDataGridTermName": "amount",
                          "direction": "input",
                          "stepTermName": "amount"
                      },
                      {
                          "targetDataGridTermName": "isBad",
                          "direction": "output",
                          "stepTermName": "badTransaction"
                      }
	              ]
	         },
	         {
	             "type": "application/vnd.sas.decision.step.ruleset",
	             "ruleset": {
	                 "name": "Aggregate Transaction Flag",
	                 "id": "e73870fa-5631-44d1-99b9-2157a084adcc",
	                 "versionId": "123456-f66c-4090-89ed-331c3743eaf2"
	              },
	              "mappings": [
	                  {
	                      "stepTermName": "flaggedTransactions",
	                      "direction": "input",
	                      "targetDecisionTermName": "transactions"
	                  },
	                  {
	                      "stepTermName": "hasBadTransactionFlag",
	                      "direction": "input",
	                      "targetDecisionTermName": "hasBadTransactionFlag"
	                  }
	              ]
	         }, 
	         {
	            "type": "application/vnd.sas.decision.step.model",
	            "model": {
	               "name": "CreditScore",
	               "id": "dfeda8cc-2d5a-4e86-bfcd-17add2b08b87"
	            },
	            "mappings": [
	               {
	                  "stepTermName": "CustIncome",
	                  "direction": "input",
	                  "targetDecisionTermName": "CustIncome"
	               },
	               {
	                  "stepTermName": "EM_PROBABILITY",
	                  "direction": "output",
	                  "targetDecisionTermName": "EM_PROBABILITY"
	               },
	               {
	                  "stepTermName": "EM_SEGMENT",
	                  "direction": "output",
	                  "targetDecisionTermName": "EM_SEGMENT"
	               }
	            ]            
	         },
             {
                "type": "application/vnd.sas.decision.step.custom.object",
                "customObject": {
                   "uri": "/files/files/6135a87e-f568-11e7-8c3f-9a214cf093ae",
                   "name": "ExtendedCreditScoreFromFile",
                   "type" : "decisionDs2CodeFile"
                },
                "mappings": [
                   {
                      "stepTermName": "CustIncome",
                      "direction": "input",
                      "targetDecisionTermName": "CustIncome"
                   },
                   {
                      "stepTermName": "EM_PROBABILITY",
                      "direction": "output",
                      "targetDecisionTermName": "EM_PROBABILITY"
                   },
                   {
                      "stepTermName": "EM_SEGMENT",
                      "direction": "output",
                      "targetDecisionTermName": "EM_SEGMENT"
                   }
                ]
             },
             {
                "type": "application/vnd.sas.decision.step.record.contact",
                "recordContact": {
                   "name": "Record Contact 1",
                   "ruleFiredTracking" : true,
                   "pathTracking" : false,
                   "channelTerm" : "origin",
                   "auditTerms" : [
                       {"name":"EM_PROBABILITY"},
                       {"name":"CustIncome"}
                   ]
                }
             },
	         {
	            "type": "application/vnd.sas.decision.step.condition",
	            "condition": {
	               "lhsTerm": {
	                  "name": "EM_PROBABILITY"
	               },
	               "rhsConstant": ".8",
	               "operator": ">"
	            },
	            "onTrue": {
	               "steps": [
	                  {
	                     "type": "application/vnd.sas.decision.step.ruleset",
	                     "ruleset": {
	                        "name": "OfferCredit",
	                        "id": "d71140fa-1d40-44d1-99b9-2157a084adcc",
	                        "versionId": "3483ed58-f66c-4090-89ed-331c3743eaf2"
	                     },
	                     "mappings": [
	                        {
	                           "stepTermName": "YOJ",
	                           "direction": "inOut",
	                           "targetDecisionTermName": "YOJ"
	                        },
	                        {
	                           "stepTermName": "DELINQ",
	                           "direction": "inOut",
	                           "targetDecisionTermName": "DELINQ"
	                        },
	                        {
	                           "stepTermName": "CreditAmount",
	                           "direction": "output",
	                           "targetDecisionTermName": "CreditAmount"
	                        }
	                     ]
	                  }
	               ]
	            },
	            "onFalse": {
	               "steps": [
	                  {
	                     "type": "application/vnd.sas.decision.step.ruleset",
	                     "ruleset": {
	                        "name": "DenyCredit",
	                        "id": "686c1a62-0a71-486d-afca-f011c231abfc",
	                        "versionId": "fca7da09-b3fb-4261-8c28-6f8918bab7ba"
	                     },
	                     "mappings": [
	                        {
	                           "stepTermName": "CreditAmount",
	                           "direction": "output",
	                           "targetDecisionTermName": "CreditAmount"
	                        }
	                     ]
	                  }
	               ]
	            }
	         }
	      ]
	   }
	}
}
```

`Partial response headers and body:`
```json
{
      "headers" : {
            "Location": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7",
            "Last-Modified": "Wed, 11 Apr 2018 01:39:02 GMT",
            "Content-Type": "application/vnd.sas.decision+json"
      },
      "body" : {
           "name": "CreditOffer", 
	   "id": "43f73aff-2040-4152-9923-9dbb37e73ba7",
           "modifiedTimeStamp": "2018-04-11T01:39:02.912Z", 
           "creationTimeStamp": "2018-04-11T01:39:02.912Z", 
           "createdBy": "userdoe", 
           "modifiedBy": "userdoe", 
           "majorRevision": 1,
           "minorRevision": 0,
           "links": [
              {
                  "rel": "self", 
                  "method": "GET",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7",
                  "type": "application/vnd.sas.decision"
              }, 
              {
                  "rel": "revisions", 
                  "method": "GET",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/revisions", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/revisions", 
                  "type": "application/vnd.sas.collection", 
                  "itemType": "application/vnd.sas.decision"
              }, 
              {
                  "rel": "currentRevision", 
                  "method": "GET",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7",
                  "type": "application/vnd.sas.decision"
              }, 
              {
                  "rel": "code", 
                  "method": "GET",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/code", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/code",
                  "type": "text/vnd.sas.source.ds2"
              }, 
              {
                  "rel": "mappedCode", 
                  "method": "POST",
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/mappedCode", 
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/mappedCode", 
                  "type": "application/vnd.sas.score.code.generation.request", 
                  "responseType": "application/vnd.sas.score.mapped.code"
              }, 
              {
                  "rel": "externalArtifacts", 
                  "method": "GET",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/externalArtifacts", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/externalArtifacts", 
                  "type": "application/vnd.sas.collection", 
                  "itemType": "application/vnd.sas.decision.external.artifact"
              }, 
              {
                  "rel": "delete", 
                  "method": "DELETE",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7"
              }, 
              {
                  "rel": "update", 
                  "method": "PUT",
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7", 
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7", 
                  "type": "application/vnd.sas.decision", 
                  "responseType": "application/vnd.sas.decision"
              }
           ], 
           "version": 2 
        }
}

```
<br>

#### <a name='CreateDecisionTreatments'>Create a Decision with Treatments</a>

Here is an example of creating a decision with treatments.

```json
{
	"POST": "/decisions/flows/",
	"headers":{
		"Accept":"application/vnd.sas.decision+json",
		"Content-Type":"application/vnd.sas.decision+json"
	},
	"body":{
	   "name": "Loan offers with Treatments",
	   "signature": [
	      {
	         "name": "CustIncome",
	         "direction": "inOut",
	         "dataType": "decimal"
	      },
	      {
	         "name": "YOJ",
	         "direction": "inOut",
	         "dataType": "decimal"
	      },
	      {
	         "name": "DELINQ",
	         "direction": "inOut",
	         "dataType": "decimal"
	      },
	      {
	         "name": "origin",
	         "direction": "input",
	         "dataType": "string",
	         "defaultValue" : "WEB"
	      },
	      {
	         "name": "customerId",
	         "direction": "output",
	         "dataType": "string"
	      },
	      {
	         "name": "specialScenarioIndicator",
	         "direction": "output",
	         "dataType": "integer"
	      },
	      {
	         "name": "loanOffers",
	         "direction": "output",
	         "dataType": "dataGrid",
	         "dataGridExtension": [
                 {
                     "name": "loanType",
                     "dataType": "string",
                     "length": 100
                 },
                 {
                     "name": "amount",
                     "dataType": "decimal"
                 },
                 {
                     "name": "lengthOfLoan",
                     "dataType": "integer"
                 }
             ]
	      }
	   ],
	   "subjectId": {
		   "name":"customerId"
	   },
	   "subjectLevel": "customer",
	   "flow": {
	         "steps": [
	         {
	             "type": "application/vnd.sas.decision.step.ruleset",
	             "ruleset": {
	                 "name": "Examine Special Scenarios",
	                 "id": "f73870fa-5631-44d1-99b9-2157a084adcc",
	                 "versionId": "fe14fe-f66c-4090-89ed-331c3743eaf2"
	              },
	              "mappings": [
	                  {
                         "stepTermName": "CustIncome",
                         "direction": "input",
                         "targetDecisionTermName": "CustIncome"
                     },
                     {
                         "stepTermName": "YOJ",
                         "direction": "input",
                         "targetDecisionTermName": "YOJ"
                     },
                     {
                         "stepTermName": "DELINQ",
                         "direction": "input",
                         "targetDecisionTermName": "DELINQ"
                     },
	                  {
	                      "stepTermName": "specialScenarioIndicator",
	                      "direction": "output",
	                      "targetDecisionTermName": "specialScenarioIndicator"
	                  }
	              ]
	         },
	         {
                "type": "application/vnd.sas.decision.step.custom.object",
                "customObject": {
                    "uri": "/treatmentDefinitions/definitionGroups/acc6cbd5-e5bd-48ab-9c36-7e616c8147cd",
                    "name": "Combined Treatments_Final",
                    "type": "treatmentGroup"
                },
                "mappingDataGridName": "loanOffers",
                "mappings": [
                   {
                      "stepTermName": "CustIncome",
                      "direction": "input",
                      "targetDecisionTermName": "CustIncome"
                   },
                   {
                      "stepTermName": "YearsOnJob",
                      "direction": "input",
                      "targetDecisionTermName": "YOJ"
                   },
                   {
                      "stepTermName": "specialScenarioIndicator",
                      "direction": "input",
                      "targetDecisionTermName": "specialScenarioIndicator"
                   },
                   {
                      "stepTermName": "lengthOfLoan",
                      "direction": "output",
                      "targetDataGridTermName": "lengthOfLoan"
                   },
                   {
                      "stepTermName": "amount",
                      "direction": "output",
                      "targetDataGridTermName": "amount"
                   },
                   {
                      "stepTermName": "loanType",
                      "direction": "output",
                      "targetDataGridTermName": "loanType"
                   }
                ]
             },
             {
                "type": "application/vnd.sas.decision.step.record.contact",
                "recordContact": {
                   "name": "Record contact for Loan Offers",
                   "ruleFiredTracking" : true,
                   "pathTracking" : false,
                   "channelTerm" : "origin",
                   "treatmentDatagridTerm" : "loanOffers",
                   "auditTerms" : [
                       {"name":"CustIncome"}
                   ]
                }
             }
	      ]
	   }
	}
}
```

`Partial response headers and body:`
```json
{
      "headers" : {
            "Location": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7",
            "Last-Modified": "Wed, 11 Apr 2018 01:39:02 GMT",
            "Content-Type": "application/vnd.sas.decision+json"
      },
      "body" : {
           "name": "Loan offers with Treatments", 
	       "id": "43f73aff-2040-4152-9923-9dbb37e73ba7",
           "modifiedTimeStamp": "2018-04-11T01:39:02.912Z", 
           "creationTimeStamp": "2018-04-11T01:39:02.912Z", 
           "createdBy": "userdoe", 
           "modifiedBy": "userdoe", 
           "majorRevision": 1,
           "minorRevision": 0,
           "links": [
              {
                  "rel": "self", 
                  "method": "GET",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7",
                  "type": "application/vnd.sas.decision"
              }, 
              {
                  "rel": "revisions", 
                  "method": "GET",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/revisions", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/revisions", 
                  "type": "application/vnd.sas.collection", 
                  "itemType": "application/vnd.sas.decision"
              }, 
              {
                  "rel": "currentRevision", 
                  "method": "GET",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7",
                  "type": "application/vnd.sas.decision"
              }, 
              {
                  "rel": "code", 
                  "method": "GET",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/code", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/code",
                  "type": "text/vnd.sas.source.ds2"
              }, 
              {
                  "rel": "mappedCode", 
                  "method": "POST",
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/mappedCode", 
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/mappedCode", 
                  "type": "application/vnd.sas.score.code.generation.request", 
                  "responseType": "application/vnd.sas.score.mapped.code"
              }, 
              {
                  "rel": "externalArtifacts", 
                  "method": "GET",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/externalArtifacts", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7/externalArtifacts", 
                  "type": "application/vnd.sas.collection", 
                  "itemType": "application/vnd.sas.decision.external.artifact"
              }, 
              {
                  "rel": "delete", 
                  "method": "DELETE",
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7", 
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7"
              }, 
              {
                  "rel": "update", 
                  "method": "PUT",
                  "uri": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7", 
                  "href": "/decisions/flows/43f73aff-2040-4152-9923-9dbb37e73ba7", 
                  "type": "application/vnd.sas.decision", 
                  "responseType": "application/vnd.sas.decision"
              }
           ], 
           "version": 2 
        }
}

```
<br>

#### <a name='CreateDecisionBranches'>Create a Decision with Branches</a>

Here is an example of creating a decision with branches.

```json
{
    "name": "branch examples",
    "majorRevision": 1,
    "minorRevision": 0,
    "signature": [
        {
            "direction": "inOut",
            "name": "EM_PROBABILITY",
            "dataType": "decimal"
        },
        {
            "direction": "inOut",
            "name": "email",
            "dataType": "string"
        },
        {
            "direction": "inOut",
            "name": "origin",
            "dataType": "string"
        }
    ],
    "flow": {
        "steps": [
            {
                "type": "application/vnd.sas.decision.step.branch",
                "branchType": "range",
                "name": "Range Branch on EM_PROBABILITY",
                "branchCases": [
                    {
                        "label": "(EM_PROBABILITY >= .8 AND EM_PROBABILITY <= .9)",
                        "compoundCondition": {
                            "booleanOperator": "AND",
                            "lhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "EM_PROBABILITY"
                                },
                                "operator": ">=",
                                "rhsConstant": ".8"
                            },
                            "rhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "EM_PROBABILITY"
                                },
                                "operator": "<=",
                                "rhsConstant": ".9"
                            }
                        },
                        "onTrue": {
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "ruleset": {
                                        "id": "3103502a-dc7e-4964-99a6-17f9171c2141",
                                        "name": "OfferCreditMin",
                                        "versionId": "eccb8876-8243-47c8-ac06-bc924d3f4e0e",
                                        "versionName": "1.0"
                                    }
                                }
                            ]
                        }
                    },
                    {
                        "label": "EM_PROBABILITY >= .9",
                        "simpleCondition": {
                            "lhsTerm": {
                                "name": "EM_PROBABILITY"
                            },
                            "operator": ">=",
                            "rhsConstant": ".9"
                        },
                        "onTrue": {
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "ruleset": {
                                        "id": "ab9cbada-1ba3-400a-8c64-ba82fc93ee4b",
                                        "name": "OfferCreditBest",
                                        "versionId": "8babcecc-2a09-4e2f-873a-dfc6539f7f1a",
                                        "versionName": "1.0"
                                    }
                                }
                            ]
                        }
                    }
                ],
                "defaultCase": {
                    "steps": []
                }
            },
            {
                "type": "application/vnd.sas.decision.step.branch",
                "branchType": "equals",
                "name": "Equals Branch on origin",
                "branchCases": [
                    {
                        "label": "missing(origin)",
                        "simpleCondition": {
                            "lhsTerm": {
                                "name": "origin"
                            },
                            "operator": "missing"
                        },
                        "onTrue": {
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "ruleset": {
                                        "id": "b7fcd8c3-51d4-476f-989d-882b391e0fb6",
                                        "name": "missingOrigin",
                                        "versionId": "fdc29289-f47d-42c7-bd8d-7a658cce4250",
                                        "versionName": "1.0"
                                    }
                                }
                            ]
                        }
                    },
                    {
                        "label": "(origin = 'web' OR origin = 'email')",
                        "compoundCondition": {
                            "booleanOperator": "OR",
                            "lhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "origin"
                                },
                                "operator": "=",
                                "rhsConstant": "'web'",
                                "id": "1586c88e-db09-4d30-addf-8cf62d773413"
                            },
                            "rhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "origin"
                                },
                                "operator": "=",
                                "rhsConstant": "'email'",
                                "id": "8d7f4b70-d82a-42d2-8686-3f94227c54fa"
                            }
                        },
                        "onTrue": {
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "ruleset": {
                                        "id": "fc739d33-851e-4d5d-86a5-904c90300830",
                                        "name": "digitalOrigin",
                                        "versionId": "5ab5e666-dbd9-4ec6-b1e1-23bfe486a84a",
                                        "versionName": "1.0"
                                    }
                                }
                            ]
                        }
                    },
                    {
                        "label": "origin = 'direct'",
                        "simpleCondition": {
                            "lhsTerm": {
                                "name": "origin"
                            },
                            "operator": "=",
                            "rhsConstant": "'direct'"
                        },
                        "onTrue": {
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "ruleset": {
                                        "id": "a96b48ba-45b1-451a-8844-9b401b484c24",
                                        "name": "traditionalOrigin",
                                        "versionId": "9d1d4d01-90e5-4854-ae93-eee1945bd557",
                                        "versionName": "1.0"
                                    }
                                }
                            ]
                        }
                    }
                ],
                "defaultCase": {
                    "steps": []
                }
            },
            {
                "type": "application/vnd.sas.decision.step.branch",
                "branchType": "like",
                "name": "Like Branch on email",
                "branchCases": [
                    {
                        "label": "missing(email)",
                        "simpleCondition": {
                            "lhsTerm": {
                                "name": "email"
                            },
                            "operator": "missing"
                        },
                        "onTrue": {
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "ruleset": {
                                        "id": "5c90a342-4d09-4804-8ef9-9b091b180370",
                                        "name": "missingEmail",
                                        "versionId": "2a90149e-cdc0-46d1-acde-84d5a664e3d4",
                                        "versionName": "1.0"
                                    }
                                }
                            ]
                        }
                    },
                    {
                        "label": "(email like '%@our_partner.com' OR email like '%@our_email_domain.com')",
                        "compoundCondition": {
                            "booleanOperator": "OR",
                            "lhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "email"
                                },
                                "operator": "like",
                                "rhsConstant": "'%@our_partner.com'",
                                "id": "88a099d2-9d03-4c9f-9f62-712045b525e1"
                            },
                            "rhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "email"
                                },
                                "operator": "like",
                                "rhsConstant": "'%@our_email_domain.com'",
                                "id": "a1d1e29c-0495-4372-9666-52f8c4bbe41e"
                            },
                            "id": "4f99d85b-2e52-4f42-a24b-02422ff60b57"
                        },
                        "onTrue": {
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "ruleset": {
                                        "id": "78800369-14d2-4cd9-883f-31887e2dcc9d",
                                        "name": "rulesetforbranch",
                                        "versionId": "79e8c01c-00bc-49f6-b621-ef5c01d3edb2",
                                        "versionName": "1.0"
                                    }
                                }
                            ]
                        }
                    },
                    {
                        "label": "email like '%@somedomain.com'",
                        "simpleCondition": {
                            "lhsTerm": {
                                "name": "email"
                            },
                            "operator": "like",
                            "rhsConstant": "'%@somedomain.com'"
                        },
                        "onTrue": {
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "ruleset": {
                                        "id": "0f573134-7cd9-4e42-8e31-bc571661b675",
                                        "name": "somedomainEmail",
                                        "versionId": "486a4481-6c94-4789-9baa-3221b9c27c3b",
                                        "versionName": "1.0"
                                    }
                                }
                            ]
                        }
                    }
                ],
                "defaultCase": {
                    "steps": []
                }
            }
        ]
    }
}
```

`Partial response headers and body:`
```json
{
    "creationTimeStamp": "2019-09-03T16:06:12.514Z",
    "modifiedTimeStamp": "2019-09-03T16:06:12.514Z",
    "createdBy": "sasdemo",
    "modifiedBy": "sasdemo",
    "id": "df75e4fd-44f3-4992-8ca6-4e8a219448af",
    "name": "branch examples",
    "majorRevision": 1,
    "minorRevision": 0,
    "signature": [
        {
            "creationTimeStamp": "2019-09-03T16:06:12.514Z",
            "modifiedTimeStamp": "2019-09-03T16:06:12.514Z",
            "createdBy": "sasdemo",
            "modifiedBy": "sasdemo",
            "id": "b4df13ac-3016-4505-b64c-1d34d240565d",
            "direction": "inOut",
            "name": "EM_PROBABILITY",
            "dataType": "decimal"
        },
        {
            "creationTimeStamp": "2019-09-03T16:06:12.514Z",
            "modifiedTimeStamp": "2019-09-03T16:06:12.514Z",
            "createdBy": "sasdemo",
            "modifiedBy": "sasdemo",
            "id": "de70364f-671e-43fa-991b-b29321913682",
            "direction": "inOut",
            "name": "email",
            "dataType": "string"
        },
        {
            "creationTimeStamp": "2019-09-03T16:06:12.515Z",
            "modifiedTimeStamp": "2019-09-03T16:06:12.515Z",
            "createdBy": "sasdemo",
            "modifiedBy": "sasdemo",
            "id": "3ab9de18-81a9-4119-9c32-2e22d76ac725",
            "direction": "inOut",
            "name": "origin",
            "dataType": "string"
        }
    ],
    "flow": {
        "creationTimeStamp": "2019-09-03T16:06:12.515Z",
        "modifiedTimeStamp": "2019-09-03T16:06:12.515Z",
        "createdBy": "sasdemo",
        "modifiedBy": "sasdemo",
        "id": "c3239e2e-a6a4-47d8-b79e-d6a05789247b",
        "steps": [
            {
                "type": "application/vnd.sas.decision.step.branch",
                "creationTimeStamp": "2019-09-03T16:06:12.519Z",
                "modifiedTimeStamp": "2019-09-03T16:06:12.519Z",
                "createdBy": "sasdemo",
                "modifiedBy": "sasdemo",
                "id": "aec88acb-9312-4535-b1ab-b19af60e3868",
                "branchCases": [
                    {
                        "id": "d0f93191-18d3-4a95-afde-1b406eeb1a7c",
                        "label": "(EM_PROBABILITY >= .8 AND EM_PROBABILITY <= .9)",
                        "compoundCondition": {
                            "booleanOperator": "AND",
                            "lhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "EM_PROBABILITY"
                                },
                                "operator": ">=",
                                "rhsConstant": ".8",
                                "id": "191ce613-db89-476e-9d8c-a5e33eb731ec"
                            },
                            "rhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "EM_PROBABILITY"
                                },
                                "operator": "<=",
                                "rhsConstant": ".9",
                                "id": "f1b7a515-bf7d-4de8-9b03-fa580ad6acd3"
                            },
                            "id": "a55ed65f-2f46-4a6e-af85-49190fb86c14"
                        },
                        "onTrue": {
                            "creationTimeStamp": "2019-09-03T16:06:12.553Z",
                            "modifiedTimeStamp": "2019-09-03T16:06:12.553Z",
                            "createdBy": "sasdemo",
                            "modifiedBy": "sasdemo",
                            "id": "e4199490-0001-45d3-aecd-cfdf544dd9d3",
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "creationTimeStamp": "2019-09-03T16:06:12.554Z",
                                    "modifiedTimeStamp": "2019-09-03T16:06:12.554Z",
                                    "createdBy": "sasdemo",
                                    "modifiedBy": "sasdemo",
                                    "id": "2ab66d53-17ac-41a4-ac62-1fe8ae2db0bb",
                                    "ruleset": {
                                        "id": "3103502a-dc7e-4964-99a6-17f9171c2141",
                                        "name": "OfferCreditMin",
                                        "versionId": "eccb8876-8243-47c8-ac06-bc924d3f4e0e",
                                        "versionName": "1.0"
                                    },
                                    "mappings": [],
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "links": [
                                        {
                                            "method": "GET",
                                            "rel": "ruleSet",
                                            "href": "/businessRules/ruleSets/3103502a-dc7e-4964-99a6-17f9171c2141",
                                            "uri": "/businessRules/ruleSets/3103502a-dc7e-4964-99a6-17f9171c2141",
                                            "responseType": "application/vnd.sas.brm.rule.set"
                                        }
                                    ]
                                }
                            ]
                        }
                    },
                    {
                        "id": "f0825b84-4e7a-45fc-a43b-a3ed47e2c21c",
                        "label": "EM_PROBABILITY >= .9",
                        "simpleCondition": {
                            "lhsTerm": {
                                "name": "EM_PROBABILITY"
                            },
                            "operator": ">=",
                            "rhsConstant": ".9",
                            "id": "0bbccfa7-81a7-4543-b05a-e03b9f50fa1f"
                        },
                        "onTrue": {
                            "creationTimeStamp": "2019-09-03T16:06:12.572Z",
                            "modifiedTimeStamp": "2019-09-03T16:06:12.572Z",
                            "createdBy": "sasdemo",
                            "modifiedBy": "sasdemo",
                            "id": "3936f62f-b60f-4ddb-85be-7c2eec98242e",
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "creationTimeStamp": "2019-09-03T16:06:12.574Z",
                                    "modifiedTimeStamp": "2019-09-03T16:06:12.574Z",
                                    "createdBy": "sasdemo",
                                    "modifiedBy": "sasdemo",
                                    "id": "ef15cab1-027b-4566-816b-736b4e289266",
                                    "ruleset": {
                                        "id": "ab9cbada-1ba3-400a-8c64-ba82fc93ee4b",
                                        "name": "OfferCreditBest",
                                        "versionId": "8babcecc-2a09-4e2f-873a-dfc6539f7f1a",
                                        "versionName": "1.0"
                                    },
                                    "mappings": [],
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "links": [
                                        {
                                            "method": "GET",
                                            "rel": "ruleSet",
                                            "href": "/businessRules/ruleSets/ab9cbada-1ba3-400a-8c64-ba82fc93ee4b",
                                            "uri": "/businessRules/ruleSets/ab9cbada-1ba3-400a-8c64-ba82fc93ee4b",
                                            "responseType": "application/vnd.sas.brm.rule.set"
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "defaultCase": {
                    "creationTimeStamp": "2019-09-03T16:06:12.517Z",
                    "modifiedTimeStamp": "2019-09-03T16:06:12.517Z",
                    "createdBy": "sasdemo",
                    "modifiedBy": "sasdemo",
                    "id": "a2aeee5e-6bbf-4fa1-b74f-0d1d91d845f2",
                    "steps": []
                },
                "branchType": "range",
                "name": "Range Branch on EM_PROBABILITY",
                "type": "application/vnd.sas.decision.step.branch"
            },
            {
                "type": "application/vnd.sas.decision.step.branch",
                "creationTimeStamp": "2019-09-03T16:06:12.589Z",
                "modifiedTimeStamp": "2019-09-03T16:06:12.589Z",
                "createdBy": "sasdemo",
                "modifiedBy": "sasdemo",
                "id": "99b3e980-f30b-46fa-a9fc-863a93d7da70",
                "branchCases": [
                    {
                        "id": "08335ede-f27d-4a5b-bc5e-7579f288ad16",
                        "label": "missing(origin)",
                        "simpleCondition": {
                            "lhsTerm": {
                                "name": "origin"
                            },
                            "operator": "missing",
                            "id": "96a403ab-5017-4f37-b47b-76cc2b28c2f6"
                        },
                        "onTrue": {
                            "creationTimeStamp": "2019-09-03T16:06:12.597Z",
                            "modifiedTimeStamp": "2019-09-03T16:06:12.597Z",
                            "createdBy": "sasdemo",
                            "modifiedBy": "sasdemo",
                            "id": "a5591e35-f349-4696-8b8c-a5b57166c9c3",
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "creationTimeStamp": "2019-09-03T16:06:12.599Z",
                                    "modifiedTimeStamp": "2019-09-03T16:06:12.599Z",
                                    "createdBy": "sasdemo",
                                    "modifiedBy": "sasdemo",
                                    "id": "c6ada194-063c-4010-bf82-2efaf5efe579",
                                    "ruleset": {
                                        "id": "b7fcd8c3-51d4-476f-989d-882b391e0fb6",
                                        "name": "missingOrigin",
                                        "versionId": "fdc29289-f47d-42c7-bd8d-7a658cce4250",
                                        "versionName": "1.0"
                                    },
                                    "mappings": [],
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "links": [
                                        {
                                            "method": "GET",
                                            "rel": "ruleSet",
                                            "href": "/businessRules/ruleSets/b7fcd8c3-51d4-476f-989d-882b391e0fb6",
                                            "uri": "/businessRules/ruleSets/b7fcd8c3-51d4-476f-989d-882b391e0fb6",
                                            "responseType": "application/vnd.sas.brm.rule.set"
                                        }
                                    ]
                                }
                            ]
                        }
                    },
                    {
                        "id": "5c986372-a181-45f3-88b9-667c97331af8",
                        "label": "(origin = 'web' OR origin = 'email')",
                        "compoundCondition": {
                            "booleanOperator": "OR",
                            "lhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "origin"
                                },
                                "operator": "=",
                                "rhsConstant": "'web'",
                                "id": "cdc46f4f-873e-4466-9c13-02353ac02c38"
                            },
                            "rhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "origin"
                                },
                                "operator": "=",
                                "rhsConstant": "'email'",
                                "id": "bea3142a-f094-462f-abc2-88882ec73179"
                            },
                            "id": "3dac17fe-1e54-4a61-a07d-d655daad63b9"
                        },
                        "onTrue": {
                            "creationTimeStamp": "2019-09-03T16:06:12.612Z",
                            "modifiedTimeStamp": "2019-09-03T16:06:12.612Z",
                            "createdBy": "sasdemo",
                            "modifiedBy": "sasdemo",
                            "id": "28abf7bc-79f4-42d7-9a1b-73e53cee0839",
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "creationTimeStamp": "2019-09-03T16:06:12.614Z",
                                    "modifiedTimeStamp": "2019-09-03T16:06:12.614Z",
                                    "createdBy": "sasdemo",
                                    "modifiedBy": "sasdemo",
                                    "id": "32013aad-b825-4401-84ca-6284088dd371",
                                    "ruleset": {
                                        "id": "fc739d33-851e-4d5d-86a5-904c90300830",
                                        "name": "digitalOrigin",
                                        "versionId": "5ab5e666-dbd9-4ec6-b1e1-23bfe486a84a",
                                        "versionName": "1.0"
                                    },
                                    "mappings": [],
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "links": [
                                        {
                                            "method": "GET",
                                            "rel": "ruleSet",
                                            "href": "/businessRules/ruleSets/fc739d33-851e-4d5d-86a5-904c90300830",
                                            "uri": "/businessRules/ruleSets/fc739d33-851e-4d5d-86a5-904c90300830",
                                            "responseType": "application/vnd.sas.brm.rule.set"
                                        }
                                    ]
                                }
                            ]
                        }
                    },
                    {
                        "id": "e99ae667-e35f-442b-9f89-dbd3192a4cfb",
                        "label": "origin = 'direct'",
                        "simpleCondition": {
                            "lhsTerm": {
                                "name": "origin"
                            },
                            "operator": "=",
                            "rhsConstant": "'direct'",
                            "id": "9155bcc9-c87f-4e4a-a971-8219098a7b7f"
                        },
                        "onTrue": {
                            "creationTimeStamp": "2019-09-03T16:06:12.629Z",
                            "modifiedTimeStamp": "2019-09-03T16:06:12.629Z",
                            "createdBy": "sasdemo",
                            "modifiedBy": "sasdemo",
                            "id": "2d146993-4662-42c6-b33a-bf6c7ac6b9c2",
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "creationTimeStamp": "2019-09-03T16:06:12.630Z",
                                    "modifiedTimeStamp": "2019-09-03T16:06:12.630Z",
                                    "createdBy": "sasdemo",
                                    "modifiedBy": "sasdemo",
                                    "id": "7aa06883-8f58-404c-90d5-2ee2c12517b7",
                                    "ruleset": {
                                        "id": "a96b48ba-45b1-451a-8844-9b401b484c24",
                                        "name": "traditionalOrigin",
                                        "versionId": "9d1d4d01-90e5-4854-ae93-eee1945bd557",
                                        "versionName": "1.0"
                                    },
                                    "mappings": [],
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "links": [
                                        {
                                            "method": "GET",
                                            "rel": "ruleSet",
                                            "href": "/businessRules/ruleSets/a96b48ba-45b1-451a-8844-9b401b484c24",
                                            "uri": "/businessRules/ruleSets/a96b48ba-45b1-451a-8844-9b401b484c24",
                                            "responseType": "application/vnd.sas.brm.rule.set"
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "defaultCase": {
                    "creationTimeStamp": "2019-09-03T16:06:12.587Z",
                    "modifiedTimeStamp": "2019-09-03T16:06:12.587Z",
                    "createdBy": "sasdemo",
                    "modifiedBy": "sasdemo",
                    "id": "9bf0af35-21d8-4b87-a11e-40185c627c3b",
                    "steps": []
                },
                "branchType": "equals",
                "name": "Equals Branch on origin",
                "type": "application/vnd.sas.decision.step.branch"
            },
            {
                "type": "application/vnd.sas.decision.step.branch",
                "creationTimeStamp": "2019-09-03T16:06:12.641Z",
                "modifiedTimeStamp": "2019-09-03T16:06:12.641Z",
                "createdBy": "sasdemo",
                "modifiedBy": "sasdemo",
                "id": "b2f75deb-b32f-40c3-9f4b-d022cd1d8d36",
                "branchCases": [
                    {
                        "id": "47090749-13af-48ec-b509-3c0365c65ab7",
                        "label": "missing(email)",
                        "simpleCondition": {
                            "lhsTerm": {
                                "name": "email"
                            },
                            "operator": "missing",
                            "id": "3cdf2297-2bf1-4d9a-9c18-ee09c5268962"
                        },
                        "onTrue": {
                            "creationTimeStamp": "2019-09-03T16:06:12.648Z",
                            "modifiedTimeStamp": "2019-09-03T16:06:12.648Z",
                            "createdBy": "sasdemo",
                            "modifiedBy": "sasdemo",
                            "id": "cb94198e-486d-40cb-a4e0-9ac189f67ffb",
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "creationTimeStamp": "2019-09-03T16:06:12.649Z",
                                    "modifiedTimeStamp": "2019-09-03T16:06:12.649Z",
                                    "createdBy": "sasdemo",
                                    "modifiedBy": "sasdemo",
                                    "id": "90c71927-d358-41ca-a936-df6f86449e2e",
                                    "ruleset": {
                                        "id": "5c90a342-4d09-4804-8ef9-9b091b180370",
                                        "name": "missingEmail",
                                        "versionId": "2a90149e-cdc0-46d1-acde-84d5a664e3d4",
                                        "versionName": "1.0"
                                    },
                                    "mappings": [],
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "links": [
                                        {
                                            "method": "GET",
                                            "rel": "ruleSet",
                                            "href": "/businessRules/ruleSets/5c90a342-4d09-4804-8ef9-9b091b180370",
                                            "uri": "/businessRules/ruleSets/5c90a342-4d09-4804-8ef9-9b091b180370",
                                            "responseType": "application/vnd.sas.brm.rule.set"
                                        }
                                    ]
                                }
                            ]
                        }
                    },
                    {
                        "id": "4a6f2377-d9b6-4d06-a65a-3536bfc891ba",
                        "label": "(email like '%@our_partner.com' OR email like '%@our_email_domain.com')",
                        "compoundCondition": {
                            "booleanOperator": "OR",
                            "lhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "email"
                                },
                                "operator": "like",
                                "rhsConstant": "'%@our_partner.com'",
                                "id": "9b5704dc-f204-4058-b86c-5968d1af1e33"
                            },
                            "rhsSimpleCondition": {
                                "lhsTerm": {
                                    "name": "email"
                                },
                                "operator": "like",
                                "rhsConstant": "'%@our_email_domain.com'",
                                "id": "5fbeac17-0ad7-4bd9-90e4-2fa31383d98c"
                            },
                            "id": "65acc235-148d-4ddb-a71c-bb4b892682c0"
                        },
                        "onTrue": {
                            "creationTimeStamp": "2019-09-03T16:06:12.658Z",
                            "modifiedTimeStamp": "2019-09-03T16:06:12.658Z",
                            "createdBy": "sasdemo",
                            "modifiedBy": "sasdemo",
                            "id": "fc840854-e41f-4220-896b-c8c647ed7513",
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "creationTimeStamp": "2019-09-03T16:06:12.660Z",
                                    "modifiedTimeStamp": "2019-09-03T16:06:12.660Z",
                                    "createdBy": "sasdemo",
                                    "modifiedBy": "sasdemo",
                                    "id": "e59f1c40-8794-4eb2-a1c3-03710fe0659f",
                                    "ruleset": {
                                        "id": "78800369-14d2-4cd9-883f-31887e2dcc9d",
                                        "name": "rulesetforbranch",
                                        "versionId": "79e8c01c-00bc-49f6-b621-ef5c01d3edb2",
                                        "versionName": "1.0"
                                    },
                                    "mappings": [],
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "links": [
                                        {
                                            "method": "GET",
                                            "rel": "ruleSet",
                                            "href": "/businessRules/ruleSets/78800369-14d2-4cd9-883f-31887e2dcc9d",
                                            "uri": "/businessRules/ruleSets/78800369-14d2-4cd9-883f-31887e2dcc9d",
                                            "responseType": "application/vnd.sas.brm.rule.set"
                                        }
                                    ]
                                }
                            ]
                        }
                    },
                    {
                        "id": "329dce4f-baef-4a2a-8c4d-f31caa2dde76",
                        "label": "email like '%@somedomain.com'",
                        "simpleCondition": {
                            "lhsTerm": {
                                "name": "email"
                            },
                            "operator": "like",
                            "rhsConstant": "'%@somedomain.com'",
                            "id": "57c25ddd-fcdd-426f-b21b-650e1d6995cc"
                        },
                        "onTrue": {
                            "creationTimeStamp": "2019-09-03T16:06:12.671Z",
                            "modifiedTimeStamp": "2019-09-03T16:06:12.671Z",
                            "createdBy": "sasdemo",
                            "modifiedBy": "sasdemo",
                            "id": "a52b14ab-ce75-4919-bc38-576993c814ed",
                            "steps": [
                                {
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "creationTimeStamp": "2019-09-03T16:06:12.673Z",
                                    "modifiedTimeStamp": "2019-09-03T16:06:12.673Z",
                                    "createdBy": "sasdemo",
                                    "modifiedBy": "sasdemo",
                                    "id": "6e757555-3083-4649-a358-060779048bbe",
                                    "ruleset": {
                                        "id": "0f573134-7cd9-4e42-8e31-bc571661b675",
                                        "name": "somedomainEmail",
                                        "versionId": "486a4481-6c94-4789-9baa-3221b9c27c3b",
                                        "versionName": "1.0"
                                    },
                                    "mappings": [],
                                    "type": "application/vnd.sas.decision.step.ruleset",
                                    "links": [
                                        {
                                            "method": "GET",
                                            "rel": "ruleSet",
                                            "href": "/businessRules/ruleSets/0f573134-7cd9-4e42-8e31-bc571661b675",
                                            "uri": "/businessRules/ruleSets/0f573134-7cd9-4e42-8e31-bc571661b675",
                                            "responseType": "application/vnd.sas.brm.rule.set"
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "defaultCase": {
                    "creationTimeStamp": "2019-09-03T16:06:12.640Z",
                    "modifiedTimeStamp": "2019-09-03T16:06:12.640Z",
                    "createdBy": "sasdemo",
                    "modifiedBy": "sasdemo",
                    "id": "f4e8a894-5cb3-4422-b710-e974ab5c29dd",
                    "steps": []
                },
                "branchType": "like",
                "name": "Like Branch on email",
                "type": "application/vnd.sas.decision.step.branch"
            }
        ]
    },
    "links": [
        {
            "method": "GET",
            "rel": "self",
            "href": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af",
            "uri": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af",
            "type": "application/vnd.sas.decision"
        },
        {
            "method": "GET",
            "rel": "revisions",
            "href": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af/revisions",
            "uri": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af/revisions",
            "type": "application/vnd.sas.collection",
            "itemType": "application/vnd.sas.decision"
        },
        {
            "method": "GET",
            "rel": "currentRevision",
            "href": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af",
            "uri": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af",
            "type": "application/vnd.sas.decision"
        },
        {
            "method": "GET",
            "rel": "code",
            "href": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af/code",
            "uri": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af/code",
            "type": "text/vnd.sas.source.ds2"
        },
        {
            "method": "POST",
            "rel": "mappedCode",
            "href": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af/mappedCode",
            "uri": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af/mappedCode",
            "type": "application/vnd.sas.score.code.generation.request",
            "responseType": "application/vnd.sas.score.mapped.code"
        },
        {
            "method": "GET",
            "rel": "externalArtifacts",
            "href": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af/externalArtifacts",
            "uri": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af/externalArtifacts",
            "type": "application/vnd.sas.collection",
            "itemType": "application/vnd.sas.decision.external.artifact"
        },
        {
            "method": "DELETE",
            "rel": "delete",
            "href": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af",
            "uri": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af"
        },
        {
            "method": "PUT",
            "rel": "update",
            "href": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af",
            "uri": "/decisions/flows/df75e4fd-44f3-4992-8ca6-4e8a219448af",
            "type": "application/vnd.sas.decision",
            "responseType": "application/vnd.sas.decision"
        }
    ],
    "version": 2
}

```
<br>





#### <a name='UpdateDecision'>Update a Decision</a>

Here is an example of updating the content of a decision.

```json
{
	"PUT": "/dedisions/flows/",
	"headers":{
		"Accept":"application/vnd.sas.decision+json",
		"Content-Type":"application/vnd.sas.decision+json",
		"If-Unmodified-Since" : "Wed, 11 Apr 2018 01:39:02 GMT"
	},
	"body":{
	   "name": "CreditOffer",
	   "id": "43f73aff-2040-4152-9923-9dbb37e73ba7",
	   "signature": [
	      {
	         "name": "CustIncome",
	         "direction": "inOut",
	         "dataType": "decimal"
	      },
	      {
	         "name": "EM_PROBABILITY",
	         "direction": "output",
	         "dataType": "decimal"
	      },
	      {
	         "name": "EM_SEGMENT",
	         "direction": "input",
	         "dataType": "decimal"
	      },
	      {
	         "name": "YOJ",
	         "direction": "inOut",
	         "dataType": "decimal"
	      },
	      {
	         "name": "DELINQ",
	         "direction": "inOut",
	         "dataType": "decimal"
	      },
	      {
	         "name": "origin",
	         "direction": "input",
	         "dataType": "string",
	         "defaultValue" : "WEB"
	      },
	      {
	         "name": "CreditAmount",
	         "direction": "output",
	         "dataType": "integer"
	      },
	      {
	         "id" : "e4875e13-6df7-4e40-bc4e-ae977f5469e3",
	         "name": "customerId",
	         "direction": "output",
	         "dataType": "string",
	         "defaultValue" : "-"
	      },
	      {
	         "name": "hasBadTransactionFlag",
	         "direction": "output",
	         "dataType": "integer"
	      },
	      {
	         "name": "transactions",
	         "direction": "input",
	         "dataType": "dataGrid",
	         "dataGridExtension": [
                 {
                     "name": "type",
                     "dataType": "string",
                     "length": 100
                 },
                 {
                     "name": "amount",
                     "dataType": "decimal"
                 },
                 {
                     "name": "isBad",
                     "dataType": "integer"
                 }
             ]
	      }
	   ],
	   "subjectId": {
	      "id" : "e4875e13-6df7-4e40-bc4e-ae977f5469e3",
		   "name":"customerId"
	   },
	   "subjectLevel": "household",		   
	   "flow": {
		    "id": "dc271752-275f-4a15-aef7-442434f1097d",
	        "steps": [
		         {
		            "type": "application/vnd.sas.decision.step.model",
	                "id": "e1a6f9bc-c7fe-4e4e-a38a-ac3397db5fef",
		            "model": {
		               "name": "CreditScore",
		               "id": "dfeda8cc-2d5a-4e86-bfcd-17add2b08b87"
		            },
		            "mappings": [
		               {
		                  "stepTermName": "CustIncome",
		                  "direction": "input",
		                  "targetDecisionTermName": "CustIncome",
		                  "id": "023c2373-6b2f-4c33-a6ed-6cb37ec29169"
		               },
		               {
		                  "stepTermName": "EM_PROBABILITY",
		                  "direction": "output",
		                  "targetDecisionTermName": "EM_PROBABILITY",
		                  "id": "0d7251a6-7ff3-47c4-8322-386f321e8362"
		               },
		               {
		                  "stepTermName": "EM_SEGMENT",
		                  "direction": "output",
		                  "targetDecisionTermName": "EM_SEGMENT",
		                  "id": "f2e89bd1-0985-4c47-a6d8-f09136418e36"
		               }
		            ]            
		         },
                 {
                    "type": "application/vnd.sas.decision.step.custom.object",
                    "id": "78da5dbc-f568-11e7-8c3f-9a214cf093ae",
                    "customObject": {
                        "uri": "/files/files/6135a87e-f568-11e7-8c3f-9a214cf093ae",
                        "name": "ExtendedCreditScoreFromFile",
                        "type" : "decisionDs2CodeFile"
                    },
                    "mappings": [
                       {
                          "stepTermName": "CustIncome",
                          "direction": "input",
                          "targetDecisionTermName": "CustIncome",
                          "id": "99ec1306-f568-11e7-8c3f-9a214cf093ae"
                       },
                       {
                          "stepTermName": "EM_PROBABILITY",
                          "direction": "output",
                          "targetDecisionTermName": "EM_PROBABILITY",
                          "id": "9ee1e3e0-f568-11e7-8c3f-9a214cf093ae"
                       },
                       {
                          "stepTermName": "EM_SEGMENT",
                          "direction": "output",
                          "targetDecisionTermName": "EM_SEGMENT",
                          "id": "a21a9dea-f568-11e7-8c3f-9a214cf093ae"
                       }
                    ]            
                 },
                 {
                    "type": "application/vnd.sas.decision.step.record.contact",
                    "id": "97da9dbc-9568-11e7-8c3f-9b214cf093ae",
                    "recordContact": {
                        "name": "Record Contact 1",
                        "ruleFiredTracking" : false,
                        "channelTerm" : "origin",
                        "pathTracking" : false,
                        "auditTerms" : [
                           {"name":"EM_PROBABILITY"},
                           {"name":"CustIncome"},
                           {"name":"EM_SEGMENT"}
                        ]
                   }
                },
		         {
		            "type": "application/vnd.sas.decision.step.condition",
		            "id": "5a246276-9576-4ffc-917c-f4ad48d0910b",
		            "condition": {
		               "lhsTerm": {
		                  "name": "EM_PROBABILITY"
		               },
		               "rhsConstant": ".8",
		               "operator": ">"
		            },
		            "onTrue": {
		               "id": "0da51b99-e17e-4bcc-8ba5-5debb3698a3f",
		               "steps": [
		                  {
		                     "type": "application/vnd.sas.decision.step.ruleset",
		                     "id": "c8d051f9-b09c-4d73-9e01-4e60ce8fcb77",
		                     "ruleset": {
		                        "name": "OfferCredit",
		                        "id": "d71140fa-1d40-44d1-99b9-2157a084adcc",
		                        "versionId": "3483ed58-f66c-4090-89ed-331c3743eaf2"
		                     },
		                     "mappings": [
		                        {
		                           "stepTermName": "YOJ",
		                           "direction": "inOut",
		                           "targetDecisionTermName": "YOJ",
		                           "id": "0c9b5ea1-96cf-41cb-94fe-f44cde8e1d9a"
		                        },
		                        {
		                           "stepTermName": "DELINQ",
		                           "direction": "inOut",
		                           "targetDecisionTermName": "DELINQ",
		                           "id": "7f0ac7d3-33f1-45b7-8526-d63a05aeb5e6"
		                        },
		                        {
		                           "stepTermName": "CreditAmount",
		                           "direction": "output",
		                           "targetDecisionTermName": "CreditAmount",
		                           "id": "23590aee-9bfc-49da-bcba-13f21820cf04"
		                        }
		                     ]
		                  }
		               ]
		            },
		            "onFalse": {
		               "id": "495d3376-6023-4a78-94ee-9d7f70a612a2",
		               "steps": [
		                  {
		                     "type": "application/vnd.sas.decision.step.ruleset",
                             "id": "b1dd6a2f-c9f6-45b6-883e-56eb720abc6f",
		                     "ruleset": {
		                        "name": "DenyCredit",
		                        "id": "686c1a62-0a71-486d-afca-f011c231abfc",
		                        "versionId": "fca7da09-b3fb-4261-8c28-6f8918bab7ba"
		                     },
		                     "mappings": [
		                        {
		                           "stepTermName": "CreditAmount",
		                           "direction": "output",
		                           "targetDecisionTermName": "CreditAmount",
		                           "id": "5c43811f-de22-47a7-b2c5-97351dffdd26"
		                        }
		                     ]
		                  }
		               ]
		            }
		         }
		      ]
	   }
	}
}
```
<br>

#### <a name='GetDecision'>Get a Decision</a>

Here is an example of retrieving the contents of a decision.

```json
{
  "GET": "/decisions/flows/{decisionId}",
  "headers":{
		"Accept":"application/vnd.sas.decision+json"
}
}
```
<br>

#### <a name='GetDecisionSummary'>Get a Decision Summary</a>

Here is an example of retrieving the summary of a decision.

```json
{
  "GET": "/decisions/flows/{decisionId}",
  "headers":{
		"Accept":"application/vnd.sas.summary+json"
	}
}
```
<br>

#### <a name='DeleteDecision'>Delete a Decision</a>

Here is an example of deleting a decision.

```json
{
  "DELETE": "/decisions/flows/{decisionId}"
}
```
<br>

#### <a name='GetCollectionDecisions'>Get a Collection of Decisions</a>

Here is an example of retrieving a list of all decisions.

```json
{
  "GET": "/decisions/flows",
  "headers": {
    "Accept": "application/vnd.sas.collection+json"
  }
}
```
<br>

#### <a name='GetGeneratedCodeCurrentEditableVersionDecision'>Get the Generated Code for the Current Editable Version of a Decision</a>

Here is an example of retrieving the DS2 package code for the current editable version of a decision. The code can then be used to execute the decision.

```json
{
	"GET":"/decisions/flows/{decisionId}/code",
	"headers": {
	    "Accept": "text/vnd.sas.source.ds2"
	}
}
```
<br>

### <a name='CreateDecisionVersion'>Create a Decision Version</a>

Here is an example of creating a new version of a decision.

```json
{
	"POST":"/decisions/flows/{decisionId}",
	"headers":{
		"Accept":"application/vnd.sas.decision+json",
		"Content-Type":"application/vnd.sas.decision+json"
	},
	"body":{
		"name":"Credit_Offer_Decision",
		"description":"This decision is for determining whether to offer credit to an applicant.",
		"signature":[
		   {
		      "direction":"inOut",
		      "name":"Cylinders",
		      "dataType":"decimal"
		   },
		   {
		      "direction":"inOut",
		      "name":"EngineSize",
		      "dataType":"decimal"
	       },
		   {
		      "direction":"output",
		      "name":"Type",
		      "dataType":"string"
		   },
		   {
		      "direction":"output",
		      "name":"vin",
		      "dataType":"string"
		   }		   
		],
		"subjectId": {
		      "name":"vin"
		},
		"subjectLevel": "household",	 				
		"flow":{		   
		   "steps":[
		      {
		         "ruleset":{
		            "id":"0d75574d-36da-4043-b1ed-8e40e4b2a732",
		            "name":"Rule_set_1",
		            "versionId":"9fcabcfd-590f-4efd-9bf5-0568543ad84a",
		            "versionName":"1.0"
		         },
		         "mappings":[
		            {    
		               "targetDecisionTermName":"EngineSize",
		               "direction":"inOut",
		               "stepTermName":"EngineSize"
		            },
		            {
		               "targetDecisionTermName":"Cylinders",
		               "direction":"inOut",
		               "stepTermName":"Cylinders"
		            },
		            {
		               "targetDecisionTermName":"Type",
		               "direction":"output",
		               "stepTermName":"Type"
		            }
		         ]
		      }
		   ]
		}
	  }
	}
```
<br>


#### <a name='GetSpecificVersionDecision'>Get a Specific Version of a Decision</a>

Here is an example of retrieving a specific version of a decision.

```json
{
  "GET": "/decisions/flows/{decisionId}/revisions/{revisionId}",
  "headers":{
		"Accept":"application/vnd.sas.decision+json"
	}
}
```
<br>

#### <a name='DeleteDecisionRevision'>Delete a Decision Version</a>

Here is an example of deleting a version of a decision.

```json
{
  "DELETE": "/decisions/flows/{decisionId}/revisions/{revisionId}"
}
```
<br>

#### <a name='GetAllVersionsDecision'>Get All Versions for a Decision</a>

Here is an example of retrieving a list of all versions that exist for a decision.

```json
{
  "GET": "/decisions/flows/{decisionId}/revisions",
  "headers":{
		"Accept":"application/vnd.sas.collection+json"
	}
}
```
<br>

#### <a name='GettheLastModifiedDateTimeDecisionVersion'>Get the Last Modified Date and Time for a Decision Version</a>

Here is an example of retrieving the header information for a decision revision. The header information contains the last modified date and time.

```json
{
  "HEAD": "/decisions/flows/{decisionId}/revisions/{revisionId}"  
}
```
<br>

#### <a name='GetGeneratedCodeSpecificVersionDecision'>Get the Generated Code for a Specific Version of a Decision</a>

Here is an example of retrieving the DS2 package code for a specific version of a decision. The code can then be used to execute the decision version.

```json
{
	"GET":"/decisions/flows/{decisionId}/revisions/{revisionId}/code",
	"headers": {
	    "Accept": "text/vnd.sas.source.ds2"
	}
}
```
<br>


#### <a name='CreateCodeFile'>Create a Code File</a>

Here is an example of creating a code file.

```json
{
    "POST": "/decisions/codeFiles",
    "headers":{
        "Accept":"application/vnd.sas.decision.code.file+json",
        "Content-Type":"application/vnd.sas.decisioncode.file+json"
    },
    "body":{
       "type": "decisionDS2PackageFile",
       "fileUri": "/files/files/0c7281d8-063d-49dd-be6b-392e9c9e930c"
    }
}
```
<br>


#### <a name='GetCodeFile'>Get a Code File</a>

Here is an example of retrieving a specific code file.

```json
{
  "GET": "/decisions/codeFiles/{codeFileId}",
  "headers":{
        "Accept":"application/vnd.sas.decision.code.file+json"
  }
}
```
<br>

#### <a name='GetCodeFileSummary'>Get a Code File Summary</a>

Here is an example of retrieving the summary of the specific code file.

```json
{
  "GET": "/decisions/codeFiles/{codeFileId}",
  "headers":{
        "Accept":"application/vnd.sas.summary+json"
    }
}
```
<br>

#### <a name='DeleteCodeFile'>Delete a Code File</a>

Here is an example of deleting a specific code file.

```json
{
  "DELETE": "/decisions/codeFiles/{codeFileId}"
}
```
<br>

#### <a name='GetCollectionCodeFiles'>Get the Collection of Code Files</a>

Here is an example of retrieving a list of code files.

```json
{
  "GET": "/decisions/codeFiles",
  "headers": {
    "Accept": "application/vnd.sas.collection+json"
  }
}
```
<br>


version 5, last updated 20 Nov, 2019