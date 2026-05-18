# UK Housing - Switchee MuleSoft Integration

# Project Name
Switchee Insights MuleSoft Integration

# Project Overview
## Vision & Goals
This project provides a MuleSoft integration layer between the [Switchee](https://www.switchee.co) smart heating API and Salesforce, enabling housing providers to surface real-time property health insights directly within their Salesforce org and Salesforce Data Cloud.

* Authenticate with the Switchee OAuth API and retrieve property-level insights (mould risk, fuel poverty risk, heat loss rate, heat stroke risk, time to heat)
* Sync single-property insights daily to a Salesforce Asset record via scheduled flow
* Stream paginated bulk insights from the Switchee API into Salesforce Data Cloud via a second scheduled flow
* Externalise all configuration (credentials, endpoints, schedules) into environment-specific property files, following MuleSoft best practices

## Project Vertical
Housing (Other)

## Trailblazer Group or Slack Channel Link (access required)
Please replace with the URL for your Trailblazer Community group and/or Slack channel issued by the Commons program team.

## How to Contribute:
- Review the [CONFIGURATION_GUIDE.md](CONFIGURATION_GUIDE.md) to understand environment setup and property file structure
- Clone the repo and configure your environment-specific `.properties` files (do not commit credentials)
- Submit pull requests against the `master` branch with clear descriptions of changes

## Project Resources and Documentation
- [CONFIGURATION_GUIDE.md](CONFIGURATION_GUIDE.md) — full guide to environment configuration, Maven profiles, and deployment
- MuleSoft documentation: https://docs.mulesoft.com/general/
- Switchee API documentation: https://www.switchee.co

***

# Sprint (18 May 2026):
## Project Team & Accomplishments
- Initial MuleSoft project created with two integration flows: single-property daily sync to Salesforce Asset and bulk paginated sync to Salesforce Data Cloud
- All configuration externalised into environment-specific property files (`config-dev`, `config-test`, `config-prod`)
- Maven profiles added to support multi-environment builds and CloudHub deployment
- Global connector configurations updated to reference externalised properties

## Contributors

Full Name            | Team Role     | Github Username                                                        | Working Group?
------------         | ------------- | -------------                                                          | -------------
Stephane Pajone      | Contributor   | [spajon-muley](https://github.com/spajon-muley)                        |

## Future Contributions
(AKA what were you unable to finish at the Sprint)
- Expand single-property flow to iterate over multiple properties rather than a single hardcoded `property.id`
- Add error handling and dead-letter queue for failed Salesforce updates
- Add MUnit tests for both flows
- Document Salesforce Data Cloud ingestion API object schema
