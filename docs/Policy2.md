

🔹 Updated Azure Policy: Using Subscription Display Name with Default Fallback

This policy: ✅ Uses subscription().displayName as the key.
✅ Checks if the display name starts with "di", and if so, defaults to "go".
✅ Maps the correct CostCenter-WBSCode parameter based on the subscription name.
✅ Flags resources as non-compliant if their CostCenter.WBSCode is not in the allowed list.


---

🔹 Updated Policy Definition

{
  "mode": "All",
  "parameters": {
    "subscriptionMapping": {
      "type": "Object",
      "metadata": {
        "description": "Mapping of Subscription Names to CostCenter-WBSCode List Prefix"
      },
      "defaultValue": {
        "AH": "AH",
        "AU": "AU",
        "EU": "EU",
        "GO": "GO"  // Default when subscription name starts with "di"
      }
    },
    "AH_CostCenter_WBSCode": {
      "type": "Array",
      "metadata": {
        "description": "Allowed CostCenter-WBSCode combinations for AH subscriptions"
      },
      "defaultValue": ["CC001.WB001", "CC002.WB002"]
    },
    "AU_CostCenter_WBSCode": {
      "type": "Array",
      "metadata": {
        "description": "Allowed CostCenter-WBSCode combinations for AU subscriptions"
      },
      "defaultValue": ["CC003.WB003", "CC004.WB004"]
    },
    "EU_CostCenter_WBSCode": {
      "type": "Array",
      "metadata": {
        "description": "Allowed CostCenter-WBSCode combinations for EU subscriptions"
      },
      "defaultValue": ["CC005.WB005", "CC006.WB006"]
    },
    "GO_CostCenter_WBSCode": {
      "type": "Array",
      "metadata": {
        "description": "Allowed CostCenter-WBSCode combinations for GO (Default)"
      },
      "defaultValue": ["CC007.WB007", "CC008.WB008"]
    }
  },
  "policyRule": {
    "if": {
      "not": {
        "value": "[concat(field('tags[CostCenter]'), '.', field('tags[WBSCode]'))]",
        "in": "[parameters(concat(if(contains(subscription().displayName, 'di'), 'GO', parameters('subscriptionMapping')[subscription().displayName]), '_CostCenter_WBSCode'))]"
      }
    },
    "then": {
      "effect": "Audit"
    }
  }
}


---

🔹 How This Works

1. Extracts the subscription name using subscription().displayName.


2. Checks if the name starts with "di" using:

"if(contains(subscription().displayName, 'di'), 'GO', parameters('subscriptionMapping')[subscription().displayName])"

If true, it defaults to "GO".

If false, it uses the mapped value from subscriptionMapping.



3. Uses the selected subscription prefix to retrieve the correct CostCenter_WBSCode list.


4. Concatenates the CostCenter and WBSCode values.


5. Checks if the concatenated value exists in the correct allowed list.


6. If not found, the resource is flagged as non-compliant.




---

🔹 Key Features

✅ Dynamically assigns "GO" when the subscription name starts with "di".
✅ Supports multiple subscriptions with their own mappings.
✅ Fully scalable—just add more subscriptions to subscriptionMapping.
✅ Flags non-compliant resources without blocking them (use "Deny" instead of "Audit" if needed).


---

🔹 Next Steps

🚀 To block non-compliant resources, change "effect": "Audit" to "Deny".
🔄 Would you like to apply this policy to specific resource types only (e.g., VMs, Storage)? 🚀

