---
title: テンプレート リソースの場所
description: Azure Resource Manager テンプレートでリソースの場所を設定する方法について説明します。
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 24d278df8f71fecfaec4f0fa3a84172bf1db942b
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122408"
---
# <a name="set-resource-location-in-resource-manager-template"></a>Resource Manager テンプレートでリソースの場所を設定する

テンプレートをデプロイするときに、各リソースの場所を指定する必要があります。 場所は、リソース グループの場所と同じ場所である必要はありません。

## <a name="get-available-locations"></a>利用可能な場所を取得する

場所ごとに、異なるリソースの種類がサポートされます。 リソースの種類にサポートされている場所を取得するには、Azure PowerShell または Azure CLI を使用します。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>location パラメーターを使用する

テンプレートを柔軟にデプロイできるようにするには、パラメーターを使用してリソースの場所を指定します。 パラメーターの既定値を `resourceGroup().location` に設定します。

次の例は、パラメーターとして指定された場所にデプロイされたストレージ アカウントを示したものです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

* テンプレート関数の完全一覧が必要な場合、「 [Azure リソース マネージャーのテンプレートの関数](template-functions.md)」を参照してください。
* テンプレート ファイルの詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
