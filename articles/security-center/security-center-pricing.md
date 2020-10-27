---
title: Azure Security Center 가격 책정
description: Azure Security Center는 Azure Defender를 사용하거나 사용하지 않는 두 가지 모드로 제공됩니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 01d7d29e9fffff3be2c98c0ec52c01774439e3f7
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341191"
---
# <a name="pricing-of-azure-security-center"></a>Azure Security Center 가격 책정
Azure Security Center는 Azure, 온-프레미스 및 기타 클라우드용으로 통합 보안 관리 및 고급 위협 보호 기능을 제공합니다. 또한 하이브리드 클라우드 작업을 확인하고 제어하는 기능, 위협에 대한 노출을 줄이는 적극적인 방어 기능, 그리고 빠르게 발전하는 사이버 공격에 대응할 수 있는 지능형 검색 기능을 제공합니다.


## <a name="free-option-vs-azure-defender-enabled"></a>무료 옵션과 Azure Defender 사용 모드 비교

Security Center는 두 모드로 제공됩니다.

- **Azure Defender 꺼짐** (무료) - Azure Portal의 Azure Security Center 대시보드를 처음 방문하거나 API를 통해 프로그래밍 방식으로 사용하도록 설정한 경우 모든 Azure 구독에서 Azure Defender가 없는 Security Center를 무료로 사용할 수 있습니다. 이 무료 모드에서는 Azure 리소스를 보호할 수 있도록 보안 정책, 지속적인 보안 평가 및 실행 가능한 보안 권장 사항을 제공합니다.

