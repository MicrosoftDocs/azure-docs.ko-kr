---
title: 데이터 암호화-Azure CLI-Azure Database for MySQL
description: Azure CLI를 사용 하 여 Azure Database for MySQL에 대 한 데이터 암호화를 설정 하 고 관리 하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6d9abc67035b4581a028d8e59ef080b4f1ffa5b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96519045"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Database for MySQL에 대 한 데이터 암호화

Azure CLI를 사용 하 여 Azure Database for MySQL 데이터 암호화를 설정 하 고 관리 하는 방법을 알아봅니다.

## <a name="prerequisites-for-azure-cli"></a>Azure CLI에 대 한 필수 구성 요소

* Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
* 고객 관리 키에 사용할 키 자격 증명 모음 및 키를 만듭니다. 또한 키 자격 증명 모음에서 보호 제거 및 일시 삭제를 사용 하도록 설정 합니다.

  ```azurecli-interactive
  az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
  ```

* 만든 Azure Key Vault에서 Azure Database for MySQL의 데이터 암호화에 사용할 키를 만듭니다.

  ```azurecli-interactive
  az keyvault key create --name <key_name> -p software --vault-name <vault_name>
  ```

* 기존 키 자격 증명 모음을 사용 하려면 고객 관리 키로 사용할 다음 속성이 있어야 합니다.

  * [일시 삭제](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [보호 된 데이터 삭제](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * 보존 일 수를 90 일로 설정
  ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* 고객 관리형 키로 사용하려면 키에 다음 특성이 있어야 합니다.
  * 만료 날짜 없음
  * 사용 안 함 없음
  * **가져오기**, **래핑**, **래핑** 해제 작업 수행
  * recoverylevel 특성이 **복구** 가능으로 설정 됨 (보존 기간이 90 일로 설정 된 일시 삭제를 사용 해야 함)
  * 제거 보호 사용

다음 명령을 사용 하 여 위의 키 특성을 확인할 수 있습니다.

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```

## <a name="set-the-right-permissions-for-key-operations"></a>키 작업에 대 한 올바른 사용 권한 설정

1. Azure Database for MySQL에 대 한 관리 되는 id를 가져오는 방법에는 두 가지가 있습니다.

   ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>관리 id를 사용 하 여 새 Azure Database for MySQL 서버를 만듭니다.

   ```azurecli-interactive
   az mysql server create --name -g <resource_group> --location <locations> --storage-size size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> -geo-redundant-backup <Enabled/Disabled>  --assign-identity
   ```

   ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>관리 id를 가져오기 위해 기존 Azure Database for MySQL 서버를 업데이트 합니다.

   ```azurecli-interactive
   az mysql server update --name  <server name>  -g <resource_group> --assign-identity
   ```

2. **보안 주체** 에 대 한 **키 사용 권한** (**가져오기**, **래핑**, **래핑** 해제)을 MySQL 서버의 이름으로 설정 합니다.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대 한 데이터 암호화 설정

1. Azure Key Vault에서 만든 키를 사용 하 여 Azure Database for MySQL에 대 한 데이터 암호화를 사용 하도록 설정 합니다.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    키 url:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>복원 또는 복제 서버에 데이터 암호화 사용

Key Vault에 저장된 고객 관리형 키를 사용하여 Azure Database for MySQL을 암호화한 후에는 새로 만든 서버 복사본도 암호화됩니다. 로컬 또는 지역 복원 작업을 통해 또는 복제본 (로컬/지역 간) 작업을 통해이 새 복사본을 만들 수 있습니다. 따라서 암호화 된 MySQL 서버의 경우 다음 단계를 사용 하 여 암호화 된 복원 된 서버를 만들 수 있습니다.

### <a name="creating-a-restoredreplica-server"></a>복원/복제 서버 만들기

* [복원 서버 만들기](howto-restore-server-cli.md) 
* [읽기 복제본 서버 만들기](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>서버를 복원 하 고 나면 복원 된 서버의 데이터 암호화 유효성을 다시 검사 합니다.

*   복제 서버에 대 한 id 할당
```azurecli-interactive
az mysql server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   복원/복제 서버에 사용 해야 하는 기존 키를 가져옵니다.

```azurecli-interactive
az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   복원/복제 서버의 새 id에 대 한 정책을 설정 합니다.
  
```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* 암호화 키를 사용 하 여 복원 된/복제 서버의 유효성을 다시 검사 합니다.

```azurecli-interactive
az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Azure Database for MySQL에 사용 되는 키에 대 한 추가 기능

### <a name="get-the-key-used"></a>사용 된 키를 가져옵니다.

```azurecli-interactive
az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
```

키 url: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>사용 된 키를 나열 합니다.

```azurecli-interactive
az mysql server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>사용 중인 키를 삭제 합니다.

```azurecli-interactive
az mysql server key delete -g <resource_group> --kid <key url>
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Azure Resource Manager 템플릿을 사용 하 여 데이터 암호화 사용

Azure Portal 외에도 새 서버 및 기존 서버에 대해 Azure Resource Manager 템플릿을 사용 하 여 Azure Database for MySQL 서버에서 데이터 암호화를 사용 하도록 설정할 수 있습니다.

### <a name="for-a-new-server"></a>새 서버의 경우

미리 만든 Azure Resource Manager 템플릿 중 하나를 사용 하 여 데이터 암호화를 사용 하도록 설정 된 서버 프로 비전: [데이터 암호화 사용 예](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

이 Azure Resource Manager 템플릿은 Azure Database for MySQL 서버를 만들고 매개 변수로 전달 되는 키 **자격 증명 모음** 및 **키** 를 사용 하 여 서버에서 데이터 암호화를 사용 하도록 설정 합니다.

### <a name="for-an-existing-server"></a>기존 서버의 경우

또한 Azure Resource Manager 템플릿을 사용 하 여 기존 Azure Database for MySQL 서버에서 데이터 암호화를 사용 하도록 설정할 수 있습니다.

* 속성 개체의 속성 아래에서 이전에 복사한 Azure Key Vault 키의 리소스 ID를 전달 `Uri` 합니다.

* API 버전으로 *2020-01-01-preview* 를 사용 합니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>다음 단계

 데이터 암호화에 대 한 자세한 내용은 [고객 관리 키를 사용 하 여 데이터 암호화 Azure Database for MySQL](concepts-data-encryption-mysql.md)를 참조 하세요.
