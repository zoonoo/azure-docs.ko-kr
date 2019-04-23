---
title: 청사진 리소스 잠금으로 새 리소스 보호
description: Azure Blueprints 리소스 잠금 읽기 전용 및 삭제 안 함을 사용하여 새로 배포한 리소스를 보호하는 방법을 알아봅니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d315fb5fe3ce7844946e6a9405a9a5f6a0be8b9d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791612"
---
# <a name="protect-new-resources-with-azure-blueprints-resource-locks"></a>Azure Blueprints 리소스 잠금으로 새 리소스 보호

Azure Blueprints [리소스 잠금](../concepts/resource-locking.md)을 사용하면 새로 배포한 리소스를 _소유자_ 역할 계정으로도 변조할 수 없게 보호할 수 있습니다. 이 보호를 청사진 정의에서 Resource Manager 템플릿 아티팩트로 만든 리소스에 추가할 수 있습니다.

다음 단계가 설명됩니다.

> [!div class="checklist"]
> - 새 청사진 정의 만들기
> - 청사진 정의를 **게시됨**으로 표시
> - 기존 구독에 청사진 정의 할당
> - 새 리소스 그룹 조사
> - 청사진을 할당 취소하여 잠금 제거

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="create-new-blueprint-definition"></a>새 청사진 정의 만들기

먼저 새 청사진 정의를 만듭니다.

1. 왼쪽 창에서 **모든 서비스**를 선택합니다. **청사진**을 검색하고 선택합니다.

1. **시작** 페이지 왼쪽에서 _청사진 만들기_ 아래의 **만들기** 단추를 선택합니다.

1. 페이지 맨 위에 있는 **빈 청사진** 청사진 샘플을 찾은 후 **빈 청사진으로 시작**을 선택합니다.

1. 청사진 샘플의 _기본 사항_을 입력합니다.

   - **청사진 이름**: 청사진 샘플 사본의 이름을 지정합니다. 이 자습서에서는 이름으로 _locked-storageaccount_를 사용합니다.
   - **청사진 설명**: 청사진 정의에 대해 설명합니다. "배포된 리소스의 청사진 리소스 잠금 테스트용"이라고 지정합니다.
   - **정의 위치**: 줄임표를 사용하고 관리 그룹 또는 구독을 선택하여 청사진 정의를 저장합니다.

1. 페이지 위쪽의 _아티팩트_ 탭 또는 페이지 아래쪽의 **다음: 아티팩트**를 선택합니다.

1. 구독에서 리소스 그룹을 추가합니다. **구독** 아래의 **+ 아티팩트 추가...** 행을 선택합니다.
   _아티팩트 형식_으로 ‘리소스 그룹’을 선택합니다. _아티팩트 표시 이름_을 **RGtoLock**으로 설정합니다.
   _리소스 그룹 이름_ 및 _위치_ 필드는 비워 두되 각 속성의 확인란은 선택하여 해당 속성을 **동적 매개 변수**로 설정합니다. **추가**를 클릭하여 이 아티팩트를 청사진에 추가합니다.

1. 리소스 그룹 아래에 템플릿을 추가합니다. **RGtoLock** 항목 아래에서 **+ 아티팩트 추가..** 행을 선택합니다. _아티팩트 형식_으로 ‘Azure Resource Manager 템플릿’을 선택하고 _아티팩트 표시 이름_을 'StorageAccount'로 설정합니다. _설명_은 비워 둡니다. **템플릿** 탭의 편집기 상자에 다음 Resource Manager 템플릿을 붙여넣습니다. 템플릿을 붙여넣은 후 **추가**를 선택하여 청사진에 이 아티팩트를 추가합니다.

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
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

1. 페이지 아래쪽에서 **초안 저장**을 선택합니다.

이 단계에서는 선택한 관리 그룹 또는 구독에 청사진 정의를 만듭니다.

**청사진 정의를 저장함** 포털 알림이 나타나면 다음 단계로 이동합니다.

## <a name="publish-the-blueprint-definition"></a>청사진 정의 게시

이제 작업 환경에 청사진 정의가 만들어졌습니다. 이 청사진 정의는 **초안** 모드로 만들어졌으며 먼저 **게시**해야만 할당하고 배포할 수 있습니다.

1. 왼쪽 창에서 **모든 서비스**를 선택합니다. **청사진**을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의**를 선택합니다. 필터를 사용하여 _locked-storageaccount_ 청사진을 찾은 후 선택합니다.

1. 페이지 맨 위에서 **청사진 게시**를 선택합니다. 오른쪽의 새 창에서 **버전**을 _1.0_으로 지정합니다. 이 속성은 나중에 수정할 경우에 유용합니다. "청사진 배포 리소스를 잠그기 위해 게시된 첫 번째 버전"과 같은 **변경 정보**를 제공합니다. 그런 후 페이지 아래쪽의 **게시**를 선택합니다.