- **Azure Defender 켜짐** - Azure Defender를 사용하도록 설정하면 무료 모드의 기능이 프라이빗 클라우드 및 기타 퍼블릭 클라우드에서 실행되는 작업으로 확장 적용되며, 하이브리드 클라우드 작업 전반에 걸쳐 통합 보안 관리 및 위협 방지 기능이 제공됩니다. Azure Defender의 주요 기능 중 일부는 다음과 같습니다.

    - **엔드포인트용 Microsoft Defender** - 서버용 Azure Defender에는 포괄적인 EDR(엔드포인트 검색 및 응답)을 위한 [엔드포인트용 Microsoft Defender](https://www.microsoft.com/microsoft-365/security/endpoint-defender)가 포함되어 있습니다. [Security Center의 통합 EDR 솔루션 사용](security-center-wdatp.md)에서 Azure Defender와 함께 엔드포인트용 Azure Defender를 사용하는 이점에 대해 자세히 알아봅니다.
    - **가상 머신 및 컨테이너 레지스트리의 취약성 검사** - 업계에서 가장 진보적인 취약성 관리 솔루션을 제공하는 검사기를 모든 가상 머신에 손쉽게 배포할 수 있습니다. Security Center 내에서 검사 결과를 직접 확인, 조사 및 수정하세요. 
    - **하이브리드 보안** – 모든 온-프레미스 및 클라우드 작업에 걸쳐 보안을 통합 확인할 수 있습니다. 또한 보안 정책을 적용하고 하이브리드 클라우드 작업의 보안을 지속적으로 평가하여 보안 표준을 준수할 수 있습니다. 뿐만 아니라 방화벽 및 기타 파트너 솔루션을 포함한 여러 소스에서 보안 데이터를 수집, 검색 및 분석할 수 있습니다.
    - **위협 방지 경고** - 고급 동작 분석 및 Microsoft Intelligent Security Graph가 갈수록 발전하는 사이버 공격에 효율적으로 대응합니다. 기본 제공 행동 분석 및 Machine Learning을 활용하여 공격 및 제로 데이 익스플로잇을 식별할 수 있습니다. 또한 네트워크, 컴퓨터 및 클라우스 서비스에서 들어오는 공격 및 위반 후 활동을 모니터링할 수 있습니다. 대화형 도구 및 상황에 맞는 위협 인텔리전스를 사용하면 조사를 손쉽게 수행할 수 있습니다.
    - **액세스 및 애플리케이션 컨트롤** - 특정 워크로드에 맞게 허용 및 거부 목록을 생성하는 기계 학습 지원 권장 사항을 적용하여 맬웨어 및 기타 원치 않는 애플리케이션을 차단할 수 있습니다. Azure VM의 관리 포트에 대한 액세스를 JIT(Just-In-Time) 방식으로 제어하여 네트워크 공격 표면을 줄입니다. 따라서 무차별 암호 대입 및 기타 네트워크 공격에 대한 노출을 크게 줄일 수 있습니다.
    - **컨테이너 보안 기능** - 컨테이너화된 환경에서 취약성 관리 및 실시간 위협 방지를 활용할 수 있습니다. **컨테이너 레지스트리에 대한 Azure Defender** 를 사용하도록 설정하면 모든 기능이 사용하도록 설정될 때까지 최대 12시간이 걸릴 수 있습니다. 요금은 연결된 레지스트리에 푸시된 고유한 컨테이너 이미지 수를 기반으로 청구됩니다. 한 번 검사한 이미지는 수정하여 한 번 더 푸시하지 않는 한 요금이 다시 청구되지 않습니다. 

## <a name="try-azure-defender-free-for-30-days"></a>30일 동안 무료로 Azure Defender 사용해 보기

Azure Defender는 처음 30일 동안 무료입니다. 30일 종료 시 서비스를 계속 사용하기로 선택하는 경우 사용량에 대한 요금이 자동으로 부과되기 시작합니다.

## <a name="enable-azure-defender"></a>Azure Defender 사용

Azure Defender를 사용하여 전체 Azure 구독을 보호할 수 있으며, 보호는 구독 내의 모든 리소스에 상속됩니다.

Azure Defender를 사용하도록 설정하려면 다음을 수행합니다.

1. Security Center의 주 메뉴에서 **가격 책정 및 설정** 을 선택합니다.
1. 업그레이드할 구독을 선택합니다.
1. **Azure Defender 켜기** 를 선택하여 업그레이드합니다.
1. **저장** 을 선택합니다.

다음은 구독 예제에 대한 가격 책정 페이지입니다. Azure Defender의 각 요금제는 별도로 가격이 책정되며 개별적으로 설정 또는 해제할 수 있습니다.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="포털의 Security Center 가격 책정 페이지":::

> [!NOTE]
> 위협 방지 기능을 포함한 모든 Security Center 기능을 사용하도록 설정하려면 해당 워크로드가 포함된 구독에서 Azure Defender를 사용하도록 설정해야 합니다. 작업 영역 수준에서 사용하도록 설정해도 Just-In-Time VM 액세스, 적응형 애플리케이션 제어 및 Azure 리소스에 대한 네트워크 검색 기능은 사용하도록 설정되지 않습니다. 또한 작업 영역 수준에서 사용할 수 있는 유일한 Azure Defender 플랜은 서버용 Azure Defender와 머신의 SQL Server용 Azure Defender입니다.
>
> **Azure Defender for Storage 계정** 은 구독 수준 또는 리소스 수준에서 사용하도록 설정할 수 있습니다.
> **Azure Defender for SQL** 은 구독 수준 또는 리소스 수준에서 사용하도록 설정할 수 있습니다.
> **Azure Database for MariaDB/ MySQL/ PostgreSQL** 에 대한 위협 방지는 리소스 수준에서만 사용하도록 설정할 수 있습니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center의 가격 책정 방식에 대해 알아보았습니다. 관련 자료는 다음을 참조하세요.

- [Azure 워크로드 비용을 최적화하는 방법](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [선택한 통화 및 해당 지역에 맞는 가격 세부 정보](https://azure.microsoft.com/pricing/details/security-center/)
- 특정 에이전트 집합으로 제한하여 비용을 관리하고 솔루션에 대해 수집되는 데이터 양을 제한할 수 있습니다. [솔루션 대상](../azure-monitor/insights/solution-targeting.md)을 사용하면 솔루션에 범위를 적용하고 작업 영역의 컴퓨터 하위 집합을 대상으로 지정할 수 있습니다. 솔루션 대상을 사용하는 경우 Security Center에는 작업 영역에 솔루션이 없는 것으로 표시됩니다.