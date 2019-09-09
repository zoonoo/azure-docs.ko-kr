---
title: Azure Security Center에서 보안 인시던트 관리 Microsoft Docs
description: 이 문서에서는 Azure Security Center를 사용 하 여 보안 인시던트를 관리 하는 방법을 안내 합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e8feb669-8f30-49eb-ba38-046edf3f9656
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/27/2019
ms.author: rkarlin
ms.openlocfilehash: 9f757d5fb2c8d3a807e325068d386e16a7d6caa7
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806563"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Azure Security Center에서 보안 인시던트 관리

보안 경고를 심사 하 고 조사 하는 것이 가장 숙련 된 보안 분석가의 경우에도 많은 시간이 소요 될 수 있으며, 대부분의 경우 시작할 위치를 알아야 합니다. 보안 센터에서는 고유한 [보안 경고](security-center-managing-and-responding-alerts.md) 간에 정보를 연결하기 위해 [분석](security-center-detection-capabilities.md)을 사용하여 공격 캠페인의 단일 보기 및 모든 관련된 경고를 제공할 수 있습니다. 공격자가 사용한 작업 및 영향을 받는 리소스를 신속하게 이해할 수 있습니다.

이 항목에서는 Security Center의 인시던트에 대해 설명 하 고 해당 경고를 수정 하는 방법을 설명 합니다.

## <a name="what-is-a-security-incident"></a>보안 인시던트란?

보안 센터에서 보안 인시던트는 [kill 체인](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) 패턴과 일치하는 리소스에 대한 모든 경고의 집계입니다. [보안 경고](security-center-managing-and-responding-alerts.md) 목록에 인시던트가 표시 됩니다. 각 항목에 대 한 자세한 정보를 얻을 수 있는 관련 경고를 보려면 인시던트로 마이그레이션를 클릭 합니다.

## <a name="managing-security-incidents"></a>보안 인시던트 관리

1. Security Center 대시보드에서 **보안 경고** 타일을 클릭 합니다. 인시던트 및 경고가 나열 됩니다. 보안 인시던트 설명에 다른 경고와 다른 아이콘이 지정되어 있습니다.

    ![보안 인시던트 보기](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 세부 정보를 보려면 인시던트를 클릭 합니다. **보안 인시던트가 검색 됨** 블레이드에서 자세한 정보를 표시 합니다. **일반 정보** 섹션은 보안 경고를 트리거한 항목에 대 한 통찰력을 제공할 수 있습니다. 대상 리소스, 원본 IP 주소 (해당 하는 경우), 경고가 아직 활성 상태인 경우, 수정 하는 방법에 대 한 권장 사항 등의 정보가 표시 됩니다.  

    ![Azure Security Center의 보안 인시던트에 대응](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. 각 경고에 대 한 자세한 내용을 보려면 경고를 클릭 합니다. Security Center에서 제안하는 수정은 보안 경고에 따라 다릅니다.

   > [!NOTE]
   > 동일한 경고가 인시던트의 일부로 있을 수 있을 뿐만 아니라 독립 실행형 경고로 표시 될 수도 있습니다.

    ![경고 정보](./media/security-center-incident/security-center-incident-alert.png)

1. 각 경고에 대해 제공 된 수정 단계를 따릅니다.

경고에 대 한 자세한 내용은 [보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)을.

다음 항목에서는 리소스 유형에 따라 다양 한 경고를 안내 합니다.

* [IaaS Vm 및 서버 경고](security-center-alerts-iaas.md)
* [기본 계산 경고](security-center-alerts-compute.md)
* [데이터 서비스 경고](security-center-alerts-data-services.md)

다음 항목에서는 azure에 배포 된 리소스에 대 한 추가 보호 계층을 적용 하기 위해 Security Center Azure 인프라와 통합 하 여 수집 하는 다양 한 원격 분석을 사용 하는 방법에 대해 설명 합니다.

* [서비스 계층 경고](security-center-alerts-service-layer.md)
* [Azure 보안 제품과 통합](security-center-alerts-integration.md)

## <a name="see-also"></a>참고자료
이 문서에서는 보안 센터에서 보안 인시던트 기능을 사용하는 방법을 살펴보았습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center의 보안 경고](security-center-alerts-overview.md)
* [보안 경고 관리](security-center-managing-and-responding-alerts.md)
* [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/)--Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
