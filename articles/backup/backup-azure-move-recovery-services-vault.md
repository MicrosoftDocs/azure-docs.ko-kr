---
title: 자격 증명 모음 Azure Backup Recovery Services 이동 하는 방법
description: Azure 구독 및 리소스 그룹에서 Recovery Services 자격 증명 모음을 이동 하는 방법에 대 한 지침입니다.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 4f75bec533181b29625fb0a10cc26d03f2875036
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466374"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Azure 구독 및 리소스 그룹 간에 Recovery Services 자격 증명 모음 이동

이 문서에서는 Azure 구독 간에 또는 동일한 구독의 다른 리소스 그룹에 Azure Backup에 대해 구성된 Recovery Services 자격 증명 모음을 이동하는 방법을 설명합니다. Azure Portal 또는 PowerShell을 사용하여 Recovery Services 자격 증명 모음을 이동할 수 있습니다.

## <a name="supported-regions"></a>지원되는 지역

프랑스 중부, 프랑스 남부, 독일 북동쪽, 독일 중부, 중국 북부, 중국 North2, 중국 동부 및 중국 동부 2,를 제외 하 고 모든 공용 지역 및 소 버린 지역이 지원 됩니다.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Recovery Services 자격 증명 모음 이동에 대 한 필수 조건

- 리소스 그룹 간 자격 증명 모음 이동 중에는 쓰기 및 삭제 작업을 방지 하기 위해 원본 및 대상 리소스 그룹이 모두 잠깁니다. 자세한 내용은 [이 문서](../azure-resource-manager/management/move-resource-group-and-subscription.md)를 참조하세요.
- 자격 증명 모음을 이동할 수 있는 권한은 관리자 구독에만 있습니다.
- 구독에서 자격 증명 모음을 이동 하는 경우 대상 구독은 원본 구독과 동일한 테 넌 트에 상주해 야 하며 상태를 사용 하도록 설정 해야 합니다. 자격 증명 모음을 다른 Azure AD 디렉터리로 이동 하려면 [다른 디렉터리에 구독 전송](../role-based-access-control/transfer-subscription.md) 및 [Recovery services 자격 증명 모음 faq](backup-azure-backup-faq.md#recovery-services-vault)를 참조 하세요.
- 대상 리소스 그룹에 쓰기 작업을 수행할 수 있는 권한이 있어야 합니다.
- 자격 증명 모음을 이동하면 리소스 그룹이 변경됩니다. Recovery Services 자격 증명 모음은 동일한 위치에 있으며 변경할 수 없습니다.
- 한 번에 하나의 지역에서 하나의 Recovery Services 자격 증명 모음만 이동할 수 있습니다.
- VM이 구독 또는 새 리소스 그룹에 대해 Recovery Services 자격 증명 모음과 함께 이동 하지 않는 경우 만료 될 때까지 현재 VM 복구 지점은 자격 증명 모음에 그대로 유지 됩니다.
- VM을 자격 증명 모음과 함께 이동했는지 여부에 관계없이 자격 증명 모음의 유지된 백업 기록에서 항상 VM을 복원할 수 있습니다.
- Azure Disk Encryption 키 자격 증명 모음 및 Vm이 동일한 Azure 지역 및 구독에 있어야 합니다.
- 관리 디스크가 있는 가상 머신을 이동하려면 이 [문서](../azure-resource-manager/management/move-resource-group-and-subscription.md)를 참조하세요.
- 클래식 모델을 통해 배포 된 리소스를 이동 하는 옵션은 구독 내에서 리소스를 이동 하는지 아니면 새 구독으로 이동 하는지에 따라 달라 집니다. 자세한 내용은 [이 문서](../azure-resource-manager/management/move-resource-group-and-subscription.md)를 참조하세요.
- 자격 증명 모음에 대해 정의된 백업 정책은 자격 증명 모음이 구독 간에 또는 새 리소스 그룹으로 이동 후에 유지됩니다.
- 다음 유형의 백업 항목을 포함 하는 자격 증명 모음만 이동할 수 있습니다. 아래에 나열 되지 않은 유형의 백업 항목은 모두 중지 되어야 하며 데이터는 자격 증명 모음을 이동 하기 전에 영구적으로 삭제 해야 합니다.
  - Azure Virtual Machines
  - MARS (Microsoft Azure Recovery Services) 에이전트
  - Microsoft Azure Backup 서버 (MABS)
  - DPM(Data Protection Manager)
- 구독에서 VM 백업 데이터를 포함 하는 자격 증명 모음을 이동 하는 경우에는 Vm을 동일한 구독으로 이동 하 고 이전 구독과 동일한 대상 VM 리소스 그룹 이름을 사용 하 여 백업을 계속 진행 해야 합니다.

> [!NOTE]
> Azure 지역에서 Azure Backup에 대 한 Recovery Services 자격 증명 모음 이동은 지원 되지 않습니다.<br><br>
> **Azure Site Recovery** 를 사용 하 여 재해 복구를 위해 Vm (Azure IaaS, Hyper-v, VMware) 또는 물리적 컴퓨터를 구성한 경우 이동 작업이 차단 됩니다. Azure Site Recovery 자격 증명 모음을 이동 하려면 [이 문서](../site-recovery/move-vaults-across-regions.md) 를 검토 하 여 자격 증명 모음을 수동으로 이동 하는 방법에 대해 알아보세요.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Azure Portal를 사용 하 여 Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동

Recovery Services 자격 증명 모음 및 연결 된 리소스를 다른 리소스 그룹으로 이동 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Recovery Services 자격 증명 모음** 의 목록을 열고 이동하려는 자격 증명 모음을 선택합니다. 자격 증명 모음 대시보드가 열리면 다음 이미지에 표시된 것처럼 나타납니다.

   ![Recovery Services 자격 증명 모음 열기](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   자격 증명 모음에 대 한 **필수** 정보가 표시 되지 않으면 드롭다운 아이콘을 선택 합니다. 이제 자격 증명 모음에 대한 Essentials 정보가 표시됩니다.

   ![Essentials 정보 탭](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 자격 증명 모음 개요 메뉴에서 **리소스 그룹** 옆의 **변경** 을 선택 하 여 **리소스 이동** 창을 엽니다.

   ![리소스 그룹 변경](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. **리소스 이동** 창에서 선택한 자격 증명 모음에 대해 다음 그림에 표시 된 것 처럼 확인란을 선택 하 여 선택적 관련 리소스를 이동 하는 것이 좋습니다.

   ![구독 이동](./media/backup-azure-move-recovery-services/move-resource.png)

5. 대상 리소스 그룹을 추가 하려면 **리소스 그룹** 드롭다운 목록에서 기존 리소스 그룹을 선택 하거나 **새 그룹 만들기** 옵션을 선택 합니다.

   ![리소스 만들기](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. 리소스 그룹을 추가한 후에 **는 이동 된 리소스와 연결 된 도구와 스크립트가 새 리소스 id를 사용 하도록 업데이트 될 때까지 작동 하지 않는지** 확인 하 고 **확인** 을 선택 하 여 자격 증명 모음 이동을 완료 해야 합니다.

   ![확인 메시지](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Azure Portal를 사용 하 여 Recovery Services 자격 증명 모음을 다른 구독으로 이동

Recovery Services 자격 증명 모음 및 연결된 해당 리소스를 다른 구독으로 이동할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Recovery Services 자격 증명 모음의 목록을 열고 이동하려는 자격 증명 모음을 선택합니다. 자격 증명 모음 대시보드가 열리면 다음 이미지에 표시된 것처럼 나타납니다.

    ![Recovery Services 자격 증명 모음 열기](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    자격 증명 모음에 대 한 **필수** 정보가 표시 되지 않으면 드롭다운 아이콘을 선택 합니다. 이제 자격 증명 모음에 대한 Essentials 정보가 표시됩니다.

    ![Essentials 정보 탭](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 자격 증명 모음 개요 메뉴에서 **구독** 옆의 **변경** 을 선택 하 여 **리소스 이동** 창을 엽니다.

   ![구독 변경](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. 이동할 리소스를 선택합니다. 여기에서 **모두 선택** 옵션을 사용하여 나열된 모든 선택적 리소스를 선택하는 것이 좋습니다.

   ![리소스 이동](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. 자격 증명 모음을 이동하려는 **구독** 드롭다운 목록에서 대상 구독을 선택합니다.
6. 대상 리소스 그룹을 추가 하려면 **리소스 그룹** 드롭다운 목록에서 기존 리소스 그룹을 선택 하거나 **새 그룹 만들기** 옵션을 선택 합니다.

   ![구독 추가](./media/backup-azure-move-recovery-services/add-subscription.png)

7. **이동 된 리소스와 연결 된 도구 및 스크립트가 새 리소스 id를 사용 하도록 업데이트 될 때까지 작동 하지** 않습니다. 옵션을 선택 하 여 확인을 선택한 다음 **확인** 을 선택 합니다.

> [!NOTE]
> 크로스 구독 백업 (RS 자격 증명 모음 및 보호 된 Vm은 서로 다른 구독에 있는 경우)은 지원 되는 시나리오가 아닙니다. 또한 LRS (로컬 중복 저장소)에서 GRS (global 중복 저장소)로 저장소 중복성 옵션을 사용할 수 없으며, 그 반대의 경우도 자격 증명 모음 이동 작업을 수행 하는 동안 수정할 수 없습니다.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>PowerShell을 사용 하 여 Recovery Services 자격 증명 모음 이동

Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동하려면 `Move-AzureRMResource` cmdlet을 사용합니다. `Move-AzureRMResource`에는 리소스 이름 및 리소스 종류가 필요합니다. `Get-AzureRmRecoveryServicesVault` cmdlet에서 가져올 수 있습니다.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

리소스를 다른 구독으로 이동하기 위해 `-DestinationSubscriptionId` 매개 변수를 포함합니다.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

위의 cmdlet을 실행 한 후에는 지정 된 리소스를 이동할 것인지 묻는 메시지가 표시 됩니다. **Y** 를 입력하여 확인합니다. 유효성 검사에 성공 후 리소스를 이동합니다.

## <a name="use-cli-to-move-recovery-services-vault"></a>CLI를 사용 하 여 Recovery Services 자격 증명 모음 이동

Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동하려면 다음 cmdlet을 사용합니다.

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

새 구독으로 이동하려면 `--destination-subscription-id` 매개 변수를 제공합니다.

## <a name="post-migration"></a>마이그레이션 후

1. 리소스 그룹에 대 한 액세스 제어를 설정/확인 합니다.  
2. 이동이 완료 된 후에 자격 증명 모음에 대해 백업 보고 및 모니터링 기능을 다시 구성 해야 합니다. 이전 구성은 이동 작업 중 손실됩니다.

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Azure 가상 머신을 다른 recovery services 자격 증명 모음으로 이동 합니다. 

Azure backup을 사용 하도록 설정 된 Azure 가상 머신을 이동 하려는 경우 두 가지 옵션을 선택할 수 있습니다. 비즈니스 요구 사항에 따라 달라 집니다.

- [이전 백업 데이터를 보존할 필요가 없습니다.](#dont-need-to-preserve-previous-backed-up-data)
- [이전 백업 데이터를 유지 해야 합니다.](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>이전 백업 데이터를 보존할 필요가 없습니다.

새 자격 증명 모음에서 작업을 보호 하려면 이전 자격 증명 모음에서 현재 보호 및 데이터를 삭제 하 고 백업이 다시 구성 되어 있어야 합니다.

>[!WARNING]
>다음 작업은 소거식 이며 실행 취소할 수 없습니다. 보호 된 서버와 연결 된 모든 백업 데이터 및 백업 항목은 영구적으로 삭제 됩니다. 주의하여 진행하세요.

**이전 자격 증명 모음에서 현재 보호를 중지 하 고 삭제 합니다.**

1. 자격 증명 모음 속성에서 일시 삭제를 사용 하지 않도록 설정 합니다. 일시 삭제를 사용 하지 않도록 설정 하려면 [다음 단계](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) 를 수행 합니다.

2. 보호를 중지 하 고 현재 자격 증명 모음에서 백업을 삭제 합니다. 자격 증명 모음 대시보드 메뉴에서 **백업 항목** 을 선택 합니다. 새 자격 증명 모음으로 이동 해야 하는 여기에 나열 된 항목은 해당 백업 데이터와 함께 제거 되어야 합니다. [클라우드에서 보호 된 항목을 삭제](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) 하 고 [온-프레미스에서 보호 된 항목을 삭제](backup-azure-delete-vault.md#delete-protected-items-on-premises)하는 방법을 참조 하세요.

3. AFS (Azure 파일 공유), SQL server 또는 SAP HANA 서버를 이동할 계획인 경우 등록을 취소 해야 합니다. 자격 증명 모음 대시보드 메뉴에서 **백업 인프라** 를 선택 합니다. [SQL server 등록을 취소](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)하 고, [Azure 파일 공유와 연결 된 저장소 계정을 등록 취소](manage-afs-backup.md#unregister-a-storage-account)하 고, [SAP HANA 인스턴스의 등록](sap-hana-db-manage.md#unregister-an-sap-hana-instance)을 취소 하는 방법을 참조 하세요.

4. 이전 자격 증명 모음에서 제거 되 면 새 자격 증명 모음에서 워크 로드에 대 한 백업을 계속 구성 합니다.

### <a name="must-preserve-previous-backed-up-data"></a>이전 백업 데이터를 유지 해야 합니다.

이전 자격 증명 모음에서 현재 보호 된 데이터를 유지 하 고 새 자격 증명 모음에서 보호를 계속 해야 하는 경우 일부 워크 로드에 대해 제한 된 옵션이 있습니다.

- MARS의 경우 [데이터 보관을 사용 하 여 보호를 중지](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) 하 고 새 자격 증명 모음에 에이전트를 등록할 수 있습니다.

  - Azure Backup 서비스는 이전 자격 증명 모음의 기존 복구 지점은 계속 유지 합니다.
  - 이전 자격 증명 모음에서 복구 지점이 유지 되도록 요금을 지불 해야 합니다.
  - 이전 자격 증명 모음에 있는 만료 되지 않은 복구 지점의 경우에만 백업 된 데이터를 복원할 수 있습니다.
  - 새 데이터의 초기 복제본을 새 자격 증명 모음에 만들어야 합니다.

- Azure VM의 경우 이전 자격 증명 모음에서 VM에 대 한 [데이터 보관을 사용 하 여 보호를 중지](backup-azure-manage-vms.md#stop-protecting-a-vm) 하 고 vm을 다른 리소스 그룹으로 이동한 다음 새 자격 증명 모음에서 vm을 보호할 수 있습니다. 다른 리소스 그룹으로 VM을 이동 하는 방법에 대 한 [지침 및 제한 사항](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) 을 참조 하세요.

  VM은 한 번에 하나의 자격 증명 모음 에서만 보호할 수 있습니다. 그러나 새 리소스 그룹의 VM은 다른 VM으로 간주 되는 새 자격 증명 모음에서 보호할 수 있습니다.

  - Azure Backup 서비스는 이전 자격 증명 모음에 백업 된 복구 지점이 유지 됩니다.
  - 이전 자격 증명 모음에서 복구 지점이 유지 되도록 요금을 지불 해야 합니다 (자세한 내용은 [Azure Backup 가격](azure-backup-pricing.md) 정보 참조).
  - 필요한 경우 이전 자격 증명 모음에서 VM을 복원할 수 있습니다.
  - 새 리소스에서 VM의 새 자격 증명 모음에 대 한 첫 번째 백업은 초기 복제본이 됩니다.

## <a name="next-steps"></a>다음 단계

리소스 그룹 및 구독 간의 다양한 유형의 리소스를 이동할 수 있습니다.

자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.