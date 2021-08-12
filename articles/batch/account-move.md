---
title: Azure Batch 계정을 다른 지역으로 이동
description: Azure Batch 계정을 다른 지역으로 이동하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 05/05/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: 9c0e45b62f6dd6152ab80beaa751925702b1fa37
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108777070"
---
# <a name="move-an-azure-batch-account-to-another-region"></a>Azure Batch 계정을 다른 지역으로 이동

기존 [Batch 계정](accounts.md)을 한 지역에서 다른 지역으로 이동하는 데 유용할 수 있는 시나리오가 있습니다. 예를 들어 재해 복구 계획의 일부로 다른 지역으로 이동하는 것이 좋습니다.

Azure Batch 계정은 한 지역에서 다른 지역으로 직접 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용하여 Batch 계정의 기존 구성을 내보낼 수 있습니다. 그런 다음, Batch 계정을 템플릿으로 내보내고, 대상 지역과 일치하도록 매개 변수를 수정한 다음, 템플릿을 새 지역에 배포하여 리소스를 다른 지역에 준비할 수 있습니다. 그런 다음, 계정에서 작업 및 기타 기능을 다시 만들 수 있습니다.

 Resource Manager 및 템플릿에 대한 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조하세요.

이 항목에서는 Azure Portal을 사용하여 지역 간에 Batch 계정을 이동하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [다른 지역으로 Azure Storage 계정 이동](../storage/common/storage-account-move.md)의 단계를 수행하여 Batch 계정과 연결된 스토리지 계정을 새 대상 지역으로 이동합니다. 원하는 경우에는 스토리지 계정을 원래 지역에 그대로 둘 수 있습니다. 그러나 Batch 계정과 동일한 지역에 있는 경우 일반적으로 더 나은 성능을 볼 수 있으므로 이를 이동하는 것이 좋습니다. 아래 지침에서는 스토리지 계정을 이미 마이그레이션했다고 가정합니다.
- Batch 계정에서 사용하는 서비스 및 기능이 대상 지역에서 지원되는지 확인합니다.

## <a name="prepare"></a>준비

시작하려면 Resource Manager 템플릿을 내보낸 다음, 수정합니다.

### <a name="export-a-template"></a>템플릿 내보내기

먼저, Batch 계정에 대한 설정 및 정보를 포함하는 템플릿을 내보냅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모든 리소스** 를 선택한 다음, Batch 계정을 선택합니다.

3. > **자동화** > **템플릿 내보내기** 를 선택합니다.

4. **템플릿 내보내기** 블레이드에서 **다운로드** 를 선택합니다.

5. 포털에서 다운로드한 .zip 파일을 찾은 다음, 선택한 폴더에 파일 압축을 풉니다.

   이 zip 파일에는 템플릿과 템플릿 배포 스크립트로 구성된 .json 파일이 들어 있습니다.

### <a name="modify-the-template"></a>템플릿 수정

그런 다음, 대상 지역에서 새 Batch 계정을 만들 수 있도록 템플릿을 로드하고 수정합니다.

1. Azure Portal에서 **리소스 생성** 를 선택합니다.

1. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **ENTER** 를 누릅니다.

1. **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)** 를 선택합니다.

1. **만들기** 를 선택합니다.

1. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.

1. **파일 로드** 를 선택한 다음, 마지막 섹션에서 다운로드한 **template.json** 파일을 선택합니다.

