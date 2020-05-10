---
title: 질문과 대답-Azure Load Balancer
description: Azure Load Balancer에 대 한 자주 묻는 질문에 대 한 대답입니다.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3be8ce241817b3b2fa03976eebe3147c1dc9c877
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005148"
---
# <a name="frequently-asked-questions"></a>질문과 대답

## <a name="what-types-of-load-balancer-exist"></a>어떤 유형의 Load Balancer 존재 하나요?
내부 부하 분산 장치는 VNET 내의 트래픽과 인터넷 연결 끝점 간에 트래픽을 분산 하는 외부 부하 분산 장치를 분산 합니다. 자세한 내용은 [Load Balancer 형식](components.md#frontend-ip-configurations)을 참조 하세요. 

이러한 두 유형 모두 Azure는 기능, 성능, 보안 및 상태 추적 기능이 서로 다른 기본 SKU 및 표준 SKU를 제공 합니다. 이러한 차이점은 [SKU 비교](skus.md) 문서에 설명 되어 있습니다.

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>기본에서 표준 Load Balancer로 업그레이드 하려면 어떻게 해야 하나요?
자동화 된 스크립트에 대 한 [기본에서 표준으로 업그레이드](upgrade-basic-standard.md) 문서와 Load Balancer SKU 업그레이드에 대 한 지침은을 참조 하세요.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Azure의 다양 한 부하 분산 옵션은 무엇 인가요?
사용할 수 있는 부하 분산 서비스 및 권장 사용에 대 한 [부하 분산 장치 기술 가이드](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) 를 참조 하세요.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Load Balancer ARM 템플릿은 어디에서 찾을 수 있나요?
일반적인 배포의 ARM 템플릿에 대 한 [Azure Load Balancer 빠른 시작 템플릿 목록을](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) 참조 하세요.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>인바운드 NAT 규칙은 부하 분산 규칙과 어떻게 다른가?
NAT 규칙은 트래픽을 라우팅하는 백 엔드 리소스를 지정 하는 데 사용 됩니다. 예를 들어 특정 부하 분산 장치 포트를 구성 하 여 특정 VM에 RDP 트래픽을 전송 합니다. 부하 분산 규칙은 트래픽을 라우팅하는 백 엔드 리소스의 풀을 지정 하는 데 사용 되며, 각 인스턴스에 대해 부하를 분산 합니다. 예를 들어 부하 분산 장치 규칙은 웹 서버 풀에서 부하 분산 장치의 포트 80에서 TCP 패킷을 라우팅할 수 있습니다.

## <a name="next-steps"></a>다음 단계
위에 나열 되지 않은 경우 질문에 대 한 피드백을 보내 주세요. 이렇게 하면 제품 팀에 대 한 GitHub 문제를 만들어 모든 중요 한 고객 질문이 답변 되었는지 확인할 수 있습니다.
