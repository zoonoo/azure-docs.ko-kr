---
title: Azure Blob Storage(미리 보기)의 변경할 수 없는 저장소 기능 | Microsoft Docs
description: 이제 Azure Storage는 사용자가 지정한 시간 동안 데이터를 지울 수 없고 수정할 수 없는 상태로 저장할 수 있는 Blob 개체 저장소에 대한 WORM 지원을 제공합니다. 여러 규제가 적용되는 산업의 조직, 특히 브로커-딜러 조직은 이 기능을 통해 SEC 17a-4(f) 및 기타 규정을 준수하는 방식으로 데이터를 저장할 수 있습니다.
services: storage
author: sangsinh
manager: twooley
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: sangsinh
ms.openlocfilehash: 195537b271c442b954d6d6e6fa8d1491c07822e8
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970247"
---
# <a name="immutable-storage-feature-of-azure-blob-storage-preview"></a>Azure Blob Storage의 변경할 수 없는 저장소 기능(미리 보기)

Azure Blob의 변경할 수 없는 저장소 기능을 사용하면 사용자는 중요 비즈니스용 데이터를 Azure BLOB 저장소에 WORM(Write Once Read Many) 상태로 저장할 수 있습니다. 이 상태는 사용자가 지정한 시간 동안 데이터를 지울 수 없고 수정할 수 없게 만듭니다. Blob을 만들고 읽을 수 있지만, 보존 기간에는 수정하거나 삭제할 수 없습니다.

## <a name="overview"></a>개요

여러 규제가 적용되는 산업의 조직, 특히 브로커-딜러 조직은 변경할 수 없는 저장소 기능을 통해 SEC 17a-4(f) 및 기타 규정을 준수하는 방식으로 데이터를 저장할 수 있습니다.

일반적인 적용 분야는 다음과 같습니다.

- **규정 준수**: Azure Blob의 변경할 수 없는 저장소 기능은 금융 기관 및 관련 산업에서 SEC 17a-4(f), CFTC 1.31©-(d), FINRA 등을 해결할 수 있도록 설계되었습니다.

- **안전하게 문서 보존**: BLOB 저장소 서비스는 계정 관리 권한이 있는 사용자를 포함한 그 누구도 데이터를 수정 또는 삭제할 수 없게 보호하여 사용자에게 최고 수준의 데이터 보호를 제공합니다.

- **법적 보존**: 사용자는 Azure Blob의 변경할 수 없는 저장소 기능을 통해 소송, 범죄 수사 등에 중요한 정보를 원하는 기간 동안 변조 불가능한 상태로 저장할 수 있습니다.

변경할 수 없는 저장소 기능을 통해 다음을 지원할 수 있습니다.

- **시간 기반 보존 정책 지원:** 사용자가 지정된 시간 동안 데이터를 저장하는 정책을 설정합니다.

- **법적 보존 정책 지원:** 보존 기간을 알 수 없는 경우 사용자는 법적 보존 기간이 끝날 때까지 데이터를 변경할 수 없는 상태로 저장하도록 법적 보존을 설정할 수 있습니다.  법적 보존을 설정하면 BLOB을 만들고 읽을 수 있지만, 수정하거나 삭제할 수는 없습니다. 각 법적 보존은 식별자 문자열(예: 사례 ID)로 사용되는 사용자 정의 영숫자 태그와 연결됩니다.

- **모든 BLOB 계층에 대한 지원:** WORM 정책은 Azure BLOB 저장소 계층과 독립적인 관계이며 핫 저장소 계층, 쿨 저장소 계층, 보관 저장소 계층에 모두 적용됩니다. 따라서 고객은 데이터 불변성을 유지하면서도 워크로드에 가장 비용 최적화된 계층에 데이터를 저장할 수 있습니다.

- **컨테이너 수준 구성:** 변경할 수 없는 저장소 기능을 통해 사용자는 컨테이너 수준에서 시간 기반 보존 정책 및 법적 보존 태그를 구성할 수 있습니다.  사용자는 간단한 컨테이너 수준 설정을 통해 시간 기반 보존 정책을 만들고/잠그고, 보존 기간을 연장하고, 법적 보존을 설정하고 지울 수 있습니다.  이러한 정책은 컨테이너의 모든 BLOB(기존 및 신규)에 적용됩니다.

- **감사 로깅 지원:** 각 컨테이너에는 최대 3개의 보존 기간 연장 로그와 함께 잠긴 시간 기반 보존 정책에 대한 시간 기반 보존 명령을 5개까지 표시하는 감사 로그가 포함되어 있습니다.  시간 기반 보존의 경우 로그에 사용자 ID, 명령 유형, 타임스탬프 및 보존 기간이 포함됩니다. 법적 보존의 경우 로그에 사용자 ID, 명령 유형, 타임스탬프 및 법적 보존 태그가 포함됩니다. 이 로그는 SEC 17a-4(f) 규정 지침에 정의된 컨테이너 수명 동안 보존됩니다. 보다 포괄적인 모든 제어 평면 활동 로그는 [Azure 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)에서 확인할 수 있습니다. 이러한 로그는 규정 또는 기타 용도에 필요할 수 있으므로 사용자는 이러한 로그를 영구적으로 저장할 책임이 있습니다.

 이 기능은 모든 Azure 공용 지역에서 사용됩니다.

## <a name="how-it-works"></a>작동 방법

Azure Blob의 변경할 수 없는 저장소 기능은 두 가지 유형의 WORM 또는 변경할 수 없는 정책, 즉, 시간 기반 보존과 법적 보존을 지원합니다. 이와 같은 변경할 수 없는 정책을 만드는 방법에 대한 자세한 내용은 [시작](#Getting-started) 섹션을 참조하세요.
컨테이너에서 시간 기반 보존 정책 또는 법적 보존을 적용하면 모든 기존 BLOB은 변경할 수 없는(쓰기 및 삭제 금지) 상태로 전환됩니다. 컨테이너에 업로드된 모든 새 BLOB 역시 변경할 수 없는 상태로 전환됩니다.

> [!IMPORTANT]
> SEC 17a-4(f) 및 기타 규정을 준수하도록 BLOB을 변경할 수 없는(쓰기 및 삭제 금지) 상태로 만들려면 시간 기반 보존 정책을 *잠가야* 합니다. 합리적인 시간 내에 정책을 잠그는 것이 좋으며, 일반적으로 24시간 이내에 잠글 것을 권장합니다. *잠기지 않은* 상태는 단기간 동안 기능을 평가하는 용도 외에는 사용하지 않는 것이 좋습니다.

 컨테이너에서 시간 기반 보존 정책을 적용하면 컨테이너의 모든 BLOB이 *유효* 보존 기간 동안 변경할 수 없는 상태로 보존됩니다. 기존 BLOB의 유효 보존 기간은 BLOB 생성 시간과 사용자가 지정한 보존 기간의 차와 같습니다. 새 BLOB의 경우 유효 보존 기간은 사용자가 지정한 보존 기간과 같습니다. 사용자가 보존 기간을 변경할 수 있으므로 사용자가 가장 최근에 지정한 보존 기간 값을 사용하여 유효 보존 기간을 계산합니다.

> [!TIP]
> 예: 사용자가 보존 기간 5년의 시간 기반 보존 정책을 만듭니다.
> 기존 BLOB인 testblob1이 있고, 이 컨테이너는 1년 전에 만들었습니다. testblob1의 유효 보존 기간은 4년이 됩니다.
> 이제 새 BLOB testblob2가 컨테이너에 업로드됩니다. 이 새 BLOB의 유효 보존 기간은 5년이 됩니다.

### <a name="legal-holds"></a>법적 보존

법적 보존 시 모든 기존 및 새 BLOB은 법적 보존이 삭제될 때까지 변경할 수 없는 상태로 보존됩니다.
법적 보존을 설정하고 지우는 방법에 대한 자세한 내용은 [시작](#Getting-started) 섹션을 참조하세요.

한 컨테이너에 법적 보존 및 시간 기반 보존 정책을 모두 사용할 수 있습니다. 이 컨테이너의 모든 BLOB은 유효 보존 기간이 만료되더라도 법적 보존이 삭제될 때까지 변경할 수 없는 상태로 보존됩니다. 반대로, 모든 법적 보존이 삭제되더라도 유효 보존 기간이 만료될 때까지 BLOB이 변경할 수 없는 상태로 보존됩니다.
다음 표는 여러 변경할 수 없는 시나리오에 사용할 수 없는 BLOB 작업의 종류를 보여줍니다.
Blob REST API에 대한 자세한 내용은 [Azure Blob Service API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)를 참조하세요.

|시나리오  |Blob 상태  |Blob 작업이 허용되지 않음  |
|---------|---------|---------|
|BLOB의 유효 보존 기간이 아직 만료되지 않았고/않았거나 법적 보존이 설정되어 있음     |변경할 수 없음: 삭제 및 쓰기 금지         |컨테이너 삭제, Blob 삭제, Blob1 배치, 블록 배치, 블록 목록 배치, Blob 메타데이터 설정, 페이지 배치, Blob 속성 설정, 스냅숏 Blob, Blob 증분 복사, 블록 추가         |
|BLOB의 유효 보존 기간이 만료됨     |쓰기만 금지(삭제 작업은 허용)         |Blob 배치, 블록 배치, 블록 목록 배치, Blob 메타데이터 설정, 페이지 배치, Blob 속성 설정, 스냅숏 Blob, Blob 증분 복사, 블록 추가         |
|모든 법적 보존을 만들고, 컨테이너에 시간 기반 보존 정책이 설정되지 않음     |변경 가능         |없음         |
|WORM 정책 없음(시간 기반 보존 또는 법적 보존)     |변경 가능         |없음         |

> [!NOTE]
> 첫 번째 Blob 배치와 BLOB을 만드는 데 필요한 블록 목록 배치 및 블록 배치 작업은 위의 표에서 보여드린 처음 두 시나리오에서 허용되고 모든 후속 작업은 허용되지 않습니다.
> 변경할 수 없는 저장소 기능은 GPv2 및 BLOB 저장소 계정에서만 사용할 수만 있으며 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 통해 만들어야 합니다.

## <a name="pricing"></a>가격

이 기능을 사용해도 추가 요금이 발생하지 않으며 변경할 수 없는 데이터는 변경 가능한 일반 데이터와 동일한 방식으로 가격이 책정됩니다. 관련 가격 책정 정보는 [Azure Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

### <a name="restrictions"></a>제한

공개 미리 보기 기간에는 다음과 같은 제한 사항이 적용됩니다.

- **프로덕션 또는 중요 비즈니스용 데이터 저장 금지**
- 모든 미리 보기/NDA 제한 적용

## <a name="getting-started"></a>시작

Azure Blob의 변경할 수 없는 저장소 기능은 대부분의 최신 [Azure Portal](http://portal.azure.com), Azure [CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 및 Azure [PowerShell](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May2018) 릴리스에서 지원됩니다.

### <a name="azure-portal"></a>Azure portal

1. 변경할 수 없는 상태로 유지해야 하는 BLOB을 저장할 새 컨테이너를 만들거나 기존 컨테이너를 선택합니다.
 컨테이너는 GPv2 저장소 계정에 있어야 합니다.
2. 아래 그림과 같이 컨테이너 설정에서 [액세스 정책]을 클릭한 다음, **변경할 수 없는 BLOB 저장소** 정책 아래에서 **+ 정책 추가**를 클릭합니다.

    ![포털](media/storage-blob-immutable-storage/portal-image-1.png)

3. 시간 기반 보존을 사용하려면 드롭다운 메뉴에서 시간 기반 보존을 선택합니다.

    ![보존](media/storage-blob-immutable-storage/portal-image-2.png)

4. 원하는 보존 기간을 일 단위로 선택합니다(최솟값은 1일).

    ![보존 기간](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    위에서 볼 수 있듯이, 정책의 초기 상태는 잠겨 있지 않습니다. 따라서 정책을 잠그기 전에 더 짧은 보존 기간으로 기능을 테스트하고, 정책을 변경할 수 있습니다. SEC 17a-4 등의 규정을 준수하려면 잠금이 필수입니다.

5. ...를 마우스 오른쪽 단추로 클릭하여 정책을 잠그면 다음과 같은 메뉴가 표시됩니다.

    ![정책 잠금](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    [정책 잠금]을 클릭하면 정책 상태가 잠김으로 표시됩니다. 잠긴 정책은 더 이상 삭제할 수 없으며 보존 기간 연장만 허용됩니다.

6. 법적 보존을 사용하려면 [+ 정책 추가]를 클릭하고 드롭다운 메뉴에서 법적 보존을 선택합니다.

    ![법적 보존](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. 하나 이상의 태그를 사용하여 법적 보존 만들기

    ![법적 보존 태그 설정](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

### <a name="cli-20"></a>CLI 2.0

`az extension add -n storage-preview` 명령을 사용하여 [CLI 확장](http://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 설치

이미 확장을 설치한 경우 `az extension update -n storage-preview` 명령을 사용하여 변경할 수 없는 저장소 기능을 사용하도록 설정합니다.

이 기능은 명령 그룹 `az storage container immutability-policy` 및 `az storage container legal-hold`에 포함되어 있습니다(명령을 보려면 그룹에서 "-h" 실행).

### <a name="powershell"></a>PowerShell

변경할 수 없는 저장소 기능은 [PowerShell 버전 4.4.0 미리 보기](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May20180)에서 지원됩니다.
기능을 사용하려면 다음 단계를 수행합니다.

1. `Install-Module PowerShellGet –Repository PSGallery –Force` 명령을 사용하여 최신 버전의 PowerShellGet이 설치되어 있는지 확인합니다.
2. 이전에 설치한 Azure PowerShell 제거
3. AzureRM을 설치(마찬가지로 이 리포지토리에서 Azure 설치 가능) `Install-Module AzureRM –Repository PSGallery –AllowClobber`
4. 저장소 관리 평면 cmdlet의 미리 보기 버전 설치`Install-Module -Name AzureRM.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

아래는 기능 사용법을 보여주는 샘플 PowerShell 코드입니다.

## <a name="client-libraries"></a>클라이언트 라이브러리

Azure Blob의 변경할 수 없는 저장소 기능은 다음 클라이언트 라이브러리 릴리스에서 지원됩니다.

- [.net 클라이언트 라이브러리(버전 7.2.0 미리 보기 이상](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [node.js 클라이언트 라이브러리(버전 4.0.0 이상)](https://www.npmjs.com/package/azure-arm-storage)
- [Python 클라이언트 라이브러리(버전 2.0.0 릴리스 후보 2 이상)](https://pypi.org/project/azure-mgmt-storage/2.0.0rc1/)

## <a name="supported-values"></a>지원되는 값

- 최소 보존 기간은 1일, 최대 보존 기간은 400년
- 특정 저장소 계정에서, 정책이 잠겨서 변경할 수 없는 저장소 계정당 최대 컨테이너 수는 1000개
- 특정 저장소 계정에서, 법적 보존이 설정된 최대 컨테이너 수는 1000개
- 특정 컨테이너에서, 최대 법적 보존 태그 수는 10개
- 법적 보존 태그의 최대 길이는 영숫자 23자, 최소 길이는 문자 3자
- 특정 컨테이너에서, 잠긴 변경할 수 없는 정책에 허용되는 최대 보존 확장 수는 3개
- 정책이 잠겨서 변경할 수 없는 특정 컨테이너에서, 최대 시간 기반 보존 정책 로그 수는 5개이고 컨테이너 보존 기간 동안 보존되는 최대 법적 보존 정책 로그 수는 10개.

## <a name="faq"></a>FAQ

**이 기능이 블록 Blob에만 적용되나요 아니면 페이지 및 추가 Blob에도 적용되나요?**

Blob의 변경할 수 없는 저장소 기능은 모든 종류의 BLOB에 사용할 수 있습니다.  그러나 되도록이면 블록 Blob에만 사용하는 것이 좋습니다. 블록 Blob과는 달리, 페이지 Blob 및 추가 Blob은 WORM 컨테이너 외부에서 만든 후 복사해야 합니다.  WORM 컨테이너에 복사한 후에는 더 이상 추가 Blob에 *추가*하거나 페이지 Blob을 변경할 수 없습니다.

**이 기능을 사용하려면 항상 새 저장소 계정을 만들어야 하나요?**

기존 GPv2 계정 또는 저장소 형식이 GPv2인 경우 새 저장소 계정에서 변경할 수 없는 저장소 기능을 사용할 수 있습니다. 이 기능은 Blob 저장소에만 사용할 수 있습니다.

***잠긴* 시간 기반 보존 정책 또는 법적 보존이 있는 컨테이너를 삭제하려고 시도하면 어떻게 되나요?**

잠긴 시간 기반 보존 정책 또는 법적 보존이 있는 BLOB이 하나 이상 있는 경우 컨테이너 삭제 작업이 실패합니다. BLOB에 활성 보존 기간이 없고 법적 보존이 없는 경우 컨테이너 삭제 작업이 성공합니다. 컨테이너를 삭제하려면 먼저 BLOB을 삭제해야 합니다.

**WORM 컨테이너에 *잠긴* 시간 기반 보존 정책 또는 법적 보존이 있는 저장소 계정을 삭제하려고 시도하면 어떻게 되나요?**

법적 보존 또는 보존 기간이 활성화된 BLOB이 있는 WORM 컨테이너가 하나 이상 있는 경우 저장소 계정 삭제 작업이 실패합니다.  저장소 계정을 삭제하려면 먼저 모든 WORM 컨테이너를 삭제해야 합니다.  컨테이너 삭제에 대한 내용은 질문 2를 참조하세요.

**BLOB이 변경할 수 없는 상태인 경우 BLOB 계층(핫, 쿨, 콜드) 간에 데이터를 이동할 수 있나요?**

예, Blob 계층 설정 명령을 사용하면 데이터를 변경할 수 없는 상태로 유지하면서 BLOB 계층 간에 데이터를 이동할 수 있습니다. 변경할 수 없는 저장소 기능은 핫, 쿨 및 콜드 BLOB 계층에서 지원됩니다.

**보존 기간이 만료되지는 않았지만 요금을 지불하지 않은 경우 어떻게 되나요?**

요금을 지불하지 않으면 Microsoft와 맺은 사용 약관에 지정된 유예 기간 동안 일반 데이터 보존 정책이 적용됩니다.

**기능을 경험해 볼 수 있는 평가판 또는 유예 기간이 제공되나요?**

예, 시간 기반 보존 정책을 처음으로 만들면 정책이 *잠기지 않은* 상태입니다. 이 상태에서는 필요에 따라 보존 기간을 늘리거나 줄일 수 있고 심지어 정책을 삭제할 수도 있습니다. 일단 정책이 잠긴 후에는 삭제를 방지하기 위해 영원히 잠긴 상태를 유지합니다. 또한 정책이 잠기면 더 이상 보존 기간을 줄일 수 있습니다. *잠기지 않은* 상태는 기능 평가 목적으로만 사용하는 것이 좋으며, SEC 17a-4(f) 및 기타 규정을 위반하지 않도록 24시간 내에 정책을 잠글 것을 강력하게 권장합니다.

**국가 및 정부 클라우드에서 이 기능을 사용할 수 있나요?**

변경할 수 없는 저장소 기능은 현재 Azure 공용 지역에서만 사용할 수 있습니다. 특정 국가 클라우드에 대한 내용은 azurestoragefeedback@microsoft.com으로 이메일을 보내주세요.

## <a name="sample-code"></a>샘플 코드

아래는 참조용으로 제공된 샘플 PowerShell 스크립트입니다.
이 스크립트는 새 저장소 계정 및 컨테이너를 만든 후 법적 보존을 설정하고/지우고, 시간 기반 보존 정책(즉, ImmutabilityPolicy)을 만들고/잠그고, 보존 기간을 연장하는 방법을 보여줍니다.

```powershell
\$ResourceGroup = "\<Enter your resource group\>”

\$StorageAccount = "\<Enter your storage account name\>"

\$container = "\<Enter your container name\>"

\$container2 = "\<Enter another container name\>”

\$location = "\<Enter the storage account location\>"

\# Login to the Azure Resource Manager Account

Login-AzureRMAccount

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage"

\# Create your Azure Resource Group

New-AzureRmResourceGroup -Name \$ResourceGroup -Location \$location

\# Create your Azure storage account

New-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup -StorageAccountName
\$StorageAccount -SkuName Standard_LRS -Location \$location -Kind Storage

\# Create a new container

New-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Create Container 2 with Storage Account object

\$accountObject = Get-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

New-AzureRmStorageContainer -StorageAccount \$accountObject -Name \$container2

\# Get container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Get Container with Account object

\$containerObject = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container

\#list container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

\#remove container (Add -Force to dismiss prompt)

Remove-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container2

\#with Account object

Remove-AzureRmStorageContainer -StorageAccount \$accountObject -Name
\$container2

\#with Container object

\$containerObject2 = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container2

Remove-AzureRmStorageContainer -InputObject \$containerObject2

\#Set LegalHold

Add-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag1,tag2

\#with Account object

Add-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3

\#with Container object

Add-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4,tag5

\#Clear LegalHold

Remove-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag2

\#with Account object

Remove-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3,tag5

\#with Container object

Remove-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4

\# create/update ImmutabilityPolicy

\#\# with account/container name

Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 10

\#with Account object

Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -ImmutabilityPeriod 1 -Etag \$policy.Etag

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 7

\#\# with ImmutabilityPolicy object

Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy
-ImmutabilityPeriod 5

\#get ImmutabilityPolicy

Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container

\#with Account object

Get-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container

\#with Container object

Get-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject

\#Lock ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -force

\#\# with account/container name

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -Etag \$policy.Etag

\#with Container object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -Etag \$policy.Etag -force

\#Extend ImmutabilityPolicy

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -ImmutabilityPeriod 11 -ExtendPolicy

\#\# with account/container name

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 11 -Etag \$policy.Etag -ExtendPolicy

\#with Account object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -ImmutabilityPeriod 12 -Etag
\$policy.Etag -ExtendPolicy

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 13 -Etag \$policy.Etag -ExtendPolicy

\#Remove ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

Remove-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy

\#\# with account/container name

Remove-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

Remove-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -Etag \$policy.Etag

\#with Container object

Remove-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject
-Etag \$policy.Etag
```