1. 업로드된 **template.json** 파일에서 Batch 계정 이름에 대해 새 **defaultValue** 를 입력하여 대상 Batch 계정의 이름을 지정합니다. 이 예제에서는 Batch 계정 이름의 **defaultValue** 를 `mytargetaccount`로 설정한 다음, **defaultValue** 의 문자열을 `mytargetstorageaccount`의 리소스 ID로 바꿉니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "batchAccounts_mysourceaccount_name": {
                "defaultValue": "mytargetaccount",
                "type": "String"
            }
        },
   ```

1. 다음으로, 마이그레이션된 스토리지 계정의 리소스 ID로 스토리지 계정의 **defaultValue** 를 업데이트합니다. 이 값을 구하려면 Azure Portal의 스토리지 계정으로 이동하고 화면 맨 위에 있는 **JSON 보기** 를 선택한 다음, **리소스 ID** 아래에 표시된 값을 복사합니다. 다음 예제에서는 리소스 그룹 `mytargetresourcegroup`에서 `mytargetstorageaccount`라는 스토리지 계정에 대한 리소스 ID를 사용합니다.

   ```json
           "storageAccounts_mysourcestorageaccount_externalid": {
            "defaultValue": "/subscriptions/{subscriptionID}/resourceGroups/mytargetresourcegroup/providers/Microsoft.Storage/storageAccounts/mytargetstorageaccount",
            "type": "String"
        }
    },
   ```

1. 마지막으로, **위치** 속성을 편집하여 대상 지역을 사용합니다. 이 예제에서는 대상 지역을 `centralus`로 설정합니다.

```json
    {
        "resources": [
            {
                "type": "Microsoft.Batch/batchAccounts",
                "apiVersion": "2021-01-01",
                "name": "[parameters('batchAccounts_mysourceaccount_name')]",
                "location": "centralus",  
```

지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요.  지역 코드는 공백이 없는 지역 이름입니다. 예를 들어 **미국 중부** = **centralus** 입니다.

## <a name="move"></a>이동

템플릿을 배포하여 대상 지역에 새 Batch 계정을 만듭니다.

1. 이제 수정했으므로 **template.json** 파일 아래에서 **저장** 을 선택합니다.

1. 다음 속성 값을 입력하거나 선택합니다.
   - **구독**: Azure 구독을 선택합니다.
   - **리소스 그룹**: 연결된 스토리지 계정을 이동할 때 만든 리소스 그룹을 선택합니다.
   - **지역**: 계정을 이동할 Azure 지역을 선택합니다.

1. **검토 및 만들기** 를 선택한 다음, **만들기** 를 선택합니다.

### <a name="configure-the-new-batch-account"></a>새 Batch 계정 구성

일부 기능은 템플릿으로 내보내지 않으므로 새 Batch 계정에서 다시 만들어야 합니다. 여기에는 다음과 같은 옵션이 포함됩니다.

- 작업
- 작업 일정
- 인증서
- 애플리케이션 패키지

필요에 따라 새 계정에서 이를 구성해야 합니다. 참조를 위해 원본 Batch 계정에서 이러한 기능을 구성하는 방법을 확인할 수 있습니다.

## <a name="discard-or-clean-up"></a>삭제 또는 정리

새 Batch 계정이 새 지역에서 성공적으로 작동하고 있는지 확인하고 필요한 기능을 복원하면 원본 Batch 계정을 삭제할 수 있습니다.

Azure Portal을 사용하여 Batch 계정을 만들려면 다음을 수행합니다.

1. Azure Portal에서 왼쪽 메뉴를 확장하여 서비스의 메뉴를 열고 **Batch 계정** 을 선택합니다.

2. 삭제할 Batch 계정을 찾아 목록 오른쪽에 있는 **자세히** 단추( **...** )를 마우스 오른쪽 단추로 클릭합니다. 만든 새 원본 Batch 계정이 아니라 원래 원본 Batch 계정인지 확인합니다.

3. **삭제** 를 선택한 다음, 확인합니다.

## <a name="next-steps"></a>다음 단계

- [리소스를 새 리소스 그룹이나 구독으로 이동하는 방법](../azure-resource-manager/management/move-resource-group-and-subscription.md)을 알아봅니다.
- [Azure VM을 다른 지역으로 이동하는 방법](../site-recovery/azure-to-azure-tutorial-migrate.md)을 알아봅니다.
