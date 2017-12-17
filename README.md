## Bug:

Tax applied repeatedly on edits of price set events.

The problem appears when the event price is a price set and tax is applied. Each time one opens the corresponding contribution and clicks 'save', everytime tax amount was deducted from tot_amount.

Jira Ticket link:
https://issues.civicrm.org/jira/projects/CRM/issues/CRM-20676?filter=allopenissues


## Finding:
If event is having price set and tax and then while editing contribution, tax was repeatedly deducted from `total_amount`.


In database table `civicrm_contribution` their are 3 fields `total_amount`, `net_amount` & `tax_amount`, 
net_amount = total_amount + tax_amount;
total_amount = net_amount - tax_amount

and in file "sites/all/modules/civicrm/CRM/Contribute/Form/Contribution.php" 1596, evertime contribution was edited, tax amount
was deducted from total_amount, so in final result contribution amount use to get change.

## Solution:
So instead of deducting tax from total amount, I changed and deducted tax from net amount.
