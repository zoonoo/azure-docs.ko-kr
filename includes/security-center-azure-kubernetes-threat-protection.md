---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 1b650fa5a0e9ba2f7019e6e67690d9d1fd65e72a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894895"
---
Security Center는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

Security Center에서 위협 방지를 제공하는 다양한 수준은 다음과 같습니다. 

* **호스트 수준 (서버에 대해 Azure defender에서 제공)** -다른 vm에서 사용 Security Center는 것과 동일한 Log Analytics 에이전트를 사용 하 여, Azure Defender는 Linux AKS 노드를 모니터링 하 여 웹 셸 검색, 알려진 의심 스러운 IP 주소와의 연결 등의 의심 스러운 활동을 모니터링 합니다. 또한 에이전트는 특권 컨테이너 만들기, API 서버에 대 한 의심 스러운 액세스 및 Docker 컨테이너 내에서 실행 되는 SSH (Secure Shell) 서버와 같은 컨테이너 관련 분석을 모니터링 합니다.

    >[!IMPORTANT]
    > 에이전트를 호스트에 설치하지 않도록 선택하는 경우 위협 방지 혜택 및 보안 경고의 하위 집합만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 받을 수 있습니다.

    AKS 호스트 수준 경고 목록은 [경고의 참조 테이블](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)을 참조 하세요.


* **AKS 클러스터 수준 (Kubernetes 용 Azure Defender에서 제공)** -클러스터 수준에서 위협 보호는 Kubernetes의 감사 로그 분석을 기반으로 합니다. 이 **에이전트** 없는 모니터링을 사용 하도록 설정 하려면 Azure Defender를 사용 하도록 설정 합니다. 이 수준에서 경고를 생성하기 위해 Security Center는 AKS에서 검색한 로그를 사용하여 AKS 관리형 서비스를 모니터링합니다. 이 수준의 이벤트 예로 노출된 Kubernetes 대시보드, 높은 권한이 있는 역할 만들기 및 중요한 탑재 만들기가 있습니다.

    >[!NOTE]
    > Security Center는 구독 설정에서 Kubernetes 옵션을 사용하도록 설정한 후에 발생하는 Azure Kubernetes Service 작업 및 배포에 대한 보안 경고를 생성합니다. 

    AKS 클러스터 수준 경고 목록은 [경고 참조 표](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)를 참조하세요.

또한 글로벌 보안 연구 팀에서는 위협 환경을 지속적으로 모니터링합니다. 검색된 컨테이너 관련 경고 및 취약성을 추가합니다.

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)의 지침에 따라 컨테이너 경고를 시뮬레이션할 수 있습니다.