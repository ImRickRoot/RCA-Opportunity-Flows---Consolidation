# Individual Flows - Detailed Documentation

**Document Purpose:** Comprehensive documentation for all 27 active Salesforce flows  
**Last Updated:** November 14, 2025

---

## TABLE OF CONTENTS

1. [BDO Alert Flows (3)](#bdo-alert-flows)
2. [Consolidated Email Alert Flows (2)](#consolidated-email-alert-flows)
3. [Field Calculation Flows (3)](#field-calculation-flows)
4. [Business Process Automation Flows (7)](#business-process-automation-flows)
5. [Integration & Data Management Flows (12)](#integration--data-management-flows)

---

# BDO ALERT FLOWS

## 1. Opp Admitted BDO Alert

**API Name:** `Opp_Admitted_BDO_Alert`  
**Category:** BUSINESS PROCESS - Email Alert

### Purpose
Notifies BDO team when opportunity reaches admitted stage for tracking successful conversions.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `StageName` changes to "Admitted" OR "Admitted to Outpatient"

### Business Process
1. Opportunity stage changes to admitted
2. Flow sends email notification to BDO team
3. Email includes opportunity details

### Key Elements
- **Email Action:** Sends notification to bdo@recoverycoa.com
- **Email Content:** Opportunity name, patient name, facility, stage

### Business Value
Keeps BDO team informed of successful admission conversions for their tracked opportunities, supporting commission tracking and case closure.

---

## 2. Opp Created BDO Alert

**API Name:** `Opp_Created_BDO_Alert`  
**Category:** BUSINESS PROCESS - Email Alert

### Purpose
Notifies BDO team when new BDO-tracked opportunity is created for immediate follow-up.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - New opportunity created (ISNEW() = TRUE)
  - `BDO_Region__c` ≠ "Non-BDO"

### Business Process
1. New opportunity is created in Salesforce
2. Flow checks if opportunity is BDO-tracked
3. If yes, sends email notification to BDO team
4. Email includes opportunity details

### Key Elements
- **Email Action:** Sends notification to bdo@recoverycoa.com
- **Email Content:** Opportunity name, patient name, BDO region, creation date

### Business Value
Ensures BDO team is immediately aware of all new opportunities in their regions for prompt follow-up and engagement.

---

## 3. Opp Comment BDO Alert

**API Name:** `Opp_Comment_BDO_Alert`  
**Category:** BUSINESS PROCESS - Email Alert

### Purpose
Notifies BDO and their manager when important comments are added to opportunities.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Last_Case_Comment__c` field changes (indicates new comment added)

### Business Process
1. Comment is added to opportunity (updates `Last_Case_Comment__c`)
2. Flow looks up BDO User record
3. Sends email to BDO
4. Checks if BDO has a manager (`ManagerId` is populated)
5. If yes, sends copy of email to BDO's manager

### Key Elements
- **Get BDO User Lookup:** Retrieves BDO user details including manager
- **BDO Has Manager Decision:** Checks if `ManagerId` field is populated
- **Email to BDO Action:** Primary notification to BDO
- **Email to BDO Manager Action:** Escalation notification to manager
- **Email Recipients:** BDO email and optionally manager email

### Business Value
Two-tier notification system ensures critical updates reach BDO team and escalate to management when needed, supporting accountability and oversight.

---

# CONSOLIDATED EMAIL ALERT FLOWS

## 4. New Admission Avatar Sync Notification (Consolidated)

**API Name:** `New_Admission_Avatar_Sync_Notification_Consolidated`  
**Category:** EMAIL ALERT - Consolidated

### Purpose
Sends facility-specific email notifications when opportunities are synced to Avatar EHR system.

### Replaces
17 individual flows + 4 duplicate flows = 21 flows total

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Sync_to_Avatar__c = TRUE`
  - `doesRequireRecordChangedToMeetCriteria = TRUE` (only when field changes)

### Business Process
1. User checks `Sync_to_Avatar__c` checkbox on opportunity
2. Flow evaluates `Facility__c` field
3. Routes to appropriate facility branch (21 possible branches)
4. Sends facility-specific email to facility team
5. Email includes opportunity details for review

### Facility Routing Logic

| Facility Value | Email Recipient(s) | Special Notes |
|----------------|-------------------|---------------|
| Bracebridge (IP) | bracebridge@recoverycoa.com | - |
| Bracebridge (OP) | bracebridge@recoverycoa.com | - |
| MCAT (IP) | mcat@recoverycoa.com, capitalregion@recoverycoa.com | **Sends 2 emails** |
| MCAT (OP) | capitalregion@recoverycoa.com | - |
| BCAT (IP) | danvers@recoverycoa.com | Shares Danvers team |
| Danvers (IP) | danvers@recoverycoa.com | - |
| Danvers (OP) | danvers@recoverycoa.com | - |
| Devon (IP) | devon@recoverycoa.com | - |
| Greenville (IP) | greenville@recoverycoa.com | - |
| Greenville (OP) | greenville@recoverycoa.com | - |
| Indianapolis (IP) | indianapolis@recoverycoa.com | - |
| Indianapolis (OP) | indianapolis@recoverycoa.com | - |
| Lighthouse (IP) | lighthouse@recoverycoa.com | - |
| Lighthouse at Mays Landing (OP) | lighthouse@recoverycoa.com | Shares Lighthouse team |
| Monroeville (IP) | monroeville@recoverycoa.com | - |
| Monroeville (OP) | monroeville@recoverycoa.com | - |
| Raritan Bay (IP) | raritanbay@recoverycoa.com | - |
| St. Charles (IP) | stcharles@recoverycoa.com | - |
| St. Charles (OP) | stcharles@recoverycoa.com | - |
| South Elgin (OP MH/ED) | stcharles@recoverycoa.com | Shares St. Charles team |
| Westminster (IP) | westminster@recoverycoa.com | - |

### Key Elements
- **Route_by_Facility Decision:** 21-branch decision routing to facility-specific actions
- **Email Actions (21):** One per facility/program type combination
- **Comprehensive Descriptions:** Every element fully documented

### Business Value
- Single point of maintenance for all facility notifications
- Consistent email format across all facilities
- Easy to add new facilities (just add new branch)
- Reduced testing scope for changes
- Comprehensive audit trail with element descriptions

---

## 5. VOB Update Avatar Sync Notification (Consolidated)

**API Name:** `VOB_Update_Avatar_Sync_Notification_Consolidated`  
**Category:** EMAIL ALERT - Consolidated

### Purpose
Sends facility-specific email notifications when Verification of Benefits (VOB) is updated and sent to Avatar.

### Replaces
11 individual flows

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Send_VOB_to_Avatar__c = TRUE`
  - `doesRequireRecordChangedToMeetCriteria = TRUE` (only when field changes)

### Business Process
1. VOB information is updated in Salesforce
2. User checks `Send_VOB_to_Avatar__c` checkbox
3. Flow evaluates `Facility__c` field
4. Routes to appropriate facility branch (11 possible branches)
5. Sends VOB update notification to facility team
6. Facility team reviews updated benefits information

### Facility Routing Logic

| Facility Value | Email Recipient | Program Type |
|----------------|-----------------|--------------|
| Bracebridge (IP) | bracebridge@recoverycoa.com | IP |
| BCAT (IP) | bcataim@recoverycoa.com | IP |
| MCAT (IP) | capitalregion@recoverycoa.com | IP |
| MCAT (OP) | capitalregion@recoverycoa.com | OP |
| Danvers (OP) | danvers@recoverycoa.com | OP |
| Devon (IP) | devon@recoverycoa.com | IP |
| Devon (OP) | devon@recoverycoa.com | OP |
| Lighthouse (IP) | lighthouse@recoverycoa.com | IP |
| Lighthouse at Mays Landing (OP) | lighthouse@recoverycoa.com | OP |
| Raritan Bay (IP) | raritanbay@recoverycoa.com | IP |
| Westminster (IP) | westminster@recoverycoa.com | IP |

### Key Elements
- **Route_VOB_by_Facility Decision:** 11-branch decision routing to facility-specific actions
- **Email Actions (11):** One per facility/program type combination
- **Comprehensive Descriptions:** Every element fully documented

### Business Value
- Ensures billing/admissions teams are notified of updated insurance information
- Prevents delays in patient admission due to missing benefits verification
- Single point of maintenance for all VOB notifications
- Consistent notification format across facilities

---

# FIELD CALCULATION FLOWS

## 6. Set Admission Type Opp

**API Name:** `Set_Admission_Type_Opp`  
**Category:** CALCULATION - Field Update

### Purpose
Automates classification of admissions as "First Time Admission" or "Readmission" based on 180-day rule.

### Trigger Conditions
- **Trigger Type:** Record Before Save
- **Object:** Opportunity
- **Conditions:**
  - `StageName` changes to "Admitted" OR "Admitted to Outpatient"
  - `Admission_Date__c` is populated

### Business Logic - 180-Day Rule
```
IF prior admission exists WITHIN last 180 days from current admission:
    → Admission_Type__c = "Readmission"
ELSE:
    → Admission_Type__c = "First Time Admission"
```

### Business Process
1. Opportunity moves to admitted stage with admission date
2. Flow queries for previous admissions (same account, admitted stage)
3. Calculates days between current admission and most recent prior discharge
4. If prior discharge was >180 days ago OR no prior admission → First Time
5. If prior discharge was ≤180 days ago → Readmission
6. Updates `Admission_Type__c` field before record saves

### Key Elements
- **Get Previous Admission Lookup:** Finds most recent prior admission for the account
  - Filters: Same Account, Stage = Admitted/Admitted to OP, Discharge Date < Current Admission Date
  - Sorts by: Discharge Date DESC
  - Gets: First record only
- **CalculateDays Formula:** `Admission_Date__c - Prior.Discharge_Datecc__c`
- **Determine Admission Type Decision:** Applies 180-day rule logic
  - Branch 1: No prior admission found → First Time
  - Branch 2: Days > 180 → First Time
  - Branch 3: Days ≤ 180 → Readmission
- **Readmission Assignment:** Sets `Admission_Type__c = "Readmission"`
- **First Time Assignment:** Sets `Admission_Type__c = "First Time Admission"`

### Business Value
- Automates compliance tracking for readmission rates
- Eliminates manual classification errors
- Supports quality reporting and CMS requirements
- Provides data for readmission analysis

---

## 7. Calculate Days Since Last Prior Discharge Opp

**API Name:** `Calculate_Days_Since_Last_Prior_Discharge_Opp`  
**Category:** CALCULATION - Field Update

### Purpose
Calculates days between current admission and most recent prior discharge (separate calculations for IP and OP).

### Trigger Conditions
- **Trigger Type:** Record Before Save
- **Object:** Opportunity
- **Conditions:**
  - `Admission_Date__c` changes

### Business Process
1. Admission date is populated or changes
2. Flow determines if facility is IP or OP (checks if `Facility__c` contains "(IP)" or "(OP)")
3. For IP: Queries for most recent prior IP discharge
4. For OP: Queries for most recent prior OP discharge
5. Calculates days between current admission and prior discharge
6. Populates appropriate field before record saves

### Key Elements
- **Record Type Lookup:** Retrieves "Case_2_0" record type ID for filtering opportunities
- **Check the facility Decision:** 
  - Branch 1: Contains "(IP)" → IP calculation path
  - Branch 2: Contains "(OP)" → OP calculation path
- **Get All Opportunity From Account (IP):** Finds most recent IP discharge
  - Filters: Same Account, Record Type = Case_2_0, Facility contains "(IP)", Discharge Date < Admission Date, Discharge Date NOT NULL
  - Sorts by: Discharge Date DESC
  - Gets: First record only
- **Get All Opportunity From Account (OP):** Finds most recent OP discharge
  - Filters: Same Account, Record Type = Case_2_0, Facility contains "(OP)", Discharge Date < Admission Date, Discharge Date NOT NULL
  - Sorts by: Discharge Date DESC
  - Gets: First record only
- **TotalCountDiffrence Formula (IP):** `Admission_Date__c - Prior_IP.Discharge_Datecc__c`
- **TotalCountDiffrenceOP Formula (OP):** `Admission_Date__c - Prior_OP.Discharge_Datecc__c`
- **Update Records (IP):** Sets `Days_since_last_IP_discharge__c`
- **Update Records (OP):** Sets `Days_since_last_OP_discharge__c`

### Business Value
- Supports readmission tracking and analysis
- Provides data for admission type classification (used by Set_Admission_Type_Opp)
- Enables patient history reporting
- Supports compliance and quality metrics

---

## 8. Facility Admission Notes Field Update Flow Opp

**API Name:** `Facility_Admission_Notes_Field_Update_Flow_Opp`  
**Category:** BUSINESS PROCESS - Field Update

### Purpose
Auto-populates facility-specific COVID-19 admission protocols and quarantine policies.

### Trigger Conditions
- **Trigger Type:** Record Before Save
- **Object:** Opportunity
- **Conditions:** Always runs (on create or update)
- **Exception:** Exempts FinPay API user from triggering

### Supported IP Facilities (10)
1. Bracebridge (IP)
2. Danvers (IP)
3. Devon (IP)
4. Indianapolis (IP)
5. Lighthouse (IP)
6. MCAT (IP)
7. Monroeville (IP)
8. Raritan Bay (IP)
9. St. Charles (IP)
10. Westminster (IP)

### COVID-19 Policy (All Facilities)
All facilities follow the same COVID-19 admission policy:
- Patients **CAN** be admitted if COVID-positive based on medical necessity
- **7-10 day quarantine period** required
- Ensures **CDC 5-day minimum** is met
- Includes **3 additional days** of mask compliance monitoring
- Medical team assesses symptomology before ending quarantine

### Business Process
1. Opportunity is created or updated
2. Flow checks if user is FinPay API user → If yes, exit (prevents API conflicts)
3. Flow evaluates `Facility__c` field
4. If IP facility → Populates `Facility_Admission_Notes__c` with facility-specific COVID policy
5. If OP/MAT facility → Clears `Facility_Admission_Notes__c` (policy doesn't apply)
6. Field updates before record saves

### Key Elements
- **Exit FinPay User Decision:** Prevents flow from running when triggered by FinPay integration user
  - Formula: `$User.Username = "finpay@recoverycoa.com.integration"`
- **Check Facility Decision:** 10-way branch based on exact `Facility__c` picklist value
  - Branch for each IP facility
  - Default branch for OP/MAT (clears field)
- **Text Templates (10):** Each facility has identical HTML-formatted COVID policy text
- **Assignments (11):** 10 for IP facilities + 1 to clear field for OP/MAT
- **Field Updated:** `Facility_Admission_Notes__c` (Long Text Area)

### History
- **2/9/21:** Added St. Charles, Indianapolis, Monroeville facilities
- **3/21:** Modified for changing COVID requirements
- **6/23:** Updated facility-specific text
- **8/2:** Exempted FinPay API user

### Business Value
- Ensures consistent COVID-19 protocols across all IP facilities
- Automates policy communication to admissions teams
- Supports patient safety and CDC compliance
- Reduces manual data entry and errors

---

# BUSINESS PROCESS AUTOMATION FLOWS

## 9. Add BDO to Opp flow

**API Name:** `Add_BDO_to_Opp_flow`  
**Category:** BUSINESS PROCESS - Automation

### Purpose
Auto-populates BDO owner from referent account relationship for tracking and commission purposes.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - New opportunity created AND `Referent__c` is populated
  - OR `Referent__c` field changes on existing opportunity

### Business Process
1. Opportunity is created with referent OR referent is changed
2. Flow checks if this is a new record or if referent changed
3. If `Referent__c` is populated, copies `Referent__r.OwnerId` to `BDO_Case_Owner__c`
4. BDO owner field is now populated for tracking

### Key Elements
- **isnew Formula:** `ISNEW()` - Returns TRUE if this is a new record
- **ischanged Formula:** `ISCHANGED(Referent__c)` - Returns TRUE if Referent field changed
- **Referent Account Check Decision:**
  - Branch 1: New record with referent → Populate BDO
  - Branch 2: Referent changed on existing record → Update BDO
  - Default: No action needed
- **Populate BDO Update:** Sets `BDO_Case_Owner__c = Referent__r.OwnerId`
- **populate BDO change Update:** Sets `BDO_Case_Owner__c = Referent__r.OwnerId` (on change)

### Business Value
- Maintains BDO tracking relationship automatically
- Ensures proper assignment for commission tracking
- Supports case management and ownership reporting
- Eliminates manual BDO assignment errors

---

## 10. Auto Close Med Approvals on Opp Closure

**API Name:** `Auto_Close_Med_Approvals_on_Opp_Closure`  
**Category:** BUSINESS PROCESS - Automation

### Purpose
Auto-closes related medical approval records when opportunity closes, maintaining audit trail of pre-closure status.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `StageName = "Closed"`
  - `RecordTypeId = "01261000000X5QbAAK"` (Admissions record type)

### Business Process
1. Opportunity is closed
2. Flow looks up related `Case_Med_Approval__c` records
3. For each open med approval:
   - Stores current `Approval_Status__c` in `Approval_Status_Pre_Closure__c`
   - Stores current `Review_Type__c` in `Review_Type_Pre_Closure__c`
   - Updates `Approval_Status__c` to "Closed via Opportunity"
   - Updates `Review_Type__c` to "Closed via Opportunity"
4. Preserves pre-closure values for audit trail

### Key Elements
- **Get Opportunity Lookup:** Validates stage = Closed and correct record type
- **Should Close Med Approvals Decision:** Confirms closure criteria met
- **Get Related Med Approvals Lookup:** Retrieves open med approvals
  - Filters: `Opportunity__c = Current Opportunity`
  - Queries: Id, Approval_Status__c, Review_Type__c
- **Loop Med Approvals:** Processes each med approval individually
- **Set Med Previous Status Assignment:** Stores original values in variables
  - `ApprovalStatusPreClosure = Loop.Approval_Status__c`
  - `ReviewStatusPreClosure = Loop.Review_Type__c`
- **Close Med Approval Update:** Updates each approval
  - Sets `Approval_Status_Pre_Closure__c = ApprovalStatusPreClosure`
  - Sets `Approval_Status__c = "Closed via Opportunity"`
  - Sets `Review_Type_Pre_Closure__c = ReviewStatusPreClosure`
  - Sets `Review_Type__c = "Closed via Opportunity"`
  - Filters: Status NOT IN (Denied, Closed via Opportunity, Approved)

### Business Value
- Automates med approval lifecycle management
- Maintains comprehensive audit trail with pre-closure values
- Prevents orphaned approval records
- Ensures data integrity across related objects
- Supports compliance and reporting requirements

---

## 11. CMA Alert on Facility Change Opp

**API Name:** `CMA_Alert_on_Facility_Change_Opp`  
**Category:** EMAIL ALERT - Business Process

### Purpose
Notifies medical approvals team when facility changes after approval has been granted, ensuring approvals stay aligned with placement.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Facility__c` changes (ISCHANGED)
  - Related active `Case_Med_Approval__c` exists
  - Approval status is NOT "Closed via Opportunity" or "Denied"

### Business Process
1. Facility changes on opportunity (e.g., from Devon to Bracebridge)
2. Flow looks up active med approvals for the opportunity
3. Checks if any exist with open status
4. If yes, sends alert email to medical approvals team
5. Email includes opportunity details and facility change information
6. Team reviews to ensure insurance approval covers new facility

### Key Elements
- **GetCMA Lookup:** Retrieves active med approvals
  - Filters: `Opportunity__c = Current Opportunity`
  - Queries: Approval_Status__c, Review_Type__c
- **Is there active CMA Decision:** Checks if med approvals were found
- **MedApproval status is NOT closed or Denied Decision:** Validates approval is open
  - Checks: Status ≠ "Closed via Opportunity" AND Status ≠ "Denied"
- **Send email to Med Approvals Action:** Notifies team
  - Recipients: medapprovals@recoverycoa.com
  - Content: Opportunity name, patient, old facility, new facility

### Business Value
- Ensures med approvals stay aligned with patient placement decisions
- Prevents insurance denials due to facility changes
- Supports compliance with insurance authorization requirements
- Reduces revenue loss from placement/approval mismatches

---

## 12. Automate FC Review Request Email Opp

**API Name:** `Automate_FC_Review_Request_Email_Opp`  
**Category:** EMAIL ALERT - Business Process

### Purpose
Sends facility-specific review request emails to facility directors when patients are discharged with Administrative or Against Advice discharge types.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Discharge_Datecc__c` is populated (not null)

### Administrative Discharge Types Flagged
- "Administrative Discharge"
- "Against Advice"

### Supported Facilities (11)
1. Bracebridge (BBH)
2. Capital Region (MCAT)
3. Danvers
4. Devon
5. Greenville
6. Indianapolis (INDY)
7. Lighthouse (LH)
8. Monroeville
9. Raritan Bay (RB)
10. St. Charles
11. Westminster

### Business Process
1. Opportunity is discharged with admin or AMA discharge type
2. Flow checks discharge type criteria
3. Queries for active `Facility_Clearance__c` records for the patient account
4. Loops through each active FC record
5. For each FC:
   - Determines which facility based on FC facility field
   - Builds email recipient list for that facility
   - Sends customized review request email
   - Includes direct Salesforce links to FC and Account records
6. Facility directors review and remove FC if appropriate

### Email Template Components (All Similar)
- **Personalized greeting** to facility directors by name
- **Patient name** and discharge date
- **Facility discharged from** and discharge type
- **Direct clickable links** to FC record and Account record in Salesforce
- **Instructions** to remove FC if no longer needed
- **Professional sign-off** from RCA Corporate Team

### Key Elements
- **Check Discharge Type Decision:** Filters to admin or AMA discharges only
- **Get FC Record Lookup:** Retrieves all active facility clearance records
  - Filters: Account = Opportunity Account, Status = Active/Open
- **Iterate through Active FC Loop:** Processes each FC individually
- **CheckRegion Decision:** 11-way branch determines facility
- **Assignments (11):** Each facility builds its email recipient list
  - Variable: `EmailList` (Text Collection)
  - Adds facility director emails as comma-separated list
- **Email Actions (11):** Each facility sends customized email
  - Uses facility-specific text template
  - Recipient list from assignment
- **BaseURL Formula:** `LEFT($Api.Partner_Server_URL_260, FIND('/services', $Api.Partner_Server_URL_260))` 
  - Extracts Salesforce instance URL for generating record links
- **Text Templates (11):** Personalized for each facility director

### Business Value
- Automates facility clearance tracking and review process
- Ensures appropriate post-discharge patient safety reviews
- Maintains facility-level accountability for problematic discharges
- Provides direct links for efficient FC management
- Supports quality and compliance initiatives

**Note:** This flow is an excellent candidate for future metadata-driven consolidation to reduce from 11 email actions to 1 dynamic action.

---

## 13. Facility Clearance Required Opp

**API Name:** `Facility_Clearance_Required_Opp`  
**Category:** BUSINESS PROCESS - Field Update

### Purpose
Flags patient accounts requiring facility clearance review after administrative discharge types.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Discharge_Datecc__c` is populated (discharge occurred)

### Administrative Discharge Types Flagged
- "Administrative Discharge"
- "Against Advice"
- "Declined Services"
- "AWOL" (Absent Without Leave)

### Business Process
1. Opportunity is discharged
2. Flow checks if discharge type is one of the administrative types
3. If yes, sets `Patient_Account_Flag__c = TRUE` on the Account record
4. Account is now flagged for clearance review
5. Flag prevents readmission without facility approval

### Key Elements
- **Check Discharge Type Decision:** Evaluates `Discharge_Type__c`
  - Branch 1: Admin discharge types → Flag account
  - Default: No action needed
- **Flag Account for Facility Clearance Update:** Sets flag on Account
  - Object: Account (parent of Opportunity)
  - Field: `Patient_Account_Flag__c = TRUE`

### Business Value
- Tracks patients requiring clearance before readmission
- Prevents readmission of problematic cases without proper review
- Supports facility safety protocols and risk management
- Maintains account-level visibility of clearance requirements
- Works in conjunction with `Automate_FC_Review_Request_Email_Opp`

---

## 14. VOB Email Alert Controller Opp

**API Name:** `VOB_Email_Alert_Controller_Opp`  
**Category:** EMAIL ALERT - Integration

### Purpose
Legacy controller for Monroeville-specific VOB update emails (maintained for backward compatibility).

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Send_VOB_to_Avatar__c = TRUE`
  - `Facility__c` contains "Monroeville"

### Business Process
1. VOB is updated and `Send_VOB_to_Avatar__c` is checked
2. Flow checks if facility contains "Monroeville"
3. Looks up BDO User record (currently retrieves specific user)
4. Sends VOB update email to VOB team

### Key Elements
- **Get BDO User Lookup:** Retrieves user details
  - Filters: Username = specific user
  - Purpose: Historical - originally retrieved BDO info
- **Check the facitily Decision:** Validates Monroeville facility
  - Condition: `Facility__c` CONTAINS "Monroeville"
- **Email VOB Action:** Sends notification
  - Recipients: vob@recoverycoa.com
  - Content: VOB update notification with opportunity details

### Business Value
- Specialized VOB tracking for Monroeville facility
- Ensures VOB team is notified of updates
- Maintains legacy notification process

### Note
This flow is kept for backward compatibility. Most VOB alerts are now handled by `VOB_Update_Avatar_Sync_Notification_Consolidated`. This flow provides an additional notification channel for Monroeville.

---

## 15. OP Opp Workflow Emails flow

**API Name:** `OP_Opp_Workflow_Emails_flow`  
**Category:** EMAIL ALERT - Business Process

### Purpose
Triggers reschedule notifications for OP assessment appointments when scheduled date/time changes.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Sync_to_Avatar__c = TRUE`
  - `StageName = "Scheduled for Admission"`
  - `Scheduled_Date_and_Time__c` changes (ISCHANGED)
  - Facility is one of 8 supported OP locations

### Supported OP Facilities (8)
1. Lighthouse at Mays Landing (OP)
2. Lighthouse at Voorhees (OP)
3. Raritan Bay (OP)
4. Monroeville (OP)
5. Indianapolis (OP)
6. Westminster (OP)
7. Danvers (OP)
8. Devon (OP)

### Business Process
1. OP assessment appointment scheduled date/time is changed
2. Flow checks if PRIOR scheduled date was NOT blank (ensures this is a reschedule, not initial scheduling)
3. If prior value existed (this is a reschedule), calls subflow
4. Subflow `OP_Opp_Assessment_Rescheduled_Workflow` handles email generation
5. Facility staff receives notification to coordinate patient communication

### Key Elements
- **PreviousNull Formula:** `ISBLANK($Record__Prior.Scheduled_Date_and_Time__c)` 
  - Returns FALSE if prior value existed (meaning this is a reschedule)
  - Returns TRUE if prior value was blank (meaning this is initial scheduling)
- **NO Previous D/T Decision:** Only proceeds if prior value existed
  - Branch: PreviousNull = FALSE → Call subflow
  - Default: Exit (initial scheduling, no notification needed)
- **Subflow Call:** Invokes `OP_Opp_Assessment_Rescheduled_Workflow`
  - Passes: Opportunity ID
  - Subflow handles email generation and sending

### Business Value
- OP facility coordination for rescheduled appointments
- Patient communication support (facility can reach out to patient)
- Schedule management and tracking
- Reduces no-shows due to miscommunication about appointment times

---

## 16. Transportation Facility Change Post Alert Opp

**API Name:** `Transportation_Facility_Change_Post_Alert_Opp`  
**Category:** EMAIL ALERT - Business Process

### Purpose
Manages transportation coordination edge case when facility changes AFTER transportation has already been arranged and alerted.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Facility__c` changes (ISCHANGED)
  - `StageName = "Pending Schedule"` OR `"Scheduled for Admission"`

### Edge Case Handled
1. Patient is scheduled for admission to **Facility A**
2. Transportation is requested and **Facility A is alerted**
3. Facility changes to **Facility B** before patient arrives
4. **Problem:** Facility A expects patient, but patient going to Facility B
5. **Solution:** Send cancellation to Facility A, send new request to Facility B

### Business Process
1. Facility changes on scheduled/pending opportunity
2. Flow looks up most recent `Transportation_Request__c` for this opportunity
3. Checks if:
   - Facility actually changed (new ≠ old)
   - AND alert email was already sent (`Alert_Email_Sent__c = TRUE`)
4. If both conditions met:
   - Stores record ID and facility values in variables
   - Calls Subflow 1: Sends cancellation email to PRIOR facility
   - Calls Subflow 2: Sends new transportation request to NEW facility
5. Both facilities are now correctly informed

### Key Elements
- **Get Transportation Request Info Lookup:** Retrieves most recent transport request
  - Filters: `Most_Recent_Transport_Request__c = TRUE`, `Opportunity__c = Current Opportunity`
  - Queries: Id, FacilityName__c, Alert_Email_Sent__c
  - Gets: First record only
- **Assign Record to Variable Assignment:** Stores values for subflow use
  - `RecordID = TransportationRequest.Id`
  - `NewFacility = $Record.Facility__c` (current value)
  - `PriorFacility = $Record__Prior.Facility__c` (old value)
- **Decision (New Facility Selected):** Validates conditions
  - Checks: Facility changed AND Alert_Email_Sent__c = TRUE
  - Branch: Both true → Call subflows
  - Default: Exit (no alert needed)
- **Subflow 1 Call:** `Transportation_Facility_Update_Subflow_1` (Cancellation)
  - Passes: PriorFacility, TransportID
  - Sends cancellation email to old facility
- **Subflow 2 Call:** `Transportation_Facility_Update_Subflow_2` (New Request)
  - Passes: NewFacility, TransportID
  - Sends new transportation request to new facility

### Variables
- **RecordID (Text):** Transportation_Request__c Id
- **PriorFacility (Text):** Old facility value
- **NewFacility (Text):** Current facility value
- **TransportRecord (SObject):** Holds transportation record (single)

### Business Value
- Transportation vendor coordination for facility changes
- Prevents confused arrivals at wrong facility
- Ensures smooth patient transfers
- Reduces no-shows and miscommunication
- Maintains transportation request accuracy

---

# INTEGRATION & DATA MANAGEMENT FLOWS

## 17. Pushed to Avatar Opp

**API Name:** `Pushed_to_Avatar_Opp`  
**Category:** AUDIT TRAIL - Integration

### Purpose
Records which user synced the opportunity to Avatar EHR system for audit trail purposes.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Sync_to_Avatar__c = TRUE`
  - `Sent_to_Avatar_By__c` is null (first time sync only)

### Business Process
1. User checks `Sync_to_Avatar__c` checkbox to sync opportunity to Avatar
2. Flow checks if audit fields are already populated
3. If not (first sync), stamps current user's name and email
4. Audit trail is now complete with who and when

### Key Elements
- **Sent_to_Avatar_By_UpdateFormula Formula:** 
  - `$User.FirstName + " " + $User.LastName`
  - Returns current user's full name
- **Record Avatar Sync User Update:** Updates Opportunity with audit info
  - Sets `Sent_to_Avatar_By__c = User's full name`
  - Sets `Sent_to_Avatar_by_Email__c = $User.Email`
  - Only runs if `Sent_to_Avatar_By__c` is null (prevents re-stamping)

### Business Value
- Audit trail for EHR integration events
- Accountability for data synchronization
- Troubleshooting support (know who synced and when)
- Compliance and tracking for external system integration

---

## 18. Default Contact Name to Opp Name

**API Name:** `Default_Contact_Name_to_Opp_Name`  
**Category:** DATA QUALITY - Field Update

### Purpose
Sets default opportunity name from patient information to ensure human-readable record names.

### Trigger Conditions
- **Trigger Type:** Record Before Save
- **Object:** Opportunity
- **Conditions:**
  - `Name` is null OR contains "New Opportunity"

### Business Process
1. New opportunity is created (default name is "New Opportunity")
2. Flow checks if name needs to be updated
3. Constructs name from Account: `{FirstName} {LastName} - Opp {Id}`
4. Updates Name field before record saves
5. Opportunity now has meaningful, searchable name

### Key Elements
- **Set Default Opportunity Name Update:** Updates `Name` field
  - Formula: `Account.FirstName + " " + Account.LastName + " - Opp " + Opportunity.Id`
  - Only runs when Name is null or contains "New Opportunity"

### Business Value
- Ensures human-readable opportunity names
- Improves user experience when searching and viewing records
- Supports reporting clarity
- Eliminates generic "New Opportunity" names

---

## 19. Pushed to FinPay Opp

**API Name:** `Pushed_to_FinPay_Opp`  
**Category:** INTEGRATION - Audit Trail

### Purpose
Timestamps when opportunity data is pushed to FinPay billing system.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Requires_Financial_Clearance__c = TRUE`
  - `Time_Date_FinPay_File_Pushed__c` is null (first time push only)

### Business Process
1. Opportunity requires financial clearance processing
2. Integration pushes data to FinPay system
3. Flow stamps current datetime into audit field
4. Timestamp records when data was sent for tracking

### Key Elements
- **FinPayTimeStampFormula Formula:** `NOW()`
  - Returns current datetime
- **Update Timestamp:** Sets `Time_Date_FinPay_File_Pushed__c = NOW()`
  - Only runs if field is null (prevents re-stamping)

### Business Value
- Integration audit trail for FinPay billing system
- Tracks when financial clearance data was sent
- Supports troubleshooting of integration timing
- Compliance tracking for billing processes

---

## 20. Patient Added to BBH Opp

**API Name:** `Patient_Added_to_BBH_Opp`  
**Category:** EMAIL ALERT - Integration

### Purpose
Notifies Bracebridge (BBH) facility when a Lighthouse patient is transferred to their facility.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Transfer_from_LH_to_BBH__c = TRUE`

### Business Process
1. Patient is identified for transfer from Lighthouse to Bracebridge
2. User checks `Transfer_from_LH_to_BBH__c` checkbox
3. Flow sends email notification to BBH facility
4. Email includes patient and opportunity details
5. BBH team can prepare for incoming transfer

### Key Elements
- **Email Action:** Sends notification
  - Recipients: bracebridge@recoverycoa.com
  - Subject: Patient Transfer from Lighthouse to Bracebridge
  - Content: Patient name, opportunity details, transfer notification

### Business Value
- Facility coordination for patient transfers
- Ensures BBH is prepared for incoming patient
- Improves communication between facilities
- Supports smooth patient transitions

---

## 21. Update Timestamp Facility Changed After Avatar Sync Opp

**API Name:** `Update_Timestamp_Facility_Changed_After_Avatar_Sync_Opp`  
**Category:** AUDIT TRAIL - Integration

### Purpose
Timestamps facility changes that occur after the opportunity has been synced to Avatar, tracking data integrity issues.

### Trigger Conditions
- **Trigger Type:** Record Before Save
- **Object:** Opportunity
- **Conditions:**
  - `Facility__c` changes (ISCHANGED)
  - `Sync_to_Avatar__c = TRUE`
  - `StageName = "Scheduled for Admission"`

### Business Process
1. Opportunity has been synced to Avatar (checkbox is checked)
2. User changes facility field
3. Flow stamps current datetime before record saves
4. Timestamp indicates potential data sync issue (facility changed after EHR sync)

### Key Elements
- **Assignment:** Sets timestamp field
  - Field: `FacilityChangedSinceLastSyncedToAvatar__c = NOW()`
  - Only runs when all trigger conditions met

### Business Value
- Audit trail for EHR data changes post-sync
- Identifies potential sync issues between Salesforce and Avatar
- Supports troubleshooting of facility data mismatches
- Flags records that may need re-sync to Avatar

---

## 22. Nullify Opp Reason for Reopening

**API Name:** `Nullify_Opp_Reason_for_Reopening`  
**Category:** DATA QUALITY - Field Update

### Purpose
Clears closure reason fields when opportunity is reopened, preventing stale closure data.

### Trigger Conditions
- **Trigger Type:** Record Before Save
- **Object:** Opportunity
- **Conditions:**
  - Opportunity transitions from "Closed" stage to any open stage

### Business Process
1. Closed opportunity is reopened (stage changes from Closed to any other stage)
2. Flow clears `Reason__c` and `Specific_Case_Reason__c` fields
3. Fields are now blank, ready for new data if opportunity closes again
4. Prevents confusion about why opportunity was originally closed

### Key Elements
- **Update Action:** Nullifies reason fields
  - Sets `Reason__c = null`
  - Sets `Specific_Case_Reason__c = null`
  - Only runs when transitioning from Closed to open

### Business Value
- Data quality and integrity
- Prevents confusion about closure reasons when record is reopened
- Ensures reason fields reflect current state, not historical
- Supports accurate reporting

---

## 23. Copy Opp LeadSource from Account Source

**API Name:** `Copy_Opp_LeadSource_from_Account_Source`  
**Category:** DATA QUALITY - Field Update

### Purpose
Copies Account Source to Opportunity Lead Source for reporting consistency when lead source is missing.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Account.AccountSource` is populated
  - `LeadSource` is blank

### Business Process
1. New opportunity is created without lead source specified
2. Flow checks if Account has source information
3. If yes, copies `Account.AccountSource` to `Opportunity.LeadSource`
4. Lead source field is now populated for reporting

### Key Elements
- **AccountSourceCheck Formula:** `NOT(ISBLANK(Account.AccountSource))`
  - Returns TRUE if Account has source value
- **Check Account Source Decision:** Validates source exists
  - Branch: Source exists → Copy value
  - Default: Exit (no source to copy)
- **Copy Account Source to Lead Source Update:** Copies value
  - Sets `LeadSource = Account.AccountSource`

### Business Value
- Reporting consistency for lead source tracking
- Fills in missing lead source data automatically
- Supports marketing attribution analysis
- Reduces data entry burden

---

## 24. Last Discharge Date Opp

**API Name:** `Last_Discharge_Date_Opp`  
**Category:** DATA QUALITY - Field Update

### Purpose
Syncs discharge date from Opportunity to Account for account-level patient history tracking.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Discharge_Datecc__c` is populated

### Business Process
1. Opportunity is discharged (discharge date is populated)
2. Flow copies discharge date to Account record
3. Account now has most recent discharge date for the patient
4. Supports account-level history and readmission tracking

### Key Elements
- **Update Action:** Syncs date to Account
  - Object: Account (parent of Opportunity)
  - Sets `Last_Discharge_Date__pc = Discharge_Datecc__c`

### Business Value
- Account-level patient history tracking
- Supports readmission analysis at account level
- Enables reporting on patient discharge patterns
- Maintains synchronized data across related objects

---

## 25. Update Prev Scheduled Date Time Opp

**API Name:** `Update_Prev_Scheduled_Date_Time_Opp`  
**Category:** AUDIT TRAIL - Field Update

### Purpose
Tracks historical scheduled date/time changes for appointment management and patient communication.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - `Scheduled_Date_and_Time__c` changes (ISCHANGED)

### Business Process
1. Scheduled appointment date/time is changed
2. Flow captures the PRIOR value before it's overwritten
3. Stores prior value in `Previous_Scheduled_Date_and_Time__c`
4. History of schedule changes is now tracked

### Key Elements
- **PriorScheduledDateTime Formula:** `$Record__Prior.Scheduled_Date_and_Time__c`
  - Returns the old value before the change
- **Update Action:** Stamps prior value
  - Sets `Previous_Scheduled_Date_and_Time__c = Prior value`

### Business Value
- Schedule change tracking for audit trail
- Patient communication support (know when appointment was originally)
- Reschedule analysis and reporting
- Supports patient no-show investigation

---

## 26. Webform Associated Opp Checkbox update below 30days

**API Name:** `Webform_Associated_Opp_Checkbox_update_below_30days`  
**Category:** DATA QUALITY - Field Update

### Purpose
Flags opportunities from recent webform submissions (within 30 days) for lead source tracking and attribution.

### Trigger Conditions
- **Trigger Type:** Record Before Save
- **Object:** Opportunity
- **Conditions:** Always runs (on create or update)

### Business Process
1. Opportunity is created or updated
2. Flow looks up Account to check `Last_MC_Form_Submit_Date_Time__pc`
3. Calculates days between today and last webform submission
4. If ≤30 days, sets `Webform_Associated_Opportunity__c = TRUE`
5. Opportunity is now flagged as webform-sourced

### Key Elements
- **Get Account for webform Lookup:** Retrieves Account with webform date
  - Filters: Id = Opportunity Account
  - Queries: Last_MC_Form_Submit_Date_Time__pc
- **TotalCountDiffrence Formula:** `TODAY() - Account.Last_MC_Form_Submit_Date_Time__pc`
  - Returns number of days since webform submission
- **Checkbox for associated opportunity Decision:** Checks if ≤30 days
  - Branch: Days ≤ 30 → Set checkbox TRUE
  - Default: Exit (not webform-sourced)
- **set true Assignment:** Sets `Webform_Associated_Opportunity__c = TRUE`

### Business Value
- Lead source tracking for webform submissions
- Marketing attribution analysis
- Conversion rate measurement for web inquiries
- Webform ROI tracking

---

## 27. Opportunity Re Open DT Stamp

**API Name:** `Opportunity_Re_Open_DT_Stamp`  
**Category:** AUDIT TRAIL - Business Process

### Purpose
Timestamps opportunity reopening events and restores medical approval statuses to their pre-closure values.

### Trigger Conditions
- **Trigger Type:** Record After Save
- **Object:** Opportunity
- **Conditions:**
  - Prior `StageName` was "Closed"
  - Current `StageName` is NOT "Closed"
  - New stage is one of: Scheduled for Admission, Admitted, Admitted to Outpatient, Pre-Assessment, Pre-Admit

### Business Process
1. Closed opportunity is reopened (stage changes from Closed to open stage)
2. Flow stamps `Case_Re_Opened_DT__c` with current datetime
3. Flow looks up related `Case_Med_Approval__c` records
4. For each med approval:
   - Restores `Approval_Status__c` from `Approval_Status_Pre_Closure__c`
   - Restores `Review_Type__c` from `Review_Type_Pre_Closure__c`
5. Med approvals are now back to their pre-closure states

### Key Elements
- **Is_Reopen Formula:** Detects "Closed" → Open transition
  - Formula: `AND(ISPICKVAL(PRIORVALUE(StageName), "Closed"), NOT(ISPICKVAL(StageName, "Closed")))`
  - Returns TRUE if transitioning from Closed to non-Closed
- **Check_if_Reopened Decision:** Validates reopening occurred
  - Branch: Is_Reopen = TRUE → Continue
  - Default: Exit (not a reopening)
- **Get related med approvals for restore Lookup:** Retrieves med approvals to restore
  - Filters: `Opportunity__c = Current Opportunity`
  - Queries: Id, Approval_Status_Pre_Closure__c, Review_Type_Pre_Closure__c
- **Restore Med Approvals Update:** Restores pre-closure values
  - Sets `Approval_Status__c = Approval_Status_Pre_Closure__c`
  - Sets `Review_Type__c = Review_Type_Pre_Closure__c`
- **Stamp Re-Open Date Update:** Records reopening timestamp
  - Sets `Case_Re_Opened_DT__c = NOW()`

### Business Value
- Reopening audit trail for compliance and tracking
- Med approval lifecycle management and continuity
- Maintains status history across closure/reopening cycles
- Supports compliance with insurance authorization continuity
- Enables reporting on reopened opportunities

---

**END OF INDIVIDUAL FLOWS DOCUMENTATION**

*For flow consolidation details and deployment instructions, see [FLOW_CONSOLIDATION_MASTER_DOCUMENTATION.md](./FLOW_CONSOLIDATION_MASTER_DOCUMENTATION.md)*

