---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - python
  - shell


toc_footers:

includes:
  - errors

search: true
---

# Introduction

Welcome to our internal API documentation. This documentation is intended for both business and developer users to understand what API functionality we support and how to use it.

Currently, we have documented two API groups:

### 1. Events API

The Events API requires no authentication. It provides a single entry point that captures events from various stages of the user journey. Here is a summary of the functionality that it currently provides:

* Salesforce email generation
* Dialer lead generation
* Storage of leads, application journeys in Suite CRM
* Some custom functions for specific countries/verticals

### 2. Data services API - which interacts with our Data warehouse

# Dialer Lead Generation

> Example of calling the Events API to generate a Lead in TMG

```python

import httplib2

headers = {
  'Content-type': 'application/json',
  'Usercity-Auth-Token': ''
  }

payload = {
  "phone": "995551234",
  "email": "steve.walsh@compareglobalgroup.com",  
  "vertical": "car_insurance",
  "locale": "HK",  
  "language": "ES",
  "attributes": {
    "usercityEventName": "postFromFunnel",
  },
  "source_url": "http://moneyhero.hk"
}
events_url = "http://moneyhero.hk/api/events/submit"

http = httplib2.Http()
http.request(events_url, 'POST', body = payload, headers = headers)

```

In terms of dialers, currently, we support TMG only. The dialer lead generation functionality requires an initial event message that contains the required query parameters listed below. Subsequent events can be sent with additional required fields listed below. The dialer lead generation logic uses an email address to merge all of these events together and allow for 15 minutes of inactivity before sending the lead to the dialer.

### Required Query Parameters for Initial Event

Parameter | Description
--------- | -----------
phone | The dialer cannot function without a phone number
email | Usercity uses the email address field to determine unique users and merge all events associated with a user journey (within last 15 minutes together)
vertical | Used to identify segmentation config
locale | Used to identify segmentation config
language | Required but not Used
attributes/usercityEventName | The first event with the listed required parameters must have the usercityEventName set to 'postFromFunnel' in order to generate a TMG lead. Subsequent events can have a different type
source_url | Used to indicate where the user dropped off on their user journey

### Required Query Parameters for Subsequent Events

Parameter | Description
--------- | -----------
email | Usercity uses the email address field to determine unique users and merge all events


# Salesforce Marketing Cloud (Email)

3 different types of email can be triggered. 

Type of Email | Status
--------- | -----------
Top 3 Products | <a href="https://compareglobalgroup.atlassian.net/wiki/spaces/RET/pages/104398931/Top+3+Products+Email+Status+Per+Country+Per+Vertical"> Status</a>
Abandoned Checkout | <a href="https://compareglobalgroup.atlassian.net/wiki/spaces/RET/pages/113187977/Abandoned+Checkout+Email+Status+Per+Country+Per+Vertical"> Status </a>
Order Confirmation | <a href="https://compareglobalgroup.atlassian.net/wiki/spaces/RET/pages/112105747/Order+Confirmation+Email+Per+Country+Per+Vertical"> Status </a> 

<i>Please note that API calls will trigger specific journeys but all rules to be applied to the time of send, number of emails to be sent and any A/B tests are set up in the UI. </i>

## Top 3 Products Email

Every time a user goes through the funnel, we can send them the top 3 products that best fits their needs. 

```python

import httplib2

headers = {
  'Content-type': 'application/json',
  'Usercity-Auth-Token': ''
  }

payload = {
  "phone": "995551234",
  "email": "steve.walsh@compareglobalgroup.com",  
  "vertical": "car_insurance",
  "locale": "HK",  
  "language": "ES",
  "attributes": {
    "usercityEventName": "postFromFunnel",
  },
  "source_url": "http://moneyhero.hk"
}
events_url = "http://moneyhero.hk/api/events/submit"

http = httplib2.Http()
http.request(events_url, 'POST', body = payload, headers = headers)

```

### Required Query Parameters for Initial Event

Parameter | Description
--------- | -----------
email | Salesforce Marketing Cloud cannot function without an email address
vertical | Used to identify segmentation config
locale | Used to identify segmentation config
language | Used to identify which email language to be sent
attributes/usercityEventName | The first event with the listed required parameters must have the usercityEventName for Salesforce Marketing Cloud to identify which type of email to send
source_url | Used to indicate where the user dropped off on their user journey in order for them to continue their journey
dm_consent | has to be "true" for Salesforce Marketing Cloud to send email
tc_consent | has to be "true" for Salesforce Marketing Cloud to send email
attributes/top products | all product information for each of the top three product should be included (see example)
api-event-key | The key is generated by Salesforce and is unique for each journey you want to trigger. It is available in Journey Builder > Entry Source  

## Abandoned Checkout Email 

Users who drop off the application form (after step 1) can receive an email to resume their application.  

### Required Query Parameters for Initial Event

Parameter | Description
--------- | -----------
email | Salesforce Marketing Cloud cannot function without an email address
vertical | Used to identify segmentation config
locale | Used to identify segmentation config
language | Used to identify which email language to be sent
attributes/usercityEventName | The first event with the listed required parameters must have the usercityEventName for Salesforce Markeitng Cloud to identify which type of email to send
source_url | Used to indicate where the user dropped off on their user journey in order for them to continue their journey
dm_consent | has to be "true" for Salesforce Marketing Cloud to send email
tc_consent | has to be "true" for Salesforce Marketing Cloud to send email
api-event-key | The key is generated by Salesforce and is unique for each journey you want to trigger. It is available in Journey Builder > Entry Source  

### Optional Parameters
the payload should include all variables for the email template.
e.g. travel insurance abandon cart email, include related fields from the funnel and first step of the application
attributes/application form/provider | variable to display in email template
attributes/application form/destinations | variable to display in email template


## Order Confirmation Email

Users who complete the application form can receive an email to confirm their order has beem received by our team. 

### Required Query Parameters for Initial Event

Parameter | Description
--------- | -----------
email | Salesforce Marketing Cloud cannot function without an email address
vertical | Used to identify segmentation config
locale | Used to identify segmentation config
language | Used to identify which email language to be sent
attributes/usercityEventName | The first event with the listed required parameters must have the usercityEventName for Salesforce Markeitng Cloud to identify which type of email to send
dm_consent | has to be "true" for Salesforce Marketing Cloud to send email
tc_consent | has to be "true" for Salesforce Marketing Cloud to send email
attributes/application_form/completed |  has to be "true" for Salesforce Marketing Cloud to send order confirmation email

### Optional Parameters
the payload should include all variables for the email template.
e.g. travel insurance confirmation email

Parameter | Description
--------- | -----------
attributes/application form/ name | variable to display in email template
attributes/application form/policyNumber | variable to display in email template
attributes/application form/provider | variable to display in email template
attributes/application form/category | variable to display in email template
attributes/application form/date/start | variable to display in email template
attributes/application form/date/end | variable to display in email template
attributes/application form/destinations | variable to display in email template
attributes/application form/phone | variable to display in email template

## Suite CRM
## Generating a lead record

A Lead record will be created in Leads Module when customer submit funnel (email/phone submit before apply button click). No update rule applies, the system will create a new record everytime.

### Required Query Parameters for Initial Event

Parameter | Description
--------- | -----------
email OR phone | A lead record will be created with email/phone information 
vertical | Used to identify segmentation config
locale | Used to identify segmentation config
attributes/usercityEventName | field is required, field value needs to be "postFromFunnel"
tc_consent | fielf value has to be "true"
attributes/funnel fiels | CRM have to receive funnel fields and value to display these information

### optional fields
utm_source, utm_campaign, utm_medium | if a customer is from a marketing platform (FB, Google, email), these fields must present
source url | a link for user to retrieve customer last access page and some related information

## Generating an application record

An application record will be created in Applications module on or after the event of result page apply button click. the incomplete application will be updated by the same CGG ID+product code. 

### Required Query Parameters for Initial Event

Parameter | Description
--------- | -----------
email OR phone | An application record will be created with email/phone information 
vertical | Used to identify segmentation config
locale | Used to identify segmentation config
attributes/usercityEventName | not sure what value is required-lixing
providerCode | this is required
providerName | this is required
productCode | this is required
productName | this is required
tc_consent | has to be "true"
attributes/application_form/application form fields | CRM have to receive additional application form fields and value to display these information

### optional fields
utm_source, utm_campaign, utm_medium | if a customer is from a marketing platform (FB, Google, email), these fields must present
source url | a link for user to retrieve customer last access page and some related information

### Required Query Parameters for Subsequent Event -updating an existing record

Parameter | Description
--------- | -----------
email| the new record need to have the same CGG ID as the record wishing to be updated (*1. if email is changed in app form, root email cannot be changed. 2. if email is newly added in app form, the newly added email should be under attributes 3. if only phone number presents, records will be merged by the same phone number)
vertical | Used to identify segmentation config
locale | Used to identify segmentation config
attributes/usercityEventName | not sure what value is required-lixing
providerCode | this is required
providerName | this is required
productCode | this field should be the same as the record wishing to be updated
productName | this is required
tc_consent | has to be "true"
attributes/application_form/application form fields | CRM have to receive application form fields and value to display these information

## Generating an applicant record

An applicant record will be created in applicants module on or after an application is completed. the applicant profile will be updated by the same CGG ID. 

## Generating a payment record

### Required Query Parameters for Initial Event
## Custom functions

### Payment Guard - Hong Kong Travel Insurance

### Email Completed Applications to Provider - Hong Kong Personal Loan

### Application Forms submitted to MULE for Provider - Singapore


# Data Services API
