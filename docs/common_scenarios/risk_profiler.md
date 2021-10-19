---
title: Risk profiler
---

!!! Info
    Applicable to: Robo Advisor, stand-alone

## Context

InvestSuite has conducted a lot of research together with universities to create a risk profiler that evaluates in an engaging and human way someone's willingness and ability to take risk. InvestSuite risk profile questionnaires can be MiFID 2-compliant, have dynamic pathways and logic jumps, and have questions based on real numbers specific to the user. For each client, InvestSuite creates and stores one or more custom questionnaires, that the client can use to assess their users' risk profile.

## Definitions

**Questionnaire** - A questionnaire determines the logic that is used to fill out parameters in the risk profile. It determines the content of questions that will be asked, and the (dynamic) pathway of questions. 

**Assessment** - An assessment follows the questions of a questionnaire, and holds the progress of the user. For each portfolio the user takes an assessment. As such, as assessment is specific to a portfolio.

## How it works

At the end of an assessment, the InvestSuite API returns a score. The client can use this score to determine the right investing strategy for the user. The formula for how the score is calculated is determined by the client, which means that there are no customisation constraints. Parameters in the risk profile of a user do not all have to contribute to the eventual score. For example, the birth date of the user is a required parameter in order to determine whether the user is old enough to open an investment account, but it does not influence the risk score.  

Because there can be logic jumps in the question order (and the next question can depend on the previous answers), only the next question to be asked and previously answered questions are given in each assessment response. After an answer is submitted, Investsuite immediately stores it in the risk profile. This way, a user can close the application at any time without losing their progress, and can resume the assessment later.

## Create assessment

