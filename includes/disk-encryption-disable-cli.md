---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 37571a82ca73342b8edfc4702686ccd9091887c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771511"
---
Azure PowerShell, Azure CLI 또는 Resource Manager 템플릿을 사용하여 암호화를 사용하지 않도록 설정할 수 있습니다. 

>[!IMPORTANT]
>Linux VM에서 Azure Disk Encryption을 통한 암호화 사용 안 함은 데이터 볼륨에 대해서만 지원됩니다. OS 볼륨이 암호화된 경우 이 설정은 데이터 또는 OS 볼륨에서 지원되지 않습니다.  

- **Azure PowerShell을 사용하여 디스크 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용합니다. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Azure CLI를 사용하여 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) 명령을 사용합니다. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Resource Manager 템플릿으로 암호화를 사용하지 않도록 설정:** [실행 중인 Linux VM에서 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 템플릿을 사용하여 암호화를 사용하지 않도록 설정할 수 있습니다.
     1. **Deploy to Azure** 를 클릭합니다.
     2. 구독, 리소스 그룹, 위치, VM, 약관 및 규약을 선택합니다.
