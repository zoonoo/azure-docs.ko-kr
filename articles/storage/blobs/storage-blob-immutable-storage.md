---
title: Azure Storage Blob에 대한 변경 불가능한 스토리지 | Microsoft Docs
description: Azure Storage는 사용자가 지정한 간격 동안 지울 수 없고 수정할 수 없는 상태로 데이터를 저장할 수 있게 하는 Blob(개체) 스토리지에 대한 WORM(Write Once, Read Many) 지원을 제공합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/01/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: aa6bee9cceffc0252dd39d85ebe9d70625e33419
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036397"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Azure Blob Storage에 중요 비즈니스용 데이터 저장

Azure Blob 저장소에 대 한 변경할 수 없는 저장소를 통해 사용자는 비즈니스에 중요 한 데이터 개체를 웜 (한 번 쓰기, 읽기 다) 상태로 저장할 수 있습니다. 이 상태는 사용자가 지정한 간격 동안 데이터를 지울 수 없고 수정할 수 없게 만듭니다. 보존 기간 동안 Blob 개체를 만들고 수정할 수 있지만 수정할 수는 없습니다. 변경 불가능 한 저장소는 범용 v2 및 모든 Azure 지역에서 Blob Storage 계정에 대해 사용 하도록 설정 됩니다.

## <a name="overview"></a>개요

변경할 수 없는 저장소는 데이터를 안전 하 게 저장 하는 의료 기관, 금융 기관 및 관련 산업 (특히 broker-판매자 조직)을 지원 합니다. 또한 수정 또는 삭제에 대 한 중요 한 데이터를 보호 하기 위해 모든 시나리오에서 활용할 수 있습니다. 

일반적인 적용 분야는 다음과 같습니다.

- **규정 준수**: Azure Blob Storage에 대해 변경 불가능한 스토리지를 사용하면 SEC 17a-4(f), CFTC 1.31(d), FINRA 및 기타 규정을 처리할 수 있습니다. Cohasset의 기술 백서는 이러한 규정 요구 사항을 해결 하는 방법은 [Microsoft 서비스 신뢰 포털](https://aka.ms/AzureWormStorage)을 통해 다운로드할 수 있는 방법에 대해 자세히 설명 합니다. [Azure 보안 센터](https://www.microsoft.com/trustcenter/compliance/compliance-overview) 에는 규정 준수 인증에 대 한 자세한 정보가 포함 되어 있습니다.

- **보안 문서 보존**: Azure Blob 저장소에 대 한 변경할 수 없는 저장소는 계정 관리 권한이 있는 사용자를 포함 하 여 모든 사용자가 데이터를 수정 하거나 삭제할 수 없도록 합니다.

- **법적 보존**: Azure Blob 저장소에 대 한 변경할 수 없는 저장소는 보류를 제거할 때까지 사용자가 원하는 기간 동안 소송 또는 비즈니스 사용에 중요 한 중요 한 정보를 저장 하는 데 사용할 수 있습니다. 이 기능은 법률 사용 사례로 국한 되지 않지만 이벤트 트리거 또는 회사 정책을 기반으로 데이터를 보호 해야 하는 이벤트 기반 보류 또는 엔터프라이즈 잠금으로 간주할 수도 있습니다.

변경할 수 없는 저장소는 다음을 지원 합니다.

- **[시간 기반 보존 정책 지원](#time-based-retention)** : 사용자는 지정 된 간격에 대 한 데이터를 저장 하도록 정책을 설정할 수 있습니다. 시간 기반 보존 정책이 설정 된 경우 blob을 만들고 읽을 수 있지만 수정 하거나 삭제할 수는 없습니다. 보존 기간이 만료 된 후에는 blob을 삭제할 수는 있지만 덮어쓸 수는 없습니다.

- **[법적 보존 정책 지원](#legal-holds)** : 보존 기간을 알 수 없는 경우 사용자는 법적 보류를 지울 때까지 데이터 immutably을 저장 하도록 법적 고 지를 설정할 수 있습니다.  법적 보존 정책이 설정 된 경우 blob을 만들고 읽을 수 있지만 수정 하거나 삭제할 수는 없습니다. 각 법적 보류는 식별자 문자열로 사용 되는 사용자 정의 영숫자 태그 (예: 사례 ID, 이벤트 이름 등)와 연결 됩니다. 

- **모든 Blob 계층 지원**: WORM 정책은 Azure Blob Storage 계층과 별개이며, 모든 계층(핫, 쿨 및 보관)에 적용됩니다. 사용자가 데이터 불변성을 유지하면서 해당 워크로드에 대해 비용이 가장 최적화된 계층으로 데이터를 전환할 수 있습니다.

- **컨테이너 수준 구성**: 사용자가 컨테이너 수준에서 시간 기준 보존 정책 및 법적 보존 태그를 구성할 수 있습니다. 사용자는 간단한 컨테이너 수준 설정을 사용 하 여 시간 기반 보존 정책을 만들고 잠그고, 보존 간격을 확장 하 고, 법적 보류를 설정 하 고 지울 수 있습니다. 이러한 정책은 컨테이너의 모든 Blob(기존 및 신규)에 적용됩니다.

- **감사 로깅 지원**: 각 컨테이너에는 정책 감사 로그가 포함 됩니다. 잠긴 시간 기반 보존 정책에 대 한 최대 7 개의 시간 기반 보존 명령을 표시 하 고 사용자 ID, 명령 유형, 타임 스탬프 및 보존 간격을 포함 합니다. 법적 보존의 경우 로그에는 사용자 ID, 명령 유형, 타임스탬프 및 법적 보존 태그가 포함됩니다. 이 로그는 초 17a-4 (f) 규정 지침에 따라 정책의 수명 동안 유지 됩니다. [Azure 활동 로그](../../azure-monitor/platform/activity-logs-overview.md) 에는 모든 제어 평면 활동의 포괄적인 로그가 표시 됩니다. [Azure 진단 로그](../../azure-monitor/platform/diagnostic-logs-overview.md) 를 사용 하도록 설정 하는 동안 데이터 평면 작업을 유지 하 고 표시 합니다. 이러한 로그는 규정 또는 다른 목적으로 필요할 수 있으므로 사용자가 이러한 로그를 영구적으로 저장할 책임이 있습니다.

## <a name="how-it-works"></a>작동 방법

Azure Blob Storage에 대한 변경 불가능한 스토리지는 두 가지 유형의 WORM 또는 변경 불가능한 정책, 즉, 시간 기준 보존 정책과 법적 보존 정책을 지원합니다. 컨테이너에 시간 기반 보존 정책 또는 법적 보류가 적용 되 면 모든 기존 blob이 30 초 이내에 변경할 수 없는 웜 상태로 이동 합니다. 해당 컨테이너에 업로드 된 모든 새 blob도 변경할 수 없는 상태로 전환 됩니다. 모든 blob을 변경할 수 없는 상태로 옮긴 후에는 변경할 수 없는 정책이 확인 되며 변경할 수 없는 컨테이너에 있는 기존 개체 및 새 개체에 대 한 모든 덮어쓰기 또는 삭제 작업이 허용 되지 않습니다.

변경할 수 없는 정책에 의해 보호 되는 blob이 있는 경우에도 컨테이너 및 계정 삭제가 허용 되지 않습니다. 잠긴 시간 기반 보존 정책 또는 법적 보존이 있는 Blob이 하나 이상 있는 경우 컨테이너 삭제 작업이 실패합니다. 법적 보존 또는 보존 기간이 활성화된 BLOB이 있는 WORM 컨테이너가 하나 이상 있는 경우 스토리지 계정 삭제 작업이 실패합니다. 

### <a name="time-based-retention"></a>시간 기반 보존

> [!IMPORTANT]
> 시간 기반 보존 정책은 초 17a-4 (f) 및 기타 규정 준수에 대 한 호환 되지 않는 (쓰기 및 삭제 보호) 상태에 있는 blob에 대해 *잠가야* 합니다. 일반적으로 24 시간 이내에 적절 한 시간 내에 정책을 잠그는 것이 좋습니다. 적용 된 시간 기반 보존 정책의 초기 상태는 잠금 *해제*되어 있으므로 잠금 전에 기능을 테스트 하 고 정책을 변경할 수 있습니다. *잠금 해제* 된 상태는 불변성 보호를 제공 하지만 단기 기능 평가판 이외의 용도로는 *잠금 해제* 상태를 사용 하지 않는 것이 좋습니다. 

컨테이너에 시간 기준 보존 정책을 적용하면 컨테이너의 모든 Blob이 *유효* 보존 기간 동안 변경 불가능한 상태로 유지됩니다. 기존 BLOB의 유효 보존 기간은 BLOB 생성 시간과 사용자가 지정한 보존 기간의 차와 같습니다.

새 BLOB의 경우 유효 보존 기간은 사용자가 지정한 보존 기간과 같습니다. 사용자가 보존 간격을 연장할 수 있으므로 변경 불가능한 스토리지는 사용자 지정 보존 간격의 최신 값을 사용하여 유효 보존 기간을 계산합니다.

> [!TIP]
> **예제:** 사용자가 5년 보존 간격의 시간 기준 보존 정책을 만듭니다.
>
> 해당 컨테이너의 기존 blob _testblob1_는 1 년 전에 만들어졌습니다. _Testblob1_ 에 대 한 유효 보존 기간은 4 년입니다.
>
> 이제 새 blob _testblob2_이 컨테이너에 업로드 됩니다. 이 새 Blob의 유효 보존 기간은 5년입니다.

잠금 해제 된 시간 기반 보존 정책은 기능 테스트에만 사용 하는 것이 좋으며,이 경우에는 SEC 17a-4 (f) 및 기타 규정 준수를 준수 하기 위해 정책을 잠가야 합니다. 시간 기반 보존 정책이 잠기면 정책을 제거할 수 없으며 유효 보존 기간으로 최대 5 개의 증가가 허용 됩니다. 시간 기반 보존 정책을 설정 하 고 잠그는 방법에 대 한 자세한 내용은 [시작](#getting-started) 섹션을 참조 하세요.

### <a name="legal-holds"></a>법적 보존

법적 보존이 설정되면 이 법적 보존을 지울 때까지 기존 및 새 Blob이 모두 변경 불가능한 상태로 유지됩니다. 법적 보존을 설정하고 지우는 방법에 대한 자세한 내용은 [시작](#getting-started) 섹션을 참조하세요.

컨테이너에는 법적 보존 정책 및 시간 기준 보존 정책이 모두 있을 수 있습니다. 유효 보존 기간이 만료된 경우에도 법적 보존이 지워질 때까지 해당 컨테이너의 모든 Blob은 변경 불가능한 상태로 유지됩니다. 반대로, 모든 법적 보존이 지워진 경우에도 유효 보존 기간이 만료될 때까지 Blob은 변경 불가능한 상태로 유지됩니다.

다음 표에서는 변경 불가능한 여러 시나리오에 사용할 수 없는 Blob 작업의 유형을 보여 줍니다. 자세한 내용은 [Azure Blob 서비스 API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) 설명서를 참조하세요.

|시나리오  |Blob 상태  |허용되지 않는 Blob 작업  |
|---------|---------|---------|
|BLOB의 유효 보존 기간이 아직 만료되지 않았고/않았거나 법적 보존이 설정되어 있음     |변경할 수 없음: 삭제 및 쓰기 금지         | Blob<sup>1</sup>, put 블록<sup>1</sup>, 블록 목록<sup>1</sup>배치, 컨테이너 삭제, Blob 삭제, Blob 메타 데이터 설정, 페이지 배치, blob 속성 설정, 스냅숏 Blob, 증분 복사 blob, 추가 블록         |
|BLOB의 유효 보존 기간이 만료됨     |쓰기만 금지(삭제 작업은 허용)         |Blob<sup>1</sup> 배치, 블록<sup>1</sup> 배치, 블록 목록<sup>1</sup> 배치, Blob 메타데이터 설정, 페이지 배치, Blob 속성 설정, 스냅샷 Blob, Blob 증분 복사, 블록 추가         |
|모든 법적 보존을 지우고,컨테이너에 시간 기준 보존 정책이 설정되지 않음     |변경 가능         |없음         |
|만들어진 WORM 정책(시간 기준 보존 또는 법적 보존)이 없음     |변경 가능         |없음         |

<sup>1</sup> 응용 프로그램에서 이러한 작업을 통해 새 blob을 한 번 만들 수 있습니다. 변경할 수 없는 컨테이너의 기존 blob 경로에 대 한 모든 후속 덮어쓰기 작업은 허용 되지 않습니다.

## <a name="supported-values"></a>지원되는 값

### <a name="time-based-retention"></a>시간 기반 보존
- 저장소 계정의 경우 잠긴 시간 기반 변경 불가능 정책을 사용 하는 최대 컨테이너 수는 1000입니다.
- 최소 보존 기간은 1 일입니다. 최대값은 146000 일 (400 년)입니다.
- 컨테이너의 경우 잠긴 시간 기반 변경 불가능 정책에 대 한 보존 기간을 연장 하는 최대 편집 수는 5입니다.
- 컨테이너의 경우 잠긴 정책에 대해 최대 7 개의 시간 기반 보존 정책 감사 로그가 보존 됩니다.

### <a name="legal-hold"></a>법적 보존
- 스토리지 계정의 경우 법적 보존이 설정된 컨테이너의 최대 수는 1,000개입니다.
- 컨테이너의 경우 법적 보존 태그의 최대 수는 10개입니다.
- 법적 보류 태그의 최소 길이는 3 개의 영숫자 문자입니다. 최대 길이는 23 자입니다.
- 컨테이너의 경우 정책 기간 동안 최대 10 개의 법적 보류 정책 감사 로그가 보존 됩니다.

## <a name="pricing"></a>가격 책정

이 기능을 사용하는 경우 추가 요금이 부과되지 않습니다. 변경 불가능한 데이터는 일반적으로 변경 가능한 데이터와 동일한 방식으로 가격이 책정됩니다. Azure Blob Storage의 가격 책정에 대한 자세한 내용은 [Azure Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

## <a name="getting-started"></a>시작
변경할 수 없는 저장소는 범용 v2 및 Blob Storage 계정에만 사용할 수 있습니다. 이러한 계정은 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 통해 관리 되어야 합니다. 기존 범용 v1 저장소 계정 업그레이드에 대 한 자세한 내용은 [저장소 계정 업그레이드](../common/storage-account-upgrade.md)를 참조 하세요.

[Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)및 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) 의 최신 릴리스는 Azure Blob storage에 대해 변경할 수 없는 저장소를 지원 합니다. [클라이언트 라이브러리 지원](#client-libraries) 도 제공 됩니다.

### <a name="azure-portal"></a>Azure Portal

1. 변경할 수 없는 상태로 유지해야 하는 BLOB을 저장할 새 컨테이너를 만들거나 기존 컨테이너를 선택합니다.
 컨테이너는 GPv2 또는 Blob Storage 계정에 있어야 합니다.
2. 컨테이너 설정에서 **액세스 정책**을 선택합니다. 그런 다음, **변경 불가능한 Blob Storage** 아래에서 **+ 정책 추가**를 선택합니다.

    ![포털의 컨테이너 설정](media/storage-blob-immutable-storage/portal-image-1.png)

3. 시간 기준 보존을 사용하도록 설정하려면 드롭다운 메뉴에서 **시간 기준 보존**을 선택합니다.

    !["정책 유형" 아래에서 선택된 "시간 기준 보존"](media/storage-blob-immutable-storage/portal-image-2.png)

4. 보존 기간 (일)을 입력 합니다 (허용 되는 값은 1 ~ 146000 일).

    !["다음으로 보존 기간 업데이트" 상자](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    정책의 초기 상태를 잠금 해제 하 여 잠금 전에 기능을 테스트 하 고 정책을 변경할 수 있습니다. 정책 잠금은 SEC 17a-4와 같은 규정 준수에 필수적입니다.

5. 정책을 잠급니다. 줄임표 ( **...** )를 마우스 오른쪽 단추로 클릭 하면 추가 작업이 포함 된 다음 메뉴가 나타납니다.

    ![메뉴의 "잠금 정책"](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. **잠금 정책** 을 선택 하 고 잠금을 확인 합니다. 이제 정책이 잠겨 있으므로 삭제할 수 없습니다. 보존 간격의 확장명만 허용 됩니다. Blob 삭제 및 재정의는 허용 되지 않습니다. 

    ![메뉴에서 "정책 잠금" 확인](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. 법적 보존을 사용하도록 설정하려면 **+ 정책 추가**를 선택합니다. 드롭다운 메뉴에서 **법적 보존**을 선택합니다.

    ![메뉴의 "정책 유형" 아래에 있는 "법적 보존"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. 하나 이상의 태그를 사용하여 법적 보존을 만듭니다.

    ![정책 유형 아래의 "태그 이름" 상자](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. 법적 보존을 취소 하려면 적용 된 법적 보류 식별자 태그를 제거 하면 됩니다.

### <a name="azure-cli"></a>Azure CLI

이 기능은 `az storage container immutability-policy` 및 `az storage container legal-hold` 명령 그룹에 포함되어 있습니다. 이러한 그룹에 `-h`를 실행하여 명령을 확인합니다.

### <a name="powershell"></a>PowerShell

Az. Storage 모듈은 변경할 수 없는 저장소를 지원 합니다.  기능을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 최신 버전의 PowerShellGet이 설치되어 있는지 확인합니다. `Install-Module PowerShellGet –Repository PSGallery –Force`
2. Azure PowerShell의 이전 설치를 제거합니다.
3. Azure PowerShell을 설치합니다. `Install-Module Az –Repository PSGallery –AllowClobber`

이 문서의 뒷부분에 나오는 [PowerShell 코드 샘플](#sample-powershell-code) 섹션에서는 기능을 사용하는 방법을 보여 줍니다.

## <a name="client-libraries"></a>클라이언트 라이브러리

Azure Blob Storage에 대한 변경 불가능한 스토리지를 지원하는 클라이언트 라이브러리는 다음과 같습니다.

- [.NET 클라이언트 라이브러리 버전 7.2.0-preview 이상](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Node.js 클라이언트 라이브러리 버전 4.0.0 이상](https://www.npmjs.com/package/azure-arm-storage)
- [Python 클라이언트 라이브러리 버전 2.0.0 릴리스 후보 2 이상](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java 클라이언트 라이브러리](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>FAQ

**웜 규정 준수 설명서를 제공할 수 있나요?**

예. 규정 준수를 문서화 하기 위해 Microsoft는 레코드 관리 및 정보 거 버 넌 스를 전문적으로 파악 하 고, Azure 변경할 수 없는 Blob Storage을 평가 하 고, 요구 사항에 따라 규정을 준수 하는 선두 독립적인 평가 회사를 유지 금융 서비스 업계에 연결할 수 있습니다. CFTC Rule 1.31 (d), FINRA Rule 4511 및 SEC Rule 17a-4의 관련 저장소 요구 사항을 충족 하는 Azure 변경 불가능 Blob Storage Azure의 변경 불가능 상태에 대 한 cohasset의 유효성을 검사 했습니다. Microsoft는이 규칙 집합을 대상으로 합니다 .이 규칙은 금융 기관에 대 한 레코드 보존에 대해 전역적으로 가장 규범적인 지침을 나타냅니다. Cohasset 보고서는 [Microsoft 서비스 보안 센터](https://aka.ms/AzureWormStorage)에서 사용할 수 있습니다. Microsoft에서 웜 규정 준수와 관련 된 증명 문자를 요청 하려면 Azure 지원에 문의 하세요.

**이 기능은 블록 Blob에만 적용되나요, 아니면 페이지 및 추가 Blob에도 적용되나요?**

변경할 수 없는 저장소는 컨테이너 수준에서 설정 된 모든 blob 유형에 사용할 수 있지만 주로 블록 blob을 저장 하는 컨테이너에는 웜을 사용 하는 것이 좋습니다. 블록 blob과 달리 새 페이지 blob 및 추가 blob은 웜 컨테이너 외부에서 만든 다음에 복사 해야 합니다. 이러한 blob을 웜 컨테이너에 복사한 후 추가 *blob에 추가* 하거나 페이지 blob에 대 한 변경 내용이 허용 되지 않습니다. 따라서 모든 활성 Virtual Machines에 대해 Vhd (페이지 blob)를 저장 하는 컨테이너에서 웜 정책을 설정 하는 것은 VM 디스크를 잠그기 때문에 권장 되지 않습니다.

**이 기능을 사용 하려면 새 저장소 계정을 만들어야 하나요?**

아니요, 기존 또는 새로 만든 범용 v2 또는 Blob 저장소 계정으로 변경할 수 없는 저장소를 사용할 수 있습니다. 이 기능은 GPv2 및 Blob Storage 계정에서 블록 blob을 사용 하기 위한 것입니다. 범용 v1 저장소 계정은 지원 되지 않지만 범용 v2로 쉽게 업그레이드할 수 있습니다. 기존 범용 v1 저장소 계정 업그레이드에 대 한 자세한 내용은 [저장소 계정 업그레이드](../common/storage-account-upgrade.md)를 참조 하세요.

**법적 보류 및 시간 기반 보존 정책을 모두 적용할 수 있나요?**

예, 한 컨테이너에는 법적 보류와 시간 기반 보존 정책이 동시에 모두 포함 될 수 있습니다. 유효 보존 기간이 만료된 경우에도 법적 보존이 지워질 때까지 해당 컨테이너의 모든 Blob은 변경 불가능한 상태로 유지됩니다. 반대로, 모든 법적 보존이 지워진 경우에도 유효 보존 기간이 만료될 때까지 Blob은 변경 불가능한 상태로 유지됩니다.

**법률 proceedings of the에 대 한 법률 보유 정책 이거나 다른 사용 시나리오가 있나요?**

아니요, 법적 보류는 시간 기반이 아닌 보존 정책에 사용 되는 일반적인 용어입니다. 소송 관련 proceedings of the 사용할 필요는 없습니다. 법적 보류 정책은 보존 기간을 알 수 없는 중요 한 엔터프라이즈 웜 데이터를 보호 하기 위해 덮어쓰기 및 삭제를 사용 하지 않도록 설정 하는 데 유용 합니다. 이를 엔터프라이즈 정책으로 사용 하 여 중요 업무용 웜 워크 로드를 보호 하거나, 사용자 지정 이벤트 트리거에 시간 기반 보존 정책을 사용 해야 하기 전에 준비 정책으로 사용할 수 있습니다. 

**_잠긴_ 시간 기반 보존 정책 또는 법적 보류를 제거할 수 있나요?**

잠금 해제 된 시간 기반 보존 정책만 컨테이너에서 제거할 수 있습니다. 시간 기반 보존 정책이 잠긴 후에는 제거할 수 없습니다. 유효 보존 기간 확장만 허용 됩니다. 법적 보류 태그를 삭제할 수 있습니다. 모든 법적 태그를 삭제 하면 법적 보류가 제거 됩니다.

***잠긴* 시간 기반 보존 정책 또는 법적 보존이 있는 컨테이너를 삭제하려고 시도하면 어떻게 되나요?**

잠긴 시간 기반 보존 정책 또는 법적 보존이 있는 Blob이 하나 이상 있는 경우 컨테이너 삭제 작업이 실패합니다. 활성 보존 기간이 있는 BLOB이 없고 법적 보존이 없는 경우에만 컨테이너 삭제 작업이 성공합니다. 컨테이너를 삭제하려면 먼저 Blob을 삭제해야 합니다.

**WORM 컨테이너에 *잠긴* 시간 기반 보존 정책 또는 법적 보존이 있는 스토리지 계정을 삭제하려고 시도하면 어떻게 되나요?**

법적 보존 또는 보존 기간이 활성화된 BLOB이 있는 WORM 컨테이너가 하나 이상 있는 경우 스토리지 계정 삭제 작업이 실패합니다. 스토리지 계정을 삭제하려면 먼저 모든 WORM 컨테이너를 삭제해야 합니다. 컨테이너 삭제에 대한 내용은 앞의 질문을 참조하세요.

**BLOB이 변경할 수 없는 상태인 경우 BLOB 계층(핫, 쿨, 콜드) 간에 데이터를 이동할 수 있나요?**

예, 데이터를 호환 되는 변경 되지 않은 상태로 유지 하면서 blob 계층 설정 명령을 사용 하 여 blob 계층 전체에서 데이터를 이동할 수 있습니다. 변경 불가능한 스토리지는 핫, 쿨 및 아카이브 Blob 계층에서 지원됩니다.

**보존 기간이 만료되지는 않았지만 요금을 지불하지 않은 경우 어떻게 되나요?**

요금을 지불하지 않으면 Microsoft와 맺은 사용 약관에 명시된 대로 일반 데이터 보존 정책이 적용됩니다.

**기능을 경험해 볼 수 있는 평가판 또는 유예 기간이 제공되나요?**

예. 시간 기반 보존 정책을 처음 만들 때 *잠금 해제* 된 상태입니다. 이 상태에서는 필요에 따라 보존 간격을 변경할 수 있습니다. 즉 해당 정책을 늘리거나 줄이고, 심지어 삭제할 수도 있습니다. 정책을 잠근 후에는 보존 간격이 만료될 때까지 잠금 상태가 유지됩니다. 이 잠긴 정책은 보존 간격을 삭제 및 수정 하는 것을 방지 합니다. *잠금 해제* 상태는 기능 평가 목적으로만 사용하고, 24 시간 내에 해당 정책을 잠그는 것이 좋습니다. 이러한 사례는 SEC 17a-4(f) 및 기타 규정을 준수하는 데 도움이 됩니다.

**변경할 수 없는 blob 정책과 함께 일시 삭제를 사용할 수 있나요?**

예. [Azure Blob 저장소에 대 한 일시 삭제](storage-blob-soft-delete.md) 는 법적 보류 또는 시간 기반 보존 정책에 관계 없이 저장소 계정 내의 모든 컨테이너에 적용 됩니다. 변경할 수 없는 웜 정책이 적용 되 고 확인 되기 전에 추가 보호를 위해 일시 삭제를 사용 하는 것이 좋습니다. 

**기능을 사용할 수 있는 위치**

변경이 불가능한 스토리지는 Azure 공용, 중국 및 Government 지역에서 사용할 수 있습니다. 사용자의 지역에서 변경할 수 없는 저장소를 사용할 수 없는 경우 지원 및 azurestoragefeedback@microsoft.com전자 메일에 문의 하세요.

## <a name="sample-powershell-code"></a>PowerShell 코드 샘플

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

다음 PowerShell 스크립트 샘플은 참조하기 위한 목적으로 제공되는 것입니다. 이 스크립트는 새 스토리지 계정과 컨테이너를 만듭니다. 그런 다음, 법적 보존 정책을 설정하고 지우고, 시간 기준 보존 정책(변경 불가능한 정책이라고도 함)을 만들고 잠그며, 보존 간격을 연장하는 방법을 보여 줍니다.

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
Add-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

불변성 정책 만들기 또는 업데이트:
```powershell
# with an account name or container name
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

불변성 정책 검색:
```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzRmStorageContainerImmutabilityPolicy -Container $containerObject
```

불변성 정책 잠금(추가 -프롬프트 강제로 닫기):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

불변성 정책 확장:
```powershell

# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

잠금 해제 된 불변성 정책 제거 (프롬프트를 해제 하려면-Force 추가):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
