---
title: 고급 위협 보호 구성
titleSuffix: Azure Storage
description: Azure Storage에 대한 고급 위협 보호를 구성하여 계정 활동의 이상을 감지하고 계정에 액세스하려는 잠재적으로 유해한 시도를 알림을 받게 됩니다.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 27860b8761c565c45a604253efdff5f77606606e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061310"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Azure 저장소에 대한 고급 위협 보호 구성

Azure Storage에 대한 고급 위협 보호는 저장소 계정에 액세스하거나 악용하려는 이상하고 잠재적으로 유해한 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 보호 계층을 사용하면 보안 전문가가 되거나 보안 모니터링 시스템을 관리하지 않고도 위협을 해결할 수 있습니다.

보안 경고는 활동의 이상 징후가 발생할 때 트리거됩니다. 이러한 보안 경고는 [Azure Security Center와](https://azure.microsoft.com/services/security-center/)통합되며 의심스러운 활동에 대한 세부 정보와 위협을 조사하고 해결하는 방법에 대한 권장 사항과 함께 구독 관리자에게 전자 메일을 통해 전송됩니다.

> [!NOTE]
> Azure 저장소에 대한 고급 위협 보호는 현재 Blob 저장소에서만 사용할 수 있습니다. Azure 정부 및 주권 클라우드 지역에서는 사용할 수 없습니다. 무료 30일 평가판을 포함한 가격 세부 정보는 [Azure 보안 센터 가격 페이지를]( https://azure.microsoft.com/pricing/details/security-center/)참조하십시오.

Azure Storage에 대한 고급 위협 보호는 위협 탐지를 위해 Blob 저장소에 대한 읽기, 쓰기 및 삭제 요청의 진단 로그를 인제합니다. 고급 위협 보호에서 경고를 조사하려면 저장소 분석 로깅을 사용하여 관련 저장소 활동을 볼 수 있습니다. 자세한 내용은 [Azure 포털의 저장소 계정 모니터링에서](storage-monitor-storage-account.md#configure-logging) **로깅 구성을** 참조하십시오.

## <a name="set-up-advanced-threat-protection"></a>고급 위협 보호 설정

다음 섹션에 설명된 여러 가지 방법으로 고급 위협 보호를 구성할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure [포털을](https://portal.azure.com/)시작합니다.
1. Azure 저장소 계정으로 이동합니다. **설정에서** **고급 보안을**선택합니다.
1. 고급 보안 구성 페이지에서 **설정** 링크를 선택합니다.
1. **고급 보안을** **ON으로**설정합니다.
1. 새 또는 업데이트된 정책을 저장하려면 **저장을 클릭합니다.**

    ![Azure Storage Advanced Threat Protection 켜기](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure 보안 센터](#tab/azure-security-center)

Azure 보안 센터의 표준 계층을 구독하면 모든 저장소 계정에 고급 위협 보호가 자동으로 설정됩니다. 다음과 같이 특정 구독에서 저장소 계정에 대한 고급 위협 보호를 사용하거나 사용하지 않도록 설정할 수 있습니다.

1. [Azure 포털에서](https://portal.azure.com) **Azure 보안 센터를** 시작합니다.
1. 기본 메뉴에서 **가격 & 설정을**클릭합니다.
1. 저장소 계정에 대한 위협 보호를 사용하거나 사용하지 않으려는 구독을 클릭합니다.

    ![구독 선택](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. **가격 책정 계층을 클릭합니다.**
1. 리소스 **유형별 가격 선택 계층에서** **저장소 계정** 행에서 **사용 또는** 사용 **안 함**을 클릭합니다.

    ![보안 센터에서 ATP 사용](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. **저장**을 클릭합니다.

### <a name="template"></a>[템플릿](#tab/template)

Azure 리소스 관리자 템플릿을 사용하여 고급 위협 보호가 활성화된 Azure Storage 계정을 배포합니다. 자세한 내용은 [고급 위협 보호 기능이 있는 저장소 계정을](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)참조하십시오.

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Azure 정책을 사용하여 특정 구독 또는 리소스 그룹의 저장소 계정 간에 고급 위협 보호를 사용하도록 설정합니다.

1. Azure **정책 - 정의** 페이지를 시작합니다.

1. **저장소 계정에서 고급 위협 보호 배포** 정책을 검색합니다.

     ![정책 검색](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Azure 구독 또는 리소스 그룹을 선택합니다.

    ![구독 또는 그룹 선택](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. 정책을 할당합니다.

    ![정책 정의 페이지](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[나머지 API](#tab/rest-api)

Rest API 명령을 사용하여 특정 저장소 계정에 대한 고급 위협 보호 설정을 만들거나 업데이트하거나 가져옵니다.

* [고급 위협 보호 - 만들기](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [고급 위협 보호 - 받기](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

다음 PowerShell cmdlet을 사용합니다.

* [고급 위협 보호 지원](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [고급 위협 보호](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [고급 위협 보호 비활성화](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>보안 이상 살펴보기

스토리지 작업에서 잘못된 부분이 발생하면 의심스러운 보안 이벤트에 대한 정보가 포함된 이메일 알림을 받게 됩니다. 이벤트 세부 정보에는 다음과 같은 내용이 포함됩니다.

* 변칙의 본질
* 스토리지 계정 이름입니다.
* 이벤트 시간
* 수납 타입
* 잠재적인 원인
* 조사 단계
* 수정 단계

또한 이메일에는 가능한 원인에 대한 세부 정보와 잠재적 위협을 조사하고 완화하기 위한 권장 조치가 포함됩니다.

![Azure Storage Advanced Threat Protection 경고 이메일](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Azure 보안 센터의 보안 경고 타일에서 현재 보안 경고를 검토하고 관리할 수 [있습니다.](../../security-center/security-center-managing-and-responding-alerts.md) 특정 경고를 클릭하면 현재 위협을 조사하고 향후 위협을 해결하기 위한 추가 세부 정보 및 조치가 제공됩니다.

![Azure Storage Advanced Threat Protection 경고 이메일](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>보안 경고

경고는 평소와 다르고 잠재적 위험이 있는 액세스 시도 또는 스토리지 계정 악용에 의해 생성됩니다. Azure 저장소에 대한 경고 목록은 Azure 보안 [센터의 데이터 서비스에 대한 위협 보호의](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage) **저장소** 섹션을 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure 저장소 계정의 로그에](/rest/api/storageservices/About-Storage-Analytics-Logging) 대해 자세히 알아보기
* [Azure 보안 센터에](../../security-center/security-center-intro.md) 대해 자세히 알아보기
