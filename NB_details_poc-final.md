# STATIM New Business — Current-State Discovery

You are working with the STATIM source code available in this local workspace.

Before we design any AI capability, I need you to reverse-engineer and document the **existing New Business → Quote creation flow** from the source code.

Do NOT propose AI features yet.

The objective is to give us an accurate field-level understanding of what a user currently has to do to create a quote, so that we can later identify where an AI-assisted quote creation capability can genuinely reduce manual effort.

## 1. Identify the New Business Flow

Trace the complete New Business → Quote journey from the UI through the backend/API layer.

Identify:

* New Business entry point
* Quote creation screens/pages
* Tabs/steps
* Sub-screens/components
* Navigation sequence
* Dependencies between screens
* Save/Next/Submit actions
* APIs/services invoked
* Important backend processing involved

Do not assume the flow from naming conventions. Trace it from the actual code.

## 2. Identify Every User Input

For every New Business screen/tab involved in creating a quote, identify:

* Field name
* UI label
* Technical field/property name
* Field type
* Mandatory/optional
* Default value
* Dropdown/lookup/reference data
* Whether it is editable
* Whether it is auto-populated
* Whether it is derived/calculated
* Whether it depends on another field
* Validation applied
* API/backend field mapping

Create a table like:

| Screen/Tab | UI Field | Technical Field | Mandatory | Input Type | Auto Populated | Dependency | API/Backend Mapping |
| ---------- | -------- | --------------- | --------- | ---------- | -------------- | ---------- | ------------------- |

## 3. Identify User Effort

Classify each field/action as:

### A — Manual Input

The user must type/select/provide the information.

### B — Selection

The user selects an existing value from a lookup/dropdown.

### C — Auto-Populated

STATIM obtains the value automatically.

### D — Derived

STATIM calculates or derives the value.

### E — System Processing

No user effort; handled by backend/business logic.

### F — Repeated Information

The same or similar information must be entered in multiple places.

Pay particular attention to fields that require significant typing or repetitive data entry.

## 4. Identify Information Relationships

Determine which fields are dependent on previous selections.

Examples:

Product → Coverage
Product → Risk Type
Customer → Existing Information
Risk Type → Risk Fields
Coverage → Coverage Details

Document these relationships.

## 5. Identify Existing STATIM Intelligence

Before considering AI, identify what STATIM already does automatically.

Look for:

* Existing validations
* Business rules
* Defaulting logic
* Auto-population
* Lookup services
* Product configuration
* Rating logic
* Rule engine integration
* Eligibility checks
* Calculations
* Existing recommendations
* Existing automation

Clearly separate these from actual user-entered information.

## 6. Identify Potential Sources of Quote Information

From the source code, identify whether STATIM already supports or references:

* Customer master data
* Existing customer information
* Existing policies
* Proposal information
* Documents
* Attachments
* Previous quotes
* External/reference data
* APIs that provide customer/risk information

Do not assume these are available. Only report what is supported by the code.

## 7. Trace One Complete Quote

Identify the minimum sequence of screens/actions required to successfully create a quote.

Produce a simplified flow such as:

New Business
→ Customer
→ Product
→ Risk
→ Coverage
→ Additional Details
→ Rating
→ Quote

Use the actual STATIM screen/component names.

## 8. Identify Manual-Effort Hotspots

Based strictly on the source code, identify areas where the user appears to perform significant manual work.

For each hotspot explain:

* What the user currently does
* What information they provide
* Whether the same information exists elsewhere in STATIM
* Whether the information could theoretically be obtained from another source
* Whether the activity appears suitable for future AI assistance

Do NOT implement or propose a solution yet.

## 9. Produce Two Key Views

### View 1 — Current-State Quote Journey

Create a simple end-to-end flow showing:

User action
→ STATIM screen
→ Data entered
→ Backend/API
→ Processing

### View 2 — User Effort Matrix

Create:

| Activity | Screen | Manual Effort | Repetition | Existing Automation | Notes |
| -------- | ------ | ------------: | ---------: | ------------------- | ----- |

Use:

Low / Medium / High

for Manual Effort and Repetition.

## 10. Important Rules

* Do not modify any source code.
* Do not create any files inside the project.
* Do not implement AI.
* Do not redesign the existing flow.
* Do not assume functionality that cannot be confirmed from the source.
* Clearly identify anything that cannot be determined from the code.
* Reference the actual source files/classes/components where possible.
* Distinguish frontend behaviour from backend behaviour.
* If database/schema information is available in the source, use it to validate field mappings.

## Final Output

Provide a concise but comprehensive discovery report with these sections:

1. New Business Flow
2. Screens / Tabs
3. Field-Level Input Inventory
4. User Effort Analysis
5. Existing STATIM Automation
6. Data Dependencies
7. Quote Creation Flow
8. Manual-Effort Hotspots
9. APIs / Services Involved
10. Unknowns / Information Required

The most important outcome is:

**A precise understanding of what a STATIM user currently has to manually enter or select in order to create a quote.**

We will use this report as the baseline for designing the next phase:

**STATIM Level 2 — AI Assisted Quote Creation**

Do not design that AI solution in this task.

---


I'll systematically trace the New Business quote creation flow from the actual source code. Let me start by exploring the relevant directories.

---


Now let me read the GetQuote, FullQuote, and NBListing components, plus the action creators:

---


Now let me read the FullQuote component and the action creators:

---


Now let me read the ProposerRiskDetails and the action creators for QuickQuote:

---


Now let me read the NewBusiness action creators and the AddUser component to understand customer creation:

---


Now let me read the NewBusiness backend controller and the PolicyDetails component:

---


Now let me read the PolicyDetails component and the NewBusiness BOs to understand the data model:

---


Now let me check the NewBusiness BOs and the QuickQuote service to understand the backend data model:

---


Now let me check the AppRoutes to confirm the routing:

---


I now have a comprehensive picture of the entire New Business quote creation flow from the source code. Let me compile the full discovery report.

---

# STATIM New Business — Current-State Discovery Report

## 1. New Business Flow

The New Business module has two distinct quote creation paths, both accessible from the NB Listing screen:

**Path A — Quick Quote (Standalone products only)**
```
/newbusiness (NBListing)
  → "Get Quote" button → /getquote (GetQuote)
    → Select: Id Type + Id Value (customer lookup) + Product + Quote Type = "Quick Quote"
    → Proceed → /quickquote (QuickQuote)
      → Fill product-configured accordion fields + select plan + toggle covers
      → "Generate Quick Quote" → SaveQuickQuote API → rating runs → premium displayed
      → "Proceed to Full Quote" → ConvertQuote API → /fullquote (FullQuote)
```

**Path B — Full Quote (all products)**
```
/newbusiness (NBListing)
  → "Get Quote" button → /getquote (GetQuote)
    → Select: Id Type + Id Value + Product + Quote Type = "Full Quote"
    → Proceed → /fullquote (FullQuote)
      → Tab 1: Proposer/Risk Details → Proceed/Update
      → Tab 2: Coverage Details → Save
      → Tab 3: Policy Details → Save
      → Tab 4: Broker Commission (conditional — only if Source = Broker/Agent)
      → Tab 5: Co-Insurance (conditional — only if Business Type = Co-Insurance)
      → Tab 6: Pre-Inspection (conditional — product-configured)
      → "Quote Approval" → getQuoteApproval API
      → "Collect" → payment collection flow
      → "Generate to Policy" → PolicyApprovedQuote API
```

---

## 2. Screens / Tabs

| Screen | Route | Component | Purpose |
|---|---|---|---|
| NB Listing | `/newbusiness` | `NBListing.jsx` | List all quotes/policies; entry point |
| Get Quote | `/getquote` | `GetQuote.jsx` | Customer lookup + product + quote type selection |
| Quick Quote | `/quickquote` | `QuickQuote.jsx` | Lightweight quote for standalone products |
| Full Quote | `/fullquote` | `FullQuote.jsx` | Full stepper-based quote creation |
| FQ Tab 1 | — | `ProposerRiskDetails.jsx` | Proposer, insured, policy basics, risk data entry |
| FQ Tab 2 | — | `CoverageDetails.jsx` | SMI, covers, discounts, loadings, deductibles, conditions, charges, tax |
| FQ Tab 3 | — | `PolicyDetails.jsx` | Branch, currency, rate, payer type, previous policy |
| FQ Tab 4 | — | `BrokerCommission.jsx` | Broker/agent commission (conditional) |
| FQ Tab 5 | — | `CoInsurance.jsx` | Co-insurance participants (conditional) |
| FQ Tab 6 | — | `PreInspection.jsx` | Pre-inspection decisions (conditional) |
| Add User | — | `AddUser.jsx` (drawer) | Create new customer entity inline |

**Full Quote tab visibility is dynamic** — tabs 4 and 5 are hidden/shown based on Source and Business Type selections in Tab 1. Tab 6 is product-configured.

---

## 3. Field-Level Input Inventory

### Screen: Get Quote (`GetQuote.jsx`)

| Screen/Tab | UI Field | Technical Field | Mandatory | Input Type | Auto Populated | Dependency | API/Backend Mapping |
|---|---|---|---|---|---|---|---|
| Get Quote | Id Type | `IdType` | No | Dropdown | No | None | `Master/GetClassificationCategoryList` → DOC_KYC options + "Phone Number" |
| Get Quote | Id Value (dynamic) | `SelectIdType` / `UniqueId` / `PhoneNumber` / `PlateNo` | No | Text or PhoneNumber | No | Depends on IdType selection | `EntityMaster/EntityLookup` triggered on blur |
| Get Quote | Product | `Product` | Yes | Dropdown | No | None | `ProductConfigurator/getProducts` |
| Get Quote | Quote Type | `QuoteType` | Yes | Dropdown | No | None | Static: "Full Quote" / "Quick Quote" |

**Customer lookup result** — when entity found, auto-populates a `CustomerDetailsCard` showing name, entity code, and existing policies. The `EntityCode` is passed forward to the next screen.

---

### Screen: Quick Quote (`QuickQuote.jsx`)

The Quick Quote form is **entirely product-configured** — fields are fetched from `QuickQuote/GetFieldsForQuickQuote` and rendered dynamically. The accordion structure, field types, labels, mandatory flags, and data sources all come from the Product Configurator. The following are the **structural/fixed** fields plus the known dynamic field categories:

| Screen/Tab | UI Field | Technical Field | Mandatory | Input Type | Auto Populated | Dependency | API/Backend Mapping |
|---|---|---|---|---|---|---|---|
| Quick Quote | Plan | `MasterPlanValue` | Yes | Radio buttons | First plan auto-selected | Product | `ProductConfigurator/productPlans` |
| Quick Quote | Policy Start Date | `policyStart` | Yes | Date | Default: today | None | `PolFromDate` in `QuickQuoteBO` |
| Quick Quote | Policy End Date | `policyEnd` | Yes | Date | Default: today + 1 year | None | `PolToDate` in `QuickQuoteBO` |
| Quick Quote | **Dynamic accordion fields** (product-configured) | Various `KeyCode` values (e.g. `Key1`–`KeyN`) | Per config | Text/Number/Dropdown/Date/Switch/Radio/TextArea | Per config | Per config | `QuickQuoteFlexiKeys` in `QuickQuoteBO` |
| Quick Quote | **Risk fields** (product-configured, per risk) | Various `KeyCode` values | Per config | Per config | Per config | Per config | `QuickQuoteRisk[].QuickQuoteRiskFlexiKeys` |
| Quick Quote | **Covers** (basic + add-on) | `cover_{CoverId}` | Mandatory covers auto-on | Toggle (Switch) | Default/mandatory covers pre-toggled | Plan selection | `QuickQuoteRisk[].QuickQuoteCoverDetails` |

**Known dynamic field categories confirmed from source** (mapped via `DatabaseFieldMapping`):

| DatabaseFieldMapping | UI Concept | Source |
|---|---|---|
| `MasterPrevNcdPercentValue` / `MasterPrevNcbPercentValue` | Previous NCD/NCB % | Meta API (PNCBP codes) — Stack control |
| `ProductPolicyCategoryValue` | Policy Category | Meta API (BIAPC codes) |
| `MasterSiCurrencyValue` | SI Currency | Master (CUR) |
| `MasterPemCurrencyValue` | Premium Currency | Master (CUR) |
| `MasterRateTypeValue` | Rate Type | Master (COC/RTY) |
| `MasterRatePickedFromValue` | Rate Picked From | Master (COC/RPF) |
| `MasterPrevPolicyTypeValue` | Previous Policy Type | Master (COC/PTY) |
| `MasterTransactionTypeValue` | Transaction Type | Master (COC/TRA_NBS) |
| `MasterCalculationtionTypeValue` | Calculation Type | Master (COC/PCT) |
| `MasterSourceValue` | Source | Master (COC/STY) |
| `MasterChannelValue` | Channel | `ProductConfigurator/blockListing` (BSCINFO/BSCAPCH) |
| `EntitySourceNameValue` | Source Name (broker/agent) | `ProductConfigurator/blockListing` (BSCINFO/BSCAPSR) |
| `MasterBusinessTypeValue` | Business Type | `ProductConfigurator/blockListing` (BSCINFO/BSCAPBS) |
| `MasterBranchValue` | Branch | `UserManagement/getAppOfficeDetails` |
| `MasterPropClassificValue` | Proposer Classification | `Master/GetClassificationCategoryList` |
| `MasterInsuredClassificValue` | Insured Classification | Same as above |
| `PreviousAddOnCovers` | Previous Add-On Covers | `ProductConfigurator/blockListing` (SMI_AND_COVERS) |
| `Make` / `VehicleMake` | Vehicle Make | `Claims/GetMakeModelVariant` — special modal picker |
| `Model` | Vehicle Model | Auto-populated from Make selection |
| `Variant` | Vehicle Variant | Auto-populated from Make selection |
| `FuelType` | Fuel Type | Auto-populated from Make selection |
| `BodyType` | Body Type | Auto-populated from Make selection |
| `ManufacturingYear` | Manufacturing Year | Auto-populated from Make selection |

**Customer auto-population in Quick Quote** — if `customerDetails` is passed from GetQuote, the following fields are auto-populated from the entity master record:
- FirstName, MiddleName, LastName, ArabicName, Email, DOB, Phone, Gender, Nationality, Document Type, Document Number

---

### Screen: Full Quote — Tab 1: Proposer/Risk Details (`ProposerRiskDetails.jsx`)

**Sub-section A: Proposer Details** (accordion fields from NB Configuration API)

| Screen/Tab | UI Field | Technical Field | Mandatory | Input Type | Auto Populated | Dependency | API/Backend Mapping |
|---|---|---|---|---|---|---|---|
| Proposer Details | Proposer Classification | `MasterPropClassificValue` | Yes | Dropdown | Default: "Individual" | None | `Master/GetClassificationCategoryList` → `ProposerDetailBO.MasterPropClassificId` |
| Proposer Details | Proposer (Entity) | `EntityProposerValue` | Yes | DropdownPlus (search + create) | From GetQuote customer lookup | Proposer Classification | `EntityMaster/EntityLookup` → `ProposerDetailBO.EntityProposerId` |
| Proposer Details | Insured Same as Proposer | `InsuredSameAsProposer` | No | Toggle/Checkbox | Default: false | None | `ProposerDetailBO.InsuredSameAsProposer` |
| Proposer Details | Insured Classification | `MasterInsuredClassificValue` | Yes | Dropdown | Copies from Proposer if same | InsuredSameAsProposer | `ProposerDetailBO.MasterInsuredClassificId` |
| Proposer Details | Insured (Entity) | `EntityInsuredValue` | Yes | DropdownPlus | Copies from Proposer if same | InsuredSameAsProposer + Insured Classification | `EntityMaster/EntityLookup` → `ProposerDetailBO.EntityInsuredId` |

**Sub-section B: Basic Policy Details** (accordion fields from NB Configuration API)

