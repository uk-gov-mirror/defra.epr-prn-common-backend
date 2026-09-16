# PRD1 API migration assessment

**Retain 12 PRN API actions and equivalent health-probe behaviour. Exclude 12 accreditation actions under the accepted not-used-in-PRD1 scope decision, and 50 actions explicitly disabled in PRD1 configuration.**

This distinguishes migration scope from feature availability: the backend configuration enables 24 actions, but only 12 are retained. Runtime deployment and production traffic have not been independently verified.

Audited: 2026-09-16. API source: `c1c250c08da94663a64dc84eb7b18c23ad9b9c5c`. Configuration snapshot: `epr-app-config-settings@4e1bdfe779ca771434f7675e678f878044c2c980` (Azure DevOps checkout; repository-relative references below).

## Decision groups

| Group | API actions | Basis | Migration decision |
|---|---|---|---|
| PRN | 12 | Ungated; executable caller chains and PRD1 caller configuration found | Retain |
| Accreditation | 12 | Backend default on; frontend/facade classified as not used in PRD1 | Exclude under scope decision |
| Reprocessor/exporter | 50 | PRD1 ReprocessorExporter=false | Exclude |
| Health | Separate operational route | Anonymous health mapping | Retain equivalent behaviour |

Evidence: [PRN caller chains](local-callers.md), [accreditation caller chains](github-accreditation-callers.md), [structured inventory](inventory.json).

## Group 1 — PRN endpoints: retain in the migration baseline

Retain 12 PRN contracts. Executable callers and root journeys/jobs are mapped; PRD1 caller configuration supports the dependency. Live traffic remains unverified.

