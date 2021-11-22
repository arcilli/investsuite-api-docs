---
title: Suitability profiler
---

## Context

A suitability profiler captures the information needed to create a suitable portfolio for someone. Part of this is creating a risk profile, other information can include preferences on sustainable investing or specific investment themes. Through one or more assessments in a suitability profile, this information can be gathered and translated into characteristics of a suitable portfolio.

### Risk profiler

One application of an assessment in the suitaibility profiler is assessing the willingness to take risk. InvestSuite has conducted a lot of research together with universities to create a risk profiler that evaluates in an engaging and human way someone's willingness and ability to take risk. InvestSuite risk profile questionnaires can be MiFID 2-compliant, have dynamic pathways and logic jumps, and have questions based on real numbers specific to the user. For each client, InvestSuite creates and stores one or more custom questionnaires, that the client can use to assess their users' risk profile.

## Definitions

**Profile** - A profile is a portfolio-specific object that captures the information necessary to create a suitable portfolio. It contains one or more assessments, each assessing a certain aspect of the suitability profile, and property values, that capture other settings that influence the policy. The results of the assessments and property values are translated into outputs. A profiler formula can then combine these outputs into a result that defines a policy to create a suitable portfolio.

**Profiler** - A profiler determines the structure of a profile. It contains questionnaires, that determine what the assessments in the profile will look like, and profile properties. A profiler formula determines how the outputs of the assessments will combine into policy settings. A profiler is essentially a template for a portfolio-specific suitability profile. A profiler, as well as the questionnaires, properties and formula in the profiler, is designed and configured together with InvestSuite.

**Questionnaire** - A questionnaire determines the structure of an assessment. This concerns the logic that is used to fill out parameters, the content of questions that will be asked, the (dynamic) pathway of questions, and a formula that converts the captured information into an output. 

**Assessment** - An assessment follows the questions of a questionnaire, and holds the progress of the user. It contains the questions to be asked and answers that the user provides. With the information captured in the assessment, an output can be generated based on a pre-defined formula. 

**Profile properties** - Profile properties determine settings that influence the policy but are not captured in assessments. Each property in a profiler has a default value that it should be set to upon the creation of a portfolio-specific profile.  

**Profile property values** - Profile property values capture profile settings that determine the policy of a profile and are not part of an assessment. These property values are all set to a default initial value, that is determined by the profile properties in the corresponding profiler.


![Suitability Profiler Architecture](../img/suitability_profiler_architecture.jpg)

## How it works

As mentioned above, to come to guidelines for a suitable portfolio, the user must go through one or more assessments in their suitability profile to capture the necessary information.
Once designed and configured a profiler together with InvestSuite, it is possible to generate a profile. For each portfolio, a profile can be created based on a profiler. This profiler acts as a template for a specific profile. It determines which assessments will be asked, which profile properties will be set, and how the gathered information is translated into policy guidelines. All assessments in the profile object should be completed by the user in order to determine a suitable policy.

### Outputs  

At the end of an assessment, the InvestSuite API returns an output. The client can use the outputs in the profile to determine the right investing strategy for the user. The formula on how to calculate the output is determined by the client in each questionnaire, which means that there are no customisation constraints. The same is true for the profiler formula that calculates the final result based on the assessment and property values outputs. Questions in an assessment do not all have to contribute to the eventual output. For example, in the case of a risk profile assessment, the birth date of the user is a required parameter in order to determine whether the user is old enough to open an investment account, but it does not influence the risk score.  

### Assessment flow  

Because there can be logic jumps in the question order of an assessment (and the next question can depend on the previous answers), only the next question to be asked and previously answered questions are given in each assessment response. After an answer is submitted, Investsuite immediately stores it in the suitability profile. This way, a user can close the application at any time without losing their progress, and can resume the assessment later.  

### Property values

The profile property values are initialized to a default value upon creation of the profile. This means that when all assessments in the profile are completed, but the user did not yet check the settings of the property values, a policy can already be determined.

## Create profile