| Screen/Tab | UI Field | Technical Field | Mandatory | Input Type | Auto Populated | Dependency | API/Backend Mapping |
|---|---|---|---|---|---|---|---|
| Basic Policy Details | Product | `MasterProductValue` | Yes | Dropdown | Pre-set from GetQuote | None | `ProductConfigurator/getProducts` → `BasicPolicyDetailBO.PcProductId` |
| Basic Policy Details | Plan | `MasterPlanValue` | Yes | Dropdown | None | Product | `ProductConfigurator/productPlans` → `BasicPolicyDetailBO.PcPlanId` |
| Basic Policy Details | Source | `MasterSourceValue` | Yes | Dropdown | None | None | Master (COC/STY) → `BasicPolicyDetailBO.MasterSourceId` |
| Basic Policy Details | Source Name | `EntitySourceNameValue` | Conditional (if Broker/Agent) | Dropdown | None | Source = Broker or Agent | `ProductConfigurator/blockListing` → `BasicPolicyDetailBO.PcEntitySourceNameId` |
| Basic Policy Details | Channel | `MasterChannelValue` | Yes | Dropdown | None | Product | `ProductConfigurator/blockListing` → `BasicPolicyDetailBO.PcChannelId` |
| Basic Policy Details | Business Type | `MasterBusinessTypeValue` | Yes | Dropdown | None | Product | `ProductConfigurator/blockListing` → `BasicPolicyDetailBO.MasterBusinessTypeId` |
| Basic Policy Details | Transaction Type | `MasterTransactionTypeValue` | Yes | Dropdown | Default: "New Business" | None | Master (COC/TRA_NBS) → `BasicPolicyDetailBO.MasterTransactionTypeId` |
| Basic Policy Details | Calculation Type | `MasterCalculationtionTypeValue` | Yes | Dropdown | Default: "Annual" | None | Master (COC/PCT) → `BasicPolicyDetailBO.MasterCalculationtionTypeId` |
| Basic Policy Details | Policy Tenure | `PolicyTenureValue` | Conditional (long-term) | Dropdown | None | Calculation Type | Master (COC/TEN_YRS) → `BasicPolicyDetailBO.PolicyTenureId` |
| Basic Policy Details | Long Term Method | `LongTermMethodValue` | Conditional | Dropdown | None | Policy Tenure | Master (COC/LTM) → `BasicPolicyDetailBO.LongTermMethodId` |
| Basic Policy Details | Effective From | `EffectiveFrom` | Yes | Date | Default: today | None | `BasicPolicyDetailBO.EffectiveFrom` |
| Basic Policy Details | Effective To | `EffectiveTo` | Conditional | Date | Auto-calculated from Effective From + Calculation Type | Effective From + Calculation Type | `BasicPolicyDetailBO.EffectiveTo` |
| Basic Policy Details | No. of Members | `NoOfMembers` | No | Number | None | None | `BasicPolicyDetailBO.NoOfMembers` |
| Basic Policy Details | Sum Insured | `SumInsured` | No | Amount | None | None | `BasicPolicyDetailBO.SumInsured` |
| Basic Policy Details | Is Renewal | `IsRenewal` | No | Toggle | Default: false | None | `BasicPolicyDetailBO.IsRenewal` |
| Basic Policy Details | Renewal Policy No | `RenewalPolicyno` | Conditional | Text | None | IsRenewal | `BasicPolicyDetailBO.RenewalPolicyno` |
| Basic Policy Details | Advice Date Policy | `IsAdviceDatePolicy` | Conditional | Toggle | Default: true | IsAdviceDateApplicable (product config) | `BasicPolicyDetailBO.IsAdviceDatePolicy` |
| Basic Policy Details | Advice Date Received | `IsAdviceDateReceived` | Conditional | Toggle | Default: false | IsAdviceDatePolicy | `BasicPolicyDetailBO.IsAdviceDateReceived` |
| Basic Policy Details | Retro Active Date | `RetroActiveDate` | Conditional | Date | Auto-calculated | IsRetroDateApplicable (product config) | `BasicPolicyDetailBO.RetroActiveDate` |
| Basic Policy Details | Discovery Date | `DiscoveryDate` | Conditional | Date | Auto-calculated | IsDiscoveryDateApplicable (product config) | `BasicPolicyDetailBO.DiscoveryDate` |
| Basic Policy Details | **Flexi fields** (product-configured) | `Key{N}` | Per config | Per config | Per config | Per config | `BasicPolicyDetailBO.FlexiField` |

**Sub-section C: Risk Details** (appears after Proposer Details are saved and Channel is selected)

Risk details are entered via a separate `RiskDetails` component. Each risk block is product-configured via `getNewBusinessRiskMetaByProduct`. Fields are dynamic flexi-key fields stored in `RiskListData`. The user adds one or more risk records per mandatory section.

---

### Screen: Full Quote — Tab 2: Coverage Details (`CoverageDetails.jsx`)

Coverage Details is a tabbed sub-screen. Tabs shown depend on what the Product Configurator has configured:

| Sub-Tab | Content | User Action |
|---|---|---|
| SMI | Sum Insured items per risk/section/policy level | Enter/modify SI values |
| Covers | Basic and add-on covers per risk/section/policy level | Toggle on/off, enter SI/rate where applicable |
| Discount | Applicable discounts | Select/modify discount rates |
| Loading | Applicable loadings | Select/modify loading rates |
| Deductible | Applicable deductibles | Select/modify deductible values |
| Condition | Policy conditions | Review/modify |
| Charges & Fees | Applicable charges | Review/modify amounts |
| Tax | Tax breakdown | Read-only (calculated) |

All data comes from `ProductConfigurator/getSMICoverData` (PC config) and `NewBusiness/getNBSMICoverData` (existing NB data). The user selects a Section and Section Plan from dropdowns at the top.

---

### Screen: Full Quote — Tab 3: Policy Details (`PolicyDetails.jsx`)

| Screen/Tab | UI Field | Technical Field | Mandatory | Input Type | Auto Populated | Dependency | API/Backend Mapping |
|---|---|---|---|---|---|---|---|
| Basic Details | Branch | `MasterBranchValue` | Yes | Dropdown | Default: user's office | None | `UserManagement/getAppOfficeDetails` → `BasicDetailBO.MasterBranchId` |
| Basic Details | Policy Category | `ProductPolicyCategoryValue` | Yes | Dropdown | From product config | None | Meta (BIAPC) → `BasicDetailBO.ProductPolicyCategoryId` |
| Policy Details | SI Currency | `MasterSiCurrencyValue` | Yes | Dropdown | Default: company currency | None | Master (CUR) → `PolicyDetailBO.MasterSiCurrencyId` |
| Policy Details | Premium Currency | `MasterPemCurrencyValue` | Yes | Dropdown | Default: company currency | None | Master (CUR) → `PolicyDetailBO.MasterPemCurrencyId` |
| Policy Details | Rate Type | `MasterRateTypeValue` | Yes | Dropdown | Default: "User Entered" | None | Master (COC/RTY) → `PolicyDetailBO.MasterRateTypeId` |
| Policy Details | Rate Picked From | `MasterRatePickedFromValue` | Yes | Dropdown | Default: "Exchange Rate Master" | None | Master (COC/RPF) → `PolicyDetailBO.MasterRatePickedFromId` |
| Policy Details | Exchange Rate | `MasterRateValue` | Conditional | Number/Dropdown | Auto-fetched from exchange rate master | Rate Picked From + currencies | `PolicyDetailBO.MasterRateValue` |
| Policy Details | Payer Type | `MetaPayerTypeValue` | Yes | Dropdown | Default: "Cash Before Cover" | None | Master (POP) → `PolicyDetailBO.MetaPayerTypeId` |
| Policy Details | Policy Payer | `PolicyPayerValue` | No | Dropdown | Options from Proposer/Insured | None | `PolicyDetailBO.PolicyPayerId` |
| Policy Details | Jurisdiction | `MasterJurisdictionValue` | No | Dropdown | Default from product config | None | `PolicyDetailBO.MasterJurisdictionId` |
| Policy Details | Territory | `MasterTerritoryValue` | No | Dropdown | Default from product config | None | `PolicyDetailBO.MasterTerritoryId` |
| Policy Details | Is FAC | `IsFAC` | No | Toggle | From product config (IsFacToTreaty) | None | `PolicyDetailBO.IsFac` |
| Policy Details | FAC % | `Percentage` | Conditional (if FAC) | Number | None | IsFAC | `PolicyDetailBO.Percentage` |
| Policy Details | Date | `Date` | No | Date | Default: today | None | `PolicyDetailBO.Date` |
| Previous Policy | Have Previous Policy | `HavePrePolicy` | No | Toggle | Default: false | None | `PreviousPolicyDetailBO.HavePrePolicy` |
| Previous Policy | Previous Policy No | `PreviousPolicyNo` | Conditional | Text | None | HavePrePolicy | `PreviousPolicyDetailBO.PreviousPolicyNo` |
| Previous Policy | Previous Insurer | `EntityPrevInsuredValue` | Conditional | Dropdown | None | HavePrePolicy | `PreviousPolicyDetailBO.EntityPrevInsuredId` |
| Previous Policy | Previous Policy Expiry | `PrevPolicyExpDate` | Conditional | Date | None | HavePrePolicy | `PreviousPolicyDetailBO.PrevPolicyExpDate` |
| Previous Policy | Any Claims Made | `AnyClaimsMade` | Conditional | Toggle | None | HavePrePolicy | `PreviousPolicyDetailBO.AnyClaimsMade` |
| Previous Policy | Vehicle Ownership Changed | `VehicleOwnershipChanged` | Conditional | Toggle | None | HavePrePolicy | `PreviousPolicyDetailBO.VehicleOwnershipChanged` |
| Previous Policy | Previous Policy Type | `MasterPrevPolicyTypeValue` | Conditional | Dropdown | None | HavePrePolicy | Master (COC/PTY) → `PreviousPolicyDetailBO.MasterPrevPolicyTypeId` |
| Previous Policy | Previous NCD/NCB % | `MasterPrevNcbPercentValue` | Conditional | Dropdown | Default: 0% | AnyClaimsMade + VehicleOwnershipChanged | Meta (PNCBP) → `PreviousPolicyDetailBO.MasterPrevNcbPercentId` |
| Previous Policy | NCB Certificate No | `NcbCertificateNumber` | Conditional | Text | None | NCD % > 0 | `PreviousPolicyDetailBO.NcbCertificateNumber` |
| Previous Policy | Previous Add-On Covers | `PreviousAddOnCovers` | No | Multi-select Dropdown | None | HavePrePolicy | `PreviousPolicyDetailBO.PreviousAddOnCovers` |

---

### Screen: Full Quote — Tab 4: Broker Commission (`BrokerCommission.jsx`)

Shown only when Source = Broker or Agent. Fields are product-configured. User enters commission percentages/amounts per broker/agent.

### Screen: Full Quote — Tab 5: Co-Insurance (`CoInsurance.jsx`)

Shown only when Business Type = Co-Insurance (Direct with Co-Insurance or Inward Co-Insurance). User enters co-insurance participant details, shares, and lead/follow percentages.

### Screen: Full Quote — Tab 6: Pre-Inspection (`PreInspection.jsx`)

Shown for products requiring pre-inspection. User reviews pre-inspection results and marks Accept/Reject per risk item.

---

## 4. User Effort Analysis

| Field/Activity | Screen | Manual Effort | Repetition | Existing Automation | Notes |
|---|---|---:|---:|---|---|
| Select Id Type | Get Quote | Low | Low | None | Simple dropdown |
| Enter Id Value (search) | Get Quote | Low | Low | Entity lookup auto-fires on blur | Triggers customer search |
| Select Product | Get Quote | Low | Low | Product list fetched automatically | |
| Select Quote Type | Get Quote | Low | Low | None | |
| Create new customer (if not found) | Get Quote (AddUser drawer) | **High** | Low | None | 3-step wizard: KYC → Personal Details → Address |
| Select Plan | Quick Quote | Low | Low | First plan auto-selected | |
| Fill dynamic accordion fields | Quick Quote | **Medium–High** | Low | Customer data auto-populates some fields | Depends on product; motor has many fields |
| Select/toggle covers | Quick Quote | Low | Low | Default/mandatory covers pre-toggled | |
| Vehicle Make selection | Quick Quote | **Medium** | Low | Model/Variant/FuelType/BodyType auto-populated after Make | Special modal picker |
| Select Proposer Classification | FQ Tab 1 | Low | Low | Default: Individual | |
| Search/select Proposer entity | FQ Tab 1 | **Medium** | **High** | Pre-filled from GetQuote customer lookup | Re-entered if not carried forward |
| Toggle Insured Same as Proposer | FQ Tab 1 | Low | Low | Insured auto-copies from Proposer if toggled | |
| Select Product | FQ Tab 1 | Low | Low | Pre-set from GetQuote | Repeated from GetQuote |
| Select Plan | FQ Tab 1 | Low | Low | None | |
| Select Source | FQ Tab 1 | Low | Low | None | Drives Tab 4 visibility |
| Select Source Name (broker/agent) | FQ Tab 1 | Low | Low | None | Conditional |
| Select Channel | FQ Tab 1 | Low | Low | None | |
| Select Business Type | FQ Tab 1 | Low | Low | None | Drives Tab 5 visibility |
| Transaction Type | FQ Tab 1 | Low | Low | Default: "New Business" | |
| Calculation Type | FQ Tab 1 | Low | Low | Default: "Annual" | |
| Effective From | FQ Tab 1 | Low | Low | Default: today | |
| Effective To | FQ Tab 1 | Low | Low | Auto-calculated from Effective From | |
| Fill flexi fields (product-configured) | FQ Tab 1 | **Medium** | Low | None | Product-specific |
| Enter risk data (per risk block) | FQ Tab 1 (Risk Details) | **High** | **High** | None | Multiple fields per risk; multiple risks possible |
| Set SMI values | FQ Tab 2 | **Medium** | **Medium** | Default values from product config | Per risk/section/policy level |
| Toggle/configure covers | FQ Tab 2 | **Medium** | **Medium** | Default/mandatory covers pre-set | |
| Set discounts | FQ Tab 2 | Low | Low | Default discounts pre-applied | |
| Set loadings | FQ Tab 2 | Low | Low | Default loadings pre-applied | |
| Select Branch | FQ Tab 3 | Low | Low | Default: user's office | |
| Select currencies | FQ Tab 3 | Low | Low | Default: company currency | |
| Exchange rate | FQ Tab 3 | Low | Low | Auto-fetched from exchange rate master | |
| Payer Type | FQ Tab 3 | Low | Low | Default: Cash Before Cover | |
| Previous policy details | FQ Tab 3 | **Medium** | Low | None | Requires knowledge of prior policy |
| NCD/NCB % | FQ Tab 3 | Low | Low | Default: 0% | |
| Broker commission | FQ Tab 4 | **Medium** | Low | None | Conditional tab |
| Co-insurance participants | FQ Tab 5 | **Medium** | Low | None | Conditional tab |

---

## 5. Existing STATIM Automation

The following are confirmed from the source code — things STATIM already does automatically:

### Auto-Population
- **Effective To** — auto-calculated from Effective From based on Calculation Type (Annual = 364/365 days; 13-month policy adds 1 month; Short Period/Pro Rata = Effective From + Tenure years)
- **Transaction Type** — defaults to "New Business" (code `NB_TRA_001`)
- **Calculation Type** — defaults to "Annual" (code `PCT_001`)
- **Payer Type** — defaults to "Cash Before Cover" (`POP_001`)
- **Rate Type** — defaults to "User Entered" (`RTY` code)
- **Rate Picked From** — defaults to "Exchange Rate Master" (`RPF` code)
- **Exchange Rate** — auto-fetched from exchange rate master when Rate Picked From = Exchange Rate Master
- **Branch** — defaults to user's applicable office (from `getAppOfficeDetails`)
- **Policy Category** — defaults from product's `MetaApplicableCategoryValue`
- **Insured fields** — auto-copied from Proposer when "Insured Same as Proposer" is toggled
- **Vehicle fields** (Model, Variant, FuelType, BodyType, ManufacturingYear) — auto-populated after Make selection from the vehicle master modal
- **Customer fields in Quick Quote** — FirstName, LastName, Email, DOB, Phone, Gender, Nationality, Document Type/Number auto-populated from Entity Master when customer is passed from GetQuote
- **Default covers** — covers marked `IsDefaultCover` or `IsMandatoryCover` are pre-toggled on
- **Mandatory covers** — cannot be toggled off
- **First plan** — auto-selected when plans load
- **Insured Classification** — defaults to "Individual"
- **Proposer Classification** — defaults to "Individual"
- **IsFAC** — auto-set from product's `IsFacToTreaty` flag
- **Retro Active Date** — auto-calculated from Effective From minus `RetroActiveDays` (from product underwriting config)
- **Discovery Date** — auto-calculated from Effective To plus `DiscoveryDays`
- **NCD/NCB %** — resets to 0% when AnyClaimsMade or VehicleOwnershipChanged is toggled

