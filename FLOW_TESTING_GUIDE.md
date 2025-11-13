# Flow Testing Guide - End User Testing Steps

**Document Purpose:** Comprehensive testing steps for all 27 Salesforce flows for end users  
**Last Updated:** December 2025  
**Total Flows:** 27

---

## ADMIN PRE-STEPS

Before testing, ensure the following are configured:

1. **Email Templates:** Verify email templates exist for all alert flows
2. **Email Addresses:** Confirm the following email addresses are valid:
   - bdo@recoverycoa.com
   - medapprovals@recoverycoa.com
   - vob@recoverycoa.com
   - All facility email addresses (bracebridge@recoverycoa.com, danvers@recoverycoa.com, etc.)
3. **Record Types:** Ensure "Case_2_0" record type exists for Opportunities
4. **Custom Fields:** Verify all custom fields referenced in flows exist:
   - BDO_Region__c, BDO_Case_Owner__c, Referent__c
   - Admission_Date__c, Discharge_Datecc__c, Discharge_Type__c
   - Sync_to_Avatar__c, Send_VOB_to_Avatar__c
   - Facility__c, Facility_Admission_Notes__c
   - Admission_Type__c, Days_since_last_IP_discharge__c, Days_since_last_OP_discharge__c
   - Last_Case_Comment__c, Scheduled_Date_and_Time__c
   - Transfer_from_LH_to_BBH__c, Requires_Financial_Clearance__c
   - And all other referenced custom fields
5. **Test Data:** Create test Account records with:
   - Account with BDO Region populated (e.g., "Northeast", "Chicago")
   - Account with Referent relationship
   - Account with Last_MC_Form_Submit_Date_Time__pc populated (for webform tests)
6. **Related Objects:** Ensure test data exists for:
   - Case_Med_Approval__c records
   - Facility_Clearance__c records
   - Transportation_Request__c records

---

## BDO ALERT FLOWS

### 1. Opp Admitted BDO Alert
**Flow API:** `Opp_Admitted_BDO_Alert`  
**Automations Included:** Email Alert to BDO Team

**Test Case 1: Opportunity Stage Changes to Admitted**
- **Precondition:**
  - Create an Opportunity with StageName = "Qualification"
  - Set BDO_Region__c = "Northeast" (or any BDO region, not "Non-BDO")
  - Set CloseDate = any future date
- **Action:** Change StageName to "Admitted" and save the record.
- **Expected Result:**
  - Email notification is sent to bdo@recoverycoa.com
  - Email contains opportunity details (name, patient, facility, stage)

**Test Case 2: Opportunity Stage Changes to Admitted to Outpatient**
- **Precondition:**
  - Create an Opportunity with StageName = "Qualification"
  - Set BDO_Region__c = "Chicago" (or any BDO region)
  - Set CloseDate = any future date
- **Action:** Change StageName to "Admitted to Outpatient" and save the record.
- **Expected Result:**
  - Email notification is sent to bdo@recoverycoa.com
  - Email contains opportunity details

**Test Case 3: Non-BDO Opportunity (Should Not Trigger)**
- **Precondition:**
  - Create an Opportunity with StageName = "Qualification"
  - Set BDO_Region__c = "Non-BDO"
  - Set CloseDate = any future date
- **Action:** Change StageName to "Admitted" and save the record.
- **Expected Result:**
  - No email notification is sent
  - Flow does not execute

---

### 2. Opp Created BDO Alert
**Flow API:** `Opp_Created_BDO_Alert`  
**Automations Included:** Email Alert to BDO Team

**Test Case 1: New Opportunity with BDO Region**
- **Precondition:** None (creating new record)
- **Action:**
  - Create a new Opportunity record
  - Set BDO_Region__c = "Northeast" (or any BDO region, not "Non-BDO")
  - Set StageName = "Qualification"
  - Set CloseDate = any future date
  - Save the record
- **Expected Result:**
  - Email notification is sent to bdo@recoverycoa.com immediately upon creation
  - Email contains opportunity name, patient name, BDO region, creation date

**Test Case 2: New Opportunity with Non-BDO Region (Should Not Trigger)**
- **Precondition:** None (creating new record)
- **Action:**
  - Create a new Opportunity record
  - Set BDO_Region__c = "Non-BDO"
  - Set StageName = "Qualification"
  - Set CloseDate = any future date
  - Save the record
- **Expected Result:**
  - No email notification is sent
  - Flow does not execute

---

### 3. Opp Comment BDO Alert
**Flow API:** `Opp_Comment_BDO_Alert`  
**Automations Included:** Email Alert to BDO and Manager

**Test Case 1: Comment Added to Opportunity**
- **Precondition:**
  - Create an Opportunity record
  - Ensure Last_Case_Comment__c field is blank or has a different value
  - Link Opportunity to an Account that has a Referent__c relationship
  - Ensure the Referent Account has an Owner (BDO User) with email address
- **Action:**
  - Update Last_Case_Comment__c field with new comment text (e.g., "Patient requested callback")
  - Save the record
- **Expected Result:**
  - Email notification is sent to the BDO user's email address
  - If BDO user has a Manager (ManagerId populated), a copy of the email is sent to the manager
  - Email contains the new comment and opportunity details

**Test Case 2: BDO User Without Manager**
- **Precondition:**
  - Create an Opportunity record
  - Link to Account with Referent__c that has an Owner (BDO User)
  - Ensure the BDO User does NOT have a ManagerId populated
- **Action:**
  - Update Last_Case_Comment__c field with new comment text
  - Save the record