When a user is created, it is possible to create a profile with the user ID. To create a profile for a user, a user ID and profiler ID are required. The user ID is the ID that is used by InvestSuite to uniquely identify the user. You can find the user ID by querying the *User* collection `GET /users/?query=…`, see the [Search users](/docs/common_scenarios/users/#search) section for more information. The profiler ID identifies the profiler that will be used as template for the profile. This profiler is specific to the customer and is designed and configured together with InvestSuite. Let's try it out and create a profile for a specific `user_id` and `profiler_id`.

=== "HTTP"

    ```HTTP 
    POST /suitability-profiler/profiles/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "profiler_id": "Q01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
    }

    ```

=== "curl"

    ```bash
    curl -X POST \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    -d '{  \   
            "profiler_id": "Q01ARZ3NDEKTSV4RRFFQ69G5FAV",  \
            "user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",  \
        }'
    https://api.sandbox.investsuite.com/suitability-profiler/profiles/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`profiler_id` | A unique identifier for the profiler to be used as template for the profile. This field is required for knowing which assessments to add to the profile. | `string ^À[0-9A-HJKMNP-TV-Z]{26}\Z` | À01ARZ3NDEKTSV4RRFFQ69G5FAV | yes 
`user_id` | The user ID of the person that this profile is for. | `string ^U[0-9A-HJKMNP-TV-Z]{26}\Z` | U01ARZ3NDEKTSV4RRFFQ69G5FAV | yes

**Response body** 
```JSON
{
    "id": "M01ARZ3NDEKTSV4RRFFQ69G5FAV", 
    "assessment_statuses": {
        "U01ARZ3NDEKTSV4RRFFQ69G5FAV": "IN_PROGRESS",
    }, 
    "status": {
        "type": "IN_PROGRESS"
    }
}
```
The response contains the profile ID, the overall profile status, and the status of each individual assessment. The assessments in the response are identified by the ID of the questionnaire that they follow. This means that profiles of the same template will always have the same IDs under `assessment_statuses`. Now that we created the profile, we can start asking questions to the user. The now empty assessments of the profile will be filled out sporadically by submitting answers via the API.  

Only when the status of the profile is equal to `COMPLETED`, the suitability profile can be used to determine a suitable investment policy. 

## Conduct an assessment  

When a profile is created for a user, we can start conducting the assessments in order to complete the profile. As mentioned above, each assessment is linked to a questionnaire, that determines what the assessment looks like. This questionnaire ID in combination with the ID of the created profile uniquely determine an assessment. Let us give an example of how to retrieve an assessment.  

=== "HTTP"

    ```HTTP 
    GET /suitability-profiler/profiles/N01ARZ3NDEKTSV4RRFFQ69G5FAV/assessments/U01ARZ3NDEKTSV4RRFFQ69G5FAV/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    ```

=== "curl"

    ```bash
    curl -X GET \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    https://api.sandbox.investsuite.com/suitability-profiler/profiles/N01ARZ3NDEKTSV4RRFFQ69G5FAV/assessments/U01ARZ3NDEKTSV4RRFFQ69G5FAV/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`profile_id` | A unique identifier of the profile that this assessment is for. | `string ^N[0-9A-HJKMNP-TV-Z]{26}\Z` | N01ARZ3NDEKTSV4RRFFQ69G5FAV | yes 
`questionnaire_id` | The ID of the questionnaire that this assessment is based on. | `string ^M[0-9A-HJKMNP-TV-Z]{26}\Z` | U01ARZ3NDEKTSV4RRFFQ69G5FAV | yes

**Response body** 
```JSON
{
    "sections": [
        {
            "answered_questions": [], 
            "title": {
                "en-us": "Introduction",
            }, 
            "question_count": 3,
        }, 
        {
            "answered_questions": [], 
            "title": {
                "en-us": "Investment Goals",
            }, 
            "question_count": 10,
        }, 
    ],
    "status": {
        "section_index": 0,
        }, 
        "question": {
            "form": {
                "control_type": "OK", 
                "type": "CONTROL",
            },
            "title": "Welcome", 
            "body": "In the next minutes, we'll help you create your own, personalized investment portfolio. But first we want to explain how this works.",
            "index": "0", 
            "id": "D01ARZ3NDEKTSV4RRFFQ69G5FAV",
        }, 
        "type": "IN_PROGRESS",
    }
}
```

Now that we have retrieved the assessment, the assessing can begin. Conducting an assessment is done by asking a question to the user, submitting the answer to InvestSuite, receiving the next question to ask from InvestSuite, asking that question to the user, and so on until the assessment is completed. An assessment will have a status COMPLETE and show a result when all questions are answered. When there are still questions to be answered, the assessment has a status IN_PROGRESS and contains a next question to ask.  

After receiving an answer from the user, the answer is submitted to InvestSuite in order to get the next question. InvestSuite saves the answer immediately and determines the next question. This way, InvestSuite keeps track of and saves the progress of the assessment, so the user can close the application any time and resume the assessment where they left off later. 

![Conducting assessment](../img/conducting_assessment.jpg)

The assessment response contains the next question to be asked or the score, the parameters, the sections with the answered questions, and a status. This will be the response of a call to `GET /suitability-profiler/profiles/{profile_id}/assessments/{questionnaire_id}` and the `PUT /profiles/{profile_id}/questionnaires/{questionnaire_id}/assessment/questions/{question_id}/answer`
 for submitting an answer.

**Response body** 
```JSON
{
    "sections": [
        {
            "answered_questions": [
                {
                    "value": {
                        "form": {
                            "control_type": "OK", 
                            "type": "CONTROL",
                        },
                        "title": "Welcome", 
                        "body": "In the next minutes, we'll help you create your own, personalized investment portfolio. But first we want to explain how this works.",
                        "index": "0", 
                        "id": "D01RRZ3NDDKTSK4RRFFQ69G5FAV",
                    }, 
                    "answer": {
                        "type": "OK"
                    }
                }
            ], 
            "title": {
                "en-us": "Introduction",
            }, 
            "question_count": 3,
        }, 
        {
            "answered_questions": [], 
            "title": {
                "en-us": "Investment Goals",
            }
        }, 
    ],
    "status": {
        "section_index": 0,
        "question": {
            "form": {
                "control_type": "OK", 
                "type": "CONTROL",
            },
            "title": "More information", 
            "body": "Robo Advisor invests in Exchange Traded Funds (ETFs), which are made from underlying instruments like stocks or bonds, and follows an index.",
            "index": "1", 
            "id": "D01ARZ3NDEKTSV4RRFFQ69G5FAV",
        }, 
        "type": "IN_PROGRESS",
    },
}
```  

## Get a profile property value

When a profile is created for a user, profile property values are initially set to a default value. The user can later on see these properties and change them if they like. For example, if the robo advisor offers the option to only invest in sharia compliant companies, a property value sharia_compliant can initially be set to false. If the user decides that they want to invest in only sharia compliant companies, this property can be set to true. Let's retrieve a property value together.

=== "HTTP"

    ```HTTP 
    GET /suitability-profiler/profiles/N01ARZ3NDEKTSV4RRFFQ69G5FAV/properties/exclusion-list/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    ```

=== "curl"

    ```bash
    curl -X GET \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    https://api.sandbox.investsuite.com/suitability-profiler/profiles/N01ARZ3NDEKTSV4RRFFQ69G5FAV/properties/exclusion-list/

    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`profile_id` | A unique identifier of the profile that this assessment is for. | `string ^N[0-9A-HJKMNP-TV-Z]{26}\Z` | N01ARZ3NDEKTSV4RRFFQ69G5FAV | yes 
`property_id` | The ID of the property that is to be retrieved. | `string ^P[0-9A-HJKMNP-TV-Z]{26}\Z` | P01ARZ3NDEKTSV4RRFFQ69G5FAV | yes

**Response body** 
```JSON
{
    "property_id": "P01ARZ3NDEKTSV4RRFFQ69G5FAV", 
    "property_name": {
        "en-US": "Exclusion list"
    }, 
    "value": {
        "type": "lIST", 
        "value": []
    }
}
```

The exclusion list for this portfolio is still empty. When a user wants to exclude an instrument from their portfolio, this list will be updated.

## Change a property value

The user can adjust its profile property values to make their portfolio more suitable to them. For example, if a user only wants to invest in Sharia compliant instruments, and the tenant supports this option, the profile property value corresponding to this option can be adjusted. Let's look at the below example.

=== "HTTP"

    ```HTTP 
    PUT /suitability-profiler/profiles/À01ARZ3NDEKTSV4RRFFQ69G5FAV/properties/P01ARZ3NDEKTSV4RRFFQ69G5FAV/value/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}
    {
        "type": "BOOLEAN",
        "value": "true",
    }
    ```

=== "curl"

    ```bash
    curl -X PUT \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    -d '{  \   
            "type": "BOOLEAN",  \
            "value": "true",  \
        }'
    https://api.sandbox.investsuite.com/suitability-profiler/profiles/À01ARZ3NDEKTSV4RRFFQ69G5FAV/properties/P01ARZ3NDEKTSV4RRFFQ69G5FAV/value/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`profile_id` | A unique identifier for the profiler to be used as template for the profile. This field is required for knowing which assessments to add to the profile. | `string ^À[0-9A-HJKMNP-TV-Z]{26}\Z` | À01ARZ3NDEKTSV4RRFFQ69G5FAV | yes 
`property_id` | The ID of a property in the profile that you want to change. | `string ^P[0-9A-HJKMNP-TV-Z]{26}\Z` | P01ARZ3NDEKTSV4RRFFQ69G5FAV | yes 
`type` | The type of the property ID value | `string` | BOOLEAN | yes 
`value` | The value of the property ID | `Any` | true | yes

When a successful response comes back, the property is changed in the profile. 
