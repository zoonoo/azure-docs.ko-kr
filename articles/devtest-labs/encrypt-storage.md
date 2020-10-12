---
title: Azure DevTest Labs에서 랩에서 사용 되는 Azure storage 계정 암호화
description: Azure DevTest Labs에서 랩에서 사용 되는 Azure storage의 암호화를 구성 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 3c1d1531084deeabbe9a8d261e93554a2c691eb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87433593"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에서 사용 되는 Azure storage 암호화
Azure DevTest Labs에서 생성 되는 모든 랩은 연결 된 Azure storage 계정을 사용 하 여 생성 됩니다. 저장소 계정은 다음 용도로 사용 됩니다. 

- 가상 컴퓨터를 만드는 데 사용할 수 있는 [수식](devtest-lab-manage-formulas.md) 문서를 저장 합니다.
- 아티팩트를 적용 하 여 생성 된 배포 및 확장 로그를 포함 하는 아티팩트 결과를 저장 합니다. 
- [가상 하드 디스크 (Vhd)를 업로드 하 여 랩에 사용자 지정 이미지를 만듭니다.](devtest-lab-create-template.md)
- 가상 컴퓨터/환경 생성 중에 더 빠르게 검색 하기 위해 자주 사용 되는 [아티팩트와](add-artifact-vm.md) [Azure Resource Manager 템플릿을](devtest-lab-create-environment-from-arm.md) 캐시 합니다.

> [!NOTE]
> 위의 정보는 랩을 운영 하는 데 중요 합니다. 명시적으로 삭제 하지 않는 한 랩 (및 랩 리소스)의 수명 동안 저장 됩니다. 이러한 리소스를 수동으로 삭제 하면 랩 Vm을 만들 때 오류가 발생 하 고 나중에 사용할 수 있도록 수식이 손상 될 수 있습니다. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>저장소 계정을 찾고 해당 내용을 확인 합니다.

1. 랩의 홈 페이지에 있는 **개요** 페이지에서 **리소스 그룹** 을 선택 합니다. 랩을 포함 하는 리소스 그룹에 대 한 **리소스 그룹** 페이지가 표시 되어야 합니다. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="개요 페이지에서 리소스 그룹을 선택 합니다.":::
1. 랩의 Azure storage 계정을 선택 합니다. 랩 저장소 계정에 대 한 명명 규칙은 `a<labNameWithoutInvalidCharacters><4-digit number>` 입니다. 예를 들어 랩 이름이 이면 `contosolab` 저장소 계정 이름은 일 수 있습니다 `acontosolab7576` . 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="개요 페이지에서 리소스 그룹을 선택 합니다.":::
3. **저장소 계정** 페이지의 왼쪽 메뉴에서 **Storage 탐색기 (미리 보기)** 를 선택 하 고 **BLOB 컨테이너** 를 선택 하 여 관련 랩 관련 콘텐츠를 찾습니다. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="개요 페이지에서 리소스 그룹을 선택 합니다." lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>랩 저장소 계정 암호화
Azure Storage은 클라우드에 유지 될 때 데이터를 자동으로 암호화 합니다. Azure Storage 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)를 참조하세요.

랩 저장소 계정의 데이터는 **Microsoft 관리 키**를 사용 하 여 암호화 됩니다. Microsoft에서 관리 하는 키를 사용 하 여 데이터를 암호화 하거나 자신의 키를 사용 하 여 암호화를 관리할 수 있습니다. 랩의 저장소 계정에 대 한 고유한 키를 사용 하 여 암호화를 관리 하도록 선택 하는 경우 Blob storage 및 Azure Files에서 데이터를 암호화/암호 해독 하는 데 사용할 Azure Key Vault를 사용 하 여 **고객 관리 키** 를 지정할 수 있습니다. 고객 관리 키에 대 한 자세한 내용은 Azure Key Vault에서 [고객이 관리 하는 키를 사용 하 여 Azure Storage 암호화 관리를](../storage/common/encryption-customer-managed-keys.md)참조 하세요.

