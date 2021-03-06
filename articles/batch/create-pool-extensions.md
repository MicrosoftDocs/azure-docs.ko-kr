---
title: Batch 풀에서 확장 사용
description: 확장은 Batch 계산 노드에서 사후 프로 비전 구성 및 설치를 용이 하 게 하는 작은 응용 프로그램입니다.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 1bf9847af57347c143ee3d790d89988ba7cd48e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417447"
---
# <a name="use-extensions-with-batch-pools"></a>Batch 풀에서 확장 사용

확장은 Batch 계산 노드에서 사후 프로 비전 구성 및 설치를 용이 하 게 하는 작은 응용 프로그램입니다. Azure Batch에서 허용 하는 확장을 선택 하 고 프로 비전 될 때 계산 노드에 설치할 수 있습니다. 그런 다음 확장에서 원하는 작업을 수행할 수 있습니다.

사용할 확장의 라이브 상태를 확인 하 고 검색, 수정 또는 진단 기능을 실행 하기 위해 반환 하는 정보를 검색할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 확장이 포함 된 풀은 [가상 머신 구성을](nodes-and-pools.md#virtual-machine-configuration)사용 해야 합니다.
- CustomScript 확장 형식은 Azure Batch 서비스에 예약 되어 있으므로 재정의할 수 없습니다.

### <a name="supported-extensions"></a>지원 되는 확장

Batch 풀을 만들 때 현재 다음 확장을 설치할 수 있습니다. 

- [Linux](../virtual-machines/extensions/key-vault-linux.md) 및 [Windows](../virtual-machines/extensions/key-vault-windows.md) 에 대 한 Azure Key Vault 확장
- [Linux](../virtual-machines/extensions/oms-linux.md) 및 [Windows](../virtual-machines/extensions/oms-windows.md) 에 대 한 Log analytics 및 모니터링 확장
- Azure 보안 팩

지원 요청을 열어 추가 게시자 및/또는 확장 형식에 대 한 지원을 요청할 수 있습니다.

## <a name="create-a-pool-with-extensions"></a>확장을 사용 하 여 풀 만들기

아래 예제에서는 Azure Key Vault 확장을 사용 하는 Linux 노드의 Batch 풀을 만듭니다.

REST API URI

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

요청 본문

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>풀에서 확장 데이터 가져오기

아래 예제에서는 Azure Key Vault 확장에서 데이터를 검색 합니다.

REST API URI

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

응답 본문

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>다음 단계

- [응용 프로그램 및 데이터를 풀 노드에 복사](batch-applications-to-pool-nodes.md)하는 다양 한 방법에 대해 알아봅니다.
- [노드 및 풀](nodes-and-pools.md)작업에 대해 자세히 알아보세요.
