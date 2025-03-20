

"in": "[parameters(concat(if(equals(subscription().displayName, 'disabled'), 'GO', substring(subscription().displayName, 0, 2)), '_CostCenter_WBSCode'))]"
