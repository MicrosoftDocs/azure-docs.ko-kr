---
title: Service Fabric 관리 클러스터 노드 형식에 가상 컴퓨터 확장 집합 확장 추가 (미리 보기)
description: Service Fabric 관리 클러스터 노드 형식에 가상 컴퓨터 확장 집합 확장을 추가 하는 방법은 다음과 같습니다.
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: a47908b511f79c18482e9d21e623f1cb4dc70ed1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737769"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Service Fabric 관리 클러스터 노드 형식에 가상 컴퓨터 확장 집합 확장 추가 (미리 보기)

Service Fabric 관리 클러스터의 각 노드 유형은 가상 머신 확장 집합에 의해 지원 됩니다. 이를 통해 [가상 컴퓨터 확장 집합 확장](../virtual-machines/extensions/overview.md) 을 Service Fabric 관리 클러스터 노드 형식에 추가할 수 있습니다.

[AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) PowerShell 명령을 사용 하 여 노드 형식에 가상 머신 확장 집합 확장을 추가할 수 있습니다.

또는 Azure Resource Manager 템플릿의 Service Fabric 관리 클러스터 노드 형식에 대 한 가상 머신 확장 집합 확장을 사용할 수 있습니다. 예를 들면 다음과 같습니다.

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

관리 클러스터 노드 형식 Service Fabric 구성 하는 방법에 대 한 자세한 내용은 [관리 되는 클러스터 노드 형식](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Service Fabric 관리형 클러스터에 대해 자세히 알아보려면 다음을 참고하세요.

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터 질문과 대답](./faq-managed-cluster.md)
