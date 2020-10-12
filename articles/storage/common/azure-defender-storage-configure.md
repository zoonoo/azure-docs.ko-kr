---
title: 저장소에 대 한 Azure Defender 구성
titleSuffix: Azure Storage
description: 계정 활동의 이상 상태를 검색 하 고 잠재적으로 위험한 계정 액세스 시도에 대 한 알림이 표시 되도록 Azure Defender for Storage를 구성 합니다.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: c7e0c9aee1ce6b4a2524ac756673784b63be3b31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289772"
---
# <a name="configure-azure-defender-for-storage"></a>저장소에 대 한 Azure Defender 구성

Storage 용 Azure Defender는 저장소 계정에 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 이 보호 계층을 사용 하면 보안 전문가가 되거나 보안 모니터링 시스템을 관리 하지 않고도 위협을 해결할 수 있습니다.

보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 이러한 보안 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합 되며, 의심 스러운 활동 및 위협 조사 및 해결 방법에 대 한 권장 사항을 포함 하 여 구독 관리자에 게 전자 메일을 통해 전송 됩니다.

Service 수집는 Blob 저장소에 대 한 읽기, 쓰기 및 삭제 요청을 기록 하 고 위협 검색을 Azure Files 합니다. Azure Defender에서 경고를 조사 하려면 스토리지 분석 로깅을 사용 하 여 관련 저장소 작업을 볼 수 있습니다. 자세한 내용은 [Azure Portal에서 저장소 계정 모니터링](storage-monitor-storage-account.md#configure-logging)의 **로깅 구성** 을 참조 하세요.

## <a name="availability"></a>가용성

Storage 용 Azure Defender는 현재 Blob storage, Azure Files 및 Azure Data Lake Storage Gen2에서 사용할 수 있습니다. Azure Defender를 지 원하는 계정 유형은 범용 v2, 블록 blob 및 Blob storage 계정을 포함 합니다. Storage 용 Azure Defender는 모든 공용 클라우드 및 미국 정부 클라우드에서 사용할 수 있지만 다른 소 버린 또는 Azure Government 클라우드 지역에서는 사용할 수 없습니다.

Data Lake Storage에 대해 계층적 네임 스페이스를 사용 하도록 설정 된 계정은 Azure Blob 저장소 Api와 Data Lake Storage Api를 모두 사용 하 여 트랜잭션을 지원 합니다. Azure 파일 공유는 SMB를 통한 트랜잭션을 지원 합니다.

무료 30 일 평가판을 비롯 한 가격 정보는 [Azure Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조 하세요.

다음 목록에는 저장소에 대 한 Azure Defender의 가용성이 요약 되어 있습니다.

- 릴리스 상태:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (일반 공급)
  - [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (일반 공급)
  - Azure Data Lake Storage Gen2 (일반 공급)
- 클라우드:<br>
    ✔ 상용 클라우드<br>
    ✔ US Gov<br>
    ✘ 중국 .Gov, 기타 .Gov

## <a name="set-up-azure-defender"></a>Azure Defender 설정

다음 섹션에서 설명 하는 여러 가지 방법으로 저장소에 대해 Azure Defender를 구성할 수 있습니다.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Azure Security Center 표준 계층을 구독할 때 Azure Defender는 모든 저장소 계정에 자동으로 설정 됩니다. 다음과 같이 특정 구독에서 저장소 계정에 대해 Azure Defender를 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Security Center** 를 시작 합니다.
1. 주 메뉴의 **관리**아래에서 **가격 책정 & 설정**을 선택 합니다.
1. Azure Defender를 사용 하거나 사용 하지 않도록 설정할 구독을 선택 합니다.
1. Azure defender **on** 을 선택 하 여 구독에 대해 azure defender를 사용 하도록 설정 합니다.
1. **리소스 유형별 Azure Defender 계획 선택**에서 **저장소** 행을 찾고 **계획** 열에서 **사용** 을 선택 합니다.
1. 변경 내용을 저장합니다.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Security Center에서 저장소에 Azure Defender를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

Azure Defender는 이제이 구독의 모든 저장소 계정에 대해 사용 하도록 설정 됩니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)를 시작 합니다.
1. 스토리지 계정으로 이동합니다. **설정**아래에서 **고급 보안**을 선택 합니다.
1. **저장소에 Azure Defender 사용**을 선택 합니다.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Security Center에서 저장소에 Azure Defender를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

이제이 저장소 계정에 대해 Azure Defender를 사용할 수 있습니다.

### <a name="template"></a>[템플릿](#tab/template)

Azure Resource Manager 템플릿을 사용 하 여 Azure Defender를 사용 하도록 설정 된 Azure Storage 계정을 배포 합니다. 자세한 내용은 [advanced threat protection을 사용 하는 저장소 계정](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)을 참조 하세요.

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Azure Policy를 사용 하 여 특정 구독 또는 리소스 그룹의 저장소 계정에서 Azure Defender를 사용 하도록 설정할 수 있습니다.

1. Azure **정책 정의** 페이지를 시작 합니다.
1. **저장소 계정에 Azure Defender 배포** 정책을 검색 합니다.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Security Center에서 저장소에 Azure Defender를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

1. Azure 구독 또는 리소스 그룹을 선택 합니다.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Security Center에서 저장소에 Azure Defender를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

1. 정책을 할당 합니다.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Security Center에서 저장소에 Azure Defender를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Rest API 명령을 사용 하 여 특정 저장소 계정에 대 한 Azure Defender 설정을 만들거나 업데이트 하거나 가져옵니다.

- [Advanced threat protection-만들기](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
- [Advanced threat protection-Get](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell cmdlet을 사용합니다.

- [Advanced threat protection 사용](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Advanced threat protection 가져오기](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [Advanced threat protection 사용 안 함](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>보안 비정상 탐색

스토리지 작업에서 잘못된 부분이 발생하면 의심스러운 보안 이벤트에 대한 정보가 포함된 이메일 알림을 받게 됩니다. 이벤트 세부 정보에는 다음과 같은 내용이 포함됩니다.

- 변칙의 특성
- 스토리지 계정 이름
- 이벤트 시간
- 저장소 유형
- 가능한 원인
- 조사 단계
- 수정 단계

또한 이메일에는 가능한 원인에 대한 세부 정보와 잠재적 위협을 조사하고 완화하기 위한 권장 조치가 포함됩니다.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Security Center에서 저장소에 Azure Defender를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

Azure Security Center의 [보안 경고 타일](../../security-center/security-center-managing-and-responding-alerts.md)에서 현재 보안 경고를 검토 하 고 관리할 수 있습니다. 특정 경고를 클릭하면 현재 위협을 조사하고 향후 위협을 해결하기 위한 추가 세부 정보 및 조치가 제공됩니다.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Security Center에서 저장소에 Azure Defender를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

## <a name="security-alerts"></a>보안 경고

경고는 평소와 다르고 잠재적 위험이 있는 액세스 시도 또는 스토리지 계정 악용에 의해 생성됩니다. Azure Storage에 대 한 경고 목록은 [Azure Storage에 대 한 경고](../../security-center/alerts-reference.md#alerts-azurestorage)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 계정의 로그](/rest/api/storageservices/About-Storage-Analytics-Logging) 에 대해 자세히 알아보기
- [Azure Security Center](../../security-center/security-center-intro.md)에 대한 자세한 정보
