---
title: Azure Defender for Kubernetes-혜택 및 기능
description: Azure Defender for Kubernetes의 이점 및 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: e72875135243733a6acf03bb3aa6fb9405392d9d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940324"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes 소개

AKS (Azure Kubernetes Service)는 컨테이너 화 된 응용 프로그램 개발, 배포 및 관리를 위한 Microsoft의 관리 되는 서비스입니다.

Azure Security Center 및 AKS는 Kubernetes 보안 제품을 제공 하 고, 아래에 설명 된 대로 환경 강화, 워크 로드 보호 및 런타임 보호를 제공 합니다.

Kubernetes 클러스터에 대 한 위협 검색의 경우 **Kubernetes 용 Azure Defender**를 사용 하도록 설정 합니다.

[서버에 대해 Azure Defender](defender-for-servers-introduction.md)를 사용 하도록 설정한 경우 Linux AKS 노드에 대 한 호스트 수준 위협 감지를 사용할 수 있습니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|결정|**Azure Defender For Kubernetes** 는 [가격 책정 페이지](security-center-pricing.md) 에 표시 된 대로 청구 됩니다.|
|필요한 역할 및 사용 권한:|**보안 관리자** 는 경고를 해제할 수 있습니다.<br>**보안 읽기 권한자**는 발견 사항을 볼 수 있습니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![아니요](./media/icons/no-icon.png) 국가/소 버린 (US Gov, 중국 .Gov, 기타 .Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes의 이점은 무엇 인가요?

### <a name="run-time-protection"></a>런타임 보호

다음 AKS 원본에 대 한 지속적인 분석을 통해 Security Center 컨테이너 화 된 환경에 대 한 실시간 위협 방지 기능을 제공 하 고 호스트 *및* AKS 클러스터 수준에서 검색 된 위협 및 악의적인 활동에 대 한 경고를 생성 합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

Security Center에서 위협 방지를 제공하는 다양한 수준은 다음과 같습니다. 

- **호스트 수준 (서버에 대해 Azure defender에서 제공)** -다른 vm에서 사용 Security Center는 것과 동일한 Log Analytics 에이전트를 사용 하 여, Azure Defender는 Linux AKS 노드를 모니터링 하 여 웹 셸 검색, 알려진 의심 스러운 IP 주소와의 연결 등의 의심 스러운 활동을 모니터링 합니다. 또한 에이전트는 특권 컨테이너 만들기, API 서버에 대 한 의심 스러운 액세스 및 Docker 컨테이너 내에서 실행 되는 SSH (Secure Shell) 서버와 같은 컨테이너 관련 분석을 모니터링 합니다.

    AKS 호스트 수준 경고 목록은 [경고의 참조 테이블](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)을 참조 하세요.

    >[!IMPORTANT]
    > 에이전트를 호스트에 설치하지 않도록 선택하는 경우 위협 방지 혜택 및 보안 경고의 하위 집합만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 받을 수 있습니다.

- **AKS 클러스터 수준 (Kubernetes 용 Azure Defender에서 제공)** -클러스터 수준에서 위협 보호는 Kubernetes의 감사 로그 분석을 기반으로 합니다. 이 **에이전트** 없는 모니터링을 사용 하도록 설정 하려면 Azure Defender를 사용 하도록 설정 합니다. 이 수준에서 경고를 생성하기 위해 Security Center는 AKS에서 검색한 로그를 사용하여 AKS 관리형 서비스를 모니터링합니다. 이 수준의 이벤트 예로 노출된 Kubernetes 대시보드, 높은 권한이 있는 역할 만들기 및 중요한 탑재 만들기가 있습니다.

    AKS 클러스터 수준 경고 목록은 [경고 참조 표](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)를 참조하세요.

    >[!NOTE]
    > Security Center는 구독 설정에서 Kubernetes 옵션을 사용하도록 설정한 후에 발생하는 Azure Kubernetes Service 작업 및 배포에 대한 보안 경고를 생성합니다. 

또한 글로벌 보안 연구 팀에서는 위협 환경을 지속적으로 모니터링합니다. 검색된 컨테이너 관련 경고 및 취약성을 추가합니다.

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)의 지침에 따라 컨테이너 경고를 시뮬레이션할 수 있습니다.



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Security Center의 Kubernetes 보호는 어떻게 작동 하나요?

다음은 Azure Security Center, Azure Kubernetes Service 및 Azure Policy 간의 상호 작용에 대 한 개략적인 다이어그램입니다.

Security Center에서 받아서 분석 한 항목에는 다음이 포함 되어 있습니다.

- API 서버의 감사 로그
- Log Analytics 에이전트의 원시 보안 이벤트
- AKS 클러스터의 클러스터 구성 정보
- Azure Policy의 작업 구성 ( **Kubernetes 용 Azure Policy 추가 기능**을 통해) [Kubernetes 허용 제어를 사용 하 여 워크 로드 보호 모범 사례에 대 한 자세한 정보](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Azure Security Center, Azure Kubernetes Service 및 Azure Policy 간의 상호 작용에 대 한 개략적인 아키텍처" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender for Kubernetes-FAQ

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Log Analytics 에이전트가 없으면 여전히 AKS 보호를 받을 수 있나요?

위에서 언급 한 것 처럼 선택적 **Azure defender For Kubernetes** 계획은 클러스터 수준에서 보호를 제공 하 고, **서버의 azure defender** Log Analytics 에이전트는 노드를 보호 합니다. 

가능 하면 가장 완전 한 보호를 위해 둘 다 배포 하는 것이 좋습니다.

호스트에 에이전트를 설치 하지 않도록 선택 하는 경우 위협 방지 혜택 및 보안 경고의 하위 집합만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 받을 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Kubernetes 용 Azure Defender를 비롯 한 Security Center의 Kubernetes 보호에 대해 알아보았습니다. 

관련 자료는 다음 문서를 참조하세요. 

- [Azure Defender 사용](security-center-pricing.md)
- [Azure 센티널 또는 타사 SIEM으로 경고 내보내기](continuous-export.md)
- [경고 참조 테이블](alerts-reference.md)