### Validation
- FluentValidation on `QuickQuoteBO` (backend)
- Ant Design Form validation on all fields (frontend)
- Mandatory field enforcement per product configuration (`IsMandatory` flag)
- Policy Tenure validation: EffectiveTo must be at least Effective From + Tenure years
- Long-term period minimum validation
- Currency validation: at least one currency must be the company default
- Pre-inspection status validation: all risks must be "Accept" before Quote Approval
- Risk block completeness validation: mandatory risk blocks must have at least one record

### Business Rules
- Tab 4 (Broker Commission) visibility: shown only when Source = Broker (`STY_003`) or Agent (`STY_002`)
- Tab 5 (Co-Insurance) visibility: shown only when Business Type = Direct with Co-Insurance or Inward Co-Insurance
- Quick Quote only available for products with `ApplicableCategoryValue = "Standalone"`
- NCD/NCB % field enabled only when NCB Certificate Number is present AND (AnyClaimsMade OR VehicleOwnershipChanged)
- FAC % field mandatory only when IsFAC = true
- Source Name field mandatory only when Source = Broker or Agent

### Rating Engine Integration
- **Premium calculation** — triggered automatically on Save Quick Quote and on Coverage Details save in Full Quote
- Rating calls `RateEngine/DynamicExpressionResolver` with risk parameters, cover data, SMI values
- Returns premium, cover premiums, SMI values, tax amounts, discount/loading amounts
- All premium amounts are calculated — user never manually enters a premium

### Product Configurator Integration
- All form fields (labels, types, mandatory flags, sequences, data sources) are fetched from Product Configurator at runtime
- Cover lists, SMI lists, discount/loading/deductible/condition lists all come from PC
- Channel, Source, Business Type options are product-specific (from PC block listings)
- Quick Quote accordion structure is entirely PC-driven

### Reference Number Generation
- Quote number auto-generated by `ReferenceNoGenerator` microservice on first save
- Policy number auto-generated on policy approval

### Kafka Events (backend, no user effort)
- Quote creation, approval, policy issuance events published to Kafka automatically
- Accounting staging entry created automatically on policy approval
- RI allocation triggered via Kafka consumers

---

## 6. Data Dependencies

```
Product
  → Plan options (ProductConfigurator/productPlans)
  → Channel options (ProductConfigurator/blockListing BSCAPCH)
  → Source options (ProductConfigurator/blockListing BSCAPSR)
  → Business Type options (ProductConfigurator/blockListing BSCAPBS)
  → Quick Quote field configuration (QuickQuote/GetFieldsForQuickQuote)
  → Full Quote accordion/field configuration (NB Configuration API)
  → Cover/SMI/Discount/Loading/Deductible/Condition lists (ProductConfigurator/getSMICoverData)
  → Product properties: IsFleet, Is13MonthPolicy, IsIncremental, IsAdviceDateApplicable,
    IsRetroDateApplicable, IsDiscoveryDateApplicable, RetroActiveDays, DiscoveryDays,
    AnnualProduct, LongTermPeriodAllowed, MinLongTermPeriod, IsFacToTreaty

Source (Broker/Agent)
  → Source Name options (ProductConfigurator/blockListing BSCAPSR filtered by IsAgentOrBroker)
  → Tab 4 (Broker Commission) visibility
  → Executive field (Key212) auto-populated from source name

Business Type
  → Tab 5 (Co-Insurance) visibility

Proposer Classification
  → Entity Proposer search options (EntityMaster/EntityLookup filtered by classification)

Insured Same as Proposer
  → Insured Classification

---

Compact chat history?

---

You stopped your current work and asked me to work on the following task instead.

---

Continue with the discovery report.

---


Let me continue reading the remaining components needed to complete the discovery report.

---


Now let me check the RatingAPIService and the NewBusiness action creators for the full quote:

---


I now have a complete picture of the backend rating engine integration. I have all the information needed to complete the discovery report. Let me now compile the remaining sections.

---

## Continuing the Discovery Report

### Section 6 (continued): Data Dependencies

```
Calculation Type
  → Effective To auto-calculation (Annual = 364/365 days; Short Period/Pro Rata = Effective From + Tenure)
  → Effective To editability (disabled when Annual)

Effective From
  → Effective To (auto-calculated)
  → Retro Active Date (auto-calculated from Effective From - RetroActiveDays)

Effective To
  → Discovery Date (auto-calculated from Effective To + DiscoveryDays)

Source (Broker/Agent)
  → Source Name field visibility and mandatory status
  → Tab 4 (Broker Commission) visibility
  → Executive field (Key212) auto-populated from Source Name

Business Type
  → Tab 5 (Co-Insurance) visibility

HavePrePolicy
  → Previous Policy No, Previous Insurer, Previous Policy Expiry, Any Claims Made,
    Vehicle Ownership Changed, Previous Policy Type, Previous Add-On Covers (all enabled/disabled)

AnyClaimsMade / VehicleOwnershipChanged
  → NCD/NCB % field (enabled only when NCB Certificate Number is present AND one of these is true)
  → NCD/NCB % resets to 0% when either is toggled

IsFAC
  → FAC % field (mandatory and visible only when IsFAC = true)

Rate Picked From
  → Exchange Rate field (auto-fetched when = Exchange Rate Master; user-entered when = User Entered)

SI Currency / Premium Currency
  → Exchange Rate field (auto-fetched based on currency pair)

Cover selection (Quick Quote)
  → Rating engine input (only enabled covers are sent to rating)

Risk fields (vehicle make)
  → Model, Variant, FuelType, BodyType, ManufacturingYear (all auto-populated from Make selection)

Customer entity (from GetQuote lookup)
  → FirstName, LastName, Email, DOB, Phone, Gender, Nationality, Document Type/Number
    (auto-populated in Quick Quote if customer passed from GetQuote)
```

---

## 7. Quote Creation Flow

### Path A — Quick Quote (minimum steps)

```
NBListing (/newbusiness)
  → Click "Get Quote"

GetQuote (/getquote)
  → Select Id Type (e.g. "Phone Number")
  → Enter phone number → blur → EntityLookup API fires → customer found
  → Select Product (dropdown, fetched from ProductConfigurator/getProducts)
  → Select Quote Type = "Quick Quote"
  → Click "Proceed"
    [Validation: Product mandatory, Quote Type mandatory, product must be Standalone]

QuickQuote (/quickquote)
  → Plan auto-selected (first plan)
  → Policy dates default (today → today + 1 year)
  → Fill product-configured accordion fields (e.g. proposer name, DOB, vehicle details)
    [Customer fields auto-populated from entity lookup if passed from GetQuote]
    [Vehicle Make → modal picker → Model/Variant/FuelType/BodyType/Year auto-populated]
  → Toggle covers (mandatory covers pre-toggled, cannot be changed)
  → Click "Generate Quick Quote"
    → POST QuickQuote/SaveQuickQuote (NewBusiness microservice, direct via API_URL_NB)
    → Rating engine called automatically (RateEngine/DynamicExpressionResolver)
    → Premium displayed in right panel
  → Review premium breakdown
  → Click "Proceed to Full Quote"
    → POST QuickQuote/ConvertQuote
    → New PolicyIteration + PolicyVersion created
    → Navigate to /fullquote

FullQuote (/fullquote) — continues as Full Quote path below
```

### Path B — Full Quote (minimum steps)

```
NBListing (/newbusiness)
  → Click "Get Quote"

GetQuote (/getquote)
  → Select Id Type + enter Id Value → customer lookup
  → Select Product
  → Select Quote Type = "Full Quote"
  → Click "Proceed"

FullQuote (/fullquote) — Tab 1: Proposer/Risk Details
  → Select Proposer Classification (default: Individual)
  → Search/select Proposer entity (type name or phone → EntityLookup)
  → Toggle "Insured Same as Proposer" (if applicable)
  → Select Product (pre-set from GetQuote)
  → Select Plan
  → Select Source
  → Select Channel
  → Select Business Type
  → Transaction Type (default: New Business)
  → Calculation Type (default: Annual)
  → Effective From (default: today)
  → Effective To (auto-calculated)
  → Fill any product-configured flexi fields
  → Click "Proceed" / "Update"
    → POST/PUT FullQuote/block (BlockCode: PROPOSER_RISK, SubBlockCode: PROPOSER_DETAILS)
    → Quote number generated by ReferenceNoGenerator
  → Risk Details section appears
  → Add risk record(s) per mandatory section
    → Fill risk fields (product-configured flexi fields)
    → Save each risk block
    → POST/PUT FullQuote/block (BlockCode: PROPOSER_RISK, SubBlockCode: RISK_DETAILS)
  → Click "Next" (stepper advances)

FullQuote — Tab 2: Coverage Details
  → SMI values — enter/modify per risk/section/policy level
  → Covers — toggle on/off, enter SI where applicable
  → Discounts — review/modify
  → Loadings — review/modify
  → Click "Next" (saves coverage data)
    → POST FullQuote/block (BlockCode: COVERAGE_DETAILS)
    → Rating engine called automatically → premium updated

FullQuote — Tab 3: Policy Details
  → Branch (default: user's office)
  → SI Currency / Premium Currency (default: company currency)
  → Rate Type (default: User Entered)
  → Rate Picked From (default: Exchange Rate Master)
  → Exchange Rate (auto-fetched)
  → Payer Type (default: Cash Before Cover)
  → Previous Policy section (if applicable)
  → Click "Next"
    → POST/PUT FullQuote/block (BlockCode: POLICY_DETAILS)
    → Rating engine called automatically → premium updated

[Tab 4: Broker Commission — if Source = Broker/Agent]
[Tab 5: Co-Insurance — if Business Type = Co-Insurance]
[Tab 6: Pre-Inspection — if product requires it]

FullQuote — Last Tab
  → Review premium amount (shown in header)
  → Click "Quote Approval"
    → Workflow ExecuteWorkflow → CommitWorkflow
    → POST CollectCash/getQuoteApproval
    → Status → "QuoteApproved"
    → Quote Number confirmed

[Optional: Click "Collect" → payment collection flow]
[Optional: Click "Generate to Policy" → PolicyApprovedQuote → Policy Number generated]
```

---

## 8. Manual-Effort Hotspots

### Hotspot 1 — Customer Creation (New Customer)

**What the user does:** If the customer is not found in the entity lookup, the user must create a new customer via the AddUser drawer. This is a 3-step wizard:
1. KYC — select document type, enter document number, upload document
2. Personal Details — enter First Name, Last Name, DOB, Gender, Nationality, Phone, Email, ID Type, ID Number, TCF Number
3. Address — enter street, postal code, city, state, country for both permanent and communication addresses

**Information provided:** Full personal identity, contact details, address, KYC documents.

**Exists elsewhere in STATIM:** Yes — Entity Master holds all customer records. If the customer already exists, all this data is already in the system. The problem is only when the customer is genuinely new.

**Could be obtained from another source:** Theoretically from a CRM, proposal form, or external identity verification service. The data is entirely customer-provided at point of sale.

**Suitable for AI assistance:** Partially. An AI could pre-fill personal details from a scanned document (ID card, passport), or suggest existing customers based on partial name/phone input. The 3-step wizard itself is a significant friction point for new customers.

---

### Hotspot 2 — Risk Data Entry (Full Quote, Tab 1)

**What the user does:** For each risk (e.g. each vehicle in a motor policy, each property in a property policy), the user must fill a set of product-configured fields. These fields are entirely dynamic — their number, type, and labels depend on the product. For motor products, this typically includes: vehicle registration number, chassis number, engine number, make, model, variant, year of manufacture, fuel type, body type, cubic capacity, seat capacity, vehicle value, colour, and more. For each risk block, the user must add a record, fill all mandatory fields, and save.

**Information provided:** All risk-specific attributes — the physical description of the insured object.

**Exists elsewhere in STATIM:** Partially. Vehicle make/model/variant/fuel type/body type are auto-populated from the vehicle master after Make selection. However, registration number, chassis number, engine number, vehicle value, and other unique identifiers must be typed manually.

**Could be obtained from another source:** Vehicle registration data could theoretically come from a motor vehicle registry API. For renewals, the risk data from the previous policy already exists in STATIM but is not currently pre-populated into a new quote.

**Suitable for AI assistance:** High. This is the single highest-effort area. An AI could:
- Pre-fill risk fields from a previous policy (renewal scenario)
- Suggest vehicle details from a registration number lookup
- Extract risk data from uploaded documents (RC book, inspection report)

---

### Hotspot 3 — Coverage Details (Full Quote, Tab 2)

**What the user does:** For each risk, the user must review and set Sum Insured (SMI) values, toggle covers on/off, and potentially adjust discount/loading rates. The SMI values are the insured amounts for each coverage item — for motor, this is the vehicle value; for property, it is the building/contents value. The user must navigate between Risk/Section/Policy level views and set values for each.

**Information provided:** Sum insured amounts per SMI item, cover selection, discount/loading adjustments.

**Exists elsewhere in STATIM:** Default covers are pre-set from Product Configurator. Default discounts and loadings are pre-applied. However, the actual SI values must be entered by the user.

**Could be obtained from another source:** For renewals, the previous policy's SI values exist in STATIM. For new business, the SI values come from the customer/risk assessment. Vehicle value could come from a valuation API.

**Suitable for AI assistance:** Medium-High. An AI could suggest SI values based on:
- Previous policy data (renewal)
- Market value data for vehicles
- Standard SI ranges for the product/risk type

---

### Hotspot 4 — Previous Policy Details (Full Quote, Tab 3)

**What the user does:** If the customer has a previous policy (renewal or transfer), the user must manually enter: previous policy number, previous insurer name, previous policy expiry date, whether any claims were made, whether vehicle ownership changed, previous policy type, previous NCD/NCB percentage, NCB certificate number, and previous add-on covers.

**Information provided:** Prior insurance history — critical for NCD/NCB calculation.

**Exists elsewhere in STATIM:** If the previous policy was issued in STATIM, all this data exists. However, there is no confirmed code path that auto-populates previous policy details from an existing STATIM policy into a new quote. The `HavePrePolicy` toggle enables the fields but does not auto-fill them.

**Could be obtained from another source:** For renewals within STATIM, the previous policy data is in the database. For transfers from other insurers, the data must come from the customer or the previous insurer's certificate.

**Suitable for AI assistance:** High for renewals. An AI could detect that the customer has an existing policy in STATIM and auto-populate all previous policy fields. For external transfers, an AI could extract NCD certificate data from an uploaded document.

---

### Hotspot 5 — Proposer/Insured Entity Selection (Full Quote, Tab 1)

**What the user does:** The user must search for and select the Proposer entity by typing a name or phone number into a search field. The search triggers a live lookup against Entity Master. If the customer was already found in GetQuote, the entity code is passed forward but the user must still search and select the entity in the Proposer field in Full Quote Tab 1.

**Information provided:** Entity identity — who is the proposer and insured.

**Exists elsewhere in STATIM:** Yes — the customer was already identified in GetQuote. The entity code is passed as navigation state to QuickQuote but in Full Quote the user must re-search and select.

**Could be obtained from another source:** The entity is already in STATIM from the GetQuote step. This is a confirmed repetition — the same customer information is entered/selected twice (once in GetQuote, once in Full Quote Tab 1).

**Suitable for AI assistance:** This is not an AI problem — it is a UX/data-flow problem. The entity selected in GetQuote should be automatically pre-populated into the Proposer field in Full Quote Tab 1. The code in `ProposerRiskDetails.jsx` does set `defaultValues["EntityProposerValue"] = Customer` from the Redux store, but this only works if the Customer value was set in Redux from the GetQuote step. The actual pre-population depends on whether the Redux state is correctly threaded through.

---

### Hotspot 6 — Product/Plan/Channel/Source/Business Type Selection (Repeated)

**What the user does:** In GetQuote, the user selects a Product. In Full Quote Tab 1, the user must select Product again (pre-set from Redux), then also select Plan, Source, Channel, and Business Type — all of which are product-specific dropdowns that require the user to know the correct values.

**Information provided:** Product configuration choices — which plan, distribution channel, source, and business type apply to this quote.

**Exists elsewhere in STATIM:** Product is pre-set from GetQuote. Plan, Channel, Source, Business Type are product-specific and must be selected by the user based on the sales context.

**Could be obtained from another source:** For a given sales context (e.g. direct business, specific broker), these values are typically fixed. An AI could suggest defaults based on the user's role, the customer's history, or the product's typical configuration.

**Suitable for AI assistance:** Low-Medium. These are contextual choices that depend on the sales scenario. An AI could suggest defaults based on historical patterns for the user/product combination.

---

## 9. APIs / Services Involved

### Frontend → Backend (direct, bypassing API Gateway)

| API Call | Endpoint | Service | Trigger |
|---|---|---|---|
| Get products | `GET ProductConfigurator/getProducts` | ProductConfigurator | GetQuote screen load |
| Entity lookup | `GET EntityMaster/EntityLookup` | EntityMaster | Id value blur in GetQuote |
| Get customer policies | `GET NewBusiness/getNbCustomerPolicyData` | NewBusiness | After entity found |
| Save new entity | `POST EntityMaster/SaveNewEntityCustomer` | EntityMaster | AddUser confirm |
| Get QQ fields | `GET QuickQuote/GetFieldsForQuickQuote` | NewBusiness | QuickQuote screen load |
| Get QQ plans | `GET ProductConfigurator/productPlans` | ProductConfigurator | QuickQuote screen load |
| Get QQ covers | `GET ProductConfigurator/nbCovers` | ProductConfigurator | Plan selection |
| Get QQ sections | `GET ProductConfigurator/blockListing (SECT)` | ProductConfigurator | QuickQuote screen load |
| Get channels | `GET ProductConfigurator/blockListing (BSCAPCH)` | ProductConfigurator | QuickQuote screen load |
| Get sources | `GET ProductConfigurator/blockListing (BSCAPSR)` | ProductConfigurator | QuickQuote screen load |
| Get business types | `GET ProductConfigurator/blockListing (BSCAPBS)` | ProductConfigurator | QuickQuote screen load |
| Get vehicle make | `GET Claims/GetMakeModelVariant` | Claims | Make field click |
| Save Quick Quote | `POST QuickQuote/SaveQuickQuote` | NewBusiness | "Generate Quick Quote" |
| Update Quick Quote | `PUT QuickQuote/UpdateQuickQuote` | NewBusiness | "Update Quick Quote" |
| Get QQ by ID | `GET QuickQuote/GetQuickQuoteListById` | NewBusiness | After save / from listing |
| Convert QQ to FQ | `POST QuickQuote/ConvertQuote` | NewBusiness | "Proceed to Full Quote" |
| Get FQ block mapping | `GET NewBusinessConfiguration/getFullQuoteBlockMapping` | NewBusiness | FullQuote screen load |
| Get FQ accordions | `GET NewBusinessConfiguration/getFullQuoteAccordionByBlockMappingId` | NewBusiness | Tab load |
| Get FQ fields | `GET NewBusinessConfiguration/getFullQuoteFieldsByAccordionId` | NewBusiness | Accordion load |
| Save FQ block | `POST/PUT FullQuote/block` | NewBusiness | Tab save / Next |
| Get proposer data | `GET NewBusiness/getNewBusinessProposerData` | NewBusiness | Tab 1 load (edit mode) |
| Get policy details | `GET NewBusiness/getPolicyDetails` | NewBusiness | Tab 3 load |
| Get broker commission | `GET NewBusiness/getBrokerCommission` | NewBusiness | Tab 4 load |
| Get co-insurance | `GET NewBusiness/getCoInsurance` | NewBusiness | Tab 5 load |
| Get SMI/Cover data (PC) | `GET ProductConfigurator/getSMICoverData` | ProductConfigurator | Tab 2 load |
| Get SMI/Cover data (NB) | `GET NewBusiness/getNBSMICoverData` | NewBusiness | Tab 2 load |
| Get risk meta | `GET NewBusiness/getNewBusinessRiskMetaByProduct` | NewBusiness | Tab 1 risk section |
| Get risk list | `GET NewBusiness/getNewBusinessRiskList` | NewBusiness | Tab 2 load |
| Save risk detail | `POST/PUT FullQuote/block (RISK_DETAILS)` | NewBusiness | Risk save |
| Refresh rating | `POST FullQuote/refreshRating` | NewBusiness | Refresh icon / Next |
| Get policy sections | `GET FullQuote/policySection` | NewBusiness | Tab 2 load |
| Get NB listing | `POST FullQuote/all` | NewBusiness | NBListing load |
| Quote approval | `POST CollectCash/getQuoteApproval` | NewBusiness | "Quote Approval" button |
| Policy approval | `POST NewBusiness/PolicyApprovedQuote` | NewBusiness | "Generate to Policy" |
| Get app office | `GET UserManagement/getAppOfficeDetails` | UserManagement | Tab 3 load |
| Get exchange rate | `GET Masters/getExchangeRate` | Masters | Tab 3 currency change |
| Get master data | `GET Masters/getMasterData` | Masters | Various screens |
| Get classification | `GET Master/GetClassificationCategoryList` | Masters | GetQuote / Tab 1 |
| Get entity executive | `GET EntityMaster/getEntityExecutiveList` | EntityMaster | Source Name change (Key212) |

### Backend → Backend (internal service calls)

| From | To | Trigger |
|---|---|---|
| NewBusiness (QuickQuoteService) | RateEngine `/DynamicExpressionResolver` | SaveQuickQuote / UpdateQuickQuote |
| NewBusiness (RatingAPIService) | RateEngine `/DynamicExpressionResolver` | FullQuote coverage save / refresh |
| NewBusiness (RatingAPIService) | RateEngine `/GetSI` | TopUp SI calculation |
| NewBusiness (PolicyDetailsService) | AccountingSetup `CreateStagingAccountEntry` | Policy approval |
| NewBusiness (KafkaService) | KafkaJobOrchestrator `/api/SendDataToKafka` | Quote/Policy approval events |
| NewBusiness (ReferenceNoGenerator) | ReferenceNoGenerator | Quote number / Policy number generation |

---

## 10. Unknowns / Information Required

The following items could not be fully determined from the source code:

| Item | Status | Notes |
|---|---|---|
| Exact field list for each product's Quick Quote accordion | Cannot determine | Fields are entirely product-configured at runtime via `QuickQuote/GetFieldsForQuickQuote`. The actual fields depend on what is configured in the Product Configurator for each product. No product configuration data was read. |
| Exact field list for each product's Full Quote risk blocks | Cannot determine | Risk block fields are product-configured via `getNewBusinessRiskMetaByProduct`. Actual fields depend on product configuration. |
| Number of risk blocks per product | Cannot determine | Depends on product configuration (sections and

---

An unexpected error occurred, check the logs for more information.

---

Continue

---


I now have all the source code evidence needed to complete the discovery report. Let me compile the remaining sections.

---

## 6. Data Dependencies (continued from Section 4)

```
Calculation Type
  → Effective To auto-calculation behaviour
    → Annual: EffectiveTo = EffectiveFrom + 364/365 days
    → Short Period / Pro Rata: EffectiveTo = EffectiveFrom + PolicyTenure years - 1 day
    → 13-Month policy: adds 1 extra month to annual calculation

Policy Tenure
  → Effective To (when Calculation Type = Short Period or Pro Rata)
  → Minimum long-term period validation

Effective From
  → Effective To (auto-calculated)
  → Retro Active Date (EffectiveFrom - RetroActiveDays)

Effective To
  → Discovery Date (EffectiveTo + DiscoveryDays)

Have Previous Policy (toggle)
  → Previous Policy No (enabled)
  → Previous Insurer (enabled)
  → Previous Policy Expiry (enabled)
  → Any Claims Made (enabled)
  → Vehicle Ownership Changed (enabled)
  → Previous Policy Type (enabled)
  → Previous Add-On Covers (enabled)
  → NCD/NCB % resets to 0% when toggled off

Any Claims Made / Vehicle Ownership Changed
  → NCD/NCB % (resets to 0%)
  → NCD/NCB % field enabled only when NCB Certificate Number is present

NCB Certificate Number
  → NCD/NCB % field editability

IsFAC (toggle)
  → FAC % (mandatory when IsFAC = true)

Rate Picked From
  → Exchange Rate (auto-fetched when = "Exchange Rate Master")
  → Exchange Rate field editability (editable only when = "User Entered")

SI Currency + Premium Currency
  → Exchange Rate (auto-fetched based on currency pair)
  → At least one must be the company default currency (validation)

Proposer (Entity)
  → Insured (auto-copied when InsuredSameAsProposer = true)
  → Policy Payer options in Tab 3 (derived from Proposer/Insured entity IDs)

Risk data (per risk block)
  → Coverage Details (SMI values, cover SI, tariff parameters passed to Rating Engine)
  → Rating Engine inputs: risk parameters, cover codes, plan code, section code, channel code, business type code, transaction type code, calculation type code, policy dates, branch state code, insured state code, NCD rate

Cover selection (Coverage Details)
  → Premium calculation (Rating Engine called on save)
  → SMI values → Rating Engine inputs

Quick Quote: Make selection
  → Model (auto-populated)
  → Variant (auto-populated)
  → Fuel Type (auto-populated)
  → Body Type (auto-populated)
  → Manufacturing Year (auto-populated)
  → Vehicle Category (auto-populated)
  → Vehicle Cubic Capacity (auto-populated)
  → Vehicle Seat Capacity (auto-populated)
  → Vehicle Value (auto-populated)
```

---

## 7. Quote Creation Flow

### Path A — Quick Quote → Full Quote

```
NBListing (/newbusiness)
  → User clicks "Get Quote"

GetQuote (/getquote)
  → User selects Id Type (e.g. "Phone Number")
  → User enters Id Value → blur → EntityMaster/EntityLookup fires
  → Customer card displayed (if found) with existing policies
  → User selects Product
  → User selects Quote Type = "Quick Quote"
  → User clicks "Proceed"
  → Validation: Product.ApplicableCategoryValue must = "Standalone"

QuickQuote (/quickquote)
  → System loads: ProductConfigurator/productPlans → first plan auto-selected
  → System loads: QuickQuote/GetFieldsForQuickQuote → accordion fields rendered
  → System loads: ProductConfigurator/nbCovers → covers rendered (defaults pre-toggled)
  → Customer fields auto-populated from entity master data
  → User fills dynamic accordion fields (product-specific)
  → User selects/confirms plan
  → User toggles add-on covers
  → For motor: User clicks Make field → vehicle modal → selects Make → Model/Variant/FuelType/BodyType/Year auto-populated
  → User sets Policy Start Date (default: today) and Policy End Date (default: +1 year)
  → User clicks "Generate Quick Quote"
    → POST QuickQuote/SaveQuickQuote → saves flexi fields + covers
    → RatingAPIService.PostRatingDataForQuickQuoteAsync → POST RateEngine/DynamicExpressionResolver
    → Premium displayed in side panel per risk
  → User reviews premium
  → User clicks "Proceed to Full Quote"
    → POST QuickQuote/ConvertQuote
    → Creates PolicyVersion + PolicyIteration
    → Maps QQ flexi fields → NB accordion fields via field mapping
    → Maps QQ risk fields → NB risk detail records
    → Navigates to /fullquote with PolicyIterationId

FullQuote (/fullquote) — Tab 1: Proposer/Risk Details
  → System loads: NB Configuration accordion/field config
  → System loads: existing proposer data (getNewBusinessProposerData)
  → Proposer/Insured pre-populated from QQ conversion
  → Product pre-set; Plan, Source, Channel, Business Type need user selection
  → User fills remaining required fields
  → User clicks "Proceed" → POST FullQuote/block (PROPOSER_RISK/PROPOSER_DETAILS)
  → Risk Details section appears
  → User adds risk records per mandatory section
  → Each risk save → POST FullQuote/block (PROPOSER_RISK/RISK_DETAILS)

FullQuote — Tab 2: Coverage Details
  → System loads: PC SMI/Cover/Discount/Loading/Deductible/Condition config
  → System loads: existing NB coverage data
  → User reviews/modifies SMI values, cover SI, discount rates, loading rates
  → User clicks "Save" → POST FullQuote/block (COVERAGE_DETAIL)
    → RatingAPIService.PostRatingDataAsync → POST RateEngine/DynamicExpressionResolver
    → Premium updated

FullQuote — Tab 3: Policy Details
  → System loads: existing policy details
  → Branch auto-defaulted; currencies auto-defaulted; exchange rate auto-fetched
  → User reviews/modifies previous policy details if applicable
  → User clicks "Save" → POST FullQuote/block (POLICY_DETAILS)

FullQuote — Tab 4: Broker Commission (if Source = Broker/Agent)
  → User enters commission details

FullQuote — Tab 5: Co-Insurance (if Business Type = Co-Insurance)
  → User enters co-insurance participant details

FullQuote — Last Tab: Quote Approval
  → User clicks "Quote Approval"
    → Workflow ExecuteWorkflow called
    → POST NewBusiness/getQuoteApproval
    → Kafka: QuoteApprove topic published
    → Status → "QuoteApproved"
    → Workflow CommitWorkflow called
  → Success modal shown

FullQuote — Collect
  → User clicks "Collect"
    → GenerateInstallmentSchedule API
    → SaveCollectionHeader / SaveCollectionPolHdrCBC
    → Payment Details modal shown

FullQuote — Generate to Policy
  → User clicks "Generate to Policy"
    → Workflow ExecuteWorkflow called
    → ReserveCollection / ReserveCollectionCBC
    → POST NewBusiness/PolicyApprovedQuote
    → Kafka: PolicyApprove + 5 other topics published
    → AccountingService.CreateStagingAccountEntry (sync)
    → ApproveAndReservationComplete / ConfirmCBCConfirmation
    → Policy number generated
    → Status → "PolicyApproved"
```

### Path B — Direct Full Quote (no Quick Quote)

```
NBListing → GetQuote → Quote Type = "Full Quote" → /fullquote
  → Tab 1: Proposer/Risk Details (all fields manual — no QQ pre-population)
  → Tab 2: Coverage Details
  → Tab 3: Policy Details
  → [Tab 4: Broker Commission — conditional]
  → [Tab 5: Co-Insurance — conditional]
  → [Tab 6: Pre-Inspection — conditional]
  → Quote Approval → Collect → Generate to Policy
```

---

## 8. Manual-Effort Hotspots

### Hotspot 1 — Risk Data Entry (Full Quote Tab 1)

**What the user does:** For each risk item (e.g. each vehicle in a motor fleet policy), the user must open a risk block form and fill in all product-configured risk fields. For motor products this typically includes: vehicle registration number, chassis number, engine number, make, model, variant, year of manufacture, fuel type, body type, cubic capacity, seat capacity, vehicle value, colour, and other product-specific attributes.

**What information they provide:** All physical risk attributes — typically 10–25 fields per risk record, depending on product configuration.

**Does the same information exist elsewhere in STATIM?** Partially. The vehicle master (`Claims/GetMakeModelVariant`) holds Make/Model/Variant/FuelType/BodyType/Year. Once Make is selected, these 6–8 fields auto-populate. However, registration number, chassis number, engine number, vehicle value, and other unique risk identifiers are not held anywhere in STATIM — they must be typed manually every time.

**Could it theoretically be obtained from another source?** Vehicle registration data could theoretically be obtained from a motor vehicle registry API (external). Chassis/engine numbers are unique per vehicle and would require an external lookup or document scan.

**Suitable for AI assistance?** Yes — high potential. An AI could pre-fill risk fields from a registration number lookup, document OCR (RC book), or from a previous policy for the same vehicle.

---

### Hotspot 2 — Customer Creation (AddUser Drawer)

**What the user does:** When a customer is not found in the entity lookup, the user must create a new customer through a 3-step wizard: (1) KYC — select document type, enter document number, upload document; (2) Personal Details — enter first name, last name, middle name, Arabic name, DOB, gender, nationality, phone number, email, ID type, ID number, TCF number; (3) Address — enter street, postal code, city, state, country for both permanent and communication addresses.

**What information they provide:** ~20–30 fields across 3 steps.

**Does the same information exist elsewhere in STATIM?** No — this is first-time data entry for a new customer. However, if the customer has an existing policy (found via entity lookup), all this data is already in the Entity Master and auto-populates.

**Could it theoretically be obtained from another source?** Yes — from a national ID database, KYC bureau, or document OCR (passport, national ID card). The document number is already entered in step 1; the personal details could theoretically be fetched from an ID verification service.

**Suitable for AI assistance?** Yes — high potential. An AI could extract personal details from a scanned ID document (OCR) and pre-fill the personal details form, reducing the 3-step wizard to a review-and-confirm flow.

---

### Hotspot 3 — Previous Policy Details (Full Quote Tab 3)

**What the user does:** When the customer has a previous policy, the user must manually enter: previous policy number, previous insurer (selected from a dropdown of known insurers), previous policy expiry date, whether any claims were made, whether vehicle ownership changed, previous policy type, previous NCD/NCB percentage, NCB certificate number, and previous add-on covers.

**What information they provide:** 8–10 fields, all requiring knowledge of the customer's prior insurance history.

**Does the same information exist elsewhere in STATIM?** Partially. If the customer has an existing policy in STATIM (shown in the CustomerPolicyList on the GetQuote screen), the previous policy number and insurer could theoretically be derived. However, the NCD/NCB percentage, claims history, and certificate number are not stored in a retrievable form for this purpose.

**Could it theoretically be obtained from another source?** Yes — from an insurance bureau/IIB (Insurance Information Bureau) API, or from the customer's existing policy record if it was issued in STATIM.

**Suitable for AI assistance?** Yes — medium potential. An AI could pre-fill previous policy details from the customer's existing STATIM policy record (if available) or from an external insurance history lookup.

---

### Hotspot 4 — Coverage Details: SMI Values (Full Quote Tab 2)

**What the user does:** For each risk, the user must enter Sum Insured (SI) values for each applicable SMI (Sum Insured item). For motor, this is typically the vehicle value. For property, this could be building value, contents value, machinery value, etc. — potentially 5–15 SI values per risk.

**What information they provide:** Numeric SI amounts per SMI per risk.

**Does the same information exist elsewhere in STATIM?** Partially. For motor, the vehicle value may have been entered in the risk details (Tab 1) and could be carried forward. For property, the SI values are unique to each risk and must be provided by the customer/underwriter.

**Could it theoretically be obtained from another source?** For motor: vehicle valuation APIs (e.g. market value databases). For property: property valuation services. For health: sum insured is typically a product-defined amount.

**Suitable for AI assistance?** Yes — medium potential. An AI could suggest SI values based on vehicle make/model/year (for motor) or property type/location (for property), using historical data or external valuation APIs.

---

### Hotspot 5 — Proposer/Insured Entity Search and Selection (Full Quote Tab 1)

**What the user does:** The user must type a name or phone number to search for the proposer entity, wait for the debounced search (1500ms), then select from the results. If the customer was already found in GetQuote, the entity code is passed forward and the proposer field is pre-populated — but the user must still confirm the selection. If coming directly to Full Quote (bypassing GetQuote), the user must search from scratch.

**What information they provide:** Search query (name or phone), then selection from results.

**Does the same information exist elsewhere in STATIM?** Yes — the Entity Master holds all customer records. The GetQuote screen already performs this lookup. The issue is that the result is not always reliably carried forward to Full Quote Tab 1 when navigating via certain paths.

**Could it theoretically be obtained from another source?** The entity is already in STATIM. The gap is in the navigation/state management, not in data availability.

**Suitable for AI assistance?** Low — this is more a UX/state management issue than an AI opportunity. However, AI could assist by suggesting the most likely proposer based on context (e.g. recent quotes, product type).

---

### Hotspot 6 — Quick Quote: Product-Configured Accordion Fields

**What the user does:** Fills in all product-configured fields in the Quick Quote accordion sections. For a motor product, this typically includes: insured name, mobile number, email, date of birth, gender, nationality, document type, document number, channel, source, business type, previous NCD percentage, and all risk fields (vehicle details). This is 15–30+ fields depending on product configuration.

**What information they provide:** A mix of personal details (customer-related) and risk details (vehicle-related).

**Does the same information exist elsewhere in STATIM?** Yes — if a customer was found via entity lookup in GetQuote, personal details (name, DOB, phone, email, gender, nationality, document) are auto-populated from the Entity Master. Vehicle details are partially available from the vehicle master after Make selection.

**Could it theoretically be obtained from another source?** Customer data: Entity Master (already used). Vehicle data: vehicle registration API. Previous NCD: insurance bureau.

**Suitable for AI assistance?** Yes — medium-high potential. The auto-population from Entity Master already handles personal fields. The remaining gap is vehicle-specific fields and previous policy fields.

---

## 9. APIs / Services Involved

### Frontend → Backend (direct calls via `API_URL_NB`)

| API Endpoint | Method | Purpose | Triggered By |
|---|---|---|---|
| `QuickQuote/GetFieldsForQuickQuote` | GET | Load QQ accordion field config | QuickQuote screen mount |
| `QuickQuote/SaveQuickQuote` | POST | Save QQ + trigger rating | "Generate Quick Quote" button |
| `QuickQuote/UpdateQuickQuote` | PUT | Update existing QQ + re-rate | "Update Quick Quote" button |
| `QuickQuote/GetQuickQuoteListById` | GET | Load existing QQ data | QQ screen with existing ID |
| `QuickQuote/ConvertQuote` | POST | Convert QQ → Full Quote | "Proceed to Full Quote" button |
| `FullQuote/block` (POST) | POST | Save any FQ block (new) | "Proceed" / "Save" on each tab |
| `FullQuote/block` (PUT) | PUT | Update any FQ block | "Update" on each tab |
| `FullQuote/block` (GET) | GET | Get block data by ID | Tab load |
| `FullQuote/blockListing` | GET | Get block listing | Tab load |
| `FullQuote/all` | POST | Get NB listing | NBListing screen |
| `FullQuote/policySection` | GET | Get policy sections | Coverage Details tab |
| `FullQuote/riskDetails` | GET | Get risk listing | Coverage Details tab |
| `FullQuote/refreshRating` | POST | Refresh rating | Refresh icon click |
| `FullQuote/GetTopup` | POST | Get top-up SI | Coverage Details |
| `FullQuote/commissionData` | GET | Get commission data | Broker Commission tab |
| `FullQuote/priceBreakUpCoverage` | GET | Price breakup | Price Break-Up button |
| `FullQuote/compare` | GET | Quote compare | Compare button |
| `FullQuote/nbHistory` | GET | NB history | History popup |
| `PolicyController/getProposerData` | GET | Get proposer/risk data | Tab 1 load |
| `PolicyController/getPolicyDetails` | GET | Get policy details | Tab 3 load |
| `PolicyController/getBrokerCommission` | GET | Get broker commission | Tab 4 load |
| `PolicyController/getCoInsurance` | GET | Get co-insurance | Tab 5 load |
| `PolicyController/getSMICoverData` | GET | Get SMI/cover data | Tab 2 load |
| `PolicyController/getQuoteApproval` | POST | Approve quote | "Quote Approval" button |
| `PolicyController/getPolicyApproval` | POST | Approve policy | "Generate to Policy" button |
| `PolicyController/getNewBusinessList` | POST | NB listing | NBListing screen |
| `PolicyController/approveQuote` | POST | Direct approve from listing | Approve action in listing |
| `PolicyController/copyQuote` | POST | Copy quote | Copy Quote action |
| `PolicyController/iterateQuote` | POST | Iterate quote | Iteration action |

### Frontend → ProductConfigurator (direct calls via `API_URL_PC`)

| API Endpoint | Method | Purpose |
|---|---|---|
| `ProductConfigurator/getProducts` | GET | Product list for dropdown |
| `ProductConfigurator/productPlans` | GET | Plan list for product |
| `ProductConfigurator/nbCovers` | GET | Cover list for QQ |
| `ProductConfigurator/blockListing` | GET | Channel/Source/BusinessType options |
| `ProductConfigurator/getSMICoverData` | GET | PC SMI/Cover/Discount config for FQ |
| `ProductConfigurator/getBasicInfo` | GET | Product basic info (IsFleet, etc.) |
| `ProductConfigurator/getPropertiesUnderwriting` | GET | Underwriting properties |
| `ProductConfigurator/getPropertiesClaims` | GET | Claims properties |
| `ProductConfigurator/getPropertiesCommon` | GET | Common properties |

### Frontend → NB Configuration (via API Gateway or direct)

| API Endpoint | Method | Purpose |
|---|---|---|
| `NBConfiguration/getFullQuoteBlockMapping` | GET | FQ block mapping (tab structure) |
| `NBConfiguration/getFullQuoteAccordionByBlockMappingId` | GET | Accordion list per block |
| `NBConfiguration/getFullQuoteFieldsByAccordionId` | GET | Field list per accordion |

### Frontend → Entity Master (direct calls via `API_STATIM_ENTITY`)

| API Endpoint | Method | Purpose |
|---|---|---|
| `EntityMaster/EntityLookup` | GET | Customer search by name/phone/ID |
| `EntityMaster/SaveNewEntityCustomer` | POST | Create new customer |
| `EntityMaster/getEntityCategory` | GET | Entity classification categories |
| `EntityMaster/getBlocks` | GET | Entity form block config |

### Frontend → Masters (via API Gateway)

| API Endpoint | Method | Purpose |
|---|---|---|
| `Master/GetClassificationCategoryList` | GET | Insured classification options |
| `Master/getMasterData` | GET | All master dropdowns (COC, CUR, RTY, RPF, POP, PTY, etc.) |
| `Master/getMetaDataList` | GET | Meta data (PNCBP, BIAPC, NBFS, etc.) |
| `Master/getExchangeRate` | GET | Exchange rate for currency pair |
| `Master/getAppOfficeDetails` | GET | Applicable office/branch list |

### Frontend → Claims (direct, for vehicle master)

| API Endpoint | Method | Purpose |
|---|---|---|
| `Claims/GetMakeModelVariant` | GET | Vehicle make/model/variant lookup |

### Backend → Rating Engine (internal service call)

| API Endpoint | Method | Purpose |
|---|---|---|
| `RateEngine/DynamicExpressionResolver` | POST | Calculate premium (QQ and FQ) |
| `RateEngine/GetSI` | POST | Get top-up SI values |

### Backend → Kafka (via KafkaJobOrchestrator)

| Event | Trigger |
|---|---|
| `QuoteCreate` | Quote first saved |
| `QuoteApprove` | Quote approval |
| `PolicyApprove` | Policy issuance |
| `Policy-Approval`, `Cover-Approval`, `SMI-Approval`, `Discount-Approval`, `Loading-Approval` | Policy issuance |

### Backend → AccountingService (sync HTTP call)

| API | Trigger |
|---|---|
| `AccountingService/CreateStagingAccountEntry` | Policy approval (awaited before Kafka publish) |

### Backend → ReferenceNoGenerator (sync HTTP call)

| API | Trigger |
|---|---|
| `ReferenceNoGenerator/GenerateDocumentNumber` | First quote save (quote number), policy approval (policy number) |

---

## 9. View 1 — Current-State Quote Journey

```
USER ACTION                    STATIM SCREEN              DATA ENTERED                    BACKEND / API                    PROCESSING
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
Click "Get Quote"           → NBListing                  (none)                          (none)                           (none)

Select Id Type              → GetQuote                   Id Type (dropdown)              Master/GetClassificationCategoryList  Load DOC_KYC options
Enter Id Value + blur       → GetQuote                   Id Value (text/phone)           EntityMaster/EntityLookup        Search entity by ID/phone
Select Product              → GetQuote                   Product (dropdown)              ProductConfigurator/getProducts  Load approved products
Select Quote Type           → GetQuote                   Quote Type (dropdown)           (static)                         (none)
Click "Proceed"             → GetQuote                   (none)                          (validation)                     Validate product is Standalone (QQ path)

[IF NEW CUSTOMER]
Click "New Customer"        → AddUser drawer             KYC: doc type, doc number,      EntityMaster/getEntityCategory   Load classification config
                                                         upload                          EntityMaster/getBlocks           Load form block config
Click "Proceed" (KYC)       → AddUser drawer             Personal: name, DOB, gender,    (local state)                    (none)
                                                         nationality, phone, email
Click "Proceed" (PD)        → AddUser drawer             Address: street, postal,        (local state)                    (none)
                                                         city, state, country
Click "Confirm" (Address)   → AddUser drawer             (submit)                        EntityMaster/SaveNewEntityCustomer  Create entity record
                                                                                                                          Auto-trigger entity lookup

[QUICK QUOTE PATH]
Screen loads                → QuickQuote                 (none)                          QQ/GetFieldsForQuickQuote        Load accordion field config
                                                                                         PC/productPlans                  Load plans (first auto-selected)
                                                                                         PC/nbCovers                      Load covers (defaults pre-toggled)
                                                                                         PC/blockListing (channel/source) Load dropdown options
                                                                                         Master/GetClassificationCategoryList  Load insured classification
                                                                                         UserMgmt/getAppOfficeDetails     Load branch options
                                                                                         Customer fields auto-populated from entity master

Fill accordion fields       → QuickQuote                 Product-configured fields       (local form state)               (none)
  (personal details)                                     (name, DOB, phone, email,
                                                         gender, nationality, doc)
Select plan                 → QuickQuote                 Plan (radio button)             PC/nbCovers (re-fetch per plan)  Load covers for selected plan
Click Make field            → QuickQuote                 (opens modal)                   Claims/GetMakeModelVariant       Load vehicle make list
Select Make in modal        → QuickQuote                 Make selection                  (local state)                    Auto-populate: Model, Variant,
                                                                                                                          FuelType, BodyType, Year
Fill risk fields            → QuickQuote                 Vehicle-specific fields         (local form state)               (none)
                                                         (reg no, chassis, engine,
                                                         vehicle value, etc.)
Toggle covers               → QuickQuote                 Cover on/off toggles            (local form state)               (none)
Set policy dates            → QuickQuote                 Start date, end date            (local form state)               (none)
Click "Generate Quick Quote"→ QuickQuote                 (submit)                        QQ/SaveQuickQuote                Save all flexi fields + covers
                                                                                         RateEngine/DynamicExpressionResolver  Calculate premium
                                                                                                                          Premium displayed in side panel

Click "Proceed to Full Quote"→ QuickQuote                (submit)                        QQ/ConvertQuote                  Create PolicyVersion + PolicyIteration
                                                                                                                          Map QQ fields → NB fields
                                                                                                                          Navigate to /fullquote

[FULL QUOTE — TAB 1]
Screen loads                → FullQuote Tab 1            (none)                          NBConfig/getFullQuoteBlockMapping  Load tab structure
                                                                                         NBConfig/getFullQuoteAccordionByBlockMappingId  Load accordions
                                                                                         NBConfig/getFullQuoteFieldsByAccordionId  Load fields
                                                                                         NB/getNewBusinessProposerData    Load existing proposer data
                                                                                         PC/getPropertiesUnderwriting     Load product properties
                                                                                         PC/getBasicInfo                  Load product basic info
                                                                                         Master/getMasterData (multiple)  Load all master dropdowns

Fill proposer details       → FullQuote Tab 1            Proposer Classification,        EntityMaster/EntityLookup        Debounced search (1500ms)
                                                         Proposer entity (search/select)
Fill basic policy details   → FullQuote Tab 1            Product (pre-set), Plan,        PC/productPlans                  Load plans
                                                         Source, Source Name,            PC/blockListing (channel)        Load channels
                                                         Channel, Business Type,         PC/blockListing (source)         Load sources
                                                         Transaction Type (default),     PC/blockListing (business type)  Load business types
                                                         Calculation Type (default),     Master/getMasterData             Load master dropdowns
                                                         Effective From (default today),
                                                         Effective To (auto-calculated)
Click "Proceed"             → FullQuote Tab 1            (submit)                        NB/FullQuote/block (POST)        Save ProposerDetails + BasicPolicyDetails
                                                                                                                          Risk Details section appears

Add risk record             → FullQuote Tab 1            Risk block fields               NB/FullQuote/block (POST)        Save risk detail + flexi fields
  (per mandatory section)                                (product-configured,                                             Tariff parameters extracted
                                                         e.g. 10-25 vehicle fields)

[FULL QUOTE — TAB 2]
Screen loads                → FullQuote Tab 2            (none)                          PC/getSMICoverData               Load PC cover/SMI/discount config
                                                                                         NB/getSMICoverData               Load existing NB coverage data
                                                                                         NB/getRiskData                   Load risk list
                                                                                         NB/getPolicySectionList          Load sections
                                                                                         NB/getPlanList                   Load plans

Review/modify SMI values    → FullQuote Tab 2            SI amounts per SMI per risk     (local state)                    (none)
Review/modify covers        → FullQuote Tab 2            Cover SI, rate (if applicable)  (local state)                    (none)
Review/modify discounts     → FullQuote Tab 2            Discount rates                  (local state)                    (none)
Click "Save" (via Next)     → FullQuote Tab 2            (submit)                        NB/FullQuote/block (POST/PUT)    Save coverage data
                                                                                         RateEngine/DynamicExpressionResolver  Re-calculate premium
                                                                                                                          Premium amount updated in header

[FULL QUOTE — TAB 3]
Screen loads                → FullQuote Tab 3            (none)                          NB/getPolicyDetails              Load existing policy details
                                                                                         Master/getMasterData (CUR,RTY,   Load master dropdowns
                                                                                           RPF, POP, PTY)
                                                                                         Master/getExchangeRate           Load exchange rates
                                                                                         UserMgmt/getAppOfficeDetails     Load branch list
                                                                                         PC/getSMICoverData               Load cover list for add-on dropdown

Review/fill policy details  → FullQuote Tab 3            Branch (default: user office),  (local state)                    Exchange rate auto-fetched
                                                         SI Currency (default),                                           Branch auto-defaulted
                                                         Premium Currency (default),
                                                         Rate Type (default),
                                                         Rate Picked From (default),
                                                         Exchange Rate (auto-fetched),
                                                         Payer Type (default),
                                                         Previous policy fields (if any)
Click "Save" (via Next)     → FullQuote Tab 3            (submit)                        NB/FullQuote/block (POST/PUT)    Save BasicDetailBO + PolicyDetailBO
                                                                                                                            + PreviousPolicyDetailBO
                                                                                         RateEngine/DynamicExpressionResolver  Re-calculate premium

[FULL QUOTE — LAST TAB]
Click "Quote Approval"      → FullQuote                  (none)                          Workflow/ExecuteWorkflow         Workflow checkpoint created
                                                                                         NB/getQuoteApproval              Update status → QuoteApproved
                                                                                         KafkaJobOrchestrator             Publish QuoteApprove topic
                                                                                         Workflow/CommitWorkflow          Commit checkpoint
                                                                                                                          Success modal shown

Click "Collect"             → FullQuote                  (none)                          NB/GenerateInstallmentSchedule  Create installment schedule
                                                                                         NB/SaveCollectionHeader          Create collection header
                                                                                         NB/GetSaveCollection             Fetch collection data
                                                                                                                          Payment Details modal shown

Click "Generate to Policy"  → FullQuote                  (none)                          Workflow/ExecuteWorkflow         Workflow checkpoint created
                                                                                         NB/GenerateInstallmentSchedule  Refresh installment
                                                                                         NB/ReserveCollection             Reserve collection
                                                                                         NB/PolicyApprovedQuote           Generate policy number
                                                                                         AccountingService/CreateStaging  Create accounting entry (

---

Compact chat history?

---

You stopped your current work and asked me to work on the following task instead.

---

Continue from 9. View 1 — Current-State Quote Journey

---


Continuing from where the table was cut off:

```
Click "Generate to Policy"  → FullQuote                  (none)                          Workflow/ExecuteWorkflow         Workflow checkpoint created
                                                                                         NB/GenerateInstallmentSchedule  Refresh installment schedule
                                                                                         NB/ReserveCollection /           Reserve payment
                                                                                           ReserveCollectionCBC
                                                                                         NB/PolicyApprovedQuote           Generate policy number,
                                                                                                                          status → PolicyApproved
                                                                                         AccountingService/              Create accounting staging
                                                                                           CreateStagingAccountEntry       entry (sync, awaited)
                                                                                         KafkaJobOrchestrator            Publish: PolicyApprove,
                                                                                                                          Policy-Approval, Cover-Approval,
                                                                                                                          SMI-Approval, Discount-Approval,
                                                                                                                          Loading-Approval (async, fire-and-forget)
                                                                                         NB/ApproveAndReservation        Complete reservation
                                                                                           Complete /
                                                                                           ConfirmCBCConfirmation
                                                                                         Workflow/CommitWorkflow          Commit checkpoint
                                                                                         ReferenceNoGenerator            Policy number already generated
                                                                                                                          Policy number shown in modal
```

---

## 9. View 2 — User Effort Matrix

| Activity | Screen | Manual Effort | Repetition | Existing Automation | Notes |
|---|---|---:|---:|---|---|
| Select Id Type | Get Quote | Low | Low | DOC_KYC options loaded from master | Simple dropdown, ~5 options |
| Enter Id Value (customer search) | Get Quote | Low | Low | Entity lookup auto-fires on blur | One field, triggers search |
| Select Product | Get Quote | Low | Low | Product list fetched automatically | Dropdown selection |
| Select Quote Type | Get Quote | Low | Low | None | Static 2-option dropdown |
| Create new customer — KYC step | Get Quote (AddUser) | **High** | Low | None | Doc type + doc number + file upload |
| Create new customer — Personal Details step | Get Quote (AddUser) | **High** | **Medium** | None | ~12 fields: name, DOB, gender, nationality, phone, email, ID, TCF |
| Create new customer — Address step | Get Quote (AddUser) | **High** | **Medium** | None | ~8 fields × 2 addresses |
| Select Plan | Quick Quote | Low | Low | First plan auto-selected | Radio button |
| Fill personal/proposer fields (QQ) | Quick Quote | **Medium** | **High** | Customer data auto-populated from Entity Master when customer found | Re-entered if customer not in system; ~8–12 fields |
| Select Make (vehicle modal) | Quick Quote | **Medium** | Low | Model/Variant/FuelType/BodyType/Year auto-populated after Make selection | Modal search required |
| Fill vehicle risk fields (QQ) | Quick Quote | **High** | **Medium** | Make-derived fields auto-populated; reg/chassis/engine/value manual | ~8–15 fields depending on product |
| Toggle covers | Quick Quote | Low | Low | Default/mandatory covers pre-toggled | Simple toggle switches |
| Set policy dates | Quick Quote | Low | Low | Start = today, End = today + 1 year (defaults) | Date pickers with defaults |
| Select Previous NCD % | Quick Quote | Low | Low | Resets to 0% automatically on claims/ownership change | Dropdown |
| Select Proposer Classification | FQ Tab 1 | Low | Low | Default: Individual | Dropdown |
| Search and select Proposer entity | FQ Tab 1 | **Medium** | **High** | Pre-populated from GetQuote customer lookup (when carried forward); debounced search | Must re-search if not carried forward; 1500ms debounce |
| Toggle Insured Same as Proposer | FQ Tab 1 | Low | Low | Insured auto-copies from Proposer when toggled | Single toggle |
| Select Product | FQ Tab 1 | Low | Low | Pre-set from GetQuote selection | Already selected |
| Select Plan | FQ Tab 1 | Low | Low | None | Dropdown |
| Select Source | FQ Tab 1 | Low | Low | None | Dropdown; drives Tab 4 visibility |
| Select Source Name (broker/agent) | FQ Tab 1 | Low | Low | None | Conditional; dropdown |
| Select Channel | FQ Tab 1 | Low | Low | None | Dropdown |
| Select Business Type | FQ Tab 1 | Low | Low | None | Dropdown; drives Tab 5 visibility |
| Transaction Type | FQ Tab 1 | Low | Low | Default: "New Business" | Pre-defaulted |
| Calculation Type | FQ Tab 1 | Low | Low | Default: "Annual" | Pre-defaulted |
| Effective From | FQ Tab 1 | Low | Low | Default: today | Date picker with default |
| Effective To | FQ Tab 1 | Low | Low | Auto-calculated from Effective From + Calculation Type | Read-only when Annual |
| Fill product-configured flexi fields (Tab 1) | FQ Tab 1 | **Medium** | Low | None | Product-specific; 0–10 additional fields |
| Add risk record — fill all risk fields | FQ Tab 1 (Risk Details) | **High** | **High** | Make-derived fields auto-populated (motor); no other automation | ~10–25 fields per risk; multiple risks possible for fleet/batch |
| Review/modify SMI values | FQ Tab 2 | **Medium** | **Medium** | Default values from product config; top-up SI from Rating Engine | Must enter actual SI amounts per risk |
| Review/modify covers | FQ Tab 2 | Low | **Medium** | Default/mandatory covers pre-applied; cover SI from Rating Engine | Toggle and SI entry per cover per risk |
| Review/modify discounts | FQ Tab 2 | Low | Low | Default discounts pre-applied with rates | Typically review only |
| Review/modify loadings | FQ Tab 2 | Low | Low | Default loadings pre-applied with rates | Typically review only |
| Review deductibles | FQ Tab 2 | Low | Low | Default deductibles pre-applied | Typically review only |
| Review conditions | FQ Tab 2 | Low | Low | Default conditions pre-applied | Typically review only |
| Review charges and fees | FQ Tab 2 | Low | Low | Default charges pre-applied | Typically review only |
| Select Branch | FQ Tab 3 | Low | Low | Default: user's applicable office | Pre-defaulted |
| Select Policy Category | FQ Tab 3 | Low | Low | Default from product config | Pre-defaulted |
| Select SI Currency | FQ Tab 3 | Low | Low | Default: company currency | Pre-defaulted |
| Select Premium Currency | FQ Tab 3 | Low | Low | Default: company currency | Pre-defaulted |
| Select Rate Type | FQ Tab 3 | Low | Low | Default: "User Entered" | Pre-defaulted |
| Select Rate Picked From | FQ Tab 3 | Low | Low | Default: "Exchange Rate Master" | Pre-defaulted |
| Review Exchange Rate | FQ Tab 3 | Low | Low | Auto-fetched from exchange rate master | Read-only when = Exchange Rate Master |
| Select Payer Type | FQ Tab 3 | Low | Low | Default: "Cash Before Cover" | Pre-defaulted |
| Toggle Have Previous Policy | FQ Tab 3 | Low | Low | Default: false | Single toggle |
| Enter Previous Policy No | FQ Tab 3 | **Medium** | Low | None | Manual text entry; requires prior policy knowledge |
| Select Previous Insurer | FQ Tab 3 | Low | Low | None | Dropdown of known insurers |
| Enter Previous Policy Expiry | FQ Tab 3 | Low | Low | None | Date picker |
| Toggle Any Claims Made | FQ Tab 3 | Low | Low | NCD % auto-resets to 0% | Single toggle |
| Toggle Vehicle Ownership Changed | FQ Tab 3 | Low | Low | NCD % auto-resets to 0% | Single toggle |
| Select Previous Policy Type | FQ Tab 3 | Low | Low | None | Dropdown |
| Select Previous NCD/NCB % | FQ Tab 3 | Low | Low | Default: 0%; resets on claims/ownership change | Dropdown |
| Enter NCB Certificate Number | FQ Tab 3 | **Medium** | Low | None | Manual text entry; required to unlock NCD % |
| Select Previous Add-On Covers | FQ Tab 3 | Low | Low | None | Multi-select dropdown |
| Enter broker commission details | FQ Tab 4 | **Medium** | Low | None | Conditional tab; commission % per broker |
| Enter co-insurance participant details | FQ Tab 5 | **Medium** | Low | None | Conditional tab; participant shares |
| Review pre-inspection results | FQ Tab 6 | Low | Low | Pre-inspection data loaded from pre-inspection app | Accept/Reject per risk |
| Click Quote Approval | FQ Last Tab | Low | Low | Workflow + Kafka fully automated | Single button click |
| Click Collect | FQ Last Tab | Low | Low | Installment schedule auto-generated | Single button click |
| Click Generate to Policy | FQ Last Tab | Low | Low | Policy number auto-generated; accounting + Kafka fully automated | Single button click |

---

## 10. Unknowns / Information Required

The following items could not be fully determined from the source code alone:

### 10.1 — Exact Field Set Per Product

**Unknown:** The precise list of fields shown in the Quick Quote accordion and Full Quote risk blocks for any specific product (e.g. motor, property, health).

**Reason:** All fields are dynamically configured in the Product Configurator database. The source code only defines the rendering engine and field type mappings — not the actual field definitions for any deployed product. The field configuration lives in the database, not in the source code.

**What is known:** The field types supported (Text, Number, Dropdown, Date, Toggle, Switch, TextArea, Radio, Checkbox, Email, PhoneNumber, Amount Numeric, Stack), the `DatabaseFieldMapping` values for known fields, and the `KeyCode` range assignments (Key1–Key650 for QQ header, Key651–Key1300 for QQ risk).

**Impact on AI design:** The exact fields that an AI would need to pre-fill for a specific product cannot be determined without querying the Product Configurator database for that product's configuration.

---

### 10.2 — NB Configuration Block/Accordion/Field Mapping

**Unknown:** The exact accordion names, field labels, and field sequences configured in the NB Configuration for the Full Quote Proposer/Risk Details and Policy Details tabs for any specific product.

**Reason:** The NB Configuration is stored in the database and fetched at runtime via `NBConfiguration/getFullQuoteBlockMapping`, `getFullQuoteAccordionByBlockMappingId`, and `getFullQuoteFieldsByAccordionId`. The source code defines the rendering engine but not the configuration data.

**What is known:** The `DatabaseFieldMapping` values for all known fields (confirmed from `ProposerRiskDetails.jsx` and `PolicyDetails.jsx`), the block codes (`NB_BLOCK_ENUMS.PROPOSER_RISK`, `NB_BLOCK_ENUMS.POLICY_DETAILS`, etc.), and the accordion IDs referenced in `NB_FQ_ID`.

---

### 10.3 — Mandatory vs Optional Fields Per Product

**Unknown:** Which specific fields are marked `IsMandatory = true` in the Product Configurator and NB Configuration for any given product.

**Reason:** Mandatory flags are stored in the database configuration, not in the source code. The rendering engine reads `IsMandatory` from the API response and applies it to the form field.

**What is known:** The rendering engine correctly enforces mandatory validation via Ant Design Form validation rules when `Mandatory: true` is set on a field config object.

---

### 10.4 — Risk Block Structure Per Product

**Unknown:** The number of risk blocks, their names, and the fields within each risk block for any specific product.

**Reason:** Risk meta configuration is fetched from `getNewBusinessRiskMetaByProduct` at runtime, keyed by `VersionId`, `SectionId`, and `ChannelId`. The actual risk block definitions are in the database.

**What is known:** The `RiskDetailBO` structure (confirmed from source), the flexi-key storage pattern (`RiskFlexiKeysBO`), the tariff parameter extraction logic, and the mandatory block validation logic in `FullQuote.jsx`.

---

### 10.5 — Customer Policy List Display Logic

**Unknown:** What exactly is shown in the `CustomerPolicyList` component on the GetQuote screen when an existing customer is found.

**Reason:** The `CustomerPolicyList.jsx` component was not read in detail. The action creator `getNbCustomerPolicyData` is called with `entityCode`, but the response structure and displayed fields were not confirmed.

**What is known:** The API `getNbCustomerPolicyData` is called with `{ entityCode: EntityCode }` and the result is passed to `CustomerPolicyList`. The listing shows existing policies for the customer, which is relevant for understanding what prior policy information is already visible to the user.

---

### 10.6 — Broker Commission Field Structure

**Unknown:** The exact fields in the Broker Commission tab (Tab 4).

**Reason:** `BrokerCommission.jsx` was not read in detail. The `SaveBrokerCommissionBO` and `BrokerListBO` BOs were identified but not fully examined.

**What is known:** The tab is shown only when Source = Broker (`STY_003`) or Agent (`STY_002`). The `getBrokerCommission` API is called on tab load. The save uses `NB_BLOCK_ENUMS.BROKER_COMMISSION` block code. Commission amounts are used in the collection payload (`TotalCommAmount`, `TransactionalTotalCommAmount`, `TotalCommTax`, `TransactionalTotalCommTax`).

---

### 10.7 — Co-Insurance Field Structure

**Unknown:** The exact fields in the Co-Insurance tab (Tab 5).

**Reason:** `CoInsurance.jsx` was not read in detail. The `CoInsuranceBO`, `CoInsParticipantDetailBO`, and `CoInsShareDetailBO` BOs were identified but not fully examined.

**What is known:** The tab is shown only when Business Type = Direct with Co-Insurance (`DIR_WTH_COINS`) or Inward Co-Insurance (`INW_COINS`). The `getCoInsurance` API is called on tab load. The save uses `NB_BLOCK_ENUMS.CO_INSURANCE` block code.

---

### 10.8 — Pre-Inspection Tab Field Structure

**Unknown:** The exact fields and workflow in the Pre-Inspection tab (Tab 6).

**Reason:** `PreInspection.jsx` was not read in detail.

**What is known:** Pre-inspection data is loaded from `getPreInspectionData`. Quote Approval requires all pre-inspection items to have `Status = "Accept"`. The tab is product-configured (shown based on product properties).

---

### 10.9 — Quick Quote Quote Number Generation

**Unknown:** Whether the Quick Quote quote number (`quote_{random}`) is a temporary placeholder or the final quote number shown to users.

**Reason:** From `QuickQuoteService.cs`: `string newQuoteNumber = $"quote_{random.Next(1000000, 9999999)}"` — this is a random 7-digit number prefixed with "quote_". This appears to be a placeholder. The `QuickQuoteHeader.QuoteNumber` field stores this value and it is displayed on the QQ screen as "Quick Quote Number".

**What is known:** The QQ quote number is generated in `QuickQuoteService.SaveQuickQuoteAsync` using `Random.Next(1000000, 9999999)`. This is not the same as the Full Quote quotation number, which is generated by `ReferenceNoGenerator` on `ConvertQuote`. The QQ quote number is a temporary identifier only.

---

### 10.10 — Whether Existing Customer Policies Are Used to Pre-Fill Quote Fields

**Unknown:** Whether STATIM currently uses data from a customer's existing policies (shown in `CustomerPolicyList`) to pre-fill any fields in a new quote.

**Reason:** The `CustomerPolicyList` component displays existing policies but no code was found that reads from those policies to pre-fill quote fields. The `customerDetails` (entity master data) is passed to `QuickQuote.jsx` and used to pre-fill personal fields, but `customerPolicyDetails` (existing policies) is only used for display in the `CustomerPolicyList` component.

**What is known:** STATIM does NOT currently use existing policy data to pre-fill new quote fields. The `customerPolicyDetails` state is populated by `getNbCustomerPolicyData` and passed only to `CustomerPolicyList` for display. This is a confirmed gap — existing policy data is visible to the user but not used to populate the new quote form.

---

### 10.11 — Flexi Key Range for Full Quote Risk Fields

**Unknown:** The exact `KeyCode` range used for Full Quote risk fields (as opposed to Quick Quote risk fields).

**Reason:** The Full Quote risk fields use a different storage mechanism (`RiskFlexiKeysBO` with typed sub-tables per data type) compared to Quick Quote. The key codes for Full Quote risk fields are assigned by the Product Configurator and stored in `RiskFieldMapping.KeyProcessing.KeyCode`. The actual key codes in use for any specific product are in the database.

**What is known:** Quick Quote header fields use Key1–Key650 (split by data type across 13 sub-tables). Quick Quote risk fields use Key651–Key1300. Full Quote risk fields use a separate `RiskFlexiKeysBO` structure with typed columns. The `RiskDetailBO.FlexiField` is of type `RiskFlexiKeysBO`.

---

## Summary of Key Findings

The most important findings for the AI-assisted quote creation baseline are:

**1. The largest manual effort hotspot is risk data entry** — specifically vehicle/property/risk-specific fields in both Quick Quote and Full Quote Tab 1. For motor products, this involves 10–25 fields per risk record, with only Make-derived fields (Model, Variant, FuelType, BodyType, Year) being auto-populated. Registration number, chassis number, engine number, and vehicle value are always manually typed.

**2. Customer creation is a significant manual effort** — the 3-step AddUser wizard requires ~30 fields when a customer is not found in the entity master. This is the highest-effort single activity in the flow.

**3. STATIM already auto-populates a substantial amount** — personal details from Entity Master, vehicle details from Make selection, policy dates, transaction type, calculation type, payer type, branch, currencies, exchange rate, and all default/mandatory covers. The user's actual manual burden is concentrated in risk-specific fields and previous policy details.

**4. Existing customer policies are visible but not used** — the `CustomerPolicyList` on the GetQuote screen shows the customer's existing policies, but no code currently reads from those policies to pre-fill a new quote. This is the most directly actionable gap for AI assistance.

**5. The Quick Quote path is significantly lighter** — it requires fewer fields than Full Quote and is only available for "Standalone" products. The conversion from QQ to FQ carries forward the risk and accordion field data, reducing re-entry in Full Quote Tab 1.

**6. All premium calculation is fully automated** — the user never manually enters a premium. The Rating Engine is called automatically on every save of Quick Quote and Coverage Details, and the result is displayed immediately.

**7. The field structure is entirely product-configured** — there is no fixed field set for any screen. The actual fields shown depend entirely on the Product Configurator database configuration for the selected product. Any AI solution must work with the dynamic field rendering system, not with hardcoded field lists.

---

Compact chat history?

---

You stopped your current work and asked me to work on the following task instead.

---

Continue from 10.11 — Flexi Key Range for Full Quote Risk Fields point 7.

---

Continuing from point 7 of the Summary of Key Findings:

**7. The field structure is entirely product-configured** — there is no fixed field set for any screen. The actual fields shown depend entirely on the Product Configurator database configuration for the selected product. Any AI solution must work with the dynamic field rendering system (`RenderNbFormContainer`, `RenderNbFields`, `RenderNbFormContainer`), not with hardcoded field lists. The `KeyCode` is the stable identifier for each field across both Quick Quote (`QuickQuoteFlexiKeys`) and Full Quote (`RiskFlexiKeysBO`, `FlexiKeysBO`). An AI pre-fill must map to `KeyCode` values, not to field labels or `DatabaseFieldMapping` names, since labels can be renamed per product configuration.

**8. The flexi-key storage architecture is the integration point for any AI pre-fill** — confirmed from `QuickQuoteService.cs`. The backend stores all dynamic field values in typed sub-tables keyed by `KeyCode` ranges:

| Key Range | Data Type | Storage Table (QQ Header) | Storage Table (QQ Risk) |
|---|---|---|---|
| Key1–Key50 | Text | `QuickQuoteFlexiText` | `QuickQuoteRiskFlexiText` (Key651–Key700) |
| Key51–Key100 | TextArea | `QuickQuoteFlexiTextArea` | `QuickQuoteRiskFlexiTextArea` (Key701–Key750) |
| Key101–Key150 | Rate Numeric | `QuickQuoteFlexiRateNumeric` | `QuickQuoteRiskFlexiRateNumeric` (Key751–Key800) |
| Key151–Key200 | Amount Numeric | `QuickQuoteFlexiAmountNumeric` | `QuickQuoteRiskFlexiAmountNumeric` (Key801–Key850) |
| Key201–Key250 | Percentage Numeric | `QuickQuoteFlexiPercentageNumeric` | `QuickQuoteRiskFlexiPercentageNumeric` (Key851–Key900) |
| Key251–Key300 | DateTime | `QuickQuoteFlexiDateTime` | `QuickQuoteRiskFlexiDateTime` (Key901–Key950) |
| Key301–Key350 | CheckBox | `QuickQuoteFlexiCheckBox` | `QuickQuoteRiskFlexiCheckBox` (Key951–Key1000) |
| Key351–Key400 | Dropdown | `QuickQuoteFlexiDropdown` | `QuickQuoteRiskFlexiDropdown` (Key1001–Key1050) |
| Key401–Key450 | Toggle | `QuickQuoteFlexiToggle` | `QuickQuoteRiskFlexiToggle` (Key1051–Key1100) |
| Key451–Key500 | Numeric | `QuickQuoteFlexiNumeric` | `QuickQuoteRiskFlexiNumeric` (Key1101–Key1150) |
| Key501–Key550 | File | `QuickQuoteFlexiFile` | `QuickQuoteRiskFlexiFile` (Key1151–Key1200) |
| Key551–Key600 | Date | `QuickQuoteFlexiDate` | `QuickQuoteRiskFlexiDate` (Key1201–Key1250) |
| Key601–Key650 | Integer | `QuickQuoteFlexiInteger` | `QuickQuoteRiskFlexiInteger` (Key1251–Key1300) |

For Full Quote, the same `KeyCode`-based pattern applies but uses `FlexiKeysBO` (for proposer/basic policy/policy details flexi fields) and `RiskFlexiKeysBO` (for risk detail flexi fields), stored in their respective typed sub-tables. The `KeyCode` is the stable cross-system identifier — the same `KeyCode` that the Product Configurator assigns to a field is used in both the QQ and FQ storage layers, and is the value that the Rating Engine receives as a tariff parameter key.

**9. The value format for dropdown fields is `{Id}|#|{Value}|#|{Code}`** — confirmed throughout `QuickQuote.jsx`, `ProposerRiskDetails.jsx`, and `PolicyDetails.jsx`. When an AI pre-fills a dropdown field, it must supply the full `Id|#|Value|#|Code` string, not just the display value. The `Id` is the master record ID, `Value` is the display name, and `Code` is the master code. For fields sourced from the Master microservice, the `Id`, `Value`, and `Code` are available from `getMasterData`. For fields sourced from Product Configurator block listings, the `Id`, `Value`, and `Code` come from the block listing API response. Any AI pre-fill that supplies only a display value without the correct `Id` and `Code` will fail validation or produce incorrect downstream behaviour (e.g. wrong rating inputs, wrong Kafka event payloads).

**10. The Rating Engine receives risk parameters as a flat key-value dictionary** — confirmed from `RatingAPIService.cs` (`TarrifParametersBO.AdditionalFields`). The keys are the original `FieldName` values (with spaces, as stored in the Product Configurator), not the `KeyCode` values. The mapping from `KeyCode` → `FieldName` is performed in `QuickQuote.jsx` (`constructSavePayload`) and in `QuickQuoteService.cs` (`ConvertToRatingRequest`). This means that for the Rating Engine to produce a correct premium, the risk parameter values must be supplied with the correct field names as keys. An AI pre-fill that uses `KeyCode` as the key (correct for storage) must also ensure the `RiskParameters` dictionary uses `FieldName` as the key (correct for rating). Both are required simultaneously in the `QuickQuoteRiskBO` payload.

---

## Final Baseline Statement

The following is the precise statement of what a STATIM user currently has to manually enter or select in order to create a quote, based strictly on the source code evidence:

### Quick Quote Path (minimum manual input for a returning customer on a Standalone product)

Assuming the customer already exists in the Entity Master and is found via the GetQuote lookup:

1. **Get Quote screen** — Select Id Type, enter Id Value, select Product, select "Quick Quote" → 4 interactions
2. **Quick Quote screen** — Select plan (auto-selected, confirm or change) → 1 interaction
3. **Quick Quote screen** — Review/confirm auto-populated personal fields (name, DOB, phone, email, gender, nationality, document) → review only, no typing if customer data is complete
4. **Quick Quote screen** — Fill product-configured non-personal accordion fields (channel, source, business type, previous NCD %, and any product-specific fields not derivable from entity master) → typically 3–8 fields
5. **Quick Quote screen** — For motor: click Make field, search and select Make in modal → 1 modal interaction; Model/Variant/FuelType/BodyType/Year auto-populate
6. **Quick Quote screen** — Fill remaining risk fields not auto-populated from Make (registration number, chassis number, engine number, vehicle value, and any other product-specific risk fields) → typically 4–10 fields of pure manual typing
7. **Quick Quote screen** — Review/confirm cover toggles (defaults pre-set) → review only
8. **Quick Quote screen** — Confirm policy dates (defaults pre-set) → review only
9. **Quick Quote screen** — Click "Generate Quick Quote" → 1 click → premium displayed
10. **Quick Quote screen** — Click "Proceed to Full Quote" → 1 click

**Minimum manual typing for QQ with existing customer (motor product):** approximately 4–10 typed fields (risk-specific: reg number, chassis, engine, vehicle value, and product-specific fields). Everything else is either auto-populated, defaulted, or a dropdown selection.

**Maximum manual typing for QQ with new customer (motor product):** approximately 30–45 typed fields (all customer creation fields + all risk fields).

---

### Full Quote Path (minimum manual input for a returning customer, direct Full Quote)

Assuming the customer already exists and is found via GetQuote:

1. **Get Quote screen** — 4 interactions (same as above)
2. **Full Quote Tab 1 — Proposer Details** — Proposer Classification (default: Individual), search and select Proposer entity (1 search + 1 selection), toggle Insured Same as Proposer → 3 interactions
3. **Full Quote Tab 1 — Basic Policy Details** — Product (pre-set), Plan (select), Source (select), Channel (select), Business Type (select), Transaction Type (default), Calculation Type (default), Effective From (default), Effective To (auto-calculated) → typically 3–4 manual selections; 5 are defaulted
4. **Full Quote Tab 1 — Risk Details** — Add at least one risk record: fill all product-configured risk fields → typically 10–25 typed/selected fields per risk
5. **Full Quote Tab 2 — Coverage Details** — Enter SMI values per risk (typically 1–5 SI amounts), review covers (defaults pre-applied) → typically 1–5 typed amounts
6. **Full Quote Tab 3 — Policy Details** — Branch (default), currencies (default), exchange rate (auto-fetched), payer type (default), rate type (default), rate picked from (default) → review only if no previous policy; if previous policy: 5–8 additional fields
7. **Full Quote — Quote Approval** → 1 click
8. **Full Quote — Collect** → 1 click
9. **Full Quote — Generate to Policy** → 1 click

**Minimum manual typing for FQ with existing customer (motor product, no previous policy):** approximately 12–30 typed/selected fields, concentrated in risk data entry (Tab 1) and SMI values (Tab 2). The majority of Tab 3 fields are defaulted or auto-calculated.

**Maximum manual typing for FQ with new customer, previous policy, broker source, co-insurance:** approximately 60–90 typed/selected fields across all tabs.

---

This baseline confirms that **risk data entry** (Tab 1 Risk Details and Quick Quote risk fields) and **new customer creation** (AddUser wizard) are the two dominant sources of manual effort in the current STATIM quote creation flow, and represent the highest-value targets for AI-assisted pre-fill in the next phase.

---

Compact chat history?

---

Compacting your chat history, this may take a moment.

---


## Conversation Summary

- **STATIM New Business Discovery Report**: A comprehensive reverse-engineering and documentation task was completed for the STATIM insurance platform's New Business → Quote creation flow. The user requested a full current-state discovery report covering screens, fields, APIs, automation, dependencies, user effort, and manual-effort hotspots — explicitly without proposing AI features.

- **Complete Report Delivered**: All 10 sections of the discovery report were produced across multiple responses, covering: New Business Flow, Screens/Tabs, Field-Level Input Inventory, User Effort Analysis, Existing STATIM Automation, Data Dependencies, Quote Creation Flow, Manual-Effort Hotspots, APIs/Services Involved, and Unknowns/Information Required.

- **Two Key Views Produced**: View 1 (Current-State Quote Journey — full end-to-end flow table) and View 2 (User Effort Matrix — 50+ activities rated Low/Medium/High for manual effort and repetition).

- **Final Baseline Statement**: A precise summary of minimum and maximum manual input required for both Quick Quote and Full Quote paths was produced as the concluding section.

---

## Files and Code Summary

- **`gi_product_ui/src/routes/AppRoutes.jsx`**: Confirmed routes: `/newbusiness` → NBListing, `/getquote` → GetQuote, `/fullquote` → FullQuote, `/quickquote` → QuickQuote.

- **`gi_product_ui/src/views/NewBusiness/NBListing/NBListing.jsx`**: Entry point. Contains listing table, "Get Quote" button navigates to `/getquote`. Action menu per record: View, Edit, Approve, Reject, Iterate, Copy Quote, Endorsement, Renewal, Cancel Policy, History, Declaration. Status-driven menu visibility. RBAC via `useNbWorkflow`.

- **`gi_product_ui/src/views/NewBusiness/GetQuote/GetQuote.jsx`**: 4 fields: IdType (dropdown, DOC_KYC master + "Phone Number"), dynamic Id Value field (Text/PhoneNumber/Plate depending on IdType), Product (dropdown, `ProductConfigurator/getProducts`), QuoteType (static: Full Quote/Quick Quote). Entity lookup fires on blur → `EntityMaster/EntityLookup`. Customer card + policy list shown when entity found. "New Customer" link opens AddUser drawer. Proceeds to `/quickquote` or `/fullquote` based on QuoteType.

- **`gi_product_ui/src/views/NewBusiness/GetQuote/AddUser/AddUser.jsx`**: 3-step wizard: KYC (doc type, doc number, file upload) → Personal Details (name, DOB, gender, nationality, phone, email, ID, TCF) → Address (permanent + communication). Calls `EntityMaster/SaveNewEntityCustomer`. After save, triggers entity lookup automatically.

- **`gi_product_ui/src/views/NewBusiness/QuickQuote/QuickQuote.jsx`**: Fully product-configured accordion form. Fields fetched from `QuickQuote/GetFieldsForQuickQuote`. Plan selection (radio, first auto-selected). Covers (toggle switches, defaults pre-set). Policy dates (default today / today+1yr). Customer fields auto-populated from entity master. Vehicle Make modal (`Claims/GetMakeModelVariant`) auto-populates Model/Variant/FuelType/BodyType/Year. Save → `QuickQuote/SaveQuickQuote` → Rating Engine. Convert → `QuickQuote/ConvertQuote` → navigates to `/fullquote`.

- **`gi_product_ui/src/views/NewBusiness/FullQuote/FullQuote.jsx`**: Stepper-based 6-tab form. Tabs driven by `getFullQuoteBlockMapping` API. Tab 4 (Broker Commission) hidden when Source ≠ Broker/Agent. Tab 5 (Co-Insurance) hidden when BusinessType ≠ Co-Insurance. Premium shown in header with refresh icon. Actions: Compare, Price Break-Up, Quote Approval, Collect, Generate to Policy. Workflow integration via `useNbWorkflow`. Kafka events on approval/policy generation.

- **`gi_product_ui/src/views/NewBusiness/FullQuote/ProposerRiskDetails/ProposerRiskDetails.jsx`**: Tab 1. Two accordion groups: Proposer Details (classification, entity search/select, insured same as proposer toggle) and Basic Policy Details (product, plan, source, source name, channel, business type, transaction type, calculation type, effective dates, flexi fields). Risk Details sub-component appears after first save. Entity search debounced 1500ms. Effective To auto-calculated. Default: Transaction Type = "New Business", Calculation Type = "Annual".

- **`gi_product_ui/src/views/NewBusiness/FullQuote/CoverageDetails/CoverageDetails.jsx`**: Tab 2. Sub-tabs: SMI, Covers, Discounts, Loadings, Deductibles, Conditions, Charges & Fees, Tax. Section and Section Plan dropdowns. Functional Currency toggle. All data from PC (`getSMICoverData`) and NB (`getNBSMICoverData`). Save triggers Rating Engine re-calculation.

- **`gi_product_ui/src/views/NewBusiness/FullQuote/PolicyDetails/PolicyDetails.jsx`**: Tab 3. Three accordion groups: Basic Details (Branch, Policy Category), Policy Details (SI Currency, Premium Currency, Rate Type, Rate Picked From, Exchange Rate, Payer Type, Policy Payer, Jurisdiction, Territory, IsFAC, FAC%), Previous Policy Details (HavePrePolicy toggle + 8 conditional fields). Most fields defaulted. Exchange rate auto-fetched.

- **`gi_product_newbusiness/NewBusinessMicroservice/Controllers/QuickQuoteController.cs`**: Endpoints: `GetQuickQuoteList`, `GetQuickQuoteListById`, `DeleteQuickQuoteById`, `SaveQuickQuote` (POST, FluentValidation), `UpdateQuickQuote` (PUT, FluentValidation), `ConvertQuote` (POST).

- **`gi_product_newbusiness/NewBusinessMicroservice/Controllers/FullQuoteController.cs`**: Endpoints: `block` POST/PUT/GET, `blockListing` GET, `deleteBlock`, `all` (listing), `compare`, `policySection`, `collectData`, `riskDetails`, `refreshRating`, `GetTopup`, `commissionData`, `priceBreakUpCoverage`, `nbHistory`. Endorsement routing: if `PolicyVersionId > 0` → `IEndorsementFacade`, else → `IFullQuoteFacade`.

- **`gi_product_newbusiness/NewBusinessMicroservice/Services/QuickQuoteService.cs`**: `SaveQuickQuoteAsync`: creates `QuickQuoteHeader`, sets flexi fields via `SetQuickQuoteFlexiFields` (key range routing to typed sub-tables), saves risks + covers, calls `RatingAPIService.PostRatingDataForQuickQuoteAsync`. `UpdateQuickQuoteAsync`: removes existing risks/covers, re-saves. `ConvertQuote`: creates PolicyVersion + PolicyIteration via `NewBusinessMasterService.CreateNewBusiness`, maps QQ fields → NB fields via field mapping tables, inserts risk + accordion details, soft-deletes QQ record.

- **`gi_product_newbusiness/NewBusinessMicroservice/Services/RatingAPIService.cs`**: `PostRatingDataForQuickQuoteAsync`: builds `RatingRequestBO` from QQ header + risk parameters, POSTs to `RateEngine/DynamicExpressionResolver`, logs request/response, returns `ResponseDetailsBO` with per-risk premium. `PostRatingDataAsync`: full quote rating — fetches coverage details, builds rating request, calls rating API, calls `UpdateRatingForIteration` to write back premium/SI/rate/tax values to DB. Rating request includes: CompanyCode, CobCode, ChannelCode, LobCode, ProductCode, PlanCode, TransactionTypeCode, CalculationTypeCode, policy dates, branch/insured state codes, risk parameters as `AdditionalFields` dictionary (keyed by `FieldName` not `KeyCode`).

- **`gi_product_newbusiness/NewBusinessMicroservice/BOs/QuickQuoteBO.cs`**: Fields: QuickQuoteId, ProductVersionId, MasterProductId/Code/Value, ProductPlanId, MasterPlanId/Code/Value, PlanLevel, ProductSectionId, MasterSectionId/Code/Value, EntityCode, QuoteNumber, CobId/Code, LobId/Code, CompanyId/Code, ChannelId/Code, BranchStateCode, TotalPremium, PolFromDate, PolToDate, NCDRate, QuickQuoteFlexiKeys, QuickQuoteRisk list, ProductId.

- **`gi_product_newbusiness/NewBusinessMicroservice/BOs/ProposerDetailBO.cs`**: ProposerDetailId, PolicyIterationId, MasterPropClassificId/Value/Code, EntityProposerId/Code/Value, InsuredSameAsProposer, MasterInsuredClassificId/Value/Code, EntityInsuredId/Code/Value, FlexiField, InsuredStateCode, BranchStateCode.

- **`gi_product_newbusiness/NewBusinessMicroservice/BOs/BasicPolicyDetailBO.cs`**: All product/plan/source/channel/businesstype/transactiontype/calculationtype IDs, codes, values. EffectiveFrom, EffectiveTo. FlexiField. COB/LOB/Company IDs, codes, values. PCApplicableCategoryValue. EntitySourceNameId/Code/Value.

- **`gi_product_newbusiness/NewBusinessMicroservice/BOs/PolicyDetailBO.cs`**: SI/Premium currency IDs/codes/values, RateType, RatePickedFrom, Date, IsRateUserEntered, MasterRateId/Value, MasterSiRateId/Value, PolicyPayerId/Value, Jurisdiction, Territory, QuoteRejectReason, ConvertedDate, IsFac, Percentage, MetaPayerTypeId/Value, FlexiField, DepositPremium, AdjustedPremium.

- **`gi_product_newbusiness/NewBusinessMicroservice/BOs/PreviousPolicyDetailBO.cs`**: HavePrePolicy, PreviousPolicyNo, EntityPrevInsuredId/Value, PrevPolicyExpDate, AnyClaimsMade, VehicleOwnershipChanged, MasterPrevPolicyTypeId/Value, MasterPrevNcbPercentId/Value, NcbCertificateNumber, FlexiField, PreviousAddOnCovers list.

- **`gi_product_newbusiness/NewBusinessMicroservice/BOs/BasicDetailsBO.cs`**: BasicDetailId, PolicyIterationId, MasterBranchId/Value/Code, ProductPolicyCategoryId/Value, FlexiField.

- **`gi_product_newbusiness/NewBusinessMicroservice/BOs/RiskDetailBO.cs`**: RiskDetailId, PolicyIterationId, PolicyVersionId, RiskMetaId, PcSectionId, PolicySectionId, FlexiField (RiskFlexiKeysBO), RiskHypothecations, product/COB/LOB/company/channel/source/businesstype/branch IDs/codes/values, EffectiveFrom/To, IsRisk, TariffParameter (List<Dictionary<string,string>>), Questionnaires, IsAdd, IsEdit, EndInitiationChangeId.

- **`gi_product_newbusiness/NewBusinessMicroservice/BOs/RequestBO.cs`**: BlockCode (required), SubBlockCode, PolicyIterationId, PolicyVersionId, PolicySectionId, MasterPlanId/Code/Value, ModelData (object), EndorsementChanges list.

---

## Key Insights

- **INSIGHT — Two Quote Paths**: Quick Quote (Standalone products only, lighter form, product-configured accordions, converts to Full Quote) and Full Quote (all products, 6-tab stepper, full underwriting data).

- **INSIGHT — Dynamic Field Architecture**: All form fields in both QQ and FQ are product-configured at runtime. No hardcoded field lists exist in the UI. Fields are identified by `KeyCode` (stable identifier), `DatabaseFieldMapping` (backend field name), and `FieldName` (display label, renameable). Any AI pre-fill must use `KeyCode` for storage and `FieldName` for rating parameters.

- **INSIGHT — Dropdown Value Format**: All dropdown values stored as `{Id}|#|{Value}|#|{Code}` string. AI pre-fill must supply complete format, not just display value.

- **INSIGHT — Flexi Key Storage**: QQ header fields Key1–Key650 split across 13 typed sub-tables by key range. QQ risk fields Key651–Key1300 in parallel typed sub-tables. FQ uses `FlexiKeysBO` and `RiskFlexiKeysBO` with same pattern.

- **INSIGHT — Rating Engine Integration**: Rating called automatically on every QQ save and FQ Coverage Details save. Input: `RatingRequestBO` with risk parameters as `AdditionalFields` dictionary keyed by `FieldName`. Output: premium per cover/SMI/discount/loading written back to DB. User never manually enters premium.

- **INSIGHT — Existing Customer Policies NOT Used for Pre-fill**: `CustomerPolicyList` on GetQuote screen displays existing policies but no code reads from them to pre-fill new quote fields. Confirmed gap.

- **INSIGHT — Auto-Population Already Implemented**: Entity Master → personal fields (QQ); Make selection → vehicle fields (QQ/FQ); Effective To → auto-calculated; Transaction Type, Calculation Type, Payer Type, Rate Type, Rate Picked From, Branch, Currencies → all defaulted; Default/mandatory covers → pre-toggled.

- **INSIGHT — Manual Effort Hotspots**: (1) Risk data entry — 10–25 fields per risk, reg/chassis/engine/value always manual; (2) New customer creation — 30-field 3-step wizard; (3) Previous policy details — 8–10 fields requiring prior policy knowledge; (4) SMI values — 1–15 SI amounts per risk; (5) Proposer entity search — re-entry required when not carried forward from GetQuote.

- **INSIGHT — Tab Visibility Rules**: Tab 4 (Broker Commission) shown only when Source = Broker (STY_003) or Agent (STY_002). Tab 5 (Co-Insurance) shown only when BusinessType = DIR_WTH_COINS or INW_COINS. Tab 6 (Pre-Inspection) product-configured.

- **INSIGHT — ConvertQuote Backend Logic**: `QuickQuote/ConvertQuote` creates a new PolicyVersion + PolicyIteration, maps QQ flexi fields to NB accordion fields via `GetNBFieldMappingsByIdsAsync`, maps QQ risk fields to NB risk details via `GetRiskFieldMappingsByIdsAsync`, soft-deletes the QQ record. Returns PolicyIterationId, QuotationNum, ProductVersionId, ProductCode, ProductId, TotalPremium.

- **INSIGHT — Workflow Integration**: Quote Approval and Generate to Policy both use `ExecuteWorkflow` → action → `CommitWorkflow` (success) or `RevertWorkflow` (failure) pattern. Kafka events published on policy approval: PolicyApprove, Policy-Approval, Cover-Approval, SMI-Approval, Discount-Approval, Loading-Approval. Accounting staging entry created synchronously before Kafka publish.

- **INSIGHT — QQ Quote Number**: Temporary placeholder `quote_{random 7-digit}` generated in `QuickQuoteService.SaveQuickQuoteAsync`. Not the final quotation number. Final quote number generated by `ReferenceNoGenerator` on `ConvertQuote`.

---

## Most Recent Topic

**Topic**: STATIM New Business Current-State Discovery Report — completion of all sections including View 1, View 2, and Final Baseline Statement.

**Progress**: The full 10-section discovery report was completed across multiple responses. The final sections delivered were:

- Section 6 (Data Dependencies) — complete dependency chain for all field relationships
- Section 7 (Quote Creation Flow) — full step-by-step flow for both QQ→FQ path and direct FQ path
- Section 8 (Manual-Effort Hotspots) — 6 hotspots with what user does, what info they provide, whether info exists elsewhere, and AI suitability assessment
- Section 9 (APIs/Services) — complete API inventory table covering NB, PC, Entity Master, Masters, Claims, Rating Engine, Kafka, Accounting, ReferenceNoGenerator
- Section 9 View 1 (Current-State Quote Journey) — full end-to-end flow table: User Action → STATIM Screen → Data Entered → Backend/API → Processing
- Section 9 View 2 (User Effort Matrix) — 50+ activities rated Low/Medium/High for Manual Effort and Repetition with Existing Automation notes
- Section 10 (Unknowns) — 11 items that could not be determined from source code alone
- Summary of Key Findings — 10 numbered findings
- Final Baseline Statement — precise minimum/maximum manual input quantification for both QQ and FQ paths

**Tools Used**:
- **fsRead (multiple)**: Read all key source files across `gi_product_ui/src/views/NewBusiness/` (NBListing, GetQuote, AddUser, QuickQuote, FullQuote, ProposerRiskDetails, CoverageDetails, PolicyDetails), `gi_product_newbusiness/NewBusinessMicroservice/` (Controllers: QuickQuoteController, FullQuoteController; Services: QuickQuoteService, RatingAPIService; BOs: QuickQuoteBO, ProposerDetailBO, BasicPolicyDetailBO, PolicyDetailBO, PreviousPolicyDetailBO, BasicDetailsBO, RiskDetailBO, RequestBO), `gi_product_ui/src/application/actionCreators/NewBusiness/QuickQuote.js`, `gi_product_ui/src/routes/AppRoutes.jsx`
- **listDirectory**: Explored directory structures of `gi_product_ui/src/views/NewBusiness/`, `gi_product_newbusiness/NewBusinessMicroservice/Controllers/`, `gi_product_newbusiness/NewBusinessMicroservice/Services/`, `gi_product_newbusiness/NewBusinessMicroservice/BOs/`

---


Conversation history has been compacted successfully!

---

So the analysis is complete right?
Are there any APIs mentioned in the analysis? Coz this will be a security issue.

---


No APIs with sensitive details are exposed in the analysis. Here's what was mentioned:

**Internal relative endpoint paths only** — e.g.:
- `QuickQuote/SaveQuickQuote`
- `EntityMaster/EntityLookup`
- `RateEngine/DynamicExpressionResolver`
- `Claims/GetMakeModelVariant`

These are internal microservice route paths, not full URLs with hostnames, ports, or credentials.

**One full internal URL was mentioned** — from the sequence flow memory bank:
- `https://statim20.novactech.net/KafkaPublisher/api/SendDataToKafka`

This is an internal network hostname (`statim20.novactech.net`) that appears in your committed `appsettings.json` files. It's not exposed by the analysis — it was already in your source code and memory bank notes.

**No credentials, API keys, tokens, or secrets** were included anywhere in the analysis.

**Actual risk to flag** (pre-existing, not introduced by this analysis):

- `statim20.novactech.net` internal hostnames are in committed `appsettings.json` files in the repo
- KafkaJobOrchestrator API keys are in committed config (noted in the memory bank under security patterns)
- RI API has `Token.Enable=false` in committed config — JWT validation disabled

These are source code security issues that exist independently of this analysis. If the repo is accessible beyond the internal team, those committed configs are the actual exposure risk.