Azure Storage 암호화를 위해 고객이 관리 하는 키를 구성 하는 방법을 알아보려면 다음 문서를 참조 하세요. 

- [Azure Portal](../storage/common/storage-encryption-keys-portal.md)
- [Azure PowerShell](../storage/common/storage-encryption-keys-powershell.md)
- [Azure CLI](../storage/common/storage-encryption-keys-cli.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Azure Blob 저장소 수명 주기 관리
앞서 설명한 것 처럼 랩의 저장소 계정에 저장 된 정보는 랩에서 오류 없이 작동 하는 데 매우 중요 합니다. 명시적으로 삭제 하지 않는 한이 데이터는 데이터의 형식에 따라 랩 또는 특정 랩 가상 컴퓨터의 수명 동안 랩의 저장소 계정에 계속 남아 있습니다.

### <a name="uploaded-vhds"></a>업로드 된 Vhd
이러한 Vhd는 사용자 지정 이미지를 만드는 데 사용 됩니다. 이러한 파일을 제거 하면 더 이상 이러한 Vhd에서 사용자 지정 이미지를 만들 수 없게 됩니다.

### <a name="artifacts-cache"></a>아티팩트 캐시
이러한 캐시는 아티팩트가 적용 될 때마다 다시 생성 됩니다. 각 참조 된 리포지토리의 최신 콘텐츠를 사용 하 여 새로 고쳐집니다. 따라서 저장소 관련 비용을 절약 하기 위해이 정보를 삭제 하는 경우에는 해당 릴리프가 일시적일 수 있습니다.

### <a name="azure-resource-manager-template-cache"></a>Azure Resource Manager 템플릿 캐시
이러한 캐시는 Azure Resource Manager 기반 템플릿 리포지토리가 연결 되 고 랩에서 분리 될 때마다 다시 생성 됩니다. 각 참조 된 리포지토리의 최신 콘텐츠를 사용 하 여 새로 고쳐집니다. 따라서 저장소 관련 비용을 절약 하기 위해이 정보를 삭제 하는 경우에는 해당 릴리프가 일시적일 수 있습니다.

### <a name="formulas"></a>수식
이러한 문서는 기존 Vm에서 수식을 만들고 수식에서 Vm을 만드는 옵션을 지 원하는 데 사용 됩니다. 이러한 수식 문서를 삭제 하면 다음 작업을 수행 하는 동안 오류가 발생할 수 있습니다.

- 기존 랩 VM에서 수식 만들기
- 수식 만들기 또는 업데이트 
- 수식에서 VM 만들기

### <a name="artifact-results"></a>아티팩트 결과
아티팩트가 적용 될 때 각 아티팩트 결과의 크기는 랩 Vm에서 실행 되는 아티팩트의 수와 유형에 따라 시간이 지남에 따라 증가할 수 있습니다. 따라서 랩 소유자는 이러한 문서의 수명 주기를 제어 하는 것이 좋습니다. 자세한 내용은 [Azure Blob 스토리지 수명 주기 관리](../storage/blobs/storage-lifecycle-management-concepts.md)를 참조하세요.

> [!IMPORTANT]
> Azure Storage 계정과 관련 된 비용을 줄이기 위해이 단계를 수행 하는 것이 좋습니다. 

예를 들어 다음 규칙은 아티팩트 결과에 대해 특별히 90 일 만료 규칙을 설정 하는 데 사용 됩니다. 이를 통해 오래 된 아티팩트 결과가 정기적으로 저장소 계정에서 재활용 됩니다.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계
Azure Storage 암호화를 위해 고객이 관리 하는 키를 구성 하는 방법을 알아보려면 다음 문서를 참조 하세요. 

- [Azure Portal](../storage/common/storage-encryption-keys-portal.md)
- [Azure PowerShell](../storage/common/storage-encryption-keys-powershell.md)
- [Azure CLI](../storage/common/storage-encryption-keys-cli.md)


