To use only the first two characters of the subscription display name, we can modify the policy to extract the first two characters dynamically. Since Azure Policy does not support direct string slicing, we use conditional checks to determine the correct mapping.


---

🔹 Updated Policy Definition

This policy: ✅ Extracts the first two characters of subscription().displayName using conditional matching.
✅ Uses separate parameters for each subscription group based on the extracted prefix.
✅ Flags resources as non-compliant if their CostCenter.WBSCode is not found in the allowed list.

{
  "mode": "All",
  "parameters": {
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
    }
  },
  "policyRule": {
    "if": {
      "allOf": [
        {
          "anyOf": [
            {
              "value": "[subscription().displayName]",
              "like": "AH*"
            },
            {
              "value": "[subscription().displayName]",
              "like": "AU*"
            },
            {
              "value": "[subscription().displayName]",
              "like": "EU*"
            }
          ]
        },
        {
          "not": {
            "value": "[concat(field('tags[CostCenter]'), '.', field('tags[WBSCode]'))]",
            "in": "[parameters(concat(substring(subscription().displayName, 0, 2), '_CostCenter_WBSCode'))]"
          }
        }
      ]
    },
    "then": {
      "effect": "Audit"
    }
  }
}


---

🔹 How This Works

1. Extracts the first two characters of subscription().displayName using substring(subscription().displayName, 0, 2).


2. Uses those first two characters (e.g., AH, AU, EU) to select the correct parameter dynamically.


3. Checks if the concatenated CostCenter.WBSCode exists in the allowed list.


4. If it does not exist, the resource is flagged as non-compliant.




---

🔹 Why This Works

✅ Uses a dynamic approach to extract the first two characters.
✅ No manual mapping needed—any subscription with a matching prefix will apply the correct policy.
✅ Scalable—just add new prefixes (e.g., US, AP) as parameters.
✅ Easy to manage—matches subscription().displayName dynamically without using full names.


---

🔹 Next Steps

🚀 To block non-compliant resources, change "effect": "Audit" to "effect": "Deny".

🔄 To auto-correct tags, use "effect": "Modify" and update the CostCenter-WBSCode tag dynamically.

🔍 Would you like to enforce this only on specific resource types (e.g., VMs, Storage)? 🚀


