---
title: 템플릿에서 배포 스크립트에 대 한 개발 환경 구성 | Microsoft Docs
description: Azure Resource Manager 템플릿 (ARM 템플릿)에서 배포 스크립트에 대 한 개발 환경을 구성 합니다.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: b2e1ffb3cbd513766945864e33589c46284bf942
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200940"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>ARM 템플릿에서 배포 스크립트에 대 한 개발 환경 구성

배포 스크립트 이미지를 사용 하 여 ARM 템플릿 배포 스크립트를 개발 하 고 테스트 하기 위한 개발 환경을 만드는 방법에 대해 알아봅니다. [Azure container instance](../../container-instances/container-instances-overview.md) 를 만들거나 [Docker](https://docs.docker.com/get-docker/)를 사용할 수 있습니다. 이 문서에서는 두 옵션을 모두 다룹니다.

## <a name="prerequisites"></a>필수 조건

### <a name="azure-powershell-container"></a>Azure PowerShell 컨테이너

Azure PowerShell 배포 스크립트가 없는 경우 다음 콘텐츠를 사용 하 여 *hello.ps1* 파일을 만들 수 있습니다.

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

### <a name="azure-cli-container"></a>Azure CLI 컨테이너

Azure CLI 컨테이너 이미지의 경우 다음 콘텐츠를 사용 하 여 *hello.sh* 파일을 만들 수 있습니다.

```bash
firstname=$1
lastname=$2
output="{\"name\":{\"displayName\":\"$firstname $lastname\",\"firstName\":\"$firstname\",\"lastName\":\"$lastname\"}}"
echo -n "Hello "
echo $output | jq -r '.name.displayName'
```

> [!NOTE]
> Azure CLI 배포 스크립트를 실행 하는 경우 라는 환경 변수는 `AZ_SCRIPTS_OUTPUT_PATH` 스크립트 출력 파일의 위치를 저장 합니다. 환경 변수는 개발 환경 컨테이너에서 사용할 수 없습니다. Azure CLI 출력을 사용 하는 방법에 대 한 자세한 내용은 [CLI 스크립트에서 출력 작업](deployment-script-template.md#work-with-outputs-from-cli-script)을 참조 하세요.

## <a name="use-azure-powershell-container-instance"></a>Azure PowerShell 컨테이너 인스턴스 사용

컴퓨터에서 스크립트를 작성 하려면 저장소 계정을 만들고 컨테이너 인스턴스에 저장소 계정을 탑재 해야 합니다. 저장소 계정에 스크립트를 업로드 하 고 컨테이너 인스턴스에서 스크립트를 실행할 수 있습니다.

> [!NOTE]
> 스크립트를 테스트 하기 위해 만드는 저장소 계정은 배포 스크립트 서비스에서 스크립트를 실행 하는 데 사용 하는 것과 동일한 저장소 계정이 아닙니다. 배포 스크립트 서비스는 모든 실행 시 파일 공유로 고유한 이름을 만듭니다.

### <a name="create-an-azure-powershell-container-instance"></a>Azure PowerShell 컨테이너 인스턴스 만들기

다음 Azure Resource Manager 템플릿 (ARM 템플릿)은 컨테이너 인스턴스 및 파일 공유를 만든 다음 컨테이너 이미지에 파일 공유를 탑재 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

탑재 경로의 기본값은 `/mnt/azscripts/azscriptinput` 입니다. 이 경로는 컨테이너 인스턴스에서 파일 공유에 탑재 된 경로입니다.

템플릿에 지정 된 기본 컨테이너 이미지는 **mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2** 입니다. [지원 되는 모든 Azure PowerShell 버전](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)목록을 참조 하세요.

템플릿은 1800 초 후에 컨테이너 인스턴스를 일시 중단 합니다. 컨테이너 인스턴스가 종료 된 상태로 전환 되 고 세션이 종료 되기까지 30 분이 소요 됩니다.

템플릿을 배포하는 방법은 다음과 같습니다.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>배포 스크립트 업로드

저장소 계정에 배포 스크립트를 업로드 합니다. PowerShell 스크립트의 예는 다음과 같습니다.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Azure Portal 또는 Azure CLI를 사용 하 여 파일을 업로드할 수도 있습니다.

### <a name="test-the-deployment-script"></a>배포 스크립트 테스트

1. Azure Portal에서 컨테이너 인스턴스 및 저장소 계정을 배포한 리소스 그룹을 엽니다.
2. 컨테이너 그룹을 엽니다. 기본 컨테이너 그룹 이름은 *cg* 와 함께 추가 되는 프로젝트 이름입니다. 컨테이너 인스턴스가 **실행 중** 상태입니다.
3. 리소스 메뉴에서 **컨테이너** 를 선택 합니다. 컨테이너 인스턴스 이름은 *컨테이너* 에 추가 된 프로젝트 이름입니다.

    ![Azure Portal의 배포 스크립트 연결 컨테이너 인스턴스 스크린샷](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

4. **연결** 을 선택한 다음 **연결** 을 선택 합니다. 컨테이너 인스턴스에 연결할 수 없는 경우 컨테이너 그룹을 다시 시작 하 고 다시 시도 하세요.
5. 콘솔 창에서 다음 명령을 실행 합니다.

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    **Hello John Dole** 출력입니다.

    ![콘솔의 배포 스크립트 연결 컨테이너 인스턴스 테스트 출력의 스크린샷](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-an-azure-cli-container-instance"></a>Azure CLI 컨테이너 인스턴스 사용

컴퓨터에서 스크립트를 작성 하려면 저장소 계정을 만들고 컨테이너 인스턴스에 저장소 계정을 탑재 합니다. 그런 다음 저장소 계정에 스크립트를 업로드 하 고 컨테이너 인스턴스에서 스크립트를 실행할 수 있습니다.

> [!NOTE]
> 스크립트를 테스트 하기 위해 만드는 저장소 계정은 배포 스크립트 서비스에서 스크립트를 실행 하는 데 사용 하는 것과 동일한 저장소 계정이 아닙니다. 배포 스크립트 서비스는 모든 실행 시 파일 공유로 고유한 이름을 만듭니다.

### <a name="create-an-azure-cli-container-instance"></a>Azure CLI 컨테이너 인스턴스 만들기

다음 ARM 템플릿은 컨테이너 인스턴스 및 파일 공유를 만든 다음 컨테이너 이미지에 파일 공유를 탑재 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azure-cli:2.9.1",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/bash",
                "-c",
                "echo hello; sleep 1800"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

탑재 경로의 기본값은 `/mnt/azscripts/azscriptinput` 입니다. 이 경로는 컨테이너 인스턴스에서 파일 공유에 탑재 된 경로입니다.

템플릿에 지정 된 기본 컨테이너 이미지는 **mcr.microsoft.com/azure-cli:2.9.1** 입니다. [지원 되는 Azure CLI 버전](https://mcr.microsoft.com/v2/azure-cli/tags/list)목록을 참조 하세요.

> [!IMPORTANT]
> 배포 스크립트는 MCR (Microsoft Container Registry)에서 사용 가능한 CLI 이미지를 사용 합니다. 배포 스크립트의 CLI 이미지를 인증 하는 데 한 달이 소요 됩니다. 30일 이내에 릴리스된 CLI 버전은 사용하지 마세요. 이미지의 릴리스 날짜를 확인하려면 [Azure CLI 릴리스 정보](/cli/azure/release-notes-azure-cli)를 참조하세요. 지원 되지 않는 버전을 사용 하는 경우 오류 메시지에 지원 되는 버전이 나열 됩니다.

템플릿은 1800 초 후에 컨테이너 인스턴스를 일시 중단 합니다. 컨테이너 인스턴스가 터미널 상태가 되 고 세션이 종료 되기까지 30 분이 소요 됩니다.

템플릿을 배포하는 방법은 다음과 같습니다.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>배포 스크립트 업로드

저장소 계정에 배포 스크립트를 업로드 합니다. 다음은 PowerShell 예제입니다.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Azure Portal 또는 Azure CLI를 사용 하 여 파일을 업로드할 수도 있습니다.

### <a name="test-the-deployment-script"></a>배포 스크립트 테스트

1. Azure Portal에서 컨테이너 인스턴스 및 저장소 계정을 배포한 리소스 그룹을 엽니다.
1. 컨테이너 그룹을 엽니다. 기본 컨테이너 그룹 이름은 *cg* 와 함께 추가 되는 프로젝트 이름입니다. 컨테이너 인스턴스는 **실행 중** 상태로 표시 됩니다.
1. 리소스 메뉴에서 **컨테이너** 를 선택 합니다. 컨테이너 인스턴스 이름은 *컨테이너* 에 추가 된 프로젝트 이름입니다.

    ![배포 스크립트 연결 컨테이너 인스턴스](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. **연결** 을 선택한 다음 **연결** 을 선택 합니다. 컨테이너 인스턴스에 연결할 수 없는 경우 컨테이너 그룹을 다시 시작 하 고 다시 시도 하세요.
1. 콘솔 창에서 다음 명령을 실행 합니다.

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    ./hello.sh John Dole
    ```

    **Hello John Dole** 출력입니다.

    ![배포 스크립트 컨테이너 인스턴스 테스트](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test-cli.png)

## <a name="use-docker"></a>Docker 사용

배포 스크립트 개발 환경으로 미리 구성 된 Docker 컨테이너 이미지를 사용할 수 있습니다. Docker를 설치 하려면 [Docker 가져오기](https://docs.docker.com/get-docker/)를 참조 하세요.
또한 배포 스크립트를 포함 하는 디렉터리를 Docker 컨테이너에 탑재 하도록 파일 공유를 구성 해야 합니다.

1. 배포 스크립트 컨테이너 이미지를 로컬 컴퓨터로 끌어옵니다.

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    이 예제에서는 버전 PowerShell 4.3.0을 사용 합니다.

    MCR에서 CLI 이미지를 꺼내려면:

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    이 예에서는 CLI 2.0.80 버전을 사용합니다. 배포 스크립트는 [여기](https://hub.docker.com/_/microsoft-azure-cli) 있는 기본 CLI 컨테이너 이미지를 사용합니다.

1. Docker 이미지를 로컬로 실행 합니다.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **&lt;host driver letter>** 및 **&lt;host directory name>** 을 공유 드라이브의 기존 폴더로 바꿉니다. 폴더가 컨테이너의 _/data_ 폴더에 매핑됩니다. 예를 들어 _D:\docker_ 를 매핑하려면 다음을 수행 합니다.

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-it** 는 컨테이너 이미지를 활성 상태로 유지하는 것을 의미합니다.

    CLI 예:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. 다음 스크린샷은 공유 드라이브에 *helloworld.ps1* 파일이 있는 경우 PowerShell 스크립트를 실행 하는 방법을 보여 줍니다.

    ![Resource Manager 템플릿 배포 스크립트 Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

스크립트를 성공적으로 테스트 한 후에는이를 템플릿에서 배포 스크립트로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 배포 스크립트를 사용하는 방법을 알아보았습니다. 배포 스크립트 자습서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [자습서: ARM 템플릿에서 배포 스크립트 사용](./template-tutorial-deployment-script.md)
