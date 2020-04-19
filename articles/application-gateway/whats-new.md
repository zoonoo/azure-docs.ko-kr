---
title: Azure Application Gateway의 새로운 소식
description: Azure Application Gateway의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능, 예정된 변경 내용 등 새로운 소식을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 70014b5ab37a07e01eaa2db3d729b7d8af520842
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311771"
---
# <a name="whats-new-in-azure-application-gateway"></a>Azure Application Gateway의 새로운 소식

Azure Application Gateway는 지속적으로 업데이트됩니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능

## <a name="new-features"></a>새로운 기능

|기능  |Description  |추가된 날짜  |
|---------|---------|---------|
| v2의 UDR(사용자 정의 경로)(미리 보기) |이제 Application Gateway v2 SKU의 일부 시나리오에서 사용자 정의 경로가 지원됩니다. 자세한 내용은 [Application Gateway 구성 개요](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)를 참조하세요. |2020년 3월 |
|선호도 쿠키 변경 내용 |쿠키 기반 선호도를 사용하도록 설정하면 Application Gateway는 기존 ApplicationGatewayAffinity 쿠키 외에도 *ApplicationGatewayAffinityCORS*라는 다른 동일한 쿠키를 삽입합니다. *ApplicationGatewayAffinityCORS*에 추가된 두 개의 특성이 있으므로(*SameSite=None; Secure*) 원본 간 요청에 대해서도 고정 세션이 유지됩니다. 자세한 내용은 [Application Gateway 쿠키 기반 선호도](configuration-overview.md#cookie-based-affinity)를 참조하세요. |2020년 2월 |
|프로브 향상 |Application Gateway v2 SKU의 사용자 지정 프로브 향상을 통해 [프로브 구성](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)을 간소화하고, [주문형 백 엔드 상태 테스트](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe)를 용이하게 하고, 백 엔드 상태 문제를 해결하는 데 도움이 되도록 [추가 진단 정보](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages)를 추가했습니다.  |2019년 10월 |
|추가 메트릭 |Applicaiton Gateway v2 SKU를 모니터링하는 데 도움이 되도록 다음과 같은 새 메트릭을 추가했습니다. [타이밍 관련 메트릭](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), 백 엔드 응답 상태, 받은 바이트 수, 보낸 바이트 수, 클라이언트 TLS 프로토콜, 현재 컴퓨팅 단위 등의 새 메트릭을 추가했습니다. [Application Gateway V2 SKU에서 지원하는 메트릭](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku)을 참조하세요. |2019년 8월 |
|WAF 사용자 지정 규칙 |Application Gateway WAF_v2는 이제 사용자 지정 규칙 만들기를 지원합니다. [Application Gateway 사용자 지정 규칙](custom-waf-rules-overview.md)을 참조하세요. |2019년 6월 |
|자동 크기 조정, 영역 중복, 정적 VIP 지원 GA |자동 크기 조정, 영역 중복, 성능 향상, 정적 VIP, Key Vault, 헤더 다시 쓰기를 지원하는 v2 SKU의 일반 가용성 [Application Gateway 자동 크기 조정 설명서](application-gateway-autoscaling-zone-redundant.md)를 참조하세요. |2019년 4월 |
|Key Vault 통합 |Application Gateway는 이제 HTTPS 지원 수신기에 연결된 서버 인증서에 대한 Key Vault(공개 미리 보기 상태)와의 통합을 지원합니다. [Key Vault 인증서를 사용한 TLS 종료](key-vault-certs.md)를 참조하세요. |2019년 4월 |
|헤더 CRUD/다시 쓰기     |이제 HTTP 헤더를 다시 쓸 수 있습니다. [자습서: 애플리케이션 게이트웨이를 만들고 HTTP 헤더 다시 쓰기](tutorial-http-header-rewrite-powershell.md)를 참조하세요.|2018년 12월|
|WAF 구성 및 제외 목록     |WAF를 구성하고 가양성을 줄이는 데 도움이 되는 옵션이 추가되었습니다. 자세한 내용은 [웹 애플리케이션 방화벽 요청 크기 제한 및 제외 목록](application-gateway-waf-configuration.md)을 참조하세요.|2018년 12월|
|자동 크기 조정, 영역 중복, 정적 VIP 지원      |v2 SKU에는 자동 크기 조정, 향상된 성능 등 여러 가지 개선 사항이 적용되었습니다. 자세한 내용은 [Azure Application Gateway란?](overview.md)을 참조하세요.|2018년 9월|
|연결 드레이닝     |연결 드레이닝을 사용하여 백 엔드 풀의 멤버를 정상적으로 제거할 수 있습니다. 자세한 내용은 [연결 드레이닝](features.md#connection-draining)을 참조하세요.|2018년 9월|
|사용자 지정 오류 페이지     |사용자 지정 오류 페이지를 사용하여 나머지 웹 사이트 형식 내에서 오류 페이지를 만들 수 있습니다. 이 기능을 사용하도록 설정하려면 [Application Gateway 사용자 지정 오류 페이지 만들기](custom-error.md)를 참조하세요.|2018년 9월|
|향상된 메트릭     |향상된 메트릭을 통해 Application Gateway의 상태를 더 잘 볼 수 있습니다. Application Gateway의 메트릭을 사용하도록 설정하려면 [Application Gateway의 백엔드 상태, 진단 로그 및 메트릭](application-gateway-diagnostics.md)을 참조하세요.|2018년 6월|

## <a name="next-steps"></a>다음 단계

Azure Application Gateway에 대한 자세한 내용은 [Azure Application Gateway란?](overview.md)을 참조하세요.
