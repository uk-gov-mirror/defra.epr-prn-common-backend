# PRD1 retained endpoints: callers and root journeys

**All 12 retained PRN endpoints have executable caller chains.** Source and configuration evidence support retention; current production traffic remains unverified. Accreditation is excluded under the separate [scope decision](github-accreditation-callers.md).

## Endpoint summary

| Method and route | Direct callers | Root journeys/jobs |
|---|---|---|
| `GET /api/v1/prn/{prnId}` | epr-pom-api-web, waste-obligations | View PRN details; acceptance/rejection pages and PDF generation also read details; View PRN details |
| `GET /api/v1/prn/search/{page?}/{search?}/{filterBy?}/{sortBy?}` | epr-pom-api-web, waste-obligations | Search/list PRNs and awaiting-acceptance results; Search/list PRNs; obligations page also lists awaiting-acceptance PRNs |
| `GET /api/v1/prn/organisation` | epr-pom-api-web | Download PRN CSV; bulk acceptance and accepted-note lists also use organisation PRNs |
| `GET /api/v1/prn/ModifiedPrnsbyDate` | epr-prn-integration-function | Scheduled UpdatePrnsList exports changed PRN statuses to NPWD |
| `GET /api/v1/prn/syncstatuses` | epr-prn-integration-function | Scheduled EmailNpwdReconciliation produces a reconciliation report |
| `GET /api/v1/prn/obligationcalculation/{year}` | epr-pom-api-web, waste-obligations | View recycling obligations for the selected year; View obligations; compliance journeys also fetch obligations; Background obligation hydration and historical-backfill workers → OrganisationObligationHydrationService → IOrganisationObligationSource (registered to PrnCommonBackendService); Admin unsubmitted-organisation details endpoint → UnsubmittedOrganisationDetailsService → ReadObligations; upstream human/tool caller unresolved |
| `POST /api/v1/prn/status` | epr-pom-api-web, waste-obligations | Accept one or multiple PRNs; reject through PrnsRejectController; Confirm acceptance of a PRN (frontend calls PATCH; backend adapter calls POST status) |
| `POST /api/v1/prn/organisation/{submitterId}/calculate` | epr-prn-obligationcalculations-function | StoreApprovedSubmissions timer fetches approved submissions → groups and sends Service Bus messages → ProcessApprovedSubmissions queue trigger → calculate obligations |
| `POST /api/v1/prn/prn-details` | epr-prn-integration-function | Scheduled FetchNpwdIssuedPrnsFunction fetches NPWD notes, queues and processes them, then saves each note |
| `POST /api/v1/prn/updatesyncstatus` | epr-prn-integration-function | Same UpdatePrnsList job records successful NPWD synchronisation |
| `POST /api/v2/prn` | epr-prn-integration-function | Scheduled FetchRrepwIssuedPrns imports issued RREPW PRNs |
| `GET /api/v2/prn/modified-prns` | epr-prn-integration-function | Scheduled UpdateRrepwPrns exports status changes to RREPW |

## Source evidence

References identify root entry points, intermediate services and direct clients. Multiple roots in an entry are alternatives, not sequential network calls.

### GET /api/v1/prn/{prnId}

**epr-pom-api-web — View PRN details; acceptance/rejection pages and PDF generation also read details.**

