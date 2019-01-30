---
title: Azure Storage Blob에 대한 변경 불가능한 스토리지 | Microsoft Docs
description: Azure Storage는 사용자가 지정한 간격 동안 지울 수 없고 수정할 수 없는 상태로 데이터를 저장할 수 있게 하는 Blob(개체) 스토리지에 대한 WORM(Write Once, Read Many) 지원을 제공합니다.
services: storage
author: xyh1
ms.service: storage
ms.topic: article
ms.date: 01/21/2019
ms.author: hux
ms.component: blobs
ms.openlocfilehash: f3a67c2eddba2b7bd734237466243da8f43a862a
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848651"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Azure Blob Storage에 중요 비즈니스용 데이터 저장

사용자는 Azure Blob(개체) 스토리지에 대한 변경 불가능한 스토리지를 사용하여 중요 비즈니스용 데이터를 WORM(Write Once, Read Many) 상태로 저장할 수 있습니다. 이 상태는 사용자가 지정한 간격 동안 데이터를 지울 수 없고 수정할 수 없게 만듭니다. 보존 간격 기간 동안 Blob을 만들고 읽을 수 있지만, 수정하거나 삭제할 수는 없습니다.

## <a name="overview"></a>개요

변경 불가능한 스토리지는 금융 기관 및 관련 업계, 특히 증권 중개인 조직에서 데이터를 안전하게 저장하는 데 도움이 됩니다. 모든 시나리오에서 중요한 데이터가 삭제되지 않도록 보호하는 데 활용할 수도 있습니다.  

일반적인 적용 분야는 다음과 같습니다.

- **규정 준수**: Azure Blob Storage에 대해 변경 불가능한 스토리지를 사용하면 SEC 17a-4(f), CFTC 1.31(d), FINRA 및 기타 규정을 처리할 수 있습니다.

- **보안 문서 보존**: Blob Storage는 계정 관리 권한이 있는 사용자를 포함하여 모든 사용자가 데이터를 수정하거나 삭제할 수 없도록 합니다.

- **법적 보존**: Azure Blob Storage에 변경 불가능한 스토리지를 사용하면 사용자가 소송 또는 범죄 수사에 중요한 정보를 원하는 기간 동안 변조 방지 상태로 저장할 수 있습니다.

변경 불가능한 스토리지에서 사용하도록 설정할 수 있는 지원은 다음과 같습니다.

- **시간 기준 보존 정책 지원**: 사용자가 지정한 간격 동안 데이터를 저장하는 정책을 설정합니다.

- **법적 보존 정책 지원**: 보존 간격을 알 수 없는 경우 사용자가 법적 보존을 지울 때까지 데이터를 변경할 수 없는 상태로 저장하도록 법적 보존을 설정할 수 있습니다.  법적 보존을 설정하면 BLOB을 만들고 읽을 수 있지만, 수정하거나 삭제할 수는 없습니다. 각 법적 보존은 식별자 문자열(예: 사례 ID)로 사용되는 사용자 정의 영숫자 태그와 연결됩니다.

- **모든 Blob 계층 지원**: WORM 정책은 Azure Blob Storage 계층과 별개이며, 모든 계층(핫, 쿨 및 보관)에 적용됩니다. 사용자가 데이터 불변성을 유지하면서 해당 워크로드에 대해 비용이 가장 최적화된 계층으로 데이터를 전환할 수 있습니다.

- **컨테이너 수준 구성**: 사용자가 컨테이너 수준에서 시간 기준 보존 정책 및 법적 보존 태그를 구성할 수 있습니다. 사용자는 간단한 컨테이너 수준 설정을 사용하여 시간 기반 보존 정책을 만들거나 잠그고, 보존 간격을 연장하고, 법적 보존을 설정 및 해제하는 등의 작업을 수행할 있습니다. 이러한 정책은 컨테이너의 모든 Blob(기존 및 신규)에 적용됩니다.

- **감사 로깅 지원**: 각 컨테이너에 감사 로그가 포함됩니다. 최대 3개의 보존 간격 연장 로그를 사용하여 잠긴 시간 기준 보존 정책에 대해 시간 기준 보존 명령을 최대 5개까지 표시합니다. 시간 기준 보존의 경우 로그에는 사용자 ID, 명령 유형, 타임스탬프 및 보존 간격이 포함됩니다. 법적 보존의 경우 로그에는 사용자 ID, 명령 유형, 타임스탬프 및 법적 보존 태그가 포함됩니다. 이 로그는 SEC 17a-4(f) 규정 지침에 따라 컨테이너 수명 동안 유지됩니다. [Azure 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 모든 제어 평면 활동에 대한 더 포괄적인 로그를 보여 줍니다. 이러한 로그는 규정 또는 다른 목적으로 필요할 수 있으므로 사용자가 이러한 로그를 영구적으로 저장할 책임이 있습니다.

변경 불가능한 스토리지는 모든 Azure 공용 지역에서 사용할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

Azure Blob Storage에 대한 변경 불가능한 스토리지는 두 가지 유형의 WORM 또는 변경 불가능한 정책, 즉, 시간 기준 보존 정책과 법적 보존 정책을 지원합니다. 이러한 변경 불가능한 정책을 만드는 방법에 대한 자세한 내용은 [시작](#getting-started) 섹션을 참조하세요.

컨테이너에 시간 기준 보존 정책 또는 법적 보존을 적용하면 모든 기존 Blob이 변경 불가능한 상태(쓰기 및 삭제 금지)로 전환됩니다. 컨테이너에 업로드된 모든 새 Blob도 변경 불가능한 상태로 전환됩니다.

> [!IMPORTANT]
> SEC 17a-4(f) 및 기타 규정을 준수하도록 Blob을 변경 불가능한 상태(쓰기 및 삭제 금지)로 유지하려면 시간 기준 보존 정책을 *잠가야* 합니다. 적절한 시간(일반적으로 24 시간 이내)에 정책을 잠그는 것이 좋습니다. 단기간의 기능 평가 이외의 용도로는 *잠금 해제* 상태를 사용하지 않는 것이 좋습니다.

컨테이너에 시간 기준 보존 정책을 적용하면 컨테이너의 모든 Blob이 *유효* 보존 기간 동안 변경 불가능한 상태로 유지됩니다. 기존 BLOB의 유효 보존 기간은 BLOB 생성 시간과 사용자가 지정한 보존 기간의 차와 같습니다.

새 BLOB의 경우 유효 보존 기간은 사용자가 지정한 보존 기간과 같습니다. 사용자가 보존 간격을 연장할 수 있으므로 변경 불가능한 스토리지는 사용자 지정 보존 간격의 최신 값을 사용하여 유효 보존 기간을 계산합니다.

> [!TIP]
> 예제:
>
> 사용자가 5년 보존 간격의 시간 기준 보존 정책을 만듭니다.
>
> 해당 컨테이너에 있는 기존 Blob인 testblob1은 1년 전에 만들었습니다. testblob1의 유효 보존 기간은 4년입니다.
>
> 이제 새 BLOB testblob2가 컨테이너에 업로드됩니다. 이 새 Blob의 유효 보존 기간은 5년입니다.

### <a name="legal-holds"></a>법적 보존

법적 보존이 설정되면 이 법적 보존을 지울 때까지 기존 및 새 Blob이 모두 변경 불가능한 상태로 유지됩니다. 법적 보존을 설정하고 지우는 방법에 대한 자세한 내용은 [시작](#getting-started) 섹션을 참조하세요.

컨테이너에는 법적 보존 정책 및 시간 기준 보존 정책이 모두 있을 수 있습니다. 유효 보존 기간이 만료된 경우에도 법적 보존이 지워질 때까지 해당 컨테이너의 모든 Blob은 변경 불가능한 상태로 유지됩니다. 반대로, 모든 법적 보존이 지워진 경우에도 유효 보존 기간이 만료될 때까지 Blob은 변경 불가능한 상태로 유지됩니다.

다음 표에서는 변경 불가능한 여러 시나리오에 사용할 수 없는 Blob 작업의 유형을 보여 줍니다. 자세한 내용은 [Azure Blob 서비스 API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) 설명서를 참조하세요.

|시나리오  |Blob 상태  |허용되지 않는 Blob 작업  |
|---------|---------|---------|
|BLOB의 유효 보존 기간이 아직 만료되지 않았고/않았거나 법적 보존이 설정되어 있음     |변경할 수 없음: 삭제 및 쓰기 금지         |컨테이너 삭제, Blob 삭제, Blob<sup>1</sup> 배치, 블록<sup>1</sup> 배치, 블록 목록<sup>1</sup> 배치, Blob 메타데이터 설정, 페이지 배치, Blob 속성 설정, 스냅숏 Blob, Blob 증분 복사, 블록 추가         |
|BLOB의 유효 보존 기간이 만료됨     |쓰기만 금지(삭제 작업은 허용)         |Blob<sup>1</sup> 배치, 블록<sup>1</sup> 배치, 블록 목록<sup>1</sup> 배치, Blob 메타데이터 설정, 페이지 배치, Blob 속성 설정, 스냅숏 Blob, Blob 증분 복사, 블록 추가         |
|모든 법적 보존을 지우고,컨테이너에 시간 기준 보존 정책이 설정되지 않음     |변경 가능         |없음         |
|만들어진 WORM 정책(시간 기준 보존 또는 법적 보존)이 없음     |변경 가능         |없음         |

<sup>1</sup> 애플리케이션에서 이 작업을 호출하여 Blob을 한 번 만들 수 있습니다. Blob에 대한 모든 후속 작업은 허용되지 않습니다.

> [!NOTE]
>
> 변경 불가능한 스토리지는 범용 v2 및 Blob Storage 계정에서만 사용할 수 있습니다. 계정은 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 통해 만들어야 합니다.

## <a name="pricing"></a>가격

이 기능을 사용하는 경우 추가 요금이 부과되지 않습니다. 변경 불가능한 데이터는 일반적으로 변경 가능한 데이터와 동일한 방식으로 가격이 책정됩니다. Azure Blob Storage의 가격 책정에 대한 자세한 내용은 [Azure Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.


## <a name="getting-started"></a>시작

최신 릴리스의 [Azure Portal](http://portal.azure.com) 및 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)는 물론 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)의 미리 보기 버전은 Azure Blob Storage에 대한 변경 불가능한 스토리지를 지원합니다.

### <a name="azure-portal"></a>Azure portal

1. 변경할 수 없는 상태로 유지해야 하는 BLOB을 저장할 새 컨테이너를 만들거나 기존 컨테이너를 선택합니다.
 컨테이너는 GPv2 또는 Blob Storage 계정에 있어야 합니다.
2. 컨테이너 설정에서 **액세스 정책**을 선택합니다. 그런 다음, **변경 불가능한 Blob Storage** 아래에서 **+ 정책 추가**를 선택합니다.

    ![포털의 컨테이너 설정](media/storage-blob-immutable-storage/portal-image-1.png)

3. 시간 기준 보존을 사용하도록 설정하려면 드롭다운 메뉴에서 **시간 기준 보존**을 선택합니다.

    !["정책 유형" 아래에서 선택된 "시간 기준 보존"](media/storage-blob-immutable-storage/portal-image-2.png)

4. 보존 간격을 일 단위로 선택합니다(최솟값은 1일).

    !["다음으로 보존 기간 업데이트" 상자](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    스크린샷에서 볼 수 있듯이, 정책의 초기 상태는 잠금 해제 상태입니다. 기능은 더 짧은 보존 간격으로 테스트할 수 있고, 정책은 잠그기 전에 변경할 수 있습니다. 잠금은 SEC 17a-4와 같은 규정을 준수하는 데 필수적입니다.

5. 정책을 잠급니다. 줄임표(**...**)를 마우스 오른쪽 단추로 클릭합니다. 그러면 다음 메뉴가 나타납니다.

    ![메뉴의 "잠금 정책"](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    **잠금 정책**을 선택합니다. 그러면 정책 상태가 이제 잠금 상태로 표시됩니다. 정책이 잠겨 있으면 삭제할 수 없으며 보존 간격 연장만 허용됩니다.

6. 법적 보존을 사용하도록 설정하려면 **+ 정책 추가**를 선택합니다. 드롭다운 메뉴에서 **법적 보존**을 선택합니다.

    ![메뉴의 "정책 유형" 아래에 있는 "법적 보존"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. 하나 이상의 태그를 사용하여 법적 보존을 만듭니다.

    ![정책 유형 아래의 "태그 이름" 상자](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

8. 법적 보존을 삭제하려면 태그를 제거하면 됩니다.

### <a name="azure-cli"></a>Azure CLI

이 기능은 `az storage container immutability-policy` 및 `az storage container legal-hold` 명령 그룹에 포함되어 있습니다. 이러한 그룹에 `-h`를 실행하여 명령을 확인합니다.

### <a name="powershell"></a>PowerShell

Az.Storage 미리 보기 모듈은 변경 불가능한 스토리지를 지원합니다.  기능을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 최신 버전의 PowerShellGet이 설치되어 있는지 확인합니다. `Install-Module PowerShellGet –Repository PSGallery –Force`
2. Azure PowerShell의 이전 설치를 제거합니다.
3. Azure PowerShell을 설치합니다. `Install-Module Az –Repository PSGallery –AllowClobber`
4. Azure PowerShell Storage 모듈의 미리 보기 버전을 설치합니다. `Install-Module Az.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

이 문서의 뒷부분에 나오는 [PowerShell 코드 샘플](#sample-powershell-code) 섹션에서는 기능을 사용하는 방법을 보여 줍니다.

## <a name="client-libraries"></a>클라이언트 라이브러리

Azure Blob Storage에 대한 변경 불가능한 스토리지를 지원하는 클라이언트 라이브러리는 다음과 같습니다.

- [.NET 클라이언트 라이브러리 버전 7.2.0-preview 이상](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Node.js 클라이언트 라이브러리 버전 4.0.0 이상](https://www.npmjs.com/package/azure-arm-storage)
- [Python 클라이언트 라이브러리 버전 2.0.0 릴리스 후보 2 이상](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java 클라이언트 라이브러리](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="supported-values"></a>지원되는 값

- 보존 간격의 최솟값은 1일이고, 최댓값은 400년입니다.
- 저장소 계정의 경우 변경 불가능한 정책이 잠겨 있는 컨테이너의 최대 수는 1,000개입니다.
- 저장소 계정의 경우 법적 보존이 설정된 컨테이너의 최대 수는 1,000개입니다.
- 컨테이너의 경우 법적 보존 태그의 최대 수는 10개입니다.
- 법적 보존 태그의 최대 길이는 23자의 영숫자이고, 최소 길이는 3자의 문자입니다.
- 컨테이너의 경우 잠겨 있는 변경 불가능한 정책에 허용되는 보존 간격 연장의 최대 수는 3개입니다.
- 변경 불가능한 정책이 잠겨 있는 컨테이너의 경우 시간 기준 보존 정책 로그의 최대 수는 5개이고, 컨테이너 보존 기간 동안 유지되는 법적 보존 정책 로그의 최대 수는 10개입니다.

## <a name="faq"></a>FAQ

**이 기능은 블록 Blob에만 적용되나요, 아니면 페이지 및 추가 Blob에도 적용되나요?**

변경이 불가능한 스토리지를 모든 Blob 유형과 함께 사용할 수 있지만 블록 Blob에 주로 사용하는 것이 좋습니다. 블록 Blob과는 달리, 페이지 Blob과 추가 Blob은 WORM 컨테이너 외부에서 만든 후에 복사해야 합니다. 이러한 Blob이 WORM 컨테이너에 복사되면 더 이상 추가 Blob에 *추가*하거나 페이지 Blob을 변경할 수 없습니다.

**이 기능을 사용하려면 항상 새 저장소 계정을 만들어야 하나요?**

변경이 불가능한 스토리지를 기존 또는 새로 만든 범용 V2나 Blob Storage 계정과 함께 사용할 수 있습니다. 이 기능은 Blob Storage에서만 사용할 수 있습니다.

***잠긴* 시간 기반 보존 정책 또는 법적 보존이 있는 컨테이너를 삭제하려고 시도하면 어떻게 되나요?**

잠긴 시간 기반 보존 정책 또는 법적 보존이 있는 Blob이 하나 이상 있는 경우 컨테이너 삭제 작업이 실패합니다. 활성 보존 기간이 있는 BLOB이 없고 법적 보존이 없는 경우에만 컨테이너 삭제 작업이 성공합니다. 컨테이너를 삭제하려면 먼저 Blob을 삭제해야 합니다.

**WORM 컨테이너에 *잠긴* 시간 기반 보존 정책 또는 법적 보존이 있는 저장소 계정을 삭제하려고 시도하면 어떻게 되나요?**

법적 보존 또는 보존 기간이 활성화된 BLOB이 있는 WORM 컨테이너가 하나 이상 있는 경우 저장소 계정 삭제 작업이 실패합니다.  저장소 계정을 삭제하려면 먼저 모든 WORM 컨테이너를 삭제해야 합니다. 컨테이너 삭제에 대한 내용은 앞의 질문을 참조하세요.

**BLOB이 변경할 수 없는 상태인 경우 BLOB 계층(핫, 쿨, 콜드) 간에 데이터를 이동할 수 있나요?**

예, Blob 계층 설정 명령을 사용하면 데이터를 변경할 수 없는 상태로 유지하면서 BLOB 계층 간에 데이터를 이동할 수 있습니다. 변경 불가능한 스토리지는 핫, 쿨 및 아카이브 Blob 계층에서 지원됩니다.

**보존 기간이 만료되지는 않았지만 요금을 지불하지 않은 경우 어떻게 되나요?**

요금을 지불하지 않으면 Microsoft와 맺은 사용 약관에 명시된 대로 일반 데이터 보존 정책이 적용됩니다.

**기능을 경험해 볼 수 있는 평가판 또는 유예 기간이 제공되나요?**

예. 시간 기준 보존 정책을 처음으로 만들면 해당 정책은 *잠금* 상태가 됩니다. 이 상태에서는 필요에 따라 보존 간격을 변경할 수 있습니다. 즉 해당 정책을 늘리거나 줄이고, 심지어 삭제할 수도 있습니다. 정책을 잠근 후에는 보존 간격이 만료될 때까지 잠금 상태가 유지됩니다. 이렇게 하면 보존 간격의 삭제 및 수정을 방지할 수 있습니다. *잠금 해제* 상태는 기능 평가 목적으로만 사용하고, 24 시간 내에 해당 정책을 잠그는 것이 좋습니다. 이러한 사례는 SEC 17a-4(f) 및 기타 규정을 준수하는 데 도움이 됩니다.

**국가 및 정부 클라우드에서 이 기능을 사용할 수 있나요?**

변경이 불가능한 스토리지는 Azure 공용, 중국 및 Government 지역에서 사용할 수 있습니다. 해당 지역에서 변경이 불가능한 스토리지를 사용할 수 없는 경우 azurestoragefeedback@microsoft.com으로 메일을 보내 주세요.

## <a name="sample-powershell-code"></a>PowerShell 코드 샘플

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

다음 PowerShell 스크립트 샘플은 참조하기 위한 목적으로 제공되는 것입니다. 이 스크립트는 새 저장소 계정과 컨테이너를 만듭니다. 그런 다음, 법적 보존 정책을 설정하고 지우고, 시간 기준 보존 정책(변경 불가능한 정책이라고도 함)을 만들고 잠그며, 보존 간격을 연장하는 방법을 보여 줍니다.

Azure Storage 계정 설정 및 테스트:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

법적 보존 설정 및 지우기:

```powershell
# Set a legal hold
Add-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

불변성 정책 만들기 또는 업데이트:
```powershell
# with an account name or container name
Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

불변성 정책 검색:
```powershell
# Get an immutability policy
Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzStorageContainerImmutabilityPolicy -Container $containerObject
```

불변성 정책 잠금(추가 -프롬프트 강제로 닫기):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

불변성 정책 확장:
```powershell

# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

불변성 정책 제거(추가 -프롬프트 강제로 닫기):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
