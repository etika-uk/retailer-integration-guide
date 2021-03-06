## Customer Intelligence

To simulate responses in the test environment, please refer to [Simulate Customer Intelligence Responses](../#simulate-customer-intelligence-responses).

Terminology | Description
---|---
Applicant | A customer who has applied for finance.
Credit Limit | Maximum amount of finance available for a customer based on data provided.
Lead | A customer who has yet to apply for finance.
Lead Score | A credit worthiness indicator that allows a merchant to optimize the sales funnel.

### Advice

The advice follows a traffic light system.

value | description
--|--
`green` | Indicates based on the data provided, that the customer is of good credit worthiness.
`amber` | We don't have enough information to decide between `green` and `red`. The customer may still proceed but would be subject to further checks.
`red` | Indicates based on the data provided, that the customer may not be worthy of credit, and we would suggest that they do not proceed as it may harm their credit profile.

> Please note that the advice is indicative and not a final decision.

### Agreement In Principle (AIP) via API 

```
POST {{ site.data.globals.api_prefix }}/installations/:installation/pre-approval
```

#### Parameters

Name | Required | Type | Description | Validation Rules
--- | --- | --- | --- | ---
`$.email` | Yes | string | Email address of the applicant |
`$.title` | Yes | string | Title. | Accepted values: 'Mr', 'Mrs', 'Miss', 'Ms' Case insensitive.
`$.first_name` | Yes | string | First name |
`$.last_name` | Yes | string | Last name |
`$.date_of_birth` | Yes | string | Date Of Birth. | Must be in an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date format. The applicant must be at least 18 years old.
`$.dependents` | Yes | int | The number of dependents the applicant has |
`$.marital_status` | Yes | int | A marital status described in [Get a Marital Status](#marital-statuses) |
`$.employment_status` | Yes | int | An employment status described in [Get an Employment Status](#employment-statuses). Must be a valid employment status. | The following employment statuses are excluded from this service: `1`: *Casual Employee*, `3`: *House Person*, `8`: *Temporary Employee*, `10`: *Unemployed*, `16`: *In Receipt of Jobseeker's Allowance*, `17`: *Working Through a Recruitment Agency*
`$.employment_start_date` | Yes | date | Date of employment start. | Must be in an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date format
`$.income` | Yes | int | Monthly income amount in pounds |
`$.debt` | Yes | int | Monthly debt repayments in pounds |
`$.consent` | Yes | string | Date when the consumer consented to the search. | Must be in an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date time format.
`$.marketing` | No | bool | Consumer's consents to being marketed towards on their provided details (e-mail and address). |
`$.addresses.*` | Yes | array | An array of [address](#address) history. |

#### Response

Name | Type | Description
--- | --- | --- | ---
`$.id` | int | Advice identifier
`$.email` | string | Email address of the applicant
`$.title` | string | Title passed from the request
`$.first_name` | string | First name passed from the request
`$.last_name` | string | Last name passed from the request
`$.date_of_birth` | string | Date Of Birth passed from the request
`$.dependents` | int | The number of dependents the applicant has
`$.marital_status` | int | A marital status described in [Get a Marital Status](#marital-statuses)
`$.employment_status` | int | An employment status described in [Get an Employment Status](#employment-statuses)
`$.employment_start_date` | date | Date of employment start passed from the request
`$.income` | int | Monthly income amount in pounds
`$.debt` | int | Monthly debt repayments in pounds
`$.addresses.*` | array | An array of [address](#address) history
`$.advice` | string | The [advice](#advice)
`$.products.*` | array | An array of product objects, each with their own advice
`$.products.[*].product.*` | array | An array of product information
`$.products.[*].product.id` | string | Product code identifier
`$.products.[*].product.name` | string | Product name
`$.products.[*].advice` | string | The [advice](#advice)
`$.products.[*].credit_limit` | int *or* null | The amount the applicant could be accepted with in pence. When a credit limit cannot be supplied, null is returned

```json
{
    "id": 1,
    "email": "test.uk@etika.com",
    "title": "Mr",
    "first_name": "Test",
    "last_name": "Tester",
    "date_of_birth": "1990-06-30",
    "dependents": 2,
    "marital_status": 1,
    "employment_status": 2,
    "employment_start_date": "2018-01-20",
    "income": 1500,
    "debt": 0,
    "addresses": [
        {
            "abode": null,
            "building_number": "1",
            "building_name": null,
            "street": "Test Road",
            "locality": null,
            "town": "Test Town",
            "postcode": "T3 STT",
            "moved_in_date": null,
            "accommodation_type": null,
            "residential_status": 0
        }
    ],
    "advice": "green",
    "products": [
        {
            "product": {
                "id": "IBC-12-049",
                "name": "12 Months Credit (4.9% APR)"
            },
            "advice": "green",
            "credit_limit": 30000
        }
    ]
}
```

### Agreement In Principle (AIP) via Widget

Please note, you will need this functionality to be enabled. Please contact your account manager who can provide you the relevant information required to get started.

#### Rendering the Widget on Page Load

Insert the following code onto your page. You'll need to insert the URL you've received from your account manager.
```html
<script>
( function() { 
    let etikaWidget = document.createElement('script');
    etikaWidget.setAttribute( 
        'src',
        '[URL From Account Manager]' 
    ); 
    window.etika_aip_config = {
        installation: "[Installation From Account Manager]",
        token: "[Token From Account Manager]",
        primaryColor: "#443749",
        secondaryColor: "#FFCCAC"
    }; 
    document.body.appendChild( etikaWidget ); 
} )();
</script>
```


#### Rendering the Widget on an Event

If you wish to load the widget in response to an event, you can do so by conditionally calling the `document.body.appendChild(etikaWidget)` line above in response to your event.

#### Available Widget Configuration

Key | Type | Description
--- | --- | ---
installation | string | Your Installation ID, provided by your account manager
token | string | Your token, provided by your account manager
primaryColor | string (Hexadecimal Color) | Primary color used for headings & action buttons
secondaryColor | string (Hexadecimal Color) | Secondary color used for subtitles & back buttons

### Fetch an AIP result

```
GET {{ site.data.globals.api_prefix }}/installations/:installation/pre-approval/:id
```

#### Response

Name | Type | Description
--- | --- | ---
`$.id` | int | Advice identifier
`$.email` | string | Email address of the applicant
`$.title` | string | Title passed from the request
`$.first_name` | string | First name passed from the request
`$.last_name` | string | Last name passed from the request
`$.date_of_birth` | string | Date Of Birth passed from the request
`$.dependents` | int | The number of dependents the applicant has
`$.marital_status` | int | A marital status described in [Get a Marital Status](#marital-statuses)
`$.employment_status` | int | An employment status described in [Get an Employment Status](#employment-statuses)
`$.employment_start_date` | date | Date of employment start passed from the request
`$.income` | int | Monthly income amount in pounds
`$.debt` | int | Monthly debt repayments in pounds
`$.addresses.*` | array | An array of [address](#address) history
`$.advice` | string | The [advice](#advice)

```json
{
    "id": 1,
    "email": "test.uk@etika.com",
    "title": "Mr",
    "first_name": "Test",
    "last_name": "Tester",
    "date_of_birth": "1990-06-30",
    "dependents": 2,
    "marital_status": 1,
    "employment_status": 2,
    "employment_start_date": "2018-01-20",
    "income": 1500,
    "debt": 0,
    "addresses": [
        {
            "abode": null,
            "building_number": "1",
            "building_name": null,
            "street": "Test Road",
            "locality": null,
            "town": "Test Town",
            "postcode": "T3 STT",
            "moved_in_date": null,
            "accommodation_type": null,
            "residential_status": 0
        }
    ],
    "advice": "green"
}
```


### Fetch AIP Results

```
GET {{ site.data.globals.api_prefix }}/installations/:installation/pre-approval
```

#### Response

Name | Type | Description
--- | --- | ---
`$.data.*` | array | Array of pre-approval responses
`$.data.[*].id` | int | Advice identifier
`$.data.[*].email` | string | Email address of the applicant
`$.data.[*].title` | string | Title passed from the request
`$.data.[*].first_name` | string | First name passed from the request
`$.data.[*].last_name` | string | Last name passed from the request
`$.data.[*].date_of_birth` | string | Date Of Birth passed from the request
`$.data.[*].dependents` | int | The number of dependents the applicant has
`$.data.[*].marital_status` | int | A marital status described in [Get a Marital Status](#marital-statuses)
`$.data.[*].employment_status` | int | An employment status described in [Get an Employment Status](#employment-statuses)
`$.data.[*].employment_start_date` | date | Date of employment start passed from the request
`$.data.[*].income` | int | Monthly income amount in pounds
`$.data.[*].debt` | int | Monthly debt repayments in pounds
`$.data.[*].addresses.*` | array | An array of [address](#address) history
`$.data.[*].advice` | string | The [advice](#advice)
`$.offset` | int | the offset passed in, or default
`$.limit` | int | the limit passed in, or default
`$.total` | int | the total number of records returned

```json
{
    "data": [
        {
            "id": 1,
            "email": "test.uk@etika.com",
            "title": "Mr",
            "first_name": "Test",
            "last_name": "Tester",
            "date_of_birth": "1990-06-30",
            "dependents": 2,
            "marital_status": 1,
            "employment_status": 2,
            "employment_start_date": "2018-01-20",
            "income": 1500,
            "debt": 0,
            "addresses": [
                {
                    "abode": null,
                    "building_number": "1",
                    "building_name": null,
                    "street": "Test Road",
                    "locality": null,
                    "town": "Test Town",
                    "postcode": "T3 STT",
                    "moved_in_date": null,
                    "accommodation_type": null,
                    "residential_status": 0
                }
            ],
            "advice": "green"
        }
    ],
    "offset": 0,
    "limit": 15,
    "total": 1
}
```

### Product Suitability Advice for an Applicant

#### `Use the Product Suitability API endpoints with caution, they are in BETA and are subject to change based on feedback from our community.`

```
POST {{ site.data.globals.api_prefix }}/installations/:installation/product-suitability-advice
```

#### Parameters

Name | Required | Type | Description | Validation Rules
--- | --- | --- | --- | ---
`$.email` | Yes | string | Email address of the applicant |
`$.title` | Yes | string | Title. | Accepted values: 'Mr', 'Mrs', 'Miss', 'Ms' Case insensitive.
`$.first_name` | Yes | string | First name |
`$.last_name` | Yes | string | Last name |
`$.date_of_birth` | Yes | string | Date Of Birth. | Must be in an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date format. The applicant must be at least 18 years old.
`$.dependents` | Yes | int | The number of dependents the applicant has |
`$.marital_status` | Yes | int | A marital status described in [Get a Marital Status](#marital-statuses) |
`$.employment_status` | Yes | int | An employment status described in [Get an Employment Status](#employment-statuses). Must be a valid employment status. | The following employment statuses are excluded from this service: `1`: *Casual Employee*, `3`: *House Person*, `8`: *Temporary Employee*, `10`: *Unemployed*, `16`: *In Receipt of Jobseeker's Allowance*, `17`: *Working Through a Recruitment Agency*
`$.employment_start_date` | Yes | date | Date of employment start. | Must be in an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date format
`$.income` | Yes | int | Monthly income amount in pounds |
`$.debt` | Yes | int | Monthly debt repayments in pounds |
`$.consent` | Yes | string | Date when the consumer consented to the search. | Must be in an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date time format.
`$.marketing` | No | bool | Consumer's consents to being marketed towards on their provided details (e-mail and address). |
`$.addresses.*` | Yes | array | An array of [address](#address) history. |

#### Response

Name | Type | Description
--- | --- | --- | ---
`$.id` | int | Advice identifier
`$.email` | string | Email address of the applicant
`$.title` | string | Title passed from the request
`$.first_name` | string | First name passed from the request
`$.last_name` | string | Last name passed from the request
`$.date_of_birth` | string | Date Of Birth passed from the request
`$.dependents` | int | The number of dependents the applicant has
`$.marital_status` | int | A marital status described in [Get a Marital Status](#marital-statuses)
`$.employment_status` | int | An employment status described in [Get an Employment Status](#employment-statuses)
`$.employment_start_date` | date | Date of employment start passed from the request
`$.income` | int | Monthly income amount in pounds
`$.debt` | int | Monthly debt repayments in pounds
`$.addresses.*` | array | An array of [address](#address) history
`$.advice` | string | The [advice](#advice)

```json
{
    "id": 1,
    "email": "test.uk@etika.com",
    "title": "Mr",
    "first_name": "Test",
    "last_name": "Tester",
    "date_of_birth": "1990-06-30",
    "dependents": 2,
    "marital_status": 1,
    "employment_status": 2,
    "employment_start_date": "2018-01-20",
    "income": 1500,
    "debt": 0,
    "addresses": [
        {
            "abode": null,
            "building_number": "1",
            "building_name": null,
            "street": "Test Road",
            "locality": null,
            "town": "Test Town",
            "postcode": "T3 STT",
            "moved_in_date": null,
            "accommodation_type": null,
            "residential_status": 0
        }
    ],
    "advice": "green",
    "_links": {
       "getSuitableProducts": {
        "description": "Get available finance products",
        "href": "/v4/installations/:installation/:advice-id/get-suitable-products",
        "dataRequired": [
            {
              "description": "What is the customer's budget?",
              "parameterName": "budget",
              "valueType": "int" 
            }
        ]
      }
  }
}
```

### Suitable Products for an Applicant

#### `Use the Product Suitability API endpoints with caution, they are in BETA and are subject to change based on feedback from our community.`

```
POST {{ site.data.globals.api_prefix }}/installations/:installation/:id/get-suitable-products
```

#### Parameters

Name | Required | Type | Description
--- | --- | --- | --- | ---
`$.budget` | Yes | int | The value of the customer's budget in pence

#### Response

Name | Type | Description
--- | --- | --- | ---
`$.id` | int | Product collection identifier
`$.products.*` | array | An array of product objects, each with their own advice
`$.products.[*].product.*` | array | An array of product information
`$.products.[*].product.id` | string | Product code identifier
`$.products.[*].product.name` | string | Product name
`$.products.[*].credit_limit` | int *or* null | The amount the applicant could be accepted with in pence. When a credit limit cannot be supplied, null is returned

```json
{
 "id": 1,
 "products": [
        {
            "product": {
                "id": "IBC-12-049",
                "name": "12 Months Credit (4.9% APR)"
            },
            "advice": "green",
            "credit_limit": 30000
        }
    ]
}
```
