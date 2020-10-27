---
title: Azure Defender for Kubernetes - 이점 및 특징
description: Azure Defender for Kubernetes의 이점 및 특징에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: dd92e1529b889671bc29939f7e9611eceac7ee20
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370511"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes 소개

AKS(Azure Kubernetes Service)는 컨테이너화된 애플리케이션 개발, 배포 및 관리를 위한 Microsoft의 관리되는 서비스입니다.

Azure Security Center 및 AKS는 최고의 클라우드 네이티브 Kubernetes 보안 제품을 구성하고, 아래에 설명된 대로 환경 강화, 워크로드 보호 및 런타임 보호를 지원합니다.

Kubernetes 클러스터에 대한 위협 탐지를 사용하려면 **Azure Defender for Kubernetes** 를 사용하도록 설정하세요.

[서버용 Azure Defender](defender-for-servers-introduction.md)를 사용하도록 설정 하면 Linux AKS 노드에 대한 호스트 수준 위협 탐지를 사용할 수 있습니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**Azure Defender for Kubernetes** 는 [가격 책정 페이지](security-center-pricing.md)에 표시된 대로 요금이 청구됩니다.|
|필요한 역할 및 권한:|**보안 관리자** 는 경고를 해제할 수 있습니다.<br>**보안 읽기 권한자** 는 발견 사항을 볼 수 있습니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![아니요](./media/icons/no-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes의 이점은?

### <a name="run-time-protection"></a>실시간 보호

Security Center는 다음 AKS 원본에 대한 지속적인 분석을 통해 컨테이너화된 환경에 대한 실시간 위협 방지 기능을 제공하고, 호스트 *및* AKS 클러스터 수준에서 검색된 위협 및 악의적인 활동에 대한 경고를 생성합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

Security Center에서 위협 방지를 제공하는 다양한 수준은 다음과 같습니다. 

- **호스트 수준(서버용 Azure Defender에서 제공)** - Azure Defender는 Security Center가 다른 VM에서 사용하는 것과 동일한 Log Analytics 에이전트를 사용하여 Linux AKS 노드에서 웹 셸 검색 및 알려진 의심스러운 IP 주소와의 연결과 같은 의심스러운 활동을 모니터링합니다. 이 에이전트는 또한 권한 있는 컨테이너 만들기, API 서버에 대한 의심스러운 액세스 및 Docker 컨테이너 내에서 실행되는 SSH(Secure Shell) 서버와 같은 컨테이너 관련 분석을 모니터링합니다.

    AKS 호스트 수준 경고 목록은 [경고 참조 테이블](alerts-reference.md#alerts-containerhost)을 참조하세요.

    >[!IMPORTANT]
    > 에이전트를 호스트에 설치하지 않도록 선택하는 경우 위협 방지 혜택 및 보안 경고의 하위 집합만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 받을 수 있습니다.

- **AKS 클러스터 수준(Azure Defender for Kubernetes에서 제공)** - 클러스터 수준의 위협 보호는 Kubernetes의 감사 로그 분석을 기반으로 합니다. 이 **에이전트 없는** 모니터링을 사용하도록 설정하려면 Azure Defender를 사용하도록 설정합니다. 이 수준에서 경고를 생성하기 위해 Security Center는 AKS에서 검색한 로그를 사용하여 AKS 관리형 서비스를 모니터링합니다. 이 수준의 이벤트 예로 노출된 Kubernetes 대시보드, 높은 권한이 있는 역할 만들기 및 중요한 탑재 만들기가 있습니다.

    AKS 클러스터 수준 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-akscluster)를 참조하세요.

    >[!NOTE]
    > Security Center는 구독 설정에서 Kubernetes 옵션을 사용하도록 설정한 후에 발생하는 Azure Kubernetes Service 작업 및 배포에 대한 보안 경고를 생성합니다. 

또한 글로벌 보안 연구 팀에서는 위협 환경을 지속적으로 모니터링합니다. 검색된 컨테이너 관련 경고 및 취약성을 추가합니다.

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)의 지침에 따라 컨테이너 경고를 시뮬레이션할 수 있습니다.



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Security Center의 Kubernetes 보호는 어떻게 작동하나요?

다음은 Azure Security Center, Azure Kubernetes Service 및 Azure Policy 간의 상호 작용에 대한 개략적인 다이어그램입니다.

Security Center에서 수신하고 분석한 항목에 다음이 포함된 것을 알 수 있습니다.

- API 서버의 감사 로그
- Log Analytics 에이전트의 원시 보안 이벤트
- AKS 클러스터의 클러스터 구성 정보
- **Kubernetes용 Azure Policy 추가 항목** 을 통해 Azure Policy의 워크로드 구성. [Kubernetes 허용 제어를 사용한 워크로드 보호 모범 사례에 대한 자세한 정보](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Azure Security Center, Azure Kubernetes Service 및 Azure Policy 간의 상호 작용에 대한 개략적인 아키텍처" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender for Kubernetes - FAQ

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Log Analytics 에이전트가 없어도 여전히 AKS 보호를 이용할 수 있나요?

위에서 설명한 것처럼, 선택적 **Azure Defender for Kubernetes** 요금제는 클러스터 수준에서 보호를 제공하며, **서버용 Azure Defender** 의 Log Analytics 에이전트는 노드를 보호합니다. 

가능하면 가장 완전한 보호를 위해 둘 다 배포하는 것이 좋습니다.

에이전트를 호스트에 설치하지 않도록 선택하는 경우 위협 방지 혜택 및 보안 경고를 일부만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 받을 수 있습니다.


### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>AKS를 사용하면 사용자 지정 VM 확장을 내 AKS 노드에 설치할 수 있나요?

Azure Defender에서 AKS 노드를 모니터링하려면 Log Analytics 에이전트를 실행해야 합니다. 

AKS는 관리형 서비스이며, Log Analytics 에이전트가 Microsoft에서 관리하는 확장이므로 AKS 클러스터에서도 지원됩니다.



### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>내 클러스터에서 컨테이너 에이전트용 Azure Monitor를 이미 실행하고 있는 경우 Log Analytics 에이전트도 필요한가요?

Azure Defender에서 AKS 노드를 모니터링하려면 Log Analytics 에이전트를 실행해야 합니다.

클러스터에서 컨테이너용 Azure Monitor 에이전트를 이미 실행하고 있는 경우 Log Analytics 에이전트도 설치할 수 있으며 두 에이전트가 문제없이 서로 간에 함께 작업할 수 있습니다.

[컨테이너 에이전트용 Azure Monitor에 대해 자세히 알아보세요](../azure-monitor/insights/container-insights-manage-agent.md).


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender for Kubernetes를 비롯한 Security Center의 Kubernetes 보호에 대해 알아보았습니다. 

관련 자료는 다음 문서를 참조하세요. 

- [Azure Defender 사용](security-center-pricing.md)
- [Azure Sentinel 또는 타사 SIEM으로 경고 내보내기](continuous-export.md)
- [경고 참조 테이블](alerts-reference.md)