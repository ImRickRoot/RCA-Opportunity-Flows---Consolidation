# Flow Documentation Standards & Naming Conventions

**Purpose:** Establish consistent standards for flow documentation and naming across the Salesforce org  
**Last Updated:** November 14, 2025

---

## TABLE OF CONTENTS

1. [Documentation Standards](#documentation-standards)
2. [Naming Conventions](#naming-conventions)
3. [Examples and Templates](#examples-and-templates)

---

# DOCUMENTATION STANDARDS

## Flow-Level Description

Every flow must have a comprehensive description that includes:

### Required Components

1. **Category Tag** - One of the standard categories (see list below)
2. **Purpose Statement** - Brief one-sentence purpose
3. **Detailed Explanation** - What the flow does, when it triggers, why it exists
4. **Business Value** - Why this flow matters to the business
5. **Optional: Technical Notes** - Migration notes, refactoring history, or special considerations

### Standard Categories

| Category | Usage |
|----------|-------|
| **BUSINESS PROCESS** | Core business automations and workflows |
| **CALCULATION** | Field calculations and formula logic |
| **AUDIT TRAIL** | Compliance tracking and timestamping |
| **DATA QUALITY** | Data integrity, validation, and cleanup |
| **INTEGRATION** | External system connections and data sync |
| **EMAIL ALERT** | Notification workflows |

### Template Format

```xml
<description>
[CATEGORY]: [Brief purpose statement]. [Detailed explanation of what the flow does, when it triggers, and why it exists]. [Business value or context]. [Optional: Migration/refactoring notes].
</description>
```

### Examples

**Good - Comprehensive Description:**
```xml
<description>
CALCULATION: Automates classification of admissions as "First Time Admission" or "Readmission" based on 180-day rule. Triggers before save when stage changes to admitted and admission date is populated. Queries for previous admissions within 180 days and classifies accordingly. Eliminates manual classification errors and supports compliance tracking for readmission rates.
</description>
```

**Bad - Vague Description:**
```xml
<description>
Sets admission type.
</description>
```

---

## Element-Level Descriptions

### Decision Elements

**Purpose:** Explain what condition is being evaluated and why

**Template:**
```xml
<description>
Evaluates [what condition]. If [condition is true], [what happens next]. If [condition is false], [what happens instead]. This decision ensures [business purpose/outcome].
</description>
```

**Example:**
```xml
<description>
Evaluates whether this is an IP or OP facility by checking if Facility__c contains "(IP)" or "(OP)". If IP, routes to IP discharge calculation. If OP, routes to OP discharge calculation. This decision ensures we compare apples-to-apples when calculating readmission timeframes.
</description>
```

---

### Action Elements (Email, Updates, etc.)

**Purpose:** Explain what action is being taken and why

**Template:**
```xml
<description>
[Action verb] [what/who] when [condition/trigger]. This [action] [business purpose]. [Any special notes about parameters, templates, or recipients].
</description>
```

**Example:**
```xml
<description>
Sends email notification to bracebridge@recoverycoa.com when a Bracebridge IP opportunity is synced to Avatar. This notifies the facility team that a new IP admission has been processed and they should review the opportunity in Avatar.
</description>
```

---

### Lookup Elements

**Purpose:** Explain what data is being retrieved and why

**Template:**
```xml
<description>
Queries the [Object] to retrieve [what fields/records] where [filter conditions]. This lookup is necessary to [business purpose]. [What happens with the retrieved data].
</description>
```

**Example:**
```xml
<description>
Queries the Case_Med_Approval__c object to retrieve all open medical approvals where Opportunity__c matches the current opportunity and status is not Denied or Closed. This lookup is necessary to close related approvals when the opportunity closes. Retrieved records are looped through and updated to "Closed via Opportunity" status.
</description>
```

---

### Assignment Elements

**Purpose:** Explain what value is being set and why

**Template:**
```xml
<description>
Sets the [field name] to [value/formula] in order to [business purpose]. This assignment [when it happens and why it's needed].
</description>
```

**Example:**
```xml
<description>
Sets the ApprovalStatusPreClosure variable to the current Approval_Status__c value in order to preserve the status before closure. This assignment happens within the loop before updating each medical approval to "Closed via Opportunity" status, maintaining an audit trail.
</description>
```

---

### Formula Elements

**Purpose:** Explain what calculation is being performed and how it's used

**Template:**
```xml
<description>
Formula that [calculation purpose]. Calculates [what is being calculated] by [how it's calculated]. Used to [where/how it's used in the flow]. Returns [data type and what it represents].
</description>
```

**Example:**
```xml
<description>
Formula that calculates days since last discharge. Calculates the difference between Admission_Date__c and the most recent prior Discharge_Datecc__c by subtracting the prior discharge date from current admission date. Used to populate Days_since_last_IP_discharge__c field for readmission tracking. Returns Number representing days between dates.
</description>
```

---

### Loop Elements

**Purpose:** Explain what collection is being processed and why

**Template:**
```xml
<description>
Iterates through [collection name] to [what processing happens for each item]. Each iteration [what happens per record]. This loop [business purpose].
</description>
```

**Example:**
```xml
<description>
Iterates through OppMedApprovals collection to close each related medical approval individually. Each iteration stores the current status values, then updates the approval to "Closed via Opportunity" status while preserving pre-closure values for audit trail. This loop ensures all open approvals are properly closed when the opportunity closes.
</description>
```

---

### Subflow Elements

**Purpose:** Explain which subflow is being called and why

**Template:**
```xml
<description>
Calls the [subflow name] subflow to [what the subflow does]. Passes [what parameters] as input. This subflow call [business purpose]. [What happens with outputs if applicable].
</description>
```

**Example:**
```xml
<description>
Calls the Transportation_Facility_Update_Subflow_1 subflow to send cancellation email to the prior facility. Passes PriorFacility name and TransportID as input. This subflow call notifies the old facility that the patient is no longer being transferred to their location, preventing confusion about patient arrival.
</description>
```

---

### Variable Elements

**Purpose:** Explain what data the variable stores and how it's used

**Template:**
```xml
<description>
Variable that stores [what data]. Used to [purpose in the flow]. Type: [data type]. Collection: [Yes/No]. Input/Output: [specify if used for subflow communication].
</description>
```

**Example:**
```xml
<description>
Variable that stores the transportation request record ID. Used to pass the record ID to subflows for cancellation and new request emails. Type: Text. Collection: No. Input: No. Output: No (internal use only).
</description>
```

---

# NAMING CONVENTIONS

## Flow Names

### Standard Format
`[Business_Process_Name]_[Object]`

### Rules
1. Use descriptive business-focused names
2. Include object name at end: `_Opp`, `_Account`, `_Case`
3. Use underscores to separate words
4. Avoid abbreviations unless widely known
5. Maximum length: 80 characters (Salesforce limit is 255, but keep it readable)

### Examples

**✅ Good Examples:**
- `New_Admission_Avatar_Sync_Notification_Consolidated_Opp`
- `Set_Admission_Type_Opp`
- `Auto_Close_Med_Approvals_on_Opp_Closure`
- `Calculate_Days_Since_Last_Prior_Discharge_Opp`

**❌ Bad Examples:**
- `NewAdm_Flow` (Too abbreviated, not descriptive)
- `Flow1` (Not descriptive at all)
- `Opp_Flow_Updated_Final_v2` (Version numbers don't belong in names)
- `EmailFlow` (Missing object, not specific enough)

---

## Flow API Names

Flow API names should match the flow label exactly, with spaces replaced by underscores.

**Rule:** `Flow_Label.replace(' ', '_')`

**Examples:**
- Label: `Set Admission Type Opp` → API: `Set_Admission_Type_Opp`
- Label: `Auto Close Med Approvals on Opp Closure` → API: `Auto_Close_Med_Approvals_on_Opp_Closure`

---

## Element Labels

### General Principles
1. Use complete sentences or clear action phrases
2. Start with verb for actions (Send, Update, Check, Calculate, etc.)
3. Be specific about business purpose
4. Maximum length: 80 characters (keep it readable in Flow Builder)

### Decision Element Labels

**Format:** `[What is being checked/evaluated]`

**✅ Good Examples:**
- `Check if Facility is IP or OP`
- `Is there active CMA`
- `Determine Admission Type`
- `BDO Has Manager`

**❌ Bad Examples:**
- `Decision1`
- `Check`
- `MainDecision`

---

### Action Element Labels

**Format:** `[Verb] [What/Who]` or `[Action] [Target]`

**✅ Good Examples:**
- `Send BBH IP Email`
- `Record Avatar Sync User`
- `Flag Account for Facility Clearance`
- `Close Med Approval`

**❌ Bad Examples:**
- `mainUpdate`
- `Email1`
- `Action`

---

### Lookup Element Labels

**Format:** `Get [What is being retrieved]` or `[Purpose] Lookup`

**✅ Good Examples:**
- `Get Previous Admission`
- `Get Related Med Approvals`
- `Get Account for webform`
- `Get Transportation Request Info`

**❌ Bad Examples:**
- `Query1`
- `Lookup`
- `GetData`

---

### Assignment Element Labels

**Format:** `Set [What is being set]` or `[Purpose] Assignment`

**✅ Good Examples:**
- `Set Med Previous Status`
- `Assign Record to Variable`
- `Set Default Opportunity Name`
- `set true` (acceptable for simple boolean)

**❌ Bad Examples:**
- `Assignment1`
- `Assign`
- `SetVar`

---

### Loop Element Labels

**Format:** `Loop [Collection Name]` or `Iterate through [What]`

**✅ Good Examples:**
- `Loop Med Approvals`
- `Iterate through Active FC`
- `Loop Transportation Requests`

**❌ Bad Examples:**
- `Loop1`
- `MainLoop`
- `Process`

---

### Subflow Element Labels

**Format:** `Call [Subflow Purpose]` or `Run [Subflow Purpose]`

**✅ Good Examples:**
- `Call Close Med Approvals`
- `Run Transportation Cancellation Email Subflow`
- `Call Assign BDO`

**❌ Bad Examples:**
- `Subflow1`
- `Call`
- `Execute`

---

## Variable Names

### Standard Format: PascalCase

**Rules:**
1. Use PascalCase (each word starts with capital, no separators)
2. Be descriptive about data stored
3. Include data type hint if helpful
4. Avoid abbreviations unless widely known

### Examples

**✅ Good Examples:**
- `RecordID`
- `PriorFacility`
- `NewFacility`
- `EmailList`
- `OppMedApprovals`
- `ApprovalStatusPreClosure`
- `IsNewOpportunity`
- `TotalCountDiffrence` (note: keep existing spelling if already in use)

**❌ Bad Examples:**
- `var1`
- `x`
- `temp`
- `data`
- `myVariable`

### Variable Types

| Type | Naming Pattern | Example |
|------|----------------|---------|
| **Record ID** | `[Object]ID` or `RecordID` | `OpportunityID`, `RecordID` |
| **Boolean** | `Is[Condition]` or `Has[Something]` | `IsNewOpportunity`, `HasManager` |
| **Collection** | `[Object]s` or `[PluralNoun]List` | `MedApprovals`, `EmailList` |
| **Single SObject** | `[Object]Record` or `[Object]` | `TransportRecord`, `OppMedApproval` |
| **Text/String** | `[Description]` | `PriorFacility`, `EmailAddress` |
| **Number** | `[What]Count` or `[What]Total` | `DaysSinceDischarge`, `TotalCount` |
| **DateTime** | `[Event]DateTime` or `[Event]Timestamp` | `SyncDateTime`, `PushTimestamp` |

---

## Formula Names

### Standard Format: PascalCase or camelCase

**Rules:**
1. Name describes what the formula calculates
2. Keep it concise but clear
3. Existing formulas may use different patterns (legacy)

### Examples

**✅ Good Examples:**
- `CalculateDays`
- `TotalCountDiffrence` (legacy - keep for consistency)
- `IsNewOpportunity`
- `AccountSourceCheck`
- `PreviousNull`
- `BaseURL`

**❌ Bad Examples:**
- `formula1`
- `calc`
- `temp`

---

# EXAMPLES AND TEMPLATES

## Complete Flow Documentation Example

### Flow: Set_Admission_Type_Opp

**Flow-Level Description:**
```xml
<description>
CALCULATION: Automates classification of admissions as "First Time Admission" or "Readmission" based on 180-day rule. Triggers before save when stage changes to admitted and admission date is populated. Queries for previous admissions within 180 days, calculates days since last discharge, and classifies admission type accordingly. Eliminates manual classification errors and supports compliance tracking for readmission rates. Critical for CMS reporting and quality metrics.
</description>
```

**Element Descriptions:**

**Get Previous Admission (Lookup):**
```xml
<description>
Queries the Opportunity object to retrieve the most recent prior admission for the same account where stage is Admitted or Admitted to Outpatient and discharge date is before the current admission date. This lookup is necessary to determine if patient has been admitted within the last 180 days. Retrieved record's discharge date is used to calculate days since discharge.
</description>
```

**CalculateDays (Formula):**
```xml
<description>
Formula that calculates days between current admission and prior discharge. Calculates the difference by subtracting prior discharge date from current admission date (Admission_Date__c - Prior.Discharge_Datecc__c). Used in the Determine Admission Type decision to apply the 180-day readmission rule. Returns Number representing days between dates.
</description>
```

**Determine Admission Type (Decision):**
```xml
<description>
Evaluates the 180-day readmission rule to classify admission type. If no prior admission found OR days since last discharge is greater than 180, routes to First Time Admission. If days since last discharge is 180 or less, routes to Readmission. This decision ensures accurate classification for compliance reporting and readmission rate tracking.
</description>
```

**Readmission Assignment (Assignment):**
```xml
<description>
Sets the Admission_Type__c field to "Readmission" when patient was discharged from prior admission within the last 180 days. This assignment occurs when the 180-day rule logic determines this is a readmission, supporting compliance tracking and quality metrics.
</description>
```

**First Time Admission Assignment (Assignment):**
```xml
<description>
Sets the Admission_Type__c field to "First Time Admission" when no prior admission exists OR the prior discharge was more than 180 days ago. This assignment occurs when the 180-day rule logic determines this is not a readmission, supporting accurate admission type reporting.
</description>
```

---

## Quick Reference Checklist

### Before Marking Flow as "Complete":

- [ ] Flow has category-based description
- [ ] Flow description explains what, when, why, and business value
- [ ] All decisions have descriptions explaining logic and routing
- [ ] All actions have descriptions explaining what they do and why
- [ ] All lookups have descriptions explaining what data is retrieved and why
- [ ] All assignments have descriptions explaining what is being set and why
- [ ] All formulas have descriptions explaining calculation and usage
- [ ] All variables have descriptions explaining data type and purpose
- [ ] All element labels are descriptive and business-focused
- [ ] No generic names like "Decision1" or "Update1"
- [ ] Flow API name matches label with underscores
- [ ] Variable names use PascalCase

---

## Common Mistakes to Avoid

### ❌ Don't Do This:

1. **Vague Descriptions**
   - ❌ "Updates the record"
   - ✅ "Updates the Opportunity record to set Admission_Type__c based on 180-day readmission rule"

2. **Technical Jargon Only**
   - ❌ "Executes SOQL query on Opportunity object"
   - ✅ "Queries for previous admissions to determine readmission status"

3. **Missing Business Context**
   - ❌ "Sets field to true"
   - ✅ "Sets Patient_Account_Flag__c to true to flag account for facility clearance review after administrative discharge"

4. **Generic Element Names**
   - ❌ "Decision1", "Update1", "Assignment1"
   - ✅ "Check Discharge Type", "Flag Account for Facility Clearance", "Set Med Previous Status"

5. **No Category Tag**
   - ❌ "This flow updates opportunities."
   - ✅ "DATA QUALITY: This flow updates opportunities..."

---

## Documentation Maintenance

### When to Update Documentation

1. **Always** when creating a new flow
2. **Always** when modifying flow logic
3. **Always** when adding/removing elements
4. **Recommended** when business process changes
5. **Recommended** during annual documentation reviews

### Documentation Review Process

1. **Quarterly Review:** Check that all active flows have descriptions
2. **Annual Deep Dive:** Review descriptions for accuracy and completeness
3. **During Onboarding:** Use documentation as training material
4. **During Troubleshooting:** Verify descriptions match actual behavior

---

**END OF DOCUMENTATION STANDARDS**

*For flow consolidation details, see [FLOW_CONSOLIDATION_MASTER_DOCUMENTATION.md](./FLOW_CONSOLIDATION_MASTER_DOCUMENTATION.md)*  
*For individual flow documentation, see [INDIVIDUAL_FLOWS_DOCUMENTATION.md](./INDIVIDUAL_FLOWS_DOCUMENTATION.md)*

