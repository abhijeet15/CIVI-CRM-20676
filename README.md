## Bug:

Tax applied repeatedly on edits of price set events.

The problem appears when the event price is a price set and tax is applied. Each time one opens the corresponding contribution and clicks 'save', everytime tax amount was deducted from tot_amount.

Jira Ticket link:
https://issues.civicrm.org/jira/projects/CRM/issues/CRM-20676?filter=allopenissues


## Finding:
If event is having price set and tax and then while editing contribution, tax was repeatedly deducted from `total_amount`.
Also, when editing contribution, `total_amount` was POSTED empty and in file "sites/all/modules/civicrm/CRM/Contribute/Form/Contribution.php" 1596, evertime contribution was edited, tax amount
was deducted from total_amount, so in final result contribution amount use to get change.

## Solution:
So instead of deducting tax from total amount, I have assigned submitted total amount( which is empty ) with `total_amount` stored in database, as user is doing edit contribution and user is not allowed to change amount.

## Changes:
Class Name: CRM_Contribute_Form_Contribution
Method Name: submit();
Filename: sites/all/modules/civicrm/CRM/Contribute/Form/Contribution.php
Line No: 1527

// -- ---------------------------------------------
// -- CRM-20676 - Tax applied repeatedly on edits of price set events
// -- When set price set and tax for event, membeship contribution
// -- and then editing contribution, tax was repeatedly deducted from
// -- `total_amount`.
// -- Fix - here in this case when edit contribution, if price set, "$submittedValues['total_amount']"
// -- amount is set null and as at this point we are nit doing any amount change, so set it to
// -- value in database. 
// --
// --	( total_amount(POSTED) = total_amount(DATABASE) ).
// -- @author Abhijeet Bagul
// -- ---------------------------------------------
# - OLD
//$submittedValues['total_amount'] = CRM_Utils_Array::value('total_amount', $this->_values) - CRM_Utils_Array::value('tax_amount', $this->_values);
# - NEW
$submittedValues['total_amount'] = CRM_Utils_Array::value('total_amount', $this->_values);
// -- ---------------------------------------------