When a user is created, it is possible to create an assessment with the user ID. To create an assessment for a user, a user ID and questionnaire ID are required. The user ID is the ID that is used by InvestSuite to uniquely identify the user. You can find the user ID by querying the *User* collection `GET /users/?query=…`, see the [Search users](/docs/common_scenarios/users/#search) section for more information. The questionnaire ID identifies the questionnaire that will be used for the assessment. This questionnaire is specific to the customer and is designed and configured together with InvestSuite. Let's try it out and create an assessment for a specific `user_id` and `questionnaire_id`.

=== "HTTP"

    ```HTTP 
    POST /risk-profiler/assessments/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "questionnaire_id": "Q01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
    }

    ```

=== "curl"

    ```bash
    curl -X POST \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    -d '{  \   
            "questionnaire_id": "Q01ARZ3NDEKTSV4RRFFQ69G5FAV",  \
            "user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",  \
        }'
    https://api.sandbox.investsuite.com/risk-profiler/assessments/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`questionnaire_id` | A unique identifier for the questionnaire to be used to conduct the assessment. This field is required for knowing which questions to ask the when conducting the assessment. | `string ^Q[0-9A-HJKMNP-TV-Z]{26}\Z` | Q01ARZ3NDEKTSV4RRFFQ69G5FAV | yes 
`user_id` | The user ID of the person that this assessment is for. | `string ^U[0-9A-HJKMNP-TV-Z]{26}\Z` | U01ARZ3NDEKTSV4RRFFQ69G5FAV | yes

**Response body** 

```JSON
{
    "id": "A01ARZ3NDEKTSV4RRFFQ69G5FAV",
    "sections": [
        {
            "answered_questions": [], 
            "title": {
                "en-us": "Introduction",
            }
        }, 
        {
            "answered_questions": [], 
            "title": {
                "en-us": "Investment Goals",
            }
        }, 
    ],
    "status": {
        "current_section": {
            "answered_questions": [], 
            "title": {
                "en-us": "Introduction",
            }
        }, 
        "next_question": {
            "form": {
                "control_type": "OK", 
                "type": "CONTROL",
            },
            "title": "Welcome", 
            "body": "In the next minutes, we'll help you create your own, personalized investment portfolio. But first we want to explain how this works.",
            "index": "0"
        }, 
        "type": "IN_PROGRESS",
    },
    "parameter_values": {},
}
```
Now that we created the assessment, we can start asking questions to the user. The now empty assessment object will be filled out sporadically by submitting answers via the API.  

## Conduct assessment

After an assessment is created for a user, the risk profiling can begin. Conducting an assessment is done by asking a question to the user, submitting the answer to InvestSuite, receiving the next question to ask from InvestSuite, asking that question to the user, and so on until the assessment is completed. An assessment will have a status COMPLETE and calculate a score when all questions are answered. When there are still questions to be answered, the assessment has a status IN_PROGRESS and contains a next question to ask.  

After receiving an answer from the user, the answer is submitted to InvestSuite in order to get the next question. InvestSuite saves the answer immediately and determines the next question. This way, InvestSuite keeps track of and saves the progress of the assessment, so the user can close the application any time and resume the assessment where they left off later. 

![Conducting assessment](../img/conducting_assessment.jpg)

Both calls in the sequence diagram have the same response body, containing the next question to be asked or the score, the parameters, the sections with the answered questions, and a status. 

**Response body** 

```JSON
{
    "id": "A01ARZ3NDEKTSV4RRFFQ69G5FAV",
    "sections": [
        {
            "answered_questions": [
                {
                    "question": {
                        "form": {
                            "control_type": "OK", 
                            "type": "CONTROL",
                        },
                        "title": "Welcome", 
                        "body": "In the next minutes, we'll help you create your own, personalized investment portfolio. But first we want to explain how this works.",
                        "index": "0"
                    }, 
                    "answer": {
                        "type": "OK"
                    }
                }
            ], 
            "title": {
                "en-us": "Introduction",
            }
        }, 
        {
            "answered_questions": [], 
            "title": {
                "en-us": "Investment Goals",
            }
        }, 
    ],
    "status": {
        "current_section": {
            "answered_questions": [
                {
                    "question": {
                        "form": {
                            "control_type": "OK", 
                            "type": "CONTROL",
                        },
                        "title": "Welcome", 
                        "body": "In the next minutes, we'll help you create your own, personalized investment portfolio. But first we want to explain how this works.",
                        "index": "0"
                    }, 
                    "answer": {
                        "type": "OK"
                    }
                }
            ], 
            "title": {
                "en-us": "Introduction",
            }
        }, 
        "next_question": {
            "form": {
                "control_type": "OK", 
                "type": "CONTROL",
            },
            "title": "More information", 
            "body": "Robo Advisor invests in Exchange Traded Funds (ETFs), which are made from underlying instruments like stocks or bonds, and follows an index.",
            "index": "1"
        }, 
        "type": "IN_PROGRESS",
    },
    "parameter_values": {"birth_date": "1990-09-03"},
}
```  


## Adjust parameter values 

Sometimes it is useful to adjust parameter values withouth conducting the assessment again. For example, a user may change the lump sum for a portfolio. It can also be used to pre-fill some fields in the assessment. For example, you may want to pre-fill the birth date of a user because you already have it saved in your system. When the question for their birth date is provided by the API, there will be a `default` field containing the right birth date. 

=== "HTTP"

    ```HTTP 
    POST /risk-profiler/parameters/{name}/values/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "value": {
            "type": "DATE", 
            "value": "1990-09-03"
        },
        "target": {
            "user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV", 
            "type": "USER"
        },
    }

    ```

=== "curl"

    ```bash
    curl -X POST \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    -d '{  \
            "value": {  \
                "type": "DATE",  \
                "value": "1990-09-03"  \
            },  \
            "target": {  \
                "user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",  \
                "type": "USER"  \
            },  \
        }'
    https://api.sandbox.investsuite.com/risk-profiler/parameters/{name}/values/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`value` | The value of the parameter. | `` | {"type": "DATE", "value": "1990-09-03"} | yes 
`target` | The target of the parameter. It can be at the user level or assessment level. | `` | {"user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV", "type": "USER"} | yes