이 단계에서 구독에 청사진을 할당할 수 있습니다. 게시한 후에도 여전히 변경할 수 있습니다. 추가로 변경하려면 동일한 청사진 정의의 다른 버전 간 차이를 추적할 수 있도록 새 **버전** 값으로 게시해야 합니다.

**청사진 정의를 게시하는 중** 포털 알림이 나타나면 다음 단계로 이동합니다.

## <a name="assign-the-blueprint-definition"></a>청사진 정의 할당

청사진 정의가 **게시**되면 저장된 관리 그룹 내의 구독에 할당할 수 있습니다. 이 단계에서는 청사진 정의의 배포를 고유하게 만들기 위해 매개 변수가 제공됩니다.

1. 왼쪽 창에서 **모든 서비스**를 선택합니다. **청사진**을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의**를 선택합니다. 필터를 사용하여 _locked-storageaccount_ 청사진을 찾은 후 선택합니다.

1. 청사진 정의 페이지 위쪽에서 **청사진 할당**을 선택합니다.

1. 청사진 할당의 매개 변수 값을 지정합니다.

   - 기본 사항

     - **구독**: 청사진 정의를 저장한 관리 그룹에 있는 하나 이상의 구독을 선택합니다. 둘 이상의 구독을 선택하면 입력한 매개 변수를 사용하여 각 구독의 할당이 생성됩니다.
     - **할당 이름**: 청사진 정의의 이름에 따라 이 이름이 미리 채워집니다. 이 할당으로 새 리소스 그룹 잠금을 나타내려고 하므로 할당 이름을 _assignment-locked-storageaccount-TestingBPLocks_로 변경합니다.
     - **위치**: 관리 ID를 만들 지역을 선택합니다. Azure Blueprints는 이 관리 ID를 사용하여 할당된 청사진의 모든 아티팩트를 배포합니다. 자세히 알아보려면 [Azure 리소스의 관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
       이 자습서에서는 _미국 동부 2_를 선택합니다.
     - **청사진 정의 버전**: 청사진 정의의 **게시된** 버전 _1.0_을 선택합니다.

   - 할당 잠금

     _읽기 전용_ 청사진 잠금 모드를 선택입니다. 자세한 내용은 [청사진 리소스 잠금](../concepts/resource-locking.md)을 참조하세요.

   - 관리 ID

     기본값인 _시스템 할당_ 옵션을 그대로 둡니다. 자세한 내용은 [관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

   - 아티팩트 매개 변수

     이 섹션에 정의된 매개 변수는 정의된 아티팩트에 적용됩니다. 이러한 매개 변수는 청사진 할당 중에 정의되므로 [동적 매개 변수](../concepts/parameters.md#dynamic-parameters)입니다. 각 아티팩트에 대해 매개 변수 값을 **값** 열에 정의된 값으로 설정합니다.

     |아티팩트 이름|아티팩트 형식|매개 변수 이름|값|설명|
     |-|-|-|-|-|
     |RGtoLock 리소스 그룹|리소스 그룹|Name|TestingBPLocks|청사진 잠금을 적용할 새 리소스 그룹의 이름을 정의합니다.|
     |RGtoLock 리소스 그룹|리소스 그룹|위치|미국 서부 2|청사진 잠금을 적용할 새 리소스 그룹의 위치를 정의합니다.|
     |StorageAccount|Resource Manager 템플릿|storageAccountType(StorageAccount)|Standard_GRS|스토리지 SKU를 선택합니다. 기본값은 _Standard_LRS_입니다.|

1. 모든 매개 변수를 입력한 후에는 페이지 아래쪽에서 **할당**을 선택합니다.

이 단계에서는 정의된 리소스를 배포하고 선택한 **할당 잠금**을 구성합니다. 청사진 잠금을 적용하는 데 최대 30분이 걸릴 수 있습니다.

**청사진 정의를 할당함** 포털 알림이 나타나면 다음 단계로 이동합니다.

## <a name="inspect-resources-deployed-by-the-assignment"></a>할당에서 배포한 리소스 검사

할당이 리소스 그룹 _TestingBPLocks_를 만들었으며 Resource Manager 템플릿 아티팩트를 통해 스토리지 계정이 배포되었습니다. 새 리소스 그룹 및 선택한 잠금 상태가 할당 세부 정보 페이지에 표시됩니다.

1. 왼쪽 창에서 **모든 서비스**를 선택합니다. **청사진**을 검색하고 선택합니다.

1. 왼쪽에서 **할당된 청사진** 페이지를 선택합니다. 필터를 사용하여 _assignment-locked-storageaccount-TestingBPLocks_ 청사진을 찾은 후 선택합니다.

   이 페이지에서 할당이 성공했으며 리소스가 새 청사진 잠금 상태로 배포되었음을 확인할 수 있습니다. 할당이 업데이트되면 **할당 작업** 드롭다운에 각 정의 버전 배포에 대한 세부 정보가 표시됩니다. 리소스 그룹을 클릭하여 속성 페이지를 직접 열 수 있습니다.

1. **TestingBPLocks** 리소스 그룹을 선택합니다.

1. 왼쪽에서 **액세스 제어(IAM)** 페이지를 선택하고, **역할 할당** 탭을 선택합니다.

   여기에서 _assignment-locked-storageaccount-TestingBPLocks_ 청사진 할당이 리소스 그룹을 배포 및 잠그는 데 사용되었으므로 _소유자_ 역할을 갖는다는 사실을 알 수 있습니다.

1. **거부 할당** 탭을 선택합니다.

   청사진 할당은 _읽기 전용_ 청사진 잠금 모드를 적용하기 위해 배포된 리소스 그룹에서 [거부 할당](../../../role-based-access-control/deny-assignments.md)을 만들었습니다. 거부 할당은 _역할 할당_ 탭에서 적절한 권한이 있는 사람이 특정 작업을 수행하지 못하게 합니다. 거부 할당은 _모든 보안 주체_에 영향을 미칩니다.

   거부 할당에서 보안 주체를 제외하는 방법에 대한 자세한 내용은 [청사진 리소스 잠금](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment)을 참조하세요.

1. 거부 할당을 선택한 다음, 왼쪽의 **거부된 사용 권한** 페이지를 선택합니다.

   거부 할당은 **\*** 및 **동작** 구성의 모든 작업을 방지하지만, **NotActions**를 통해 **\*/read**를 제외하여 읽기 액세스를 허용합니다.

1. Azure Portal 탐색 경로에서 **TestingBPLocks - 액세스 제어(IAM)** 를 선택합니다. 그런 후 왼쪽에서 **개요** 페이지를 선택한 후 **리소스 그룹 삭제** 단추를 선택합니다. 이름 _TestingBPLocks_를 입력하여 삭제를 확인하고 창 아래쪽에서 **삭제**를 선택합니다.

   포털 알림 **리소스 그룹 TestingBPLocks 삭제 실패**가 표시됩니다. 이 오류는 사용자 계정에 리소스 그룹을 삭제하기 위한 권한이 있지만 청사진 할당이 액세스를 거부했음을 나타냅니다. 청사진 할당 동안 _읽기 전용_ 청사진 잠금 모드를 선택했다는 사실을 기억하세요. 청사진 잠금은 권한이 있는 계정(_소유자_의 경우도 해당)이 리소스를 삭제할 수 없도록 합니다. 자세한 내용은 [청사진 리소스 잠금](../concepts/resource-locking.md)을 참조하세요.

이러한 단계는 배포된 리소스가 이제 원치 않는 삭제를 방지하는 청사진 잠금으로 보호하며, 설령 권한이 있는 계정을 사용하더라도 마찬가지라는 것을 보여 줍니다.

## <a name="unassign-the-blueprint"></a>청사진 할당 취소

마지막 단계는 청사진 정의의 할당을 제거하는 것입니다. 할당을 제거해도 수정한 아티팩트는 제거되지 않습니다.

1. 왼쪽 창에서 **모든 서비스**를 선택합니다. **청사진**을 검색하고 선택합니다.

1. 왼쪽에서 **할당된 청사진** 페이지를 선택합니다. 필터를 사용하여 _assignment-locked-storageaccount-TestingBPLocks_ 청사진을 찾은 후 선택합니다.

1. 페이지 위쪽의 **청사진 할당 취소** 단추를 선택합니다. 확인 대화 상자에서 경고를 읽은 다음, **확인**을 선택합니다.

   청사진 할당이 제거되면 청사진 잠금도 제거됩니다. 권한이 있는 계정으로 삭제된 리소스는 다시 만들 수 있습니다.

1. Azure 메뉴에서 **리소스 그룹**을 선택하고 **TestingBPLocks**를 선택합니다.

1. 왼쪽에서 **액세스 제어(IAM)** 페이지를 선택하고, **역할 할당** 탭을 선택합니다.

리소스 그룹에 대한 보안은 청사진 할당에 더 이상 _소유자_ 액세스 권한이 없음을 보여 줍니다.

**청사진 할당을 제거함** 포털 알림이 나타나면 다음 단계로 이동합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료하면 다음 리소스를 삭제합니다.

- 리소스 그룹 _TestingBPLocks_
- 청사진 정의 _locked-storageaccount_

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결