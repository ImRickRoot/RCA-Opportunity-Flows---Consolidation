# Flow Deployment Checklist

**Document Purpose:** Complete list of all Salesforce flows and their corresponding flow tests for deployment to the next organization  
**Last Updated:** December 2025  
**Total Flows:** 27  
**Total Flow Tests:** 88

---

## DEPLOYMENT INSTRUCTIONS

1. Deploy all flows from `force-app/main/default/flows/` directory
2. Deploy all flow tests from `force-app/main/default/flowtests/` directory
3. Activate all flows after deployment
4. Run flow tests to verify functionality

---

## BDO ALERT FLOWS (3 Flows)

### 1. Opp_Admitted_BDO_Alert
**Flow File:** `Opp_Admitted_BDO_Alert.flow-meta.xml`  
**Flow Tests (3):**
- `Opp_Admitted_BDO_Alert_Test_Admitted.flowtest-meta.xml`
- `Opp_Admitted_BDO_Alert_Test_AdmittedToOutpatient.flowtest-meta.xml`
- `Opp_Admitted_BDO_Alert_Test_NonBDO_ShouldNotTrigger.flowtest-meta.xml`

### 2. Opp_Created_BDO_Alert
**Flow File:** `Opp_Created_BDO_Alert.flow-meta.xml`  
**Flow Tests (2):**
- `Opp_Created_BDO_Alert_Test_NewOppWithBDO.flowtest-meta.xml`
- `Opp_Created_BDO_Alert_Test_NonBDO_ShouldNotTrigger.flowtest-meta.xml`

### 3. Opp_Comment_BDO_Alert
**Flow File:** `Opp_Comment_BDO_Alert.flow-meta.xml`  
**Flow Tests (1):**
- `Opp_Comment_BDO_Alert_Test_CommentAdded.flowtest-meta.xml`

---

## CONSOLIDATED EMAIL ALERT FLOWS (2 Flows)

### 4. New_Admission_Avatar_Sync_Notification_Consolidated
**Flow File:** `New_Admission_Avatar_Sync_Notification_Consolidated.flow-meta.xml`  
**Flow Tests (22):**
- `New_Admission_Avatar_Sync_Notification_Consolidated_New_TEST.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_BracebridgeIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_BracebridgeOP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_BCATIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_DanversIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_DanversOP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_DevonIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_GreenvilleIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_GreenvilleOP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_IndianapolisIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_IndianapolisOP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_LighthouseIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_LHMayslandingOP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_MCATIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_MCATOP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_MonroevilleIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_MonroevilleOP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_RaritanBayIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_StCharlesIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_StCharlesOP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_SouthElginOP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_WestminsterIP.flowtest-meta.xml`
- `New_Admission_Avatar_Sync_Notification_Consolidated_Test_NoChange.flowtest-meta.xml`

### 5. VOB_Update_Avatar_Sync_Notification_Consolidated
**Flow File:** `VOB_Update_Avatar_Sync_Notification_Consolidated.flow-meta.xml`  
**Flow Tests (12):**
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_BracebridgeIP.flowtest-meta.xml`
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_BCATIP.flowtest-meta.xml`
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_MCATIP.flowtest-meta.xml`
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_MCATOP.flowtest-meta.xml`
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_DanversIP.flowtest-meta.xml`
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_DanversOP.flowtest-meta.xml`
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_DevonIP.flowtest-meta.xml`
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_DevonOP.flowtest-meta.xml`
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_LighthouseIP.flowtest-meta.xml`
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_LHMayslandingOP.flowtest-meta.xml`
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_RaritanBayIP.flowtest-meta.xml`
- `VOB_Update_Avatar_Sync_Notification_Consolidated_Test_WestminsterIP.flowtest-meta.xml`

---

## FIELD CALCULATION FLOWS (3 Flows)

### 6. Set_Admission_Type_Opp
**Flow File:** `Set_Admission_Type_Opp.flow-meta.xml`  
**Flow Tests (4):**
- `Set_Admission_Type_Opp_Test_FirstTimeAdmission.flowtest-meta.xml`
- `Set_Admission_Type_Opp_Test_Readmission.flowtest-meta.xml`
- `Set_Admission_Type_Opp_Test_AdmittedToOutpatient.flowtest-meta.xml`
- `Set_Admission_Type_Opp_Test_NoAdmissionDate_ShouldNotTrigger.flowtest-meta.xml`

### 7. Calculate_Days_Since_Last_Prior_Discharge_Opp
**Flow File:** `Calculate_Days_Since_Last_Prior_Discharge_Opp.flow-meta.xml`  
**Flow Tests (2):**
- `Calculate_Days_Since_Last_Prior_Discharge_Opp_Test_IPFacility.flowtest-meta.xml`
- `Calculate_Days_Since_Last_Prior_Discharge_Opp_Test_OPFacility.flowtest-meta.xml`

### 8. Facility_Admission_Notes_Field_Update_Flow_Opp
**Flow File:** `Facility_Admission_Notes_Field_Update_Flow_Opp.flow-meta.xml`  
**Flow Tests (2):**
- `Facility_Admission_Notes_Field_Update_Flow_Opp_Test_BracebridgeIP.flowtest-meta.xml`
- `Facility_Admission_Notes_Field_Update_Flow_Opp_Test_OPFacility_ShouldClear.flowtest-meta.xml`

---

## BUSINESS PROCESS AUTOMATION FLOWS (7 Flows)

### 9. Add_BDO_to_Opp_flow
**Flow File:** `Add_BDO_to_Opp_flow.flow-meta.xml`  
**Flow Tests (3):**
- `Add_BDO_to_Opp_flow_Test_NewOppWithReferent.flowtest-meta.xml`
- `Add_BDO_to_Opp_flow_Test_ReferentChanged.flowtest-meta.xml`
- `Add_BDO_to_Opp_flow_Test_NoReferent_ShouldNotTrigger.flowtest-meta.xml`

### 10. Auto_Close_Med_Approvals_on_Opp_Closure
**Flow File:** `Auto_Close_Med_Approvals_on_Opp_Closure.flow-meta.xml`  
**Flow Tests (1):**
- `Auto_Close_Med_Approvals_on_Opp_Closure_Test_Closed.flowtest-meta.xml`

### 11. CMA_Alert_on_Facility_Change_Opp
**Flow File:** `CMA_Alert_on_Facility_Change_Opp.flow-meta.xml`  
**Flow Tests (1):**
- `CMA_Alert_on_Facility_Change_Opp_Test_FacilityChanged.flowtest-meta.xml`

### 12. Automate_FC_Review_Request_Email_Opp
**Flow File:** `Automate_FC_Review_Request_Email_Opp.flow-meta.xml`  
**Flow Tests (3):**
- `Automate_FC_Review_Request_Email_Opp_Test_AdministrativeDischarge.flowtest-meta.xml`
- `Automate_FC_Review_Request_Email_Opp_Test_AgainstAdvice.flowtest-meta.xml`
- `Automate_FC_Review_Request_Email_Opp_Test_NormalDischarge_ShouldNotTrigger.flowtest-meta.xml`

### 13. Facility_Clearance_Required_Opp
**Flow File:** `Facility_Clearance_Required_Opp.flow-meta.xml`  
**Flow Tests (4):**
- `Facility_Clearance_Required_Opp_Test_AdministrativeDischarge.flowtest-meta.xml`
- `Facility_Clearance_Required_Opp_Test_DeclinedServices.flowtest-meta.xml`
- `Facility_Clearance_Required_Opp_Test_AWOL.flowtest-meta.xml`
- `Facility_Clearance_Required_Opp_Test_NormalDischarge_ShouldNotFlag.flowtest-meta.xml`

### 14. VOB_Email_Alert_Controller_Opp
**Flow File:** `VOB_Email_Alert_Controller_Opp.flow-meta.xml`  
**Flow Tests (2):**
- `VOB_Email_Alert_Controller_Opp_Test_Monroeville.flowtest-meta.xml`
- `VOB_Email_Alert_Controller_Opp_Test_NonMonroeville_ShouldNotTrigger.flowtest-meta.xml`

### 15. OP_Opp_Workflow_Emails_flow
**Flow File:** `OP_Opp_Workflow_Emails_flow.flow-meta.xml`  
**Flow Tests (2):**
- `OP_Opp_Workflow_Emails_flow_Test_Reschedule.flowtest-meta.xml`
- `OP_Opp_Workflow_Emails_flow_Test_InitialSchedule_ShouldNotTrigger.flowtest-meta.xml`

### 16. Transportation_Facility_Change_Post_Alert_Opp
**Flow File:** `Transportation_Facility_Change_Post_Alert_Opp.flow-meta.xml`  
**Flow Tests (1):**
- `Transportation_Facility_Change_Post_Alert_Opp_Test_FacilityChanged.flowtest-meta.xml`

---

## INTEGRATION & DATA MANAGEMENT FLOWS (12 Flows)

### 17. Pushed_to_Avatar_Opp
**Flow File:** `Pushed_to_Avatar_Opp.flow-meta.xml`  
**Flow Tests (2):**
- `Pushed_to_Avatar_Opp_Test_FirstSync.flowtest-meta.xml`
- `Pushed_to_Avatar_Opp_Test_AlreadySynced_ShouldNotTrigger.flowtest-meta.xml`

### 18. Default_Contact_Name_to_Opp_Name
**Flow File:** `Default_Contact_Name_to_Opp_Name.flow-meta.xml`  
**Flow Tests (2):**
- `Default_Contact_Name_to_Opp_Name_Test_NewOpp.flowtest-meta.xml`
- `Default_Contact_Name_to_Opp_Name_Test_AlreadyNamed_ShouldNotTrigger.flowtest-meta.xml`

### 19. Pushed_to_FinPay_Opp
**Flow File:** `Pushed_to_FinPay_Opp.flow-meta.xml`  
**Flow Tests (2):**
- `Pushed_to_FinPay_Opp_Test_FirstPush.flowtest-meta.xml`
- `Pushed_to_FinPay_Opp_Test_AlreadyPushed_ShouldNotTrigger.flowtest-meta.xml`

### 20. Patient_Added_to_BBH_Opp
**Flow File:** `Patient_Added_to_BBH_Opp.flow-meta.xml`  
**Flow Tests (1):**
- `Patient_Added_to_BBH_Opp_Test_TransferChecked.flowtest-meta.xml`

### 21. Update_Timestamp_Facility_Changed_After_Avatar_Sync_Opp
**Flow File:** `Update_Timestamp_Facility_Changed_After_Avatar_Sync_Opp.flow-meta.xml`  
**Flow Tests (1):**
- `Update_Timestamp_Facility_Changed_After_Avatar_Sync_Opp_Test_FacilityChanged.flowtest-meta.xml`

### 22. Nullify_Opp_Reason_for_Reopening
**Flow File:** `Nullify_Opp_Reason_for_Reopening.flow-meta.xml`  
**Flow Tests (1):**
- `Nullify_Opp_Reason_for_Reopening_Test_Reopened.flowtest-meta.xml`

### 23. Copy_Opp_LeadSource_from_Account_Source
**Flow File:** `Copy_Opp_LeadSource_from_Account_Source.flow-meta.xml`  
**Flow Tests (3):**
- `Copy_Opp_LeadSource_from_Account_Source_Test_MissingLeadSource.flowtest-meta.xml`
- `Copy_Opp_LeadSource_from_Account_Source_Test_LeadSourceExists_ShouldNotTrigger.flowtest-meta.xml`
- `Copy_Opp_LeadSource_from_Account_Source_Test_NoAccountSource_ShouldNotTrigger.flowtest-meta.xml`

### 24. Last_Discharge_Date_Opp
**Flow File:** `Last_Discharge_Date_Opp.flow-meta.xml`  
**Flow Tests (1):**
- `Last_Discharge_Date_Opp_Test_DischargeDatePopulated.flowtest-meta.xml`

### 25. Update_Prev_Scheduled_Date_Time_Opp
**Flow File:** `Update_Prev_Scheduled_Date_Time_Opp.flow-meta.xml`  
**Flow Tests (1):**
- `Update_Prev_Scheduled_Date_Time_Opp_Test_ScheduledDateTimeChanged.flowtest-meta.xml`

### 26. Webform_Associated_Opp_Checkbox_update_below_30days
**Flow File:** `Webform_Associated_Opp_Checkbox_update_below_30days.flow-meta.xml`  
**Flow Tests (2):**
- `Webform_Associated_Opp_Checkbox_update_below_30days_Test_Within30Days.flowtest-meta.xml`
- `Webform_Associated_Opp_Checkbox_update_below_30days_Test_Over30Days_ShouldNotSet.flowtest-meta.xml`

### 27. Opportunity_Re_Open_DT_Stamp
**Flow File:** `Opportunity_Re_Open_DT_Stamp.flow-meta.xml`  
**Flow Tests (6):**
- `Opportunity_Re_Open_DT_Stamp_Test_Reopened.flowtest-meta.xml`
- `Opportunity_Re_Open_DT_Stamp_Test_AdmittedStage.flowtest-meta.xml`
- `Opportunity_Re_Open_DT_Stamp_Test_AdmittedToOutpatientStage.flowtest-meta.xml`
- `Opportunity_Re_Open_DT_Stamp_Test_PreAssessmentStage.flowtest-meta.xml`
- `Opportunity_Re_Open_DT_Stamp_Test_PreAdmitStage.flowtest-meta.xml`
- `Opportunity_Re_Open_DT_Stamp_Test_NotReopened_ShouldNotTrigger.flowtest-meta.xml`

---

## SUMMARY

- **Total Flows:** 27
- **Total Flow Tests:** 88
- **Deployment Path:** `force-app/main/default/`
- **Flows Directory:** `flows/`
- **Flow Tests Directory:** `flowtests/`

---

**END OF DEPLOYMENT CHECKLIST**

