---
title: Azure 구독 간에 또는 다른 리소스 그룹으로 Recovery Services 자격 증명 모음 이동
description: Azure 구독 및 리소스 그룹에서 복구 서비스 자격 증명 모음을 이동하는 지침입니다.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2019
ms.author: sogup
ms.openlocfilehash: 0ab626bffa3520af0ea23314cbaed118d66e280f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007511"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups-limited-public-preview"></a>Azure 구독 및 리소스 그룹(제한된 공개 미리 보기)에서 Recovery Services 자격 증명 모음 이동

이 문서에서는 Azure 구독 간에 또는 동일한 구독의 다른 리소스 그룹에 Azure Backup에 대해 구성된 Recovery Services 자격 증명 모음을 이동하는 방법을 설명합니다. Azure Portal 또는 PowerShell을 사용하여 Recovery Services 자격 증명 모음을 이동할 수 있습니다.

## <a name="prerequisites-for-moving-a-vault"></a>자격 증명 모음 이동을 위한 필수 구성 요소

- 리소스 그룹 간에 이동할 때 원본 리소스 그룹과 대상 리소스 그룹은 모두 작업 중에 잠겨 있습니다. 이동이 완료될 때까지 쓰기 및 삭제 작업은 리소스 그룹에서 차단됩니다.
- 구독 관리자만 자격 증명 모음을 이동할 권한이 있습니다.
- 구독에서 자격 증명 모음을 이동할 때 대상 구독은 활성화된 상태로 존재해야 하며, 원본 구독과 동일한 테넌트에 있어야 합니다.
- 대상 리소스 그룹에 쓰기 작업을 수행할 수 있는 권한이 있어야 합니다.
- Recovery Services 자격 증명 모음의 위치는 변경할 수 없습니다. 자격 증명 모음을 이동하면 리소스 그룹이 변경됩니다. 새 리소스 그룹은 다른 위치에 있을 수 있지만 자격 증명 모음의 위치는 변경되지 않습니다.
- 현재 지역별로 한 번에 하나의 Recovery Services 자격 증명 모음을 이동할 수 있습니다.
- VM이 구독 간에 또는 새 리소스 그룹으로 Recovery Services 자격 증명 모음과 함께 이동하지 않는 경우 현재 VM 복구 지점은 만료될 때까지 자격 증명 모음에 그대로 남아 있습니다.
- VM을 자격 증명 모음과 함께 이동했는지 여부에 관계없이 자격 증명 모음의 유지된 백업 기록에서 항상 VM을 복원할 수 있습니다.
-   Azure Disk Encryption은 키 자격 증명 모음 및 VM이 동일한 Azure 지역 및 구독에 있어야 합니다.
-   관리 디스크가 있는 가상 머신을 이동하려면 이 [문서](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/)를 참조하세요.
-   구독 내 또는 새 구독으로 리소스를 이동할지 여부에 따라 클래식 모델을 통해 배포된 리소스의 이동 옵션은 다릅니다. 자세한 내용은 이 [문서](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations)를 참조하세요.
-   자격 증명 모음에 대해 정의된 백업 정책은 자격 증명 모음이 구독 간에 또는 새 리소스 그룹으로 이동 후에 유지됩니다.
-   현재 Azure Files, Azure 파일 동기화 또는 IaaS VM의 SQL을 포함하는 자격 증명 모음을 구독 및 리소스 그룹에서 이동할 수 없습니다. 이러한 시나리오에 대한 지원이 향후 릴리스에 추가됩니다.
-   구독 간에 VM 백업 데이터를 포함하는 자격 증명 모음을 이동하는 경우에는 VM을 동일한 구독으로 이동하고, 동일한 대상 리소스 그룹을 사용하여 백업을 계속해야 합니다.<br>

> [!NOTE]
>
**Azure Site Recovery**와 함께 사용하도록 구성된 Recovery Services 자격 증명 모음은 아직 이동할 수 없습니다. **Azure Site Recovery**를 사용하여 재해 복구에 대해 VM(Azure IaaS, Hyper-V, VMware) 또는 물리적 머신을 구성한 경우 이동 작업이 차단됩니다. Site Recovery 서비스에 대한 리소스 이동 기능을 아직 사용할 수 없습니다.
>
>

## <a name="register-the-source-subscription-to-move-your-recovery-services-vault"></a>원본 구독을 등록하여 Recovery Services 자격 증명 모음 이동

원본 구독을 등록하여 Recovery Services 자격 증명 모음을 **이동**하려면 PowerShell 터미널에서 다음 cmdlet을 실행합니다.

1. Azure 계정에 로그인

  ```
  Connect-AzureRmAccount
  ```

2.  등록하려는 구독 선택

    ```
    Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```
3.  이 구독 등록

  ```
  Register-AzureRmProviderFeature -ProviderNamespace Microsoft.RecoveryServices -FeatureName RecoveryServicesResourceMove
  ```

4. 명령 실행

  ```
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
  ```

Azure Portal 또는 PowerShell을 사용하여 이동 작업을 시작하기 전에 구독이 허용 목록에 추가되도록 30분 정도 기다립니다.

## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-different-resource-group"></a>Azure Portal을 사용하여 Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동

복구 서비스 자격 증명 모음 및 연결된 해당 리소스를 다른 리소스 그룹으로 이동하려면

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Recovery Services 자격 증명 모음**의 목록을 열고 이동하려는 자격 증명 모음을 선택합니다. 자격 증명 모음 대시보드가 열리면 다음 이미지에 표시된 것처럼 나타납니다.

  ![복구 서비스 자격 증명 모음 열기](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

  자격 증명 모음에 대한 **Essentials** 정보가 표시되지 않는 경우 드롭다운 아이콘을 클릭합니다. 이제 자격 증명 모음에 대한 Essentials 정보가 표시됩니다.

  ![Essentials 정보 탭](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 자격 증명 모음 개요 메뉴에서 **리소스 그룹** 옆의 **변경**을 클릭하여 **리소스 이동** 블레이드를 엽니다.

  ![리소스 그룹 변경](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. **리소스 이동** 블레이드에서 선택한 자격 증명 모음의 경우 다음 이미지처럼 확인란을 선택하여 관련된 옵션 리소스를 이동하는 것이 좋습니다.

  ![구독 이동](./media/backup-azure-move-recovery-services/move-resource.png)

5. 대상 리소스 그룹을 추가하려면 **리소스 그룹** 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새 그룹 만들기** 옵션을 클릭합니다.

  ![리소스 만들기](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. 리소스 그룹을 추가한 후 **새 리소스 ID를 사용하려는 경우 이동한 리소스와 연결된 도구 및 스크립트를 업데이트하지 않으면 정상적으로 작동하지 않는다는 점을 이해합니다.** 옵션을 확인한 다음, **확인**을 클릭하여 자격 증명 모음 이동을 완료합니다.

  ![확인 메시지](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-a-different-subscription"></a>Azure Portal을 사용하여 Recovery Services 자격 증명 모음을 다른 구독으로 이동

Recovery Services 자격 증명 모음 및 연결된 해당 리소스를 다른 구독으로 이동할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Recovery Services 자격 증명 모음의 목록을 열고 이동하려는 자격 증명 모음을 선택합니다. 자격 증명 모음 대시보드가 열리면 다음 이미지에 표시된 것처럼 나타납니다.

    ![복구 서비스 자격 증명 모음 열기](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    자격 증명 모음에 대한 **Essentials** 정보가 표시되지 않는 경우 드롭다운 아이콘을 클릭합니다. 이제 자격 증명 모음에 대한 Essentials 정보가 표시됩니다.

    ![Essentials 정보 탭](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 자격 증명 모음 개요 메뉴에서 **구독** 옆의 **변경**을 클릭하여 **리소스 이동** 블레이드를 엽니다.

  ![구독 변경](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. 이동할 리소스를 선택합니다. 여기에서 **모두 선택** 옵션을 사용하여 나열된 모든 선택적 리소스를 선택하는 것이 좋습니다.

  ![리소스 이동](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. 자격 증명 모음을 이동하려는 **구독** 드롭다운 목록에서 대상 구독을 선택합니다.
6. 대상 리소스 그룹을 추가하려면 **리소스 그룹** 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새 그룹 만들기** 옵션을 클릭합니다.

  ![구독 추가](./media/backup-azure-move-recovery-services/add-subscription.png)

7. **새 리소스 ID를 사용하려는 경우 이동한 리소스와 연결된 도구 및 스크립트를 업데이트하지 않으면 정상적으로 작동하지 않는다는 점을 이해합니다.** 옵션을 클릭하여 확인한 다음, **확인**을 클릭합니다.

> [!NOTE]
> 구독 간 백업(RS 자격 증명 모음 및 보호된 VM이 다른 구독에 있음)은 지원되는 시나리오가 아닙니다. 또한 LRS(로컬 중복 스토리지)에서 GRS(글로벌 중복 스토리지)로 스토리지 중복 옵션 및 그 반대의 경우는 자격 증명 모음 이동 작업 중 수정할 수 없습니다.
>
>

## <a name="use-powershell-to-move-a-vault"></a>PowerShell을 사용하여 자격 증명 모음 이동

Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동하려면 `Move-AzureRMResource` cmdlet을 사용합니다. `Move-AzureRMResource`에는 리소스 이름 및 리소스 종류가 필요합니다. `Get-AzureRmRecoveryServicesVault` cmdlet에서 가져올 수 있습니다.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

리소스를 다른 구독으로 이동하기 위해 `-DestinationSubscriptionId` 매개 변수를 포함합니다.

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

위의 cmdlet을 실행한 후 지정한 리소스를 이동할 것인지 묻는 메시지가 나타납니다. **Y**를 입력하여 확인합니다. 유효성 검사에 성공 후 리소스를 이동합니다.

## <a name="use-cli-to-move-a-vault"></a>CLI를 사용하여 자격 증명 모음 이동

Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동하려면 다음 cmdlet을 사용합니다.

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

새 구독으로 이동하려면 `--destination-subscription-id` 매개 변수를 제공합니다.

## <a name="post-migration"></a>마이그레이션 후

1. 리소스 그룹에 대한 액세스 제어를 설정/확인해야 합니다.  
2. 이동이 완료된 후 자격 증명 모음에 대해 백업 보고 및 모니터링 기능을 다시 구성해야 합니다. 이전 구성은 이동 작업 중 손실됩니다.



## <a name="next-steps"></a>다음 단계

리소스 그룹 및 구독 간의 다양한 유형의 리소스를 이동할 수 있습니다.

자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)을 참조하세요.
