---
title: Azure Defender 개요 및 사용 가능한 계획
description: Azure Defender의 계획, 보호 및 경고에 대해 알아봅니다. 그런 다음 구독에서 Azure Defender를 사용 하도록 설정 합니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bb1c1e6443b5087b48aad7c3171bef557707adb1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977292"
---
# <a name="introduction-to-azure-defender"></a>Azure Defender 소개

Azure Security Center의 기능은 두 가지 광범위 한 클라우드 보안 핵심 요소을 포함 합니다.

- **클라우드 보안 상태 관리 (CSPM)**
- **CWP (클라우드 워크 로드 보호)**

보안 점수와 같은 Security Center의 CSPM 기능, Windows 및 Linux Azure 컴퓨터에서 보안 구성 요소 검색은 모든 Azure 사용자가 사용할 수 있는 무료 Security Center 환경의 일부입니다. 이러한 CSPM 기능을 사용 하 여 환경을 강화 하 고 규정 준수를 보장 하세요.

**Azure Defender** 는 azure 및 하이브리드 워크 로드에 대 한 고급 지능형 보호를 위해 Security Center 내에 통합 된 cwpp (클라우드 워크 로드 보호 플랫폼)입니다.

Azure Security Center의 Azure Defender 대시보드는 다음과 같습니다.

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Azure Defender 대시보드의 예" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Azure Defender에서 보호할 수 있는 리소스 종류는 무엇 인가요?

Azure Defender는 가상 머신, SQL 데이터베이스, 컨테이너, 웹 응용 프로그램, 네트워크 등에 대 한 보안 경고 및 고급 위협 방지 기능을 제공 합니다.

Azure Security Center의 **가격 책정 및 설정** 영역에서 Azure defender를 사용 하도록 설정 하면 다음 Defender 요금제를 모두 동시에 사용할 수 있으며 사용자 환경의 계산, 데이터 및 서비스 계층에 대 한 포괄적인 방어 기능을 제공 합니다.

- [서버용 Azure Defender](defender-for-servers-introduction.md)
- [App Service용 Azure Defender](defender-for-app-service-introduction.md)
- [스토리지용 Azure Defender](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [IoT용 Azure Defender](defender-for-iot-introduction.md)
- [Kubernetes용 Azure Defender](defender-for-kubernetes-introduction.md)
- [컨테이너 레지스트리용 Azure Defender](defender-for-container-registries-introduction.md)
- [Key Vault용 Azure Defender](defender-for-key-vault-introduction.md)

이러한 각 계획은 Security Center 설명서에 별도로 설명 되어 있습니다.


## <a name="hybrid-cloud-protection"></a>하이브리드 클라우드 보호

Azure 환경을 방어할 뿐만 아니라 하이브리드 클라우드 환경에 Azure Defender 기능을 추가할 수 있습니다.

- 비 Azure 서버 보호
- 다른 클라우드에서 가상 머신 보호 (예: AWS 및 GCP)
- IoT 장치 보호

가장 중요 한 문제에 집중할 수 있도록 특정 환경에 따라 사용자 지정 된 위협 인텔리전스 및 우선 순위가 지정 된 경고를 얻을 수 있습니다.

[Azure Arc](https://azure.microsoft.com/services/azure-arc/) 를 배포 하 고 azure Defender를 사용 하도록 설정 하 여 온-프레미스 및 다중 클라우드 가상 머신과 SQL database에 대 한 보호를 확장 합니다. 서버에 대 한 azure Arc는 무료 서비스 이지만 Arc 사용 서버 (예: Azure Defender)에서 사용 되는 서비스는 해당 서비스에 대 한 가격 책정에 따라 요금이 청구 됩니다.

[Azure Arc에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/azure-arc/overview).


## <a name="azure-defender-alerts"></a>Azure Defender 경고 

Azure Defender는 사용자 환경의 모든 영역에서 위협을 감지 하면 경고를 생성 합니다. 이러한 경고는 영향을 받는 리소스의 세부 정보, 제안된 수정 단계 및 경우에 따라 응답으로 논리 앱을 트리거하는 옵션을 설명합니다.

경고가 Security Center에 의해 생성 되거나 통합 보안 제품의 Security Center에서 수신 되었는지 여부에 관계 없이 내보낼 수 있습니다. 경고를 Azure Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM으로 경고 내보내기](continuous-export.md)의 지침을 따릅니다.

> [!NOTE]
> 서로 다른 원본의 경고가 표시되는 데 각각 다른 시간이 걸릴 수 있습니다. 예를 들어 네트워크 트래픽을 분석해야 하는 경고를 표시하는 경우 가상 머신에서 실행되는 의심스러운 프로세스와 관련된 경고보다 더 오래 걸릴 수 있습니다.


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender 고급 보호 기능

Azure Defender는 리소스와 관련 된 맞춤형 권장 사항에 대해 고급 분석을 사용 합니다. 

보호에는 just-in-time 액세스를 사용 하 여 Vm의 관리 포트를 보호 하 고 컴퓨터에서 실행 하지 않아야 하는 앱에 대 한 허용 목록을 만들기 위한 적응 응용 프로그램 컨트롤을 포함 합니다. 

Azure Defender 대시보드의 고급 보호 타일을 사용 하 여 이러한 각 보호를 모니터링 하 고 구성할 수 있습니다. 

## <a name="vulnerability-assessment-and-management"></a>취약성 평가 및 관리

Azure Defender는 추가 비용 없이 가상 머신과 컨테이너 레지스트리에 대 한 취약성 검색을 포함 합니다. 스캐너는 Qualys에서 구동 되지만 Qualys 라이선스 또는 Qualys 계정이 필요 하지 않습니다. 모든 항목이 Security Center 내에서 원활 하 게 처리 됩니다. 

이러한 취약성 스캐너의 결과를 검토 하 고 Security Center 내에서 모두에 응답 합니다. 이렇게 하면 모든 클라우드 보안 작업에 대 한 단일 창에 Security Center 더 가까이 배치 됩니다.

다음 페이지에서 자세히 알아보세요.

- [Azure virtual machines에 대 한 Security Center의 통합 취약성 평가 솔루션](deploy-vulnerability-assessment-vm.md)
- [Azure 컨테이너 레지스트리에서 이미지의 취약성 식별](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender의 이점에 대해 알아보았습니다. 

> [!div class="nextstepaction"]
> [Azure Defender 사용](security-center-pricing.md)