- **Expected Result:**
  - Email notification is sent only to the BDO user
  - No email is sent to manager (since ManagerId is blank)

---

## CONSOLIDATED EMAIL ALERT FLOWS

### 4. New Admission Avatar Sync Notification (Consolidated)
**Flow API:** `New_Admission_Avatar_Sync_Notification_Consolidated`  
**Automations Included:** Facility-Specific Email Alerts (21 facilities)

**Test Case 1: Bracebridge (IP) Facility**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Bracebridge (IP)"
  - Set Sync_to_Avatar__c = False
  - Set StageName = "Qualification"
  - Set CloseDate = any future date
- **Action:**
  - Check the Sync_to_Avatar__c checkbox (set to True)
  - Save the record
- **Expected Result:**
  - Email notification is sent to bracebridge@recoverycoa.com
  - Email contains opportunity details for review

**Test Case 2: MCAT (IP) Facility (Sends 2 Emails)**
- **Precondition:**
  - Create an Opportunity with Facility__c = "MCAT (IP)"
  - Set Sync_to_Avatar__c = False
- **Action:**
  - Check the Sync_to_Avatar__c checkbox (set to True)
  - Save the record
- **Expected Result:**
  - Email notification is sent to mcat@recoverycoa.com
  - Email notification is sent to capitalregion@recoverycoa.com
  - Both emails contain opportunity details

**Test Case 3: Danvers (OP) Facility**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Danvers (OP)"
  - Set Sync_to_Avatar__c = False
- **Action:**
  - Check the Sync_to_Avatar__c checkbox (set to True)
  - Save the record
- **Expected Result:**
  - Email notification is sent to danvers@recoverycoa.com

**Test Case 4: No Change to Sync Field (Should Not Trigger)**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Danvers (IP)"
  - Set Sync_to_Avatar__c = True (already checked)
- **Action:**
  - Update any other field (e.g., change StageName)
  - Do NOT change Sync_to_Avatar__c
  - Save the record
- **Expected Result:**
  - No email notification is sent
  - Flow does not execute (only triggers when Sync_to_Avatar__c changes)

**Note:** Test all 21 facilities listed in the documentation:
- Bracebridge (IP), Bracebridge (OP)
- MCAT (IP), MCAT (OP)
- BCAT (IP)
- Danvers (IP), Danvers (OP)
- Devon (IP)
- Greenville (IP), Greenville (OP)
- Indianapolis (IP), Indianapolis (OP)
- Lighthouse (IP), Lighthouse at Mays Landing (OP)
- Monroeville (IP), Monroeville (OP)
- Raritan Bay (IP)
- St. Charles (IP), St. Charles (OP)
- South Elgin (OP MH/ED)
- Westminster (IP)

---

### 5. VOB Update Avatar Sync Notification (Consolidated)
**Flow API:** `VOB_Update_Avatar_Sync_Notification_Consolidated`  
**Automations Included:** Facility-Specific VOB Email Alerts (11 facilities)

**Test Case 1: Bracebridge (IP) VOB Update**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Bracebridge (IP)"
  - Set Send_VOB_to_Avatar__c = False
  - Set StageName = "Qualification"
- **Action:**
  - Check the Send_VOB_to_Avatar__c checkbox (set to True)
  - Save the record
- **Expected Result:**
  - Email notification is sent to bracebridge@recoverycoa.com
  - Email contains VOB update notification with opportunity details

**Test Case 2: BCAT (IP) VOB Update**
- **Precondition:**
  - Create an Opportunity with Facility__c = "BCAT (IP)"
  - Set Send_VOB_to_Avatar__c = False
- **Action:**
  - Check the Send_VOB_to_Avatar__c checkbox (set to True)
  - Save the record
- **Expected Result:**
  - Email notification is sent to bcataim@recoverycoa.com

**Test Case 3: MCAT (OP) VOB Update**
- **Precondition:**
  - Create an Opportunity with Facility__c = "MCAT (OP)"
  - Set Send_VOB_to_Avatar__c = False
- **Action:**
  - Check the Send_VOB_to_Avatar__c checkbox (set to True)
  - Save the record
- **Expected Result:**
  - Email notification is sent to capitalregion@recoverycoa.com

**Note:** Test all 11 facilities listed in the documentation:
- Bracebridge (IP), BCAT (IP)
- MCAT (IP), MCAT (OP)
- Danvers (OP)
- Devon (IP), Devon (OP)
- Lighthouse (IP), Lighthouse at Mays Landing (OP)
- Raritan Bay (IP)
- Westminster (IP)

---

## FIELD CALCULATION FLOWS

### 6. Set Admission Type Opp
**Flow API:** `Set_Admission_Type_Opp`  
**Automations Included:** Field Update - Admission_Type__c

**Test Case 1: First Time Admission (No Prior Admission)**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Ensure the Account has NO previous Opportunities with StageName = "Admitted" or "Admitted to Outpatient"
  - Set StageName = "Qualification"
  - Set Admission_Date__c = today's date or future date
- **Action:**
  - Change StageName to "Admitted"
  - Ensure Admission_Date__c is populated
  - Save the record
- **Expected Result:**
  - Admission_Type__c field is automatically set to "First Time Admission"
  - Field is updated before the record saves (Record Before Save flow)

**Test Case 2: Readmission (Prior Admission Within 180 Days)**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Ensure the Account has a previous Opportunity with:
    - StageName = "Admitted" or "Admitted to Outpatient"
    - Discharge_Datecc__c = 90 days ago (within 180 days)
  - Set StageName = "Qualification" on new Opportunity
  - Set Admission_Date__c = today's date
- **Action:**
  - Change StageName to "Admitted"
  - Ensure Admission_Date__c is populated
  - Save the record
- **Expected Result:**
  - Admission_Type__c field is automatically set to "Readmission"
  - Flow calculates days between current admission and prior discharge (90 days < 180 days)

**Test Case 3: First Time Admission (Prior Discharge Over 180 Days Ago)**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Ensure the Account has a previous Opportunity with:
    - StageName = "Admitted"
    - Discharge_Datecc__c = 200 days ago (over 180 days)
  - Set StageName = "Qualification" on new Opportunity
  - Set Admission_Date__c = today's date
- **Action:**
  - Change StageName to "Admitted"
  - Save the record
- **Expected Result:**
  - Admission_Type__c field is automatically set to "First Time Admission"
  - Flow calculates 200 days > 180 days, so it's considered first time

**Test Case 4: Admitted to Outpatient Stage**
- **Precondition:**
  - Create an Opportunity linked to an Account with no prior admissions
  - Set StageName = "Qualification"
  - Set Admission_Date__c = today's date
- **Action:**
  - Change StageName to "Admitted to Outpatient"
  - Save the record
- **Expected Result:**
  - Admission_Type__c field is automatically set to "First Time Admission"

**Test Case 5: No Admission Date (Should Not Trigger)**
- **Precondition:**
  - Create an Opportunity with StageName = "Qualification"
  - Leave Admission_Date__c blank
- **Action:**
  - Change StageName to "Admitted"
  - Do NOT populate Admission_Date__c
  - Save the record
- **Expected Result:**
  - Flow does not execute
  - Admission_Type__c field remains unchanged

---

### 7. Calculate Days Since Last Prior Discharge Opp
**Flow API:** `Calculate_Days_Since_Last_Prior_Discharge_Opp`  
**Automations Included:** Field Updates - Days_since_last_IP_discharge__c, Days_since_last_OP_discharge__c

**Test Case 1: IP Facility - Calculate Days Since Last IP Discharge**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Ensure the Account has a previous Opportunity with:
    - Record Type = "Case_2_0"
    - Facility__c contains "(IP)" (e.g., "Danvers (IP)")
    - Discharge_Datecc__c = 45 days ago
    - Discharge_Datecc__c is NOT null
  - Set Facility__c = "Danvers (IP)" on new Opportunity
  - Set StageName = "Qualification"
- **Action:**
  - Populate Admission_Date__c = today's date
  - Save the record
- **Expected Result:**
  - Days_since_last_IP_discharge__c field is automatically calculated and set to 45
  - Field is updated before the record saves

**Test Case 2: OP Facility - Calculate Days Since Last OP Discharge**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Ensure the Account has a previous Opportunity with:
    - Record Type = "Case_2_0"
    - Facility__c contains "(OP)" (e.g., "Danvers (OP)")
    - Discharge_Datecc__c = 30 days ago
    - Discharge_Datecc__c is NOT null
  - Set Facility__c = "Danvers (OP)" on new Opportunity
  - Set StageName = "Qualification"
- **Action:**
  - Populate Admission_Date__c = today's date
  - Save the record
- **Expected Result:**
  - Days_since_last_OP_discharge__c field is automatically calculated and set to 30
  - Field is updated before the record saves

**Test Case 3: IP Facility with No Prior IP Discharge**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Ensure the Account has NO previous Opportunities with Facility containing "(IP)"
  - Set Facility__c = "Devon (IP)" on new Opportunity
- **Action:**
  - Populate Admission_Date__c = today's date
  - Save the record
- **Expected Result:**
  - Days_since_last_IP_discharge__c field remains blank (no prior IP discharge found)

---

### 8. Facility Admission Notes Field Update Flow Opp
**Flow API:** `Facility_Admission_Notes_Field_Update_Flow_Opp`  
**Automations Included:** Field Update - Facility_Admission_Notes__c

**Test Case 1: Bracebridge (IP) Facility**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Bracebridge (IP)"
  - Set StageName = "Qualification"
- **Action:**
  - Save the record (create or update)
- **Expected Result:**
  - Facility_Admission_Notes__c field is automatically populated with COVID-19 admission policy text
  - Text includes 7-10 day quarantine period information
  - Field is updated before the record saves

**Test Case 2: Danvers (IP) Facility**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Danvers (IP)"
- **Action:**
  - Save the record
- **Expected Result:**
  - Facility_Admission_Notes__c field is automatically populated with COVID-19 policy text

**Test Case 3: OP Facility (Should Clear Field)**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Danvers (OP)"
  - Set Facility_Admission_Notes__c = "Some existing notes"
- **Action:**
  - Save the record
- **Expected Result:**
  - Facility_Admission_Notes__c field is automatically cleared (set to blank)
  - COVID policy does not apply to OP facilities

**Note:** Test all 10 IP facilities:
- Bracebridge (IP), Danvers (IP), Devon (IP), Indianapolis (IP), Lighthouse (IP)
- MCAT (IP), Monroeville (IP), Raritan Bay (IP), St. Charles (IP), Westminster (IP)

---

## BUSINESS PROCESS AUTOMATION FLOWS

### 9. Add BDO to Opp flow
**Flow API:** `Add_BDO_to_Opp_flow`  
**Automations Included:** Field Update - BDO_Case_Owner__c

**Test Case 1: New Opportunity with Referent**
- **Precondition:**
  - Create a new Opportunity record
  - Ensure an Account exists with Record Type = "Referrer" (or appropriate referent type)
  - Ensure the Referent Account has an Owner (User record)
- **Action:**
  - Set Referent__c = [Select the Referent Account]
  - Set StageName = "Qualification"
  - Set CloseDate = any future date
  - Save the record (create)
- **Expected Result:**
  - BDO_Case_Owner__c field is automatically populated with the Referent Account's OwnerId
  - Field is updated after the record saves

**Test Case 2: Referent Changed on Existing Opportunity**
- **Precondition:**
  - Create an Opportunity with Referent__c = [Referent Account A]
  - Ensure Referent Account A has Owner = User A
  - Ensure Referent Account B exists with Owner = User B
- **Action:**
  - Change Referent__c from Referent Account A to Referent Account B
  - Save the record
- **Expected Result:**
  - BDO_Case_Owner__c field is automatically updated to User B's Id
  - Field reflects the new Referent Account's owner

**Test Case 3: No Referent (Should Not Trigger)**
- **Precondition:**
  - Create a new Opportunity record
- **Action:**
  - Leave Referent__c blank
  - Set StageName = "Qualification"
  - Save the record
- **Expected Result:**
  - Flow does not execute
  - BDO_Case_Owner__c field remains blank

---

### 10. Auto Close Med Approvals on Opp Closure
**Flow API:** `Auto_Close_Med_Approvals_on_Opp_Closure`  
**Automations Included:** Field Updates on Case_Med_Approval__c records

**Test Case 1: Opportunity Closed with Open Med Approvals**
- **Precondition:**
  - Create an Opportunity with Record Type = "Admissions" (RecordTypeId = "01261000000X5QbAAK")
  - Set StageName = "Admitted"
  - Create related Case_Med_Approval__c records:
    - Approval_Status__c = "Pending Review"
    - Review_Type__c = "Initial Review"
    - Opportunity__c = [Link to Opportunity]
- **Action:**
  - Change StageName to "Closed"
  - Save the record
- **Expected Result:**
  - All related Case_Med_Approval__c records are updated:
    - Approval_Status_Pre_Closure__c = "Pending Review" (original value stored)
    - Approval_Status__c = "Closed via Opportunity"
    - Review_Type_Pre_Closure__c = "Initial Review" (original value stored)
    - Review_Type__c = "Closed via Opportunity"
  - Pre-closure values are preserved for audit trail

**Test Case 2: Opportunity Closed with No Med Approvals**
- **Precondition:**
  - Create an Opportunity with Record Type = "Admissions"
  - Set StageName = "Admitted"
  - Ensure NO related Case_Med_Approval__c records exist
- **Action:**
  - Change StageName to "Closed"
  - Save the record
- **Expected Result:**
  - Flow executes but finds no med approvals to update
  - No errors occur

**Test Case 3: Med Approval Already Closed**
- **Precondition:**
  - Create an Opportunity with Record Type = "Admissions"
  - Create related Case_Med_Approval__c with Approval_Status__c = "Approved"
- **Action:**
  - Change StageName to "Closed"
  - Save the record
- **Expected Result:**
  - Med approval with status "Approved" is NOT updated (flow filters out Denied, Closed via Opportunity, Approved)

---

### 11. CMA Alert on Facility Change Opp
**Flow API:** `CMA_Alert_on_Facility_Change_Opp`  
**Automations Included:** Email Alert to Medical Approvals Team

**Test Case 1: Facility Changed with Active Med Approvals**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Danvers (IP)"
  - Create related Case_Med_Approval__c records:
    - Approval_Status__c = "Pending Review" (NOT "Closed via Opportunity" or "Denied")
    - Opportunity__c = [Link to Opportunity]
- **Action:**
  - Change Facility__c from "Danvers (IP)" to "Devon (IP)"
  - Save the record
- **Expected Result:**
  - Email notification is sent to medapprovals@recoverycoa.com
  - Email contains opportunity details and facility change information (old facility → new facility)
  - Team is notified to review insurance approval coverage for new facility

**Test Case 2: Facility Changed with Closed Med Approvals (Should Not Trigger)**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Danvers (IP)"
  - Create related Case_Med_Approval__c with Approval_Status__c = "Closed via Opportunity"
- **Action:**
  - Change Facility__c to "Devon (IP)"
  - Save the record
- **Expected Result:**
  - No email notification is sent
  - Flow checks approval status and exits if closed or denied

**Test Case 3: Facility Changed with No Med Approvals**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Danvers (IP)"
  - Ensure NO related Case_Med_Approval__c records exist
- **Action:**
  - Change Facility__c to "Devon (IP)"
  - Save the record
- **Expected Result:**
  - No email notification is sent
  - Flow exits when no active med approvals are found

---

### 12. Automate FC Review Request Email Opp
**Flow API:** `Automate_FC_Review_Request_Email_Opp`  
**Automations Included:** Facility-Specific Email Alerts (11 facilities)

**Test Case 1: Administrative Discharge**
- **Precondition:**
  - Create an Opportunity with StageName = "Admitted"
  - Set Facility__c = "Danvers (IP)"
  - Create related Facility_Clearance__c records:
    - Account__c = [Link to Opportunity's Account]
    - Status = "Active" or "Open"
    - Facility__c = "Danvers"
- **Action:**
  - Populate Discharge_Datecc__c = today's date
  - Set Discharge_Type__c = "Administrative Discharge"
  - Save the record
- **Expected Result:**
  - Email notification is sent to Danvers facility directors
  - Email contains patient name, discharge date, facility, discharge type
  - Email includes direct Salesforce links to FC record and Account record
  - Instructions to remove FC if no longer needed

**Test Case 2: Against Advice Discharge**
- **Precondition:**
  - Create an Opportunity with StageName = "Admitted"
  - Set Facility__c = "Bracebridge (IP)"
  - Create related Facility_Clearance__c records with Status = "Active"
- **Action:**
  - Populate Discharge_Datecc__c = today's date
  - Set Discharge_Type__c = "Against Advice"
  - Save the record
- **Expected Result:**
  - Email notification is sent to Bracebridge facility directors
  - Email contains review request information

**Test Case 3: Normal Discharge (Should Not Trigger)**
- **Precondition:**
  - Create an Opportunity with StageName = "Admitted"
  - Create related Facility_Clearance__c records
- **Action:**
  - Populate Discharge_Datecc__c = today's date
  - Set Discharge_Type__c = "Completed Treatment"
  - Save the record
- **Expected Result:**
  - No email notification is sent
  - Flow only triggers for Administrative Discharge or Against Advice

**Note:** Test all 11 facilities:
- Bracebridge (BBH), Capital Region (MCAT), Danvers, Devon, Greenville
- Indianapolis (INDY), Lighthouse (LH), Monroeville, Raritan Bay (RB), St. Charles, Westminster

---

### 13. Facility Clearance Required Opp
**Flow API:** `Facility_Clearance_Required_Opp`  
**Automations Included:** Field Update on Account - Patient_Account_Flag__c

**Test Case 1: Administrative Discharge**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Set StageName = "Admitted"
  - Ensure Account.Patient_Account_Flag__c = False
- **Action:**
  - Populate Discharge_Datecc__c = today's date
  - Set Discharge_Type__c = "Administrative Discharge"
  - Save the record
- **Expected Result:**
  - Account.Patient_Account_Flag__c is automatically set to True
  - Account is flagged for facility clearance review

**Test Case 2: Against Advice Discharge**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Set StageName = "Admitted"
- **Action:**
  - Populate Discharge_Datecc__c = today's date
  - Set Discharge_Type__c = "Against Advice"
  - Save the record
- **Expected Result:**
  - Account.Patient_Account_Flag__c is automatically set to True

**Test Case 3: Declined Services Discharge**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Set StageName = "Admitted"
- **Action:**
  - Populate Discharge_Datecc__c = today's date
  - Set Discharge_Type__c = "Declined Services"
  - Save the record
- **Expected Result:**
  - Account.Patient_Account_Flag__c is automatically set to True

**Test Case 4: AWOL Discharge**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Set StageName = "Admitted"
- **Action:**
  - Populate Discharge_Datecc__c = today's date
  - Set Discharge_Type__c = "AWOL"
  - Save the record
- **Expected Result:**
  - Account.Patient_Account_Flag__c is automatically set to True

**Test Case 5: Normal Discharge (Should Not Flag)**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Set StageName = "Admitted"
- **Action:**
  - Populate Discharge_Datecc__c = today's date
  - Set Discharge_Type__c = "Completed Treatment"
  - Save the record
- **Expected Result:**
  - Account.Patient_Account_Flag__c remains False
  - Flow does not flag account for normal discharges

---

### 14. VOB Email Alert Controller Opp
**Flow API:** `VOB_Email_Alert_Controller_Opp`  
**Automations Included:** Email Alert to VOB Team (Monroeville-specific)

**Test Case 1: Monroeville Facility VOB Update**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Monroeville (IP)" (or any facility containing "Monroeville")
  - Set Send_VOB_to_Avatar__c = False
  - Set StageName = "Qualification"
- **Action:**
  - Check the Send_VOB_to_Avatar__c checkbox (set to True)
  - Save the record
- **Expected Result:**
  - Email notification is sent to vob@recoverycoa.com
  - Email contains VOB update notification with opportunity details

**Test Case 2: Non-Monroeville Facility (Should Not Trigger)**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Danvers (IP)"
  - Set Send_VOB_to_Avatar__c = False
- **Action:**
  - Check the Send_VOB_to_Avatar__c checkbox (set to True)
  - Save the record
- **Expected Result:**
  - No email notification is sent
  - Flow only triggers for Monroeville facilities

---

### 15. OP Opp Workflow Emails flow
**Flow API:** `OP_Opp_Workflow_Emails_flow`  
**Automations Included:** Subflow Call - OP_Opp_Assessment_Rescheduled_Workflow

**Test Case 1: OP Appointment Rescheduled**
- **Precondition:**
  - Create an Opportunity with:
    - Facility__c = "Danvers (OP)" (or any of 8 supported OP facilities)
    - StageName = "Scheduled for Admission"
    - Sync_to_Avatar__c = True
    - Scheduled_Date_and_Time__c = "2025-11-20 10:00 AM" (prior value exists)
- **Action:**
  - Change Scheduled_Date_and_Time__c to "2025-11-22 2:00 PM" (new date/time)
  - Save the record
- **Expected Result:**
  - Subflow OP_Opp_Assessment_Rescheduled_Workflow is called
  - Reschedule notification email is sent to facility staff
  - Facility can coordinate patient communication about new appointment time

**Test Case 2: Initial Schedule (Should Not Trigger)**
- **Precondition:**
  - Create an Opportunity with:
    - Facility__c = "Danvers (OP)"
    - StageName = "Scheduled for Admission"
    - Sync_to_Avatar__c = True
    - Scheduled_Date_and_Time__c = blank (no prior value)
- **Action:**
  - Populate Scheduled_Date_and_Time__c = "2025-11-20 10:00 AM" (first time setting)
  - Save the record
- **Expected Result:**
  - No email notification is sent
  - Flow only triggers for reschedules (when prior value existed)

**Note:** Test all 8 supported OP facilities:
- Lighthouse at Mays Landing (OP), Lighthouse at Voorhees (OP)
- Raritan Bay (OP), Monroeville (OP), Indianapolis (OP)
- Westminster (OP), Danvers (OP), Devon (OP)

---

### 16. Transportation Facility Change Post Alert Opp
**Flow API:** `Transportation_Facility_Change_Post_Alert_Opp`  
**Automations Included:** Subflow Calls - Transportation cancellation and new request

**Test Case 1: Facility Changed After Transportation Alert Sent**
- **Precondition:**
  - Create an Opportunity with:
    - Facility__c = "Danvers (IP)"
    - StageName = "Scheduled for Admission" or "Pending Schedule"
  - Create related Transportation_Request__c record:
    - Opportunity__c = [Link to Opportunity]
    - Most_Recent_Transport_Request__c = True
    - FacilityName__c = "Danvers (IP)"
    - Alert_Email_Sent__c = True (alert already sent)
- **Action:**
  - Change Facility__c from "Danvers (IP)" to "Devon (IP)"
  - Save the record
- **Expected Result:**
  - Subflow 1 is called: Sends cancellation email to Danvers (old facility)
  - Subflow 2 is called: Sends new transportation request to Devon (new facility)
  - Both facilities are correctly informed

**Test Case 2: Facility Changed But Alert Not Sent Yet**
- **Precondition:**
  - Create an Opportunity with Facility__c = "Danvers (IP)"
  - Create related Transportation_Request__c with Alert_Email_Sent__c = False
- **Action:**
  - Change Facility__c to "Devon (IP)"
  - Save the record
- **Expected Result:**
  - No cancellation or new request emails are sent
  - Flow only triggers when Alert_Email_Sent__c = True

---

## INTEGRATION & DATA MANAGEMENT FLOWS

### 17. Pushed to Avatar Opp
**Flow API:** `Pushed_to_Avatar_Opp`  
**Automations Included:** Field Updates - Sent_to_Avatar_By__c, Sent_to_Avatar_by_Email__c

**Test Case 1: First Time Avatar Sync**
- **Precondition:**
  - Create an Opportunity with:
    - Sync_to_Avatar__c = False
    - Sent_to_Avatar_By__c = blank
    - Sent_to_Avatar_by_Email__c = blank
- **Action:**
  - Check the Sync_to_Avatar__c checkbox (set to True)
  - Save the record
- **Expected Result:**
  - Sent_to_Avatar_By__c is automatically populated with current user's full name (FirstName + LastName)
  - Sent_to_Avatar_by_Email__c is automatically populated with current user's email address
  - Audit trail records who synced and when

**Test Case 2: Already Synced (Should Not Re-stamp)**
- **Precondition:**
  - Create an Opportunity with:
    - Sync_to_Avatar__c = True
    - Sent_to_Avatar_By__c = "Previous User Name"
    - Sent_to_Avatar_by_Email__c = "previous@recoverycoa.com"
- **Action:**
  - Update any other field (do NOT change Sync_to_Avatar__c)
  - Save the record
- **Expected Result:**
  - Sent_to_Avatar_By__c remains "Previous User Name"
  - Sent_to_Avatar_by_Email__c remains "previous@recoverycoa.com"
  - Flow does not re-stamp (only stamps when field is null)

---

### 18. Default Contact Name to Opp Name
**Flow API:** `Default_Contact_Name_to_Opp_Name`  
**Automations Included:** Field Update - Name

**Test Case 1: New Opportunity with Default Name**
- **Precondition:**
  - Create a new Opportunity record
  - Link to an Account with FirstName = "John" and LastName = "Doe"
  - Name field = "New Opportunity" (default) or blank
- **Action:**
  - Save the record (create)
- **Expected Result:**
  - Name field is automatically updated to format: "John Doe - Opp [OpportunityId]"
  - Field is updated before the record saves

**Test Case 2: Opportunity Already Has Custom Name**
- **Precondition:**
  - Create an Opportunity with Name = "Custom Opportunity Name"
- **Action:**
  - Save the record
- **Expected Result:**
  - Name field remains "Custom Opportunity Name"
  - Flow only updates if Name is null or contains "New Opportunity"

---

### 19. Pushed to FinPay Opp
**Flow API:** `Pushed_to_FinPay_Opp`  
**Automations Included:** Field Update - Time_Date_FinPay_File_Pushed__c

**Test Case 1: First Time FinPay Push**
- **Precondition:**
  - Create an Opportunity with:
    - Requires_Financial_Clearance__c = False
    - Time_Date_FinPay_File_Pushed__c = blank
- **Action:**
  - Check the Requires_Financial_Clearance__c checkbox (set to True)
  - Save the record
- **Expected Result:**
  - Time_Date_FinPay_File_Pushed__c is automatically populated with current datetime (NOW())
  - Timestamp records when data was sent to FinPay

**Test Case 2: Already Pushed (Should Not Re-stamp)**
- **Precondition:**
  - Create an Opportunity with:
    - Requires_Financial_Clearance__c = True
    - Time_Date_FinPay_File_Pushed__c = [Previous datetime value]
- **Action:**
  - Update any other field
  - Save the record
- **Expected Result:**
  - Time_Date_FinPay_File_Pushed__c remains unchanged
  - Flow only stamps when field is null

---

### 20. Patient Added to BBH Opp
**Flow API:** `Patient_Added_to_BBH_Opp`  
**Automations Included:** Email Alert to Bracebridge

**Test Case 1: Transfer from Lighthouse to Bracebridge**
- **Precondition:**
  - Create an Opportunity with:
    - Transfer_from_LH_to_BBH__c = False
    - StageName = "Qualification"
- **Action:**
  - Check the Transfer_from_LH_to_BBH__c checkbox (set to True)
  - Save the record
- **Expected Result:**
  - Email notification is sent to bracebridge@recoverycoa.com
  - Email subject: "Patient Transfer from Lighthouse to Bracebridge"
  - Email contains patient name, opportunity details, transfer notification
  - BBH team can prepare for incoming transfer

---

### 21. Update Timestamp Facility Changed After Avatar Sync Opp
**Flow API:** `Update_Timestamp_Facility_Changed_After_Avatar_Sync_Opp`  
**Automations Included:** Field Update - FacilityChangedSinceLastSyncedToAvatar__c

**Test Case 1: Facility Changed After Avatar Sync**
- **Precondition:**
  - Create an Opportunity with:
    - Facility__c = "Danvers (IP)"
    - Sync_to_Avatar__c = True (already synced)
    - StageName = "Scheduled for Admission"
    - FacilityChangedSinceLastSyncedToAvatar__c = blank
- **Action:**
  - Change Facility__c from "Danvers (IP)" to "Devon (IP)"
  - Save the record
- **Expected Result:**
  - FacilityChangedSinceLastSyncedToAvatar__c is automatically populated with current datetime (NOW())
  - Timestamp indicates potential data sync issue (facility changed after EHR sync)
  - Field is updated before the record saves

**Test Case 2: Facility Changed But Not Synced Yet**
- **Precondition:**
  - Create an Opportunity with:
    - Facility__c = "Danvers (IP)"
    - Sync_to_Avatar__c = False (not synced yet)
    - StageName = "Scheduled for Admission"
- **Action:**
  - Change Facility__c to "Devon (IP)"
  - Save the record
- **Expected Result:**
  - Flow does not execute
  - FacilityChangedSinceLastSyncedToAvatar__c remains blank

---

### 22. Nullify Opp Reason for Reopening
**Flow API:** `Nullify_Opp_Reason_for_Reopening`  
**Automations Included:** Field Updates - Reason__c, Specific_Case_Reason__c

**Test Case 1: Opportunity Reopened from Closed**
- **Precondition:**
  - Create an Opportunity with:
    - StageName = "Closed"
    - Reason__c = "Patient Declined"
    - Specific_Case_Reason__c = "Financial Concerns"
- **Action:**
  - Change StageName from "Closed" to "Scheduled for Admission" (or any open stage)
  - Save the record
- **Expected Result:**
  - Reason__c is automatically cleared (set to null)
  - Specific_Case_Reason__c is automatically cleared (set to null)
  - Fields are ready for new data if opportunity closes again
  - Field updates occur before the record saves

---

### 23. Copy Opp LeadSource from Account Source
**Flow API:** `Copy_Opp_LeadSource_from_Account_Source`  
**Automations Included:** Field Update - LeadSource

**Test Case 1: Missing LeadSource with Account Source**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Set Account.AccountSource = "Web"
  - Set Opportunity.LeadSource = blank
  - Set StageName = "Qualification"
- **Action:**
  - Save the record
- **Expected Result:**
  - Opportunity.LeadSource is automatically populated with "Web" (copied from Account.AccountSource)
  - Field is updated after the record saves

**Test Case 2: LeadSource Already Exists (Should Not Trigger)**
- **Precondition:**
  - Create an Opportunity with LeadSource = "Referral"
  - Link to Account with AccountSource = "Web"
- **Action:**
  - Save the record
- **Expected Result:**
  - LeadSource remains "Referral"
  - Flow only copies when LeadSource is blank

**Test Case 3: No Account Source (Should Not Trigger)**
- **Precondition:**
  - Create an Opportunity with LeadSource = blank
  - Link to Account with AccountSource = blank
- **Action:**
  - Save the record
- **Expected Result:**
  - LeadSource remains blank
  - Flow only copies when AccountSource is populated

---

### 24. Last Discharge Date Opp
**Flow API:** `Last_Discharge_Date_Opp`  
**Automations Included:** Field Update on Account - Last_Discharge_Date__pc

**Test Case 1: Discharge Date Populated**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Set StageName = "Admitted"
  - Ensure Account.Last_Discharge_Date__pc = blank or old date
- **Action:**
  - Populate Discharge_Datecc__c = "2025-11-15" (today's date or any date)
  - Save the record
- **Expected Result:**
  - Account.Last_Discharge_Date__pc is automatically updated to "2025-11-15"
  - Account now has most recent discharge date for patient history tracking
  - Field is updated after the record saves

---

### 25. Update Prev Scheduled Date Time Opp
**Flow API:** `Update_Prev_Scheduled_Date_Time_Opp`  
**Automations Included:** Field Update - Previous_Scheduled_Date_and_Time__c

**Test Case 1: Scheduled Date/Time Changed**
- **Precondition:**
  - Create an Opportunity with:
    - Scheduled_Date_and_Time__c = "2025-11-20 10:00 AM"
    - Previous_Scheduled_Date_and_Time__c = blank
- **Action:**
  - Change Scheduled_Date_and_Time__c to "2025-11-22 2:00 PM"
  - Save the record
- **Expected Result:**
  - Previous_Scheduled_Date_and_Time__c is automatically populated with "2025-11-20 10:00 AM" (prior value)
  - History of schedule changes is tracked
  - Field is updated after the record saves

---

### 26. Webform Associated Opp Checkbox update below 30days
**Flow API:** `Webform_Associated_Opp_Checkbox_update_below_30days`  
**Automations Included:** Field Update - Webform_Associated_Opportunity__c

**Test Case 1: Account Webform Within 30 Days**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Set Account.Last_MC_Form_Submit_Date_Time__pc = 15 days ago (within 30 days)
  - Set Opportunity.Webform_Associated_Opportunity__c = False
- **Action:**
  - Save the record (create or update)
- **Expected Result:**
  - Webform_Associated_Opportunity__c is automatically set to True
  - Opportunity is flagged as webform-sourced
  - Field is updated before the record saves

**Test Case 2: Account Webform Over 30 Days Ago**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Set Account.Last_MC_Form_Submit_Date_Time__pc = 45 days ago (over 30 days)
- **Action:**
  - Save the record
- **Expected Result:**
  - Webform_Associated_Opportunity__c remains False
  - Flow only flags opportunities within 30 days of webform submission

**Test Case 3: No Webform Date on Account**
- **Precondition:**
  - Create an Opportunity linked to an Account
  - Set Account.Last_MC_Form_Submit_Date_Time__pc = blank
- **Action:**
  - Save the record
- **Expected Result:**
  - Webform_Associated_Opportunity__c remains False
  - Flow exits when no webform date exists

---

### 27. Opportunity Re Open DT Stamp
**Flow API:** `Opportunity_Re_Open_DT_Stamp`  
**Automations Included:** Field Updates - Case_Re_Opened_DT__c, Med Approval restoration

**Test Case 1: Opportunity Reopened to Scheduled for Admission**
- **Precondition:**
  - Create an Opportunity with StageName = "Closed"
  - Create related Case_Med_Approval__c records:
    - Approval_Status_Pre_Closure__c = "Pending Review"
    - Review_Type_Pre_Closure__c = "Initial Review"
    - Approval_Status__c = "Closed via Opportunity"
    - Review_Type__c = "Closed via Opportunity"
- **Action:**
  - Change StageName from "Closed" to "Scheduled for Admission"
  - Save the record
- **Expected Result:**
  - Case_Re_Opened_DT__c is automatically populated with current datetime (NOW())
  - All related Case_Med_Approval__c records are restored:
    - Approval_Status__c = "Pending Review" (restored from Approval_Status_Pre_Closure__c)
    - Review_Type__c = "Initial Review" (restored from Review_Type_Pre_Closure__c)
  - Med approvals return to their pre-closure states

**Test Case 2: Opportunity Reopened to Admitted**
- **Precondition:**
  - Create an Opportunity with StageName = "Closed"
  - Create related Case_Med_Approval__c with pre-closure values stored
- **Action:**
  - Change StageName from "Closed" to "Admitted"
  - Save the record
- **Expected Result:**
  - Case_Re_Opened_DT__c is automatically populated
  - Med approvals are restored to pre-closure statuses

**Test Case 3: Opportunity Reopened to Admitted to Outpatient**
- **Precondition:**
  - Create an Opportunity with StageName = "Closed"
  - Create related Case_Med_Approval__c with pre-closure values
- **Action:**
  - Change StageName from "Closed" to "Admitted to Outpatient"
  - Save the record
- **Expected Result:**
  - Case_Re_Opened_DT__c is automatically populated
  - Med approvals are restored

**Test Case 4: Opportunity Reopened to Pre-Assessment**
- **Precondition:**
  - Create an Opportunity with StageName = "Closed"
  - Create related Case_Med_Approval__c with pre-closure values
- **Action:**
  - Change StageName from "Closed" to "Pre-Assessment"
  - Save the record
- **Expected Result:**
  - Case_Re_Opened_DT__c is automatically populated
  - Med approvals are restored

**Test Case 5: Opportunity Reopened to Pre-Admit**
- **Precondition:**
  - Create an Opportunity with StageName = "Closed"
  - Create related Case_Med_Approval__c with pre-closure values
- **Action:**
  - Change StageName from "Closed" to "Pre-Admit"
  - Save the record
- **Expected Result:**
  - Case_Re_Opened_DT__c is automatically populated
  - Med approvals are restored

**Test Case 6: Stage Change But Not Reopened (Should Not Trigger)**
- **Precondition:**
  - Create an Opportunity with StageName = "Qualification" (not Closed)
- **Action:**
  - Change StageName to "Proposal"
  - Save the record
- **Expected Result:**
  - Flow does not execute
  - Case_Re_Opened_DT__c remains blank
  - Flow only triggers when transitioning from Closed to open stage

---

## TESTING NOTES

1. **Email Verification:** Check email inboxes for all alert flows to confirm emails are received
2. **Field Updates:** Verify field values are updated correctly by checking the record after saving
3. **Audit Fields:** For timestamp fields, verify the datetime is populated with current time
4. **Related Records:** For flows that update related records, verify the related records are updated correctly
5. **Negative Tests:** Ensure flows do NOT execute when conditions are not met
6. **Data Integrity:** Verify no duplicate updates or re-stamping occurs when fields already have values

---

**END OF TESTING GUIDE**