| Method | Full route | Controller action |
|---|---|---|
| GET | `/api/v1/prn/{prnId}` | [PrnController.GetPrn](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnController.cs#L39) |
| GET | `/api/v1/prn/search/{page?}/{search?}/{filterBy?}/{sortBy?}` | [PrnController.GetSearchPrns](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnController.cs#L74) |
| GET | `/api/v1/prn/organisation` | [PrnController.GetAllPrnByOrganisationId](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnController.cs#L118) |
| GET | `/api/v1/prn/ModifiedPrnsbyDate` | [PrnController.GetModifiedPrnsbyDate](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnController.cs#L144) |
| GET | `/api/v1/prn/syncstatuses` | [PrnController.GetSyncStatuses](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnController.cs#L164) |
| GET | `/api/v1/prn/obligationcalculation/{year}` | [PrnController.GetObligationCalculations](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnController.cs#L185) |
| POST | `/api/v1/prn/status` | [PrnController.UpdatePrnStatus](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnController.cs#L238) |
| POST | `/api/v1/prn/organisation/{submitterId}/calculate` | [PrnController.CalculateAsync](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnController.cs#L301) |
| POST | `/api/v1/prn/prn-details` | [PrnController.SaveAsync](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnController.cs#L416) |
| POST | `/api/v1/prn/updatesyncstatus` | [PrnController.PeprToNpwdSyncedPrns](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnController.cs#L442) |
| POST | `/api/v2/prn` | [PrnControllerV2.SaveAsync](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnControllerV2.cs#L27) |
| GET | `/api/v2/prn/modified-prns` | [PrnControllerV2.GetModifiedPrns](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/PrnControllerV2.cs#L52) |

## Group 2 — Accreditation: not used in PRD1; exclude from migration baseline

Accepted migration scope decision, supported by absent PRD1 settings for frontend 1416 and facade 1417 in the configuration snapshot.

Known source chain: frontend 416 → facade 417 → backend 418. All 12 have facade callers; 11 have UI roots. Missing PRD1 settings support the agreed exclusion decision, but do not prove technical unreachability or exclude unknown consumers. Reopen scope if production evidence contradicts the decision.

| Method | Full route | Controller action |
|---|---|---|
| GET | `/api/v1/Accreditation/{organisationId}/{materialId}/{applicationTypeId}` | [AccreditationController.Get](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/AccreditationController.cs#L27) |
| GET | `/api/v1/Accreditation/{accreditationId}` | [AccreditationController.Get](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/AccreditationController.cs#L42) |
| POST | `/api/v1/Accreditation` | [AccreditationController.Post](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/AccreditationController.cs#L57) |
| GET | `/api/v1/Accreditation/Files/{externalId}` | [AccreditationController.GetFileUpload](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/AccreditationController.cs#L77) |
| GET | `/api/v1/Accreditation/{accreditationId}/Files/{fileUploadTypeId}/{fileUploadStatusId?}` | [AccreditationController.GetFileUploads](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/AccreditationController.cs#L86) |
| POST | `/api/v1/Accreditation/{accreditationId}/Files` | [AccreditationController.UpsertFileUpload](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/AccreditationController.cs#L105) |
| DELETE | `/api/v1/Accreditation/{accreditationId}/Files/{fileId}` | [AccreditationController.DeleteFileUpload](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/AccreditationController.cs#L125) |
| GET | `/api/v1/Accreditation/accreditations/{organisationId:guid}/overview` | [AccreditationController.GetAccreditationsOverviewForOrgId](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/AccreditationController.cs#L134) |
| GET | `/api/v1/AccreditationPRNIssueAuth/{accreditationId}` | [AccreditationPrnIssueAuthController.GetByAccreditationId](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/AccreditationPrnIssueAuthController.cs#L16) |
| POST | `/api/v1/AccreditationPRNIssueAuth/{accreditationId}` | [AccreditationPrnIssueAuthController.Post](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/AccreditationPrnIssueAuthController.cs#L31) |
| GET | `/api/v1/OverseasAccreditationSite/{accreditationId}` | [OverseasAccreditationSiteController.GetAllByAccreditationId](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/OverseasAccreditationSiteController.cs#L15) |
| POST | `/api/v1/OverseasAccreditationSite/{accreditationId}` | [OverseasAccreditationSiteController.Post](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/OverseasAccreditationSiteController.cs#L30) |

## Group 3 — Reprocessor/exporter endpoints: disabled in PRD1 configuration

These 50 actions are blocked by the explicit PRD1 ReprocessorExporter=false setting. Exclude them from the initial migration scope for enabled production functionality, subject to confirming deployed configuration and any planned enablement. This is not evidence that the code can be deleted or that these endpoints were never used.

Configuration evidence: `epr-app-config-settings/prd1/PRDRWDWEBWA1418.json:23` at the configuration snapshot above.

| Method | Full route | Controller action |
|---|---|---|
| POST | `/api/v1/AccreditationTaskStatus` | [AccreditationTaskStatusController.UpdateAccreditationTaskStatus](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Accreditation/AccreditationTaskStatusController.cs#L23) |
| GET | `/api/v1/registrations/{registrationId}/carrier-broker-dealer-permits` | [CarrierBrokerDealerPermitsController.GetCarrierBrokerDealerPermits](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/ExporterJourney/CarrierBrokerDealerPermitsController.cs#L20) |
| POST | `/api/v1/registrations/{registrationId}/carrier-broker-dealer-permits` | [CarrierBrokerDealerPermitsController.CreateCarrierBrokerDealerPermits](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/ExporterJourney/CarrierBrokerDealerPermitsController.cs#L38) |
| PUT | `/api/v1/registrations/{registrationId}/carrier-broker-dealer-permits` | [CarrierBrokerDealerPermitsController.UpdateCarrierBrokerDealerPermits](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/ExporterJourney/CarrierBrokerDealerPermitsController.cs#L74) |
| GET | `/api/v1/countries` | [CountriesController.GetCountries](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Lookups/CountriesController.cs#L17) |
| GET | `/api/v1/materials` | [MaterialController.GetAllMaterials](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/MaterialController.cs#L19) |
| GET | `/api/v1/registrations/{applicationTypeId:int}/organisations/{organisationId:guid}` | [RegistrationController.GetRegistrationByOrganisation](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationController.cs#L26) |
| POST | `/api/v1/registrations` | [RegistrationController.CreateRegistration](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationController.cs#L60) |
| POST | `/api/v1/registrations/{registrationId:guid}/update` | [RegistrationController.UpdateRegistration](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationController.cs#L81) |
| POST | `/api/v1/registrations/{registrationId:guid}/taskStatus` | [RegistrationController.UpdateRegistrationTaskStatus](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationController.cs#L123) |
| POST | `/api/v1/registrations/{registrationId:guid}/applicationTaskStatus` | [RegistrationController.UpdateApplicationRegistrationTaskStatus](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationController.cs#L146) |
| POST | `/api/v1/registrations/{registrationId:guid}/siteAddress` | [RegistrationController.UpdateSiteAddress](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationController.cs#L170) |
| GET | `/api/v1/registrations/{organisationId:guid}/overview` | [RegistrationController.GetRegistrationsOverviewForOrgId](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationController.cs#L195) |
| GET | `/api/v1/registrations/{registrationId:guid}/materials` | [RegistrationMaterialController.GetAllRegistrationMaterials](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L32) |
| POST | `/api/v1/registrationMaterials/create` | [RegistrationMaterialController.CreateRegistrationMaterial](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L52) |
| POST | `/api/v1/registrationMaterials/{Id:guid}/createExemptionReferences` | [RegistrationMaterialController.CreateExemptionReferences](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L72) |
| POST | `/api/v1/registrationMaterials/{id:Guid}/permits` | [RegistrationMaterialController.UpdateRegistrationMaterialPermits](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L91) |
| POST | `/api/v1/registrationMaterials/{id:Guid}/permitCapacity` | [RegistrationMaterialController.UpdateRegistrationMaterialPermitCapacity](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L116) |
| GET | `/api/v1/registrationMaterials/permitTypes` | [RegistrationMaterialController.GetMaterialsPermitTypes](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L141) |
| DELETE | `/api/v1/registrationMaterials/{registrationMaterialId:guid}` | [RegistrationMaterialController.DeleteRegistrationMaterial](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L158) |
| POST | `/api/v1/registrationMaterials/UpdateIsMaterialRegistered` | [RegistrationMaterialController.UpdateIsMaterialRegisteredAsync](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L179) |
| POST | `/api/v1/registrationMaterials/{id:Guid}/contact` | [RegistrationMaterialController.UpsertRegistrationMaterialContactAsync](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L195) |
| POST | `/api/v1/registrationMaterials/{registrationMaterialId:Guid}/registrationReprocessingDetails` | [RegistrationMaterialController.UpsertRegistrationReprocessingDetailsAsync](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L223) |
| POST | `/api/v1/registrationMaterials/{registrationMaterialId:guid}/overseasReprocessingSites` | [RegistrationMaterialController.SaveOverseasReprocessingSites](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L247) |
| PUT | `/api/v1/registrationMaterials/{registrationMaterialId:guid}/max-weight` | [RegistrationMaterialController.UpdateMaximumWeight](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L275) |
| POST | `/api/v1/registrationMaterials/{registrationMaterialId:Guid}/materialNotReprocessingReason` | [RegistrationMaterialController.UpdateMaterialNotReprocessingReasonAsync](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L296) |
| GET | `/api/v1/registrationMaterials/{registrationMaterialId:guid}/overseasMaterialReprocessingSites` | [RegistrationMaterialController.GetOverseasMaterialReprocessingSites](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L323) |
| POST | `/api/v1/registrationMaterials/{registrationMaterialId:guid}/saveInterimSites` | [RegistrationMaterialController.SaveInterimSites](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/RegistrationMaterialController.cs#L343) |
| GET | `/api/v1/registrations/{id}/accreditations` | [AccreditationController.GetRegistrationByIdWithAccreditationsAsync](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/AccreditationMaterialController.cs#L23) |
| GET | `/api/v1/accreditations/{Id}/samplingPlan` | [AccreditationController.GetSamplingPlan](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/AccreditationMaterialController.cs#L41) |
| GET | `/api/v1/accreditations/{Id}/paymentFees` | [AccreditationController.GetRegistrationAccreditationPaymentFeeDetailsById](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/AccreditationMaterialController.cs#L59) |
| GET | `/api/v1/accreditations/{Id}/businessPlan` | [AccreditationController.GetRegistrationAccreditationBusinessPlanById](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/AccreditationMaterialController.cs#L77) |
| POST | `/api/v1/accreditations/{Id}/markAsDulyMade` | [AccreditationController.RegulatorAccreditationMarkAsDulyMade](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/AccreditationMaterialController.cs#L97) |
| GET | `/api/v1/registrations/{Id}` | [RegistrationMaterialController.GetRegistrationOverviewDetailById](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L24) |
| GET | `/api/v1/registrationMaterials/{Id}` | [RegistrationMaterialController.GetMaterialDetailById](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L42) |
| GET | `/api/v1/registrationMaterials/{Id}/wasteLicences` | [RegistrationMaterialController.GetWasteLicences](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L60) |
| GET | `/api/v1/registrationMaterials/{Id}/reprocessingIO` | [RegistrationMaterialController.GetReprocessingIO](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L71) |
| GET | `/api/v1/registrationMaterials/{Id}/samplingPlan` | [RegistrationMaterialController.GetSamplingPlan](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L82) |
| GET | `/api/v1/registrations/{Id}/siteAddress` | [RegistrationMaterialController.GetRegistrationSiteAddressById](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L93) |
| GET | `/api/v1/registrations/{Id}/wasteCarrier` | [RegistrationMaterialController.GetWasteCarrierDetailsByRegistrationId](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L111) |
| GET | `/api/v1/registrations/{Id}/authorisedMaterials` | [RegistrationMaterialController.GetAuthorisedMaterial](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L129) |
| GET | `/api/v1/registrationMaterials/{Id}/paymentFees` | [RegistrationMaterialController.GetRegistrationMaterialpaymentFeesById](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L146) |
| GET | `/api/v1/registrationMaterials/{Id}/RegistrationAccreditationReference` | [RegistrationMaterialController.GetRegistrationAccreditationReference](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L163) |
| POST | `/api/v1/registrationMaterials/{Id}/outcome` | [RegistrationMaterialController.UpdateRegistrationOutcome](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L184) |
| POST | `/api/v1/registrationMaterials/{Id}/markAsDulyMade` | [RegistrationMaterialController.RegistrationMaterialsMarkAsDulyMade](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegistrationMaterialController.cs#L207) |
| POST | `/api/v1/RegulatorAccreditationTaskStatus` | [RegulatorAccreditationTaskStatusController.UpdateAccreditationTaskStatus](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegulatorAccreditationTaskStatusController.cs#L21) |
| POST | `/api/v1/RegulatorApplicationTaskStatus` | [RegulatorApplicationTaskStatusController.UpdateRegistrationTaskStatus](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegulatorApplicationTaskStatusController.cs#L22) |
| POST | `/api/v1/RegulatorApplicationTaskStatus/{Id}/queryNote` | [RegulatorApplicationTaskStatusController.AddApplicationTaskQueryNote](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegulatorApplicationTaskStatusController.cs#L36) |
| POST | `/api/v1/RegulatorRegistrationTaskStatus` | [RegulatorRegistrationTaskStatusController.UpdateRegistrationTaskStatus](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegulatorRegistrationTaskStatusController.cs#L22) |
| POST | `/api/v1/RegulatorRegistrationTaskStatus/{Id}/queryNote` | [RegulatorRegistrationTaskStatusController.RegistrationTaskQueryNote](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Controllers/Regulator/RegulatorRegistrationTaskStatusController.cs#L36) |

## Operational endpoints

Retain `/admin/health`, or migrate its infrastructure probe configuration together with an equivalent replacement. It has no HTTP method constraint, allows anonymous access and includes a SQL Server check.

| Method | Route | Availability |
|---|---|---|
| ANY (no method constraint) | [`/admin/health`](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Startup.cs#L113) | All environments; anonymous; SQL Server health check |
| GET | [`/swagger/{documentName}/swagger.json`](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Startup.cs#L103) | Development only; default Swagger route; documents depend on configuration |
| GET | [`/swagger, /swagger/index.html, /swagger/* (UI assets)`](https://github.com/DEFRA/epr-prn-common-backend/blob/c1c250c08da94663a64dc84eb7b18c23ad9b9c5c/src/EPR.PRN.Backend.API/Startup.cs#L104) | Development only; default Swagger UI prefix |

## Remaining production evidence

- Confirm deployed versions and effective configuration for retained callers.
- Correlate requests with upstream dependency telemetry over a representative business cycle, including March for NPWD jobs.
- Reconcile the legacy-prns raw-data contract mismatch before relying on that migration job.
- Revisit excluded accreditation only if production evidence or migration requirements contradict the agreed scope.

## Other environments — supporting context

Counts below describe backend feature availability, not confirmed traffic or migration scope. Explicit settings override the audited API defaults. Both defaults are true. Full source paths and values are in inventory.json.

| Environment | ReprocessorExporter | EnableAccreditation | Enabled / disabled actions |
|---|---|---|---|
| DEV1 | On (explicit) | On (development variable) | 74 / 0 |
| DEV2 | On (explicit) | On (development variable) | 74 / 0 |
| DEV3 | On (explicit) | On (development variable) | 74 / 0 |
| DEV4 | On (explicit) | On (development variable) | 74 / 0 |
| DEV6 | On (explicit) | On (development variable) | 74 / 0 |
| DEV7 | On (explicit) | On (development variable) | 74 / 0 |
| DEV9 | On (explicit) | On (development variable) | 74 / 0 |
| DEV14 | On (explicit) | On (development variable) | 74 / 0 |
| DEV16 | On (explicit) | On (development variable) | 74 / 0 |
| DEV8 | On (explicit) | On (API default) | 74 / 0 |
| DEV15 | On (explicit) | On (API default) | 74 / 0 |
| TST1 | Off (explicit) | On (API default) | 24 / 50 |
| PRE1 | Off (explicit) | On (API default) | 24 / 50 |
| PRE2 | Off (explicit) | Off (explicit) | 12 / 62 |

## Inventory validation

74 unique method/route pairs across 16 controllers: 72 v1 and 2 v2. Each appears in exactly one API decision group. No application or database actions were executed.

- Source/configuration assessment; deployed images, effective runtime settings and production telemetry have not been independently verified.
- Canonical v1/v2 paths represent API versions 1.0/2.0. Original constraints, casing and optional segments are retained. Query parameters are not part of endpoint identity.
- Every declared HTTP action is included regardless of feature state. Gates are controller-level in this snapshot.
- API source defaults both flags to true. Checked-in environment settings are combined with those defaults; a deployed image may differ.
- Swagger can omit feature-gated routes. Inventory was checked against source declarations rather than a single Swagger output.
- The accreditation overview route includes both Accreditation and accreditations segments, as declared in source.
- Exception re-execution targets /error and /error-development have no corresponding endpoint declaration. NonAction helper methods are excluded.
- Swagger routes are development-only middleware families. Health is operational and counted separately from API actions.
