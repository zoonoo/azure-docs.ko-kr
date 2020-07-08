---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: ba695337a1da1e440895f6b9f6fe2eb34f5041e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800610"
---
Security Center는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

Security Center에서 위협 방지를 제공하는 다양한 수준은 다음과 같습니다. 

* **호스트 수준** -Log Analytics 에이전트에서 의심 스러운 활동에 대해 Linux를 모니터링 합니다. 에이전트는 노드 또는 노드에서 실행되는 컨테이너에서 시작된 의심스러운 활동에 대한 경고를 트리거합니다. 이러한 활동의 예로 웹 셸 탐지 및 알려진 의심스러운 IP 주소와의 연결이 있습니다.

    에이전트는 컨테이너화된 환경의 보안에 대한 더 심층적인 인사이트를 파악하기 위해 컨테이너 관련 분석을 모니터링합니다. 권한 있는 컨테이너 만들기, API 서버에 대한 의심스러운 액세스 및 Docker 컨테이너 내에서 실행되는 SSH(Secure Shell) 서버와 같은 이벤트에 대한 경고를 트리거합니다.

    >[!IMPORTANT]
    > 에이전트를 호스트에 설치하지 않도록 선택하는 경우 위협 방지 혜택 및 보안 경고의 하위 집합만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 받을 수 있습니다.

    AKS 호스트 수준 경고 목록은 [경고의 참조 테이블](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)을 참조 하세요.


* **AKS 클러스터 수준**의 위협 방지는 Kubernetes의 감사 로그 분석을 기반으로 합니다. 이 **에이전트 없는** 모니터링을 사용하도록 설정하려면 **가격 책정 및 설정** 페이지에서 Kubernetes 옵션을 구독에 추가합니다([가격 책정](https://docs.microsoft.com/azure/security-center/security-center-pricing) 참조). 이 수준에서 경고를 생성하기 위해 Security Center는 AKS에서 검색한 로그를 사용하여 AKS 관리형 서비스를 모니터링합니다. 이 수준의 이벤트 예로 노출된 Kubernetes 대시보드, 높은 권한이 있는 역할 만들기 및 중요한 탑재 만들기가 있습니다.

    >[!NOTE]
    > Security Center는 구독 설정에서 Kubernetes 옵션을 사용하도록 설정한 후에 발생하는 Azure Kubernetes Service 작업 및 배포에 대한 보안 경고를 생성합니다. 

    AKS 클러스터 수준 경고 목록은 [경고 참조 표](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)를 참조하세요.

또한 글로벌 보안 연구 팀에서는 위협 환경을 지속적으로 모니터링합니다. 검색된 컨테이너 관련 경고 및 취약성을 추가합니다.

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)의 지침에 따라 컨테이너 경고를 시뮬레이션할 수 있습니다.