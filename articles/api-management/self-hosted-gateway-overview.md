---
title: 자체 호스팅 게이트웨이 개요 | Microsoft Docs
description: 조직이 하이브리드 및 다중 클라우드 환경에서 Api를 관리 하는 데 도움이 되는 Azure API Management의 자체 호스팅 게이트웨이 기능을 알아봅니다.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b560b02544eeb96167e68ed305d4d9942d2b1e0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82232975"
---
# <a name="self-hosted-gateway-overview"></a>자체 호스팅 게이트웨이 개요

이 문서에서는 Azure API Management의 자체 호스팅 게이트웨이 기능을 사용 하 여 하이브리드 및 다중 클라우드 API를 관리 하 고, 개략적인 아키텍처를 제공 하 고, 해당 기능을 강조 하는 방법을 설명 합니다.

## <a name="hybrid-and-multi-cloud-api-management"></a>하이브리드 및 다중 클라우드 API management

자체 호스팅 게이트웨이 기능은 하이브리드 및 다중 클라우드 환경에 대 한 API Management 지원을 확장 하 고, 조직에서 Azure의 단일 API Management 서비스에서 온-프레미스 및 클라우드 간에 호스트 되는 Api를 효율적이 고 안전 하 게 관리할 수 있도록 합니다.

자체 호스팅 게이트웨이를 사용 하는 경우 고객은 Api를 호스트 하는 동일한 환경에 API Management gateway 구성 요소의 컨테이너 화 된 버전을 유연 하 게 배포할 수 있습니다. 모든 자체 호스팅 게이트웨이는 페더레이션 된 API Management 서비스에서 관리 되므로, 모든 내부 및 외부 Api에 대 한 가시성 및 통합 관리 환경을 고객에 게 제공 합니다. 게이트웨이를 Api 가까이 배치 하면 고객이 API 트래픽 흐름을 최적화 하 고 보안 및 규정 준수 요구 사항을 해결할 수 있습니다.

각 API Management 서비스는 다음과 같은 주요 구성 요소로 구성 됩니다.

-   API로 노출 되는 관리 평면은 Azure Portal, PowerShell 및 기타 지원 되는 메커니즘을 통해 서비스를 구성 하는 데 사용 됩니다.
-   게이트웨이 (또는 데이터 평면)는 API 요청을 프록시 하 고 정책을 적용 하 고 원격 분석을 수집 하는 일을 담당 합니다.
-   개발자가 Api를 사용 하기 위해 검색, 학습 및 등록 하는 데 사용 하는 개발자 포털

기본적으로 이러한 모든 구성 요소는 Azure에 배포 되므로 api를 구현 하는 백 엔드가 호스트 되는 위치에 관계 없이 모든 API 트래픽 (아래 그림의 검정 실선으로 표시 됨)이 Azure를 통해 전달 됩니다. 이 모델의 운영 단순성은 대기 시간, 규정 준수 문제 및 경우에 따라 추가 데이터 전송 요금을 절감 하는 것입니다.

![자체 호스팅 게이트웨이가 없는 API 트래픽 흐름](media/self-hosted-gateway-overview/without-gateways.png)

백엔드 API 구현이 호스트 되는 동일한 환경에 자체 호스팅 게이트웨이를 배포 하면 API 트래픽이 백 엔드 Api로 직접 이동 하 여 대기 시간을 개선 하 고, 데이터 전송 비용을 최적화 하 고, 규정 준수를 가능 하 게 하며, 구현이 호스트 되는 위치에 관계 없이 조직 내 모든 Api의 모든 Api에 대 한 단일 관리, 관찰성 및 검색의 이점을 유지할 수 있습니다.

![자체 호스팅 게이트웨이를 사용 하는 API 트래픽 흐름](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>패키징 및 기능

자체 호스팅 게이트웨이는 모든 API Management 서비스의 일부로 Azure에 배포 된 컨테이너 화 된 기능적으로 동일한 버전의 관리 되는 게이트웨이입니다. 자체 호스팅 게이트웨이는 Microsoft Container Registry에서 Linux 기반 Docker [컨테이너로](https://aka.ms/apim/sputnik/dhub) 사용할 수 있습니다. 이 도구는 Docker, Kubernetes 또는 온-프레미스, 클라우드 인프라의 서버 클러스터에서 실행 되는 다른 컨테이너 오케스트레이션 솔루션 또는 개인용 컴퓨터의 평가 및 개발 목적으로 배포할 수 있습니다.

관리 되는 게이트웨이에 있는 다음 기능은 자체 호스팅 게이트웨이에서 **사용할 수 없습니다** .

- Azure Monitor 로그
- 업스트림 (백 엔드 쪽) TLS 버전 및 암호화 관리
- API Management 서비스에 업로드 된 [CA 루트 인증서](api-management-howto-ca-certificates.md) 를 사용 하 여 서버 및 클라이언트 인증서의 유효성을 검사 합니다. 사용자 지정 CA에 대 한 지원을 추가 하려면 CA의 루트 인증서를 설치 하는 자체 호스팅 게이트웨이 컨테이너 이미지에 계층을 추가 합니다.
- [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) 와 통합
- TLS 세션 다시 시작
- 클라이언트 인증서를 재협상 합니다. 즉, [클라이언트 인증서 인증](api-management-howto-mutual-certificates-for-clients.md) 에서 작동 하는 API 소비자는 초기 TLS 핸드셰이크의 일부로 인증서를 제공 해야 합니다. 이렇게 하려면 자체 호스팅 게이트웨이 사용자 지정 호스트 이름을 구성할 때 클라이언트 인증서 협상 설정을 사용 하도록 설정 합니다.
- 기본 제공 캐시입니다. 자체 호스팅 게이트웨이에서 외부 캐시를 사용 하는 방법에 대 한 자세한 내용은이 [문서](api-management-howto-cache-external.md) 를 참조 하세요.

## <a name="connectivity-to-azure"></a>Azure에 대 한 연결

자체 호스팅 게이트웨이에서는 포트 443의 Azure에 대 한 아웃 바운드 TCP/IP 연결이 필요 합니다. 자체 호스트 된 각 게이트웨이는 단일 API Management 서비스와 연결 되어야 하며 해당 관리 평면을 통해 구성 됩니다. 자체 호스팅 게이트웨이는 다음을 위해 Azure에 대 한 연결을 사용 합니다.

-   1 분 마다 하트 비트 메시지를 전송 하 여 상태 보고
-   정기적으로 (10 초 마다) 확인 및 구성 업데이트를 사용할 수 있을 때마다 적용
-   요청 로그 및 메트릭을 Azure Monitor로 보내기 (구성 된 경우)
-   Application Insights로 설정 되어 있는 경우 이벤트 전송

Azure에 대 한 연결이 끊어지면 자체 호스팅 게이트웨이는 구성 업데이트를 받거나, 상태를 보고 하거나, 원격 분석을 업로드할 수 없습니다.

자체 호스팅 게이트웨이는 "정적 오류"로 설계 되었으며 임시로 Azure에 대 한 연결이 손실 될 수 있습니다. 로컬 구성 백업을 사용 하거나 사용 하지 않고 배포할 수 있습니다. 이전 경우 자체 호스팅 게이트웨이는 해당 컨테이너 또는 pod에 연결 된 영구적 볼륨에 다운로드 된 최신 구성의 백업 복사본을 정기적으로 저장 합니다.

구성 백업이 꺼져 있고 Azure에 대 한 연결이 중단 된 경우:

-   자체 호스팅 게이트웨이 실행은 구성의 메모리 내 복사본을 사용 하 여 계속 작동 합니다.
-   중지 된 자체 호스팅 게이트웨이를 시작할 수 없습니다.

구성 백업이 켜져 있고 Azure에 대 한 연결이 중단 된 경우:

-   자체 호스팅 게이트웨이 실행은 구성의 메모리 내 복사본을 사용 하 여 계속 작동 합니다.
-   중지 된 자체 호스팅 게이트웨이는 구성의 백업 복사본을 사용 하 여 시작할 수 있습니다.

연결이 복원 되 면 중단의 영향을 받는 자체 호스트 된 각 게이트웨이가 연결 된 API Management 서비스와 자동으로 다시 연결 되 고 게이트웨이가 "오프 라인" 상태일 때 발생 한 모든 구성 업데이트를 다운로드 합니다.

## <a name="next-steps"></a>다음 단계

-   [이 항목에 대 한 추가 배경의 백서 읽기](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [자체 호스팅 게이트웨이를 Docker에 배포](how-to-deploy-self-hosted-gateway-docker.md)
-   [Kubernetes에 자체 호스팅 게이트웨이 배포](how-to-deploy-self-hosted-gateway-kubernetes.md)
