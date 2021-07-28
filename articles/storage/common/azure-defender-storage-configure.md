---
title: Azure Defender for Storage 구성
titleSuffix: Azure Storage
description: 계정 활동에서 변칙을 탐지하고 계정에 액세스하는 잠재적으로 유해한 시도에 관한 알림을 받도록 Azure Defender for Storage를 구성합니다.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e2f044ab267365885260b031638572846184bc83
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063188"
---
# <a name="configure-azure-defender-for-storage"></a>Azure Defender for Storage 구성

스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 보호 레이어를 사용하면 보안 전문가가 되거나 보안 모니터링 시스템을 관리하지 않고도 위협을 해결할 수 있습니다.

보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 이 보안 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합되며, 의심스러운 활동에 대한 세부 정보 및 위협을 조사하고 수정하는 방법에 대한 권장 사항을 포함하여 구독 관리자에게 메일로도 전송됩니다.

서비스는 위협 탐지를 위해 Blob Storage 및 Azure Files에 대한 읽기, 쓰기 및 삭제 요청의 리소스 로그를 수집합니다. Azure Defender에서 경고를 조사하기 위해 스토리지 분석 로깅을 사용하여 관련 스토리지 활동을 볼 수 있습니다. 자세한 내용은 [Azure Portal에서 스토리지 계정 모니터링](./manage-storage-analytics-logs.md#configure-logging)에서 **로깅 구성** 을 참조하세요.

## <a name="availability"></a>가용성

스토리지용 Azure Defender는 현재 Blob 스토리지, Azure Files 및 Azure Data Lake Storage Gen2에서 사용할 수 있습니다. Azure Defender를 지원하는 계정 유형으로는 범용 v2, 블록 Blob, Blob 스토리지 계정 등이 있습니다. 스토리지용 Azure Defender는 모든 퍼블릭 클라우드 및 미국 정부 클라우드에서 사용할 수 있으며, 다른 소버린이나 Azure Government 클라우드 지역에서는 사용할 수 없습니다.

Data Lake Storage에 계층 구조 네임스페이스를 사용하도록 설정된 계정은 Azure Blob 스토리지 API와 Data Lake Storage API를 사용하여 트랜잭션을 지원합니다. Azure 파일 공유는 SMB를 통한 트랜잭션을 지원합니다.

30일 평가판을 포함한 자세한 가격 책정 정보는 [Azure Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

다음 목록에서는 스토리지용 Azure Defender 가용성을 요약합니다.

- 릴리스 상태:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/)(일반 공급)
  - [Azure Files](../files/storage-files-introduction.md)(일반 공급)
  - Azure Data Lake Storage Gen2(일반 공급)
- 클라우드:<br>
    ✔ 상용 클라우드<br>
    ✔ US Gov<br>
    ✘ 중국 정보, 기타 정부

## <a name="set-up-azure-defender"></a>Azure Defender 설정

다음 섹션에서 설명하는 여러 가지 방법으로 스토리지용 Azure Defender를 구성할 수 있습니다.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Azure Defender는 Azure Security Center에 기본 제공됩니다. 구독에서 Azure Defender를 사용하도록 설정하면 Azure Storage용 Azure Defender가 모든 스토리지 계정에 대해 자동으로 사용됩니다. 다음과 같이 특정 구독에서 스토리지 계정에 대해 Azure Defender를 사용하거나 사용하지 않도록 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Security Center** 를 시작합니다.
1. 기본 메뉴의 **관리** 에서 **가격 책정 및 설정** 을 선택합니다.
1. Azure Defender를 사용하거나 사용하지 않도록 설정할 구독을 선택합니다.
1. **Azure Defender 켜기** 를 선택하여 구독에 대해 Azure Defender를 사용하도록 설정합니다.
1. **리소스 종류별 Azure Defender 플랜 선택** 에서 **스토리지** 행을 찾고 **플랜** 열에서 **사용** 을 선택합니다.
1. 변경 내용을 저장합니다.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Security Center에서 스토리지용 Azure Defender를 사용하도록 설정하는 방법을 보여 주는 스크린샷":::

이제 이 구독의 모든 스토리지 계정에 Azure Defender가 사용됩니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)을 시작합니다.
1. 스토리지 계정으로 이동합니다. **설정** 아래에서 **고급 보안** 을 선택합니다.
1. **스토리지용 Azure Defender 사용** 을 선택합니다.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Azure Storage 계정에 대해 Azure Defender를 사용하도록 설정하는 방법을 보여 주는 스크린샷":::

이제 이 스토리지 계정에 Azure Defender가 사용됩니다.

### <a name="template"></a>[템플릿](#tab/template)

Azure Resource Manager 템플릿을 사용하여 Azure Defender가 사용하도록 설정된 Azure Storage 계정을 배포합니다. 자세한 내용은 [Advanced Threat Protection이 있는 스토리지 계정](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)을 참조하세요.

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Azure Policy를 사용하여 특정 구독 또는 리소스 그룹에 속한 스토리지 계정에서 Azure Defender를 사용하도록 설정합니다.

1. Azure **정책 - 정의** 페이지를 시작합니다.
1. **스토리지 계정에 Azure Defender 배포** 정책을 검색합니다.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="스토리지 계정에 대해 Azure Defender를 사용하도록 설정하는 정책 적용":::

1. Azure 구독 또는 리소스 그룹을 선택합니다.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="정책 범위에 대한 구독 또는 리소스 그룹 선택":::

1. 정책을 할당합니다.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="스토리지용 Azure Defender를 사용하도록 설정하는 정책 할당":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 스토리지 계정에 대해 Azure Defender를 사용하도록 설정하려면 먼저 [Az.Security](https://www.powershellgallery.com/packages/Az.Security) 모듈을 설치했는지 확인합니다. 그런 다음, [Enable-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection) 명령을 호출합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

PowerShell을 사용하여 스토리지 계정에 대한 Azure Defender 설정을 확인하려면 [Get-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection) 명령을 호출합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정에 대해 Azure Defender를 사용하도록 설정하려면 [az security atp storage update](/cli/azure/security/atp/storage#az_security_atp_storage_update) 명령을 호출합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

Azure CLI를 사용하여 스토리지 계정에 대한 Azure Defender 설정을 확인하려면 [az security atp storage show](/cli/azure/security/atp/storage#az_security_atp_storage_show) 명령을 호출합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>보안 변칙 탐색

스토리지 작업에서 잘못된 부분이 발생하면 의심스러운 보안 이벤트에 대한 정보가 포함된 이메일 알림을 받게 됩니다. 이벤트 세부 정보에는 다음과 같은 내용이 포함됩니다.

- 변칙의 특성
- 스토리지 계정 이름
- 이벤트 시간
- 스토리지 유형
- 가능한 원인
- 조사 단계
- 수정 단계

또한 이메일에는 가능한 원인에 대한 세부 정보와 잠재적 위협을 조사하고 완화하기 위한 권장 조치가 포함됩니다.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="스토리지용 Azure Defender 경고 메일":::

Azure Security Center의 [보안 경고 타일](../../security-center/security-center-managing-and-responding-alerts.md)에서 현재 보안 경고를 검토 및 관리할 수 있습니다. 특정 경고를 클릭하면 현재 위협을 조사하고 향후 위협을 해결하기 위한 추가 세부 정보 및 조치가 제공됩니다.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="스토리지용 Azure Defender 경고":::

## <a name="security-alerts"></a>보안 경고

경고는 평소와 다르고 잠재적 위험이 있는 액세스 시도 또는 스토리지 계정 악용에 의해 생성됩니다. Azure Storage에 대한 경고 목록은 [Azure Storage에 대한 경고](../../security-center/alerts-reference.md#alerts-azurestorage)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Defender for Storage 소개](../../security-center/defender-for-storage-introduction.md)
- [Azure Security Center](../../security-center/security-center-introduction.md)
- [Azure Storage 계정의 로그](/rest/api/storageservices/About-Storage-Analytics-Logging)
