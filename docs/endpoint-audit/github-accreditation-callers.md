# Accreditation: excluded PRD1 migration scope

**All 12 accreditation contracts have executable callers in epr-prn-rep-exp-facade. Eleven have frontend UI roots; the overview action stops at the facade API.**

**Scope decision: classify the 416 → 417 → 418 accreditation journey as not used in PRD1 and exclude these 12 contracts from the migration baseline.** This accepted assessment is supported by missing PRD1 settings for frontend 1416 and facade 1417. It is not an independently verified live-deployment result.

Backend EnableAccreditation remains true by default. Frontend ShowAccreditation and facade EnableAccreditation also gate the source chain. Their defaults are true, but production values and deployments are unverified. Reopen scope if production evidence contradicts the decision.

## Endpoint and root evidence

Reprocessor/exporter UI action → frontend service/client → facade API/service → backend HTTP client → this API.

### GET /api/v1/Accreditation/{organisationId}/{materialId}/{applicationTypeId}

EnsureAccreditation: start or resume an application. **UI root found.**

[UI controller invocation](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L86); [Frontend HTTP service](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI.App/Services/AccreditationService.cs#L21); [Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/AccreditationController.cs#L23); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/AccreditationService.cs#L16); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/AccreditationServiceClient.cs#L26); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L21).

### GET /api/v1/Accreditation/{accreditationId}

PrnTonnage and other application pages: load accreditation. **UI root found.**

[UI controller invocation](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L123); [Frontend HTTP service](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI.App/Services/AccreditationService.cs#L40); [Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/AccreditationController.cs#L36); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/AccreditationService.cs#L24); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/AccreditationServiceClient.cs#L33); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L22).

### POST /api/v1/Accreditation

PrnTonnage POST and other application steps: save accreditation. **UI root found.**

[UI controller invocation](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L161); [Frontend HTTP service](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI.App/Services/AccreditationService.cs#L60); [Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/AccreditationController.cs#L44); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/AccreditationService.cs#L29); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/AccreditationServiceClient.cs#L40); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L23).

### GET /api/v1/Accreditation/Files/{externalId}

FileDownload and DeleteUploadedFile: read file metadata. **UI root found.**

[UI controller invocation](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L710); [Frontend HTTP service](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI.App/Services/AccreditationService.cs#L109); [Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/AccreditationController.cs#L64); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/AccreditationService.cs#L41); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/AccreditationServiceClient.cs#L56); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L28).

### GET /api/v1/Accreditation/{accreditationId}/Files/{fileUploadTypeId}/{fileUploadStatusId?}

TaskList and SamplingAndInspectionPlan: list evidence files. **UI root found.**

[TaskList root](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L475); [UI controller invocation](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L1346); [Frontend HTTP service](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI.App/Services/AccreditationService.cs#L129); [Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/AccreditationController.cs#L73); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/AccreditationService.cs#L46); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/AccreditationServiceClient.cs#L62); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L29).

### POST /api/v1/Accreditation/{accreditationId}/Files

SamplingAndInspectionPlan: save completed upload metadata. **UI root found.**

[UI controller invocation](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L606); [Frontend HTTP service](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI.App/Services/AccreditationService.cs#L149); [Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/AccreditationController.cs#L81); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/AccreditationService.cs#L52); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/AccreditationServiceClient.cs#L69); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L30).

### DELETE /api/v1/Accreditation/{accreditationId}/Files/{fileId}

DeleteUploadedFile: remove uploaded evidence. **UI root found.**

[UI controller invocation](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L740); [Frontend HTTP service](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI.App/Services/AccreditationService.cs#L166); [Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/AccreditationController.cs#L89); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/AccreditationService.cs#L58); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/AccreditationServiceClient.cs#L76); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L31).

### GET /api/v1/Accreditation/accreditations/{organisationId:guid}/overview

Facade GET /api/v1/Accreditation/{organisationId}/overview; upstream root unresolved. **Upstream root unresolved; facade API entry point found.**

[Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/AccreditationController.cs#L98); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/AccreditationService.cs#L63); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/AccreditationServiceClient.cs#L83); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L32).

### GET /api/v1/AccreditationPRNIssueAuth/{accreditationId}

SelectAuthority and TaskList: show authorised PRN issuers. **UI root found.**

[UI controller invocation](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L181); [Frontend HTTP service](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI.App/Services/AccreditationService.cs#L75); [Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/AccreditationPrnIssueAuthController.cs#L20); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/AccreditationPrnIssueAuthService.cs#L12); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/AccreditationPrnIssueAuthServiceClient.cs#L22); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L24).

### POST /api/v1/AccreditationPRNIssueAuth/{accreditationId}

SelectAuthority POST: save authorised PRN issuers. **UI root found.**

[UI controller invocation](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L236); [Frontend HTTP service](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI.App/Services/AccreditationService.cs#L95); [Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/AccreditationPrnIssueAuthController.cs#L34); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/AccreditationPrnIssueAuthService.cs#L17); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/AccreditationPrnIssueAuthServiceClient.cs#L29); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L25).

### GET /api/v1/OverseasAccreditationSite/{accreditationId}

TaskList → GetEvidenceOfEquivalentStandardsStatus: assess overseas-site progress. **UI root found.**

[TaskList root](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L489); [UI controller invocation](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L1321); [Frontend HTTP service](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI.App/Services/AccreditationService.cs#L221); [Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/OverseasAccreditationSiteController.cs#L20); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/OverseasAccreditationSiteService.cs#L10); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/OverseasAccreditationSiteServiceClient.cs#L22); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L26).

### POST /api/v1/OverseasAccreditationSite/{accreditationId}

EvidenceOfEquivalentStandardsCheckSiteFulfillsConditions POST: save site check. **UI root found.**

[UI controller invocation](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L1079); [Frontend HTTP service](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI.App/Services/AccreditationService.cs#L242); [Facade API invocation](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/OverseasAccreditationSiteController.cs#L34); [Facade service](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Services/Accreditation/OverseasAccreditationSiteService.cs#L15); [Backend HTTP client](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.App/Clients/Accreditation/OverseasAccreditationSiteServiceClient.cs#L29); [Backend route template](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L27).

## Gate and deployment evidence

- [Frontend gate](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Controllers/AccreditationController.cs#L17)
- [Frontend default](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/appsettings.json#L89)
- [Facade gate](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Controllers/Accreditation/AccreditationController.cs#L13)
- [Facade default](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/appsettings.json#L11)
- [Frontend client registration](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/src/Epr.Reprocessor.Exporter.UI/Extensions/ServiceProviderExtension.cs#L147)
- [Facade client registration](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/src/Epr.Reprocessor.Exporter.Facade.Api/Extensions/HttpClientServiceCollectionExtension.cs#L49)
- [Frontend intended PRD1 resource](https://github.com/DEFRA/epr-prn-rep-exp-frontend/blob/7d38a8aea17a077bb7cdf9025cca7443f44be8a2/pipelines/vars/prod.yaml#L6)
- [Facade intended PRD1 resource](https://github.com/DEFRA/epr-prn-rep-exp-facade/blob/e5783e6594fc9e018fa9d2416c842417d72bbd75/pipelines/vars/prod.yaml#L6)

The facade overview path is `/api/v1/Accreditation/{organisationId}/overview`; its downstream path includes the extra `accreditations` segment. No upstream UI caller was identified for this facade path.

## Other environments

The supplied configuration snapshot has shared DEV, DEV8, DEV15 and TST1 files for 416/417. DEV8, DEV15 and TST1 explicitly disable both frontend ShowAccreditation and facade EnableAccreditation. No PRD1 or PRE settings files for these resources were present. Deployment YAML naming PRD resources establishes intended resource names, not deployed state.

## Coverage

- GitHub discovery was followed by direct inspection of the two main-branch source snapshots. All code references are immutable commit links.
- Broad discovery queries reached the result limit. Search is not exhaustive across all repositories, branches or historical releases; negative results are not proof of absence.
- All 12 backend contracts have executable facade callers. Eleven have UI roots; the overview chain stops at the facade API.
- Frontend v1.0 spelling and route casing map to API version 1.0 and the same ASP.NET routes.
- The frontend and facade have additional feature gates. Source defaults are true; PRD1 deployment and effective values have not been read.
- Configuration files exist for shared DEV, DEV8, DEV15 and TST1 resources 416/417, but not PRD1 or PRE in the supplied snapshot. Deployment YAML names are not evidence of deployment.
- A clear-down-database QA call is also present but has no corresponding route in the audited backend; it is outside these 12 contracts and was not executed.

| Inspected repository | Commit |
|---|---|
| DEFRA/epr-prn-rep-exp-frontend | [7d38a8aea17a](https://github.com/DEFRA/epr-prn-rep-exp-frontend/tree/7d38a8aea17a077bb7cdf9025cca7443f44be8a2) |
| DEFRA/epr-prn-rep-exp-facade | [e5783e6594fc](https://github.com/DEFRA/epr-prn-rep-exp-facade/tree/e5783e6594fc9e018fa9d2416c842417d72bbd75) |

Search queries and result limits are recorded in github-accreditation-callers.json. Broad searches were used for discovery; the endpoint conclusions come from direct source inspection.