[Packaging UI root](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Controllers/Prns/PrnsController.cs#L117); [UI service](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Services/PrnService.cs#L85); [Frontend gateway client](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.Application/Services/WebApiGatewayClient.cs#L377); [Gateway API action](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Controllers/PrnController.cs#L33); [Gateway service](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Services/PrnService.cs#L22); [Backend HTTP client](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Clients/PrnServiceClient.cs#L65).

PRD1 packaging frontend PRDRWDWEBWA1410 has ShowPrn=true and WebAPI__BaseEndpoint targeting PRDRWDWEBWA1409; that gateway targets PRDRWDWEBWA1418. Repository settings only; not live deployment verification.

**waste-obligations — View PRN details.**

[Producer UI root](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/producer/prns/prn/controller.js#L18); [CSO UI root](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/cso/prns/prn/controller.js#L18); [Frontend invocation](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/producer/_middlewares/prn.js#L10); [Frontend API client](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/services/waste-obligations-api.service.js#L96); [Waste obligations API action](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Endpoints/Organisations/Prns/ReadPrn.cs#L35); [Backend HTTP client](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Services/PrnCommonBackend/PrnCommonBackendService.cs#L58).

Additional local dependency only; production routing/deployment not established. waste-obligations-frontend gates PRN routes on features.showPrns and obligations routes on features.manageObligations; both default false. Do not treat these chains as confirmed PRD1 traffic.

### GET /api/v1/prn/search/{page?}/{search?}/{filterBy?}/{sortBy?}

**epr-pom-api-web — Search/list PRNs and awaiting-acceptance results.**

[Packaging UI root](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Controllers/Prns/PrnsController.cs#L38); [UI service](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Services/PrnService.cs#L138); [Frontend gateway client](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.Application/Services/WebApiGatewayClient.cs#L351); [Gateway API action](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Controllers/PrnController.cs#L41); [Gateway service](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Services/PrnService.cs#L34); [Backend HTTP client](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Clients/PrnServiceClient.cs#L43).

PRD1 packaging frontend PRDRWDWEBWA1410 has ShowPrn=true and WebAPI__BaseEndpoint targeting PRDRWDWEBWA1409; that gateway targets PRDRWDWEBWA1418. Repository settings only; not live deployment verification.

**waste-obligations — Search/list PRNs; obligations page also lists awaiting-acceptance PRNs.**

[Producer UI root](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/producer/prns/controller.js#L10); [CSO UI root](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/cso/prns/controller.js#L10); [Frontend invocation](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/producer/_middlewares/prns.js#L11); [Frontend API client](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/services/waste-obligations-api.service.js#L116); [Waste obligations API action](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Endpoints/Organisations/Prns/SearchPrns.cs#L39); [Backend HTTP client](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Services/PrnCommonBackend/PrnCommonBackendService.cs#L76).

Additional local dependency only; production routing/deployment not established. waste-obligations-frontend gates PRN routes on features.showPrns and obligations routes on features.manageObligations; both default false. Do not treat these chains as confirmed PRD1 traffic.

### GET /api/v1/prn/organisation

**epr-pom-api-web — Download PRN CSV; bulk acceptance and accepted-note lists also use organisation PRNs.**

[Packaging UI root](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Controllers/Prns/PrnsController.cs#L133); [UI service](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Services/PrnService.cs#L194); [Frontend gateway client](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.Application/Services/WebApiGatewayClient.cs#L331); [Gateway API action](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Controllers/PrnController.cs#L25); [Gateway service](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Services/PrnService.cs#L16); [Backend HTTP client](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Clients/PrnServiceClient.cs#L22).

PRD1 packaging frontend PRDRWDWEBWA1410 has ShowPrn=true and WebAPI__BaseEndpoint targeting PRDRWDWEBWA1409; that gateway targets PRDRWDWEBWA1418. Repository settings only; not live deployment verification.

### GET /api/v1/prn/ModifiedPrnsbyDate

**epr-prn-integration-function — Scheduled UpdatePrnsList exports changed PRN statuses to NPWD.**

[Timer root](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/UpdateNpwdPrnsFunction.cs#L28); [Function invocation](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/UpdateNpwdPrnsFunction.cs#L173); [NPWD backend HTTP client](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Common/RESTServices/PrnBackendService/NpwdPrnService.cs#L58); [Base URL composition](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Common/RESTServices/BaseHttpServiceOld.cs#L58).

RunIntegration=true; PRD1 UpdatePrnsTrigger is 0 1,31 0-7 * 3 1-5 (March weekdays). PRDRWDWEBFA1412 config targets PRDRWDWEBWA1418 and api/v1/prn. Actual execution not verified.

### GET /api/v1/prn/syncstatuses

**epr-prn-integration-function — Scheduled EmailNpwdReconciliation produces a reconciliation report.**

[Timer root](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/EmailNpwdReconciliationFunction.cs#L23); [Function invocation](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/EmailNpwdReconciliationFunction.cs#L49); [NPWD backend HTTP client](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Common/RESTServices/PrnBackendService/NpwdPrnService.cs#L114); [Base URL composition](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Common/RESTServices/BaseHttpServiceOld.cs#L58).

RunReconciliation=true; EmailNpwdReconciliationTrigger is 0 0 21 * 3 1-5 (March weekdays). PRDRWDWEBFA1412 config targets PRDRWDWEBWA1418 and api/v1/prn. Actual execution not verified.

### GET /api/v1/prn/obligationcalculation/{year}

**epr-pom-api-web — View recycling obligations for the selected year.**

[Packaging UI root](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Controllers/Prns/PrnsObligationController.cs#L69); [UI service](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Services/PrnService.cs#L250); [Frontend gateway client](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.Application/Services/WebApiGatewayClient.cs#L501); [Gateway API action](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Controllers/PrnController.cs#L49); [Gateway service](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Services/PrnService.cs#L40); [Backend HTTP client](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Clients/PrnServiceClient.cs#L102).

PRD1 packaging frontend PRDRWDWEBWA1410 has ShowPrn=true and WebAPI__BaseEndpoint targeting PRDRWDWEBWA1409; that gateway targets PRDRWDWEBWA1418. Repository settings only; not live deployment verification.

**waste-obligations — View obligations; compliance journeys also fetch obligations.**

[Producer UI root](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/producer/obligations/controller.js#L22); [CSO UI root](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/cso/obligations/controller.js#L22); [Frontend invocation](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/_shared/obligations/_middlewares/obligations-for-year.js#L14); [Frontend API client](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/services/waste-obligations-api.service.js#L54); [Waste obligations API action](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Endpoints/Organisations/Obligations/ReadObligations.cs#L36); [Backend HTTP client](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Services/PrnCommonBackend/PrnCommonBackendService.cs#L23).

Additional local dependency only; production routing/deployment not established. waste-obligations-frontend gates PRN routes on features.showPrns and obligations routes on features.manageObligations; both default false. Do not treat these chains as confirmed PRD1 traffic.

**waste-obligations — Background obligation hydration and historical-backfill workers → OrganisationObligationHydrationService → IOrganisationObligationSource (registered to PrnCommonBackendService).**

[Hydration worker root](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Services/OrganisationObligations/OrganisationObligationHydrationWorker.cs#L18); [Historical backfill worker root](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Services/OrganisationObligations/OrganisationObligationHistoricalBackfillWorker.cs#L16); [Hydration invocation](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Services/OrganisationObligations/OrganisationObligationHydrationService.cs#L470); [Client registration](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Services/PrnCommonBackend/ServiceCollectionExtensions.cs#L35); [Backend HTTP request](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Services/PrnCommonBackend/PrnCommonBackendService.cs#L23).

Local background-worker dependency; deployed worker enablement and target environment not verified.

**waste-obligations — Admin unsubmitted-organisation details endpoint → UnsubmittedOrganisationDetailsService → ReadObligations; upstream human/tool caller unresolved.**

[Admin entry point](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Endpoints/Admin/AdminUnsubmittedComplianceDeclarationEndpoints.cs#L77); [Service invocation](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Services/UnsubmittedOrganisationDetailsService.cs#L75).

Admin code path exists; no upstream application caller identified locally. Production use unknown.

Root status: Chain stops at admin API; external caller unresolved.

### POST /api/v1/prn/status

**epr-pom-api-web — Accept one or multiple PRNs; reject through PrnsRejectController.**

[Packaging UI root](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Controllers/Prns/PrnsAcceptController.cs#L159); [UI service](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Services/PrnService.cs#L102); [Frontend gateway client](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.Application/Services/WebApiGatewayClient.cs#L397); [Gateway API action](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Controllers/PrnController.cs#L57); [Gateway service](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Services/PrnService.cs#L28); [Backend HTTP client](https://github.com/DEFRA/epr-pom-api-web/blob/90327b4de21362eb025f0c59266349d974319bff/WebApiGateway/WebApiGateway.Api/Clients/PrnServiceClient.cs#L84); [Reject root](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Controllers/Prns/PrnsRejectController.cs#L48); [Bulk accept root](https://github.com/DEFRA/epr-packaging-frontend/blob/a1d483166a8c3de6d0cd22e776f767da4574d92c/src/FrontendSchemeRegistration.UI/Controllers/Prns/PrnsAcceptController.cs#L276).

PRD1 packaging frontend PRDRWDWEBWA1410 has ShowPrn=true and WebAPI__BaseEndpoint targeting PRDRWDWEBWA1409; that gateway targets PRDRWDWEBWA1418. Repository settings only; not live deployment verification.

**waste-obligations — Confirm acceptance of a PRN (frontend calls PATCH; backend adapter calls POST status).**

[Producer UI root](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/producer/prns/prn/confirm-accept/controller.js#L4); [CSO UI root](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/cso/prns/prn/confirm-accept/controller.js#L4); [Frontend invocation](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/_shared/prns/prn-status-update.js#L63); [Frontend API client](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/services/waste-obligations-api.service.js#L106); [Waste obligations API action](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Endpoints/Organisations/Prns/UpdatePrn.cs#L41); [Backend HTTP client](https://github.com/DEFRA/waste-obligations/blob/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006/src/Api/Services/PrnCommonBackend/PrnCommonBackendService.cs#L139).

Additional local dependency only; production routing/deployment not established. waste-obligations-frontend gates PRN routes on features.showPrns and obligations routes on features.manageObligations; both default false. Do not treat these chains as confirmed PRD1 traffic.

### POST /api/v1/prn/organisation/{submitterId}/calculate

**epr-prn-obligationcalculations-function — StoreApprovedSubmissions timer fetches approved submissions → groups and sends Service Bus messages → ProcessApprovedSubmissions queue trigger → calculate obligations.**

[Scheduled root](https://github.com/DEFRA/epr-prn-obligationcalculations-function/blob/ec3a8bf8cee6f7dbd1035bfe98af8105e631722a/src/EPR.PRN.ObligationCalculation.Function/StoreApprovedSubmissionsFunction.cs#L12); [Queue producer invocation](https://github.com/DEFRA/epr-prn-obligationcalculations-function/blob/ec3a8bf8cee6f7dbd1035bfe98af8105e631722a/src/EPR.PRN.ObligationCalculation.Function/StoreApprovedSubmissionsFunction.cs#L27); [Queue send](https://github.com/DEFRA/epr-prn-obligationcalculations-function/blob/ec3a8bf8cee6f7dbd1035bfe98af8105e631722a/src/EPR.PRN.ObligationCalculation.Application/Services/ServiceBusProvider.cs#L33); [Queue consumer](https://github.com/DEFRA/epr-prn-obligationcalculations-function/blob/ec3a8bf8cee6f7dbd1035bfe98af8105e631722a/src/EPR.PRN.ObligationCalculation.Function/ProcessApprovedSubmissionsFunction.cs#L13); [Client invocation](https://github.com/DEFRA/epr-prn-obligationcalculations-function/blob/ec3a8bf8cee6f7dbd1035bfe98af8105e631722a/src/EPR.PRN.ObligationCalculation.Function/ProcessApprovedSubmissionsFunction.cs#L26); [Backend HTTP client](https://github.com/DEFRA/epr-prn-obligationcalculations-function/blob/ec3a8bf8cee6f7dbd1035bfe98af8105e631722a/src/EPR.PRN.ObligationCalculation.Application/Services/PrnService.cs#L29); PRD1 route template (`epr-app-config-settings/prd1/PRDRWDWEBFA1410.json:43`, configuration snapshot).

PRDRWDWEBFA1410 targets PRDRWDWEBWA1418, FunctionIsEnabled=true; producer schedule 0 0 1 * * * (daily). Queue producer and consumer use the same configured ObligationQueueName. Root data originates in approved submissions, but the executable root for this request chain is the timer.

### POST /api/v1/prn/prn-details

**epr-prn-integration-function — Scheduled FetchNpwdIssuedPrnsFunction fetches NPWD notes, queues and processes them, then saves each note.**

[Timer root](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/FetchNpwdIssuedPrnsFunction.cs#L51); [Function invocation](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/FetchNpwdIssuedPrnsFunction.cs#L144); [NPWD backend HTTP client](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Common/RESTServices/PrnBackendService/NpwdPrnService.cs#L93); [Base URL composition](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Common/RESTServices/BaseHttpServiceOld.cs#L58); [Manual HTTP enqueue root](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/FetchSinglePrnFunction.cs#L21); [Manual queue producer](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/FetchSinglePrnFunction.cs#L44); [Scheduled queue drain](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/FetchNpwdIssuedPrnsFunction.cs#L95).

RunIntegration=true; FetchNpwdIssuedPrns__Schedule is 0 0 23 * 3 1-5 (March weekdays). FetchSinglePrnFunction can also enqueue a missing note for later processing. PRDRWDWEBFA1412 config targets PRDRWDWEBWA1418 and api/v1/prn. Actual execution not verified.

### POST /api/v1/prn/updatesyncstatus

**epr-prn-integration-function — Same UpdatePrnsList job records successful NPWD synchronisation.**

[Timer root](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/UpdateNpwdPrnsFunction.cs#L28); [Function invocation](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/UpdateNpwdPrnsFunction.cs#L110); [NPWD backend HTTP client](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Common/RESTServices/PrnBackendService/NpwdPrnService.cs#L76); [Base URL composition](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Common/RESTServices/BaseHttpServiceOld.cs#L58).

RunIntegration=true; same March-weekday schedule. PRDRWDWEBFA1412 config targets PRDRWDWEBWA1418 and api/v1/prn. Actual execution not verified.

### POST /api/v2/prn

**epr-prn-integration-function — Scheduled FetchRrepwIssuedPrns imports issued RREPW PRNs.**

[Timer root](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/FetchRrepwIssuedPrnsFunction.cs#L40); [Function invocation](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/FetchRrepwIssuedPrnsFunction.cs#L162); [v2 backend HTTP client](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Common/RESTServices/PrnBackendService/PrnService.cs#L38).

PRDRWDWEBFA1412 targets PRDRWDWEBWA1418; FetchRrepwIssuedPrns__Trigger = 0 0,30 7-23 * * *. No equivalent RunIntegration check found in these two function Run methods. Live settings not checked.

### GET /api/v2/prn/modified-prns

**epr-prn-integration-function — Scheduled UpdateRrepwPrns exports status changes to RREPW.**

[Timer root](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/UpdateRrepwPrnsFunction.cs#L26); [Function invocation](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Api/Functions/UpdateRrepwPrnsFunction.cs#L90); [v2 backend HTTP client](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Common/RESTServices/PrnBackendService/PrnService.cs#L44); [Route builder](https://github.com/DEFRA/epr-prn-integration-function/blob/4a37dbcd55b7620bf596172abec072b99b045b4e/src/EprPrnIntegration.Common/RESTServices/PrnBackendService/PrnRoutes.cs#L15).

PRDRWDWEBFA1412 targets PRDRWDWEBWA1418; UpdateRrepwPrns__Trigger = 0 10,40 7-23 * * *. No equivalent RunIntegration check found in these two function Run methods. Live settings not checked.

## PRD1 caller settings

Source: the supplied Azure DevOps `epr-app-config-settings` snapshot, not a live settings export. Only relevant routes, schedules and flags are reproduced.

| Configuration file | Setting | Value |
|---|---|---|
| `prd1/PRDRWDWEBWA1410.json:483` | `WebAPI__BaseEndpoint` | `https://prdrwdwebwa1409.azurewebsites.net/` |
| `prd1/PRDRWDWEBWA1410.json:243` | `FeatureManagement__ShowPrn` | `True` |
| `prd1/PRDRWDWEBWA1409.json:143` | `PrnServiceApi__BaseUrl` | `https://prdrwdwebwa1418.azurewebsites.net` |
| `prd1/PRDRWDWEBFA1412.json:83` | `Service__PrnBaseUrl` | `https://prdrwdwebwa1418.azurewebsites.net/` |
| `prd1/PRDRWDWEBFA1412.json:88` | `Service__PrnEndPointName` | `api/v1/prn` |
| `prd1/PRDRWDWEBFA1412.json:93` | `FeatureManagement__RunIntegration` | `True` |
| `prd1/PRDRWDWEBFA1412.json:233` | `FeatureManagement__RunReconciliation` | `True` |
| `prd1/PRDRWDWEBFA1412.json:38` | `FetchNpwdIssuedPrns__Schedule` | `0 0 23 * 3 1-5` |
| `prd1/PRDRWDWEBFA1412.json:78` | `UpdatePrnsTrigger` | `0 1,31 0-7 * 3 1-5` |
| `prd1/PRDRWDWEBFA1412.json:228` | `EmailNpwdReconciliationTrigger` | `0 0 21 * 3 1-5` |
| `prd1/PRDRWDWEBFA1412.json:43` | `FetchRrepwIssuedPrns__Trigger` | `0 0,30 7-23 * * *` |
| `prd1/PRDRWDWEBFA1412.json:58` | `UpdateRrepwPrns__Trigger` | `0 10,40 7-23 * * *` |
| `prd1/PRDRWDWEBFA1410.json:33` | `PrnServiceApi__BaseUrl` | `https://prdrwdwebwa1418.azurewebsites.net/` |
| `prd1/PRDRWDWEBFA1410.json:43` | `PrnServiceApi__PrnCalculateEndPoint` | `api/v1/prn/organisation/{0}/calculate` |
| `prd1/PRDRWDWEBFA1410.json:88` | `ApplicationConfig__FunctionIsEnabled` | `true` |
| `prd1/PRDRWDWEBFA1410.json:8` | `StoreApprovedSubmissions__Schedule` | `0 0 1 * * *` |

NPWD import, export and reconciliation schedules are restricted to March weekdays. A zero count outside that period is not evidence that their four API contracts can be removed.

## Additional consumers and unresolved root

Waste-obligations frontend routes depend on showPrns/manageObligations flags, both false by default. Production overrides were not established. Background hydration/backfill and an admin-details path also read obligations; the admin upstream caller remains unresolved.

Its API accepts rejected status, but the frontend root established here is acceptance. Packaging-frontend has both acceptance and rejection roots.

[Waste-obligations frontend feature defaults](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/config/config.js#L477); [Producer route registration gates](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/producer/index.js#L22); [CSO route registration gates](https://github.com/DEFRA/waste-obligations-frontend/blob/78c750e4fae8228cc37f2ac912e045dbef0aa0a6/src/server/routes/cso/index.js#L22); [Regulator facade route configuration](https://github.com/DEFRA/epr-regulator-service-facade/blob/e289ba355ee8571ae358ca2e0996ddeefbf9bf28/src/EPR.RegulatorService.Facade.API/appsettings.json#L134).

## Contract mismatch

Migration client requests a raw-data contract absent from the audited API snapshot. Do not silently add it to the 24-action inventory or equate it with GET /prn/{prnId}. Reconcile branches/versions before relying on this migration job.

[Migration job](https://github.com/DEFRA/legacy-prns/blob/7f9242599c6235c6bfdbcbb49ce1292332ebc6b6/src/Api/Jobs/MigrateLegacyPrns.cs#L45); [Missing contract reference](https://github.com/DEFRA/legacy-prns/blob/7f9242599c6235c6bfdbcbb49ce1292332ebc6b6/src/Api/Services/PrnCommonBackend/PrnCommonBackendService.cs#L8).

## Coverage and source revisions

- Scope is the 12 PRN contracts retained in the PRD1 migration baseline. Accreditation consumers and their separate exclusion decision are in github-accreditation-callers.md.
- Local searches covered non-ignored sibling source files, excluding dependency/build folders. This is not a complete search of every repository, branch or deployed release.
- Every retained PRN action has an executable caller and at least one identified root journey or job. Additional admin upstream callers remain unresolved where stated.
- Caller revisions are pinned below and exist on GitHub. Some were reviewed on feature branches; they are not assumed to be deployed in PRD1.
- Tests, mocks, stubs, request collections and data-generator scripts were not treated as production consumers.
- Preserve organisation/user headers and authentication as well as route and payload contracts during migration.
- Cron expressions are shown as configured, without timezone conversion. March-only NPWD schedules require representative seasonal telemetry.
- Related regulator-facade accreditation calls use the plural /accreditations/... routes in the disabled group, not the singular /Accreditation/... contracts.

| Repository | Inspected commit |
|---|---|
| DEFRA/epr-app-config-settings | [4e1bdfe779ca](https://github.com/DEFRA/epr-app-config-settings/tree/4e1bdfe779ca771434f7675e678f878044c2c980) |
| DEFRA/epr-packaging-frontend | [a1d483166a8c](https://github.com/DEFRA/epr-packaging-frontend/tree/a1d483166a8c3de6d0cd22e776f767da4574d92c) |
| DEFRA/epr-pom-api-web | [90327b4de213](https://github.com/DEFRA/epr-pom-api-web/tree/90327b4de21362eb025f0c59266349d974319bff) |
| DEFRA/epr-prn-integration-function | [4a37dbcd55b7](https://github.com/DEFRA/epr-prn-integration-function/tree/4a37dbcd55b7620bf596172abec072b99b045b4e) |
| DEFRA/epr-prn-obligationcalculations-function | [ec3a8bf8cee6](https://github.com/DEFRA/epr-prn-obligationcalculations-function/tree/ec3a8bf8cee6f7dbd1035bfe98af8105e631722a) |
| DEFRA/epr-regulator-service-facade | [e289ba355ee8](https://github.com/DEFRA/epr-regulator-service-facade/tree/e289ba355ee8571ae358ca2e0996ddeefbf9bf28) |
| DEFRA/legacy-prns | [7f9242599c62](https://github.com/DEFRA/legacy-prns/tree/7f9242599c6235c6bfdbcbb49ce1292332ebc6b6) |
| DEFRA/waste-obligations | [dd8ca744ea80](https://github.com/DEFRA/waste-obligations/tree/dd8ca744ea80572db1dc6fac94ddaa4d9fb0a006) |
| DEFRA/waste-obligations-frontend | [78c750e4fae8](https://github.com/DEFRA/waste-obligations-frontend/tree/78c750e4fae8228cc37f2ac912e045dbef0aa0a6) |
