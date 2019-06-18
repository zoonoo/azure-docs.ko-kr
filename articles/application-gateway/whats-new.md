---
title: Azure Application Gateway의 새로운 소식
description: Azure Application Gateway의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능, 예정된 변경 내용 등 새로운 소식을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 2a494b924107baeabbcf412af7e1bbdb1db0f753
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752009"
---
# <a name="whats-new-in-azure-application-gateway"></a>Azure Application Gateway의 새로운 소식

Azure Application Gateway는 지속적으로 업데이트됩니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능

## <a name="new-features"></a>새로운 기능

|기능  |설명  |추가된 날짜  |
|---------|---------|---------|
|WAF 사용자 지정 규칙 |Application Gateway WAF_v2는 이제 사용자 지정 규칙 만들기를 지원합니다. [Application Gateway 사용자 지정 규칙](custom-waf-rules-overview.md)을 참조하세요. |2019년 6월 |
|자동 크기 조정, 영역 중복, 정적 VIP 지원 GA |자동 크기 조정, 영역 중복, 성능 향상, 정적 VIP, Key Vault, 헤더 다시 쓰기를 지원하는 v2 SKU의 일반 가용성. [Application Gateway 자동 크기 조정 설명서](application-gateway-autoscaling-zone-redundant.md)를 참조하세요. |2019년 4월 |
|Key Vault 통합 |Application Gateway는 이제 HTTPS 지원 수신기에 연결된 서버 인증서에 대한 Key Vault(공개 미리 보기 상태)와의 통합을 지원합니다. [Key Vault 인증서를 사용한 SSL 종료](key-vault-certs.md)를 참조하세요. |2019년 4월 |
|헤더 CRUD/다시 쓰기     |이제 HTTP 헤더를 다시 쓸 수 있습니다. 단계별 지침은 [자습서: 애플리케이션 게이트웨이를 만들고 HTTP 헤더 다시 쓰기](tutorial-http-header-rewrite-powershell.md)를 참조하세요.|2018년 12월|
|WAF 구성 및 제외 목록     |WAF를 구성하고 가양성을 줄이는 데 도움이 되는 옵션이 추가되었습니다. 자세한 내용은 [웹 애플리케이션 방화벽 요청 크기 제한 및 제외 목록](application-gateway-waf-configuration.md)을 참조하세요.|2018년 12월|
|자동 크기 조정, 영역 중복, 정적 VIP 지원      |v2 SKU에는 자동 크기 조정, 향상된 성능 등 여러 가지 개선 사항이 적용되었습니다. 자세한 내용은 [Azure Application Gateway란?](overview.md)을 참조하세요.|2018년 9월|
|연결 드레이닝     |연결 드레이닝을 사용하여 백 엔드 풀의 멤버를 정상적으로 제거할 수 있습니다. 자세한 내용은 [연결 드레이닝](overview.md#connection-draining)을 참조하세요.|2018년 9월|
|사용자 지정 오류 페이지     |사용자 지정 오류 페이지를 사용하여 나머지 웹 사이트 형식 내에서 오류 페이지를 만들 수 있습니다. 이 기능을 사용하도록 설정하려면 [Application Gateway 사용자 지정 오류 페이지 만들기](custom-error.md)를 참조하세요.|2018년 9월|
|향상된 메트릭     |향상된 메트릭을 통해 Application Gateway의 상태를 더 잘 볼 수 있습니다. Application Gateway의 메트릭을 사용하도록 설정하려면 [Application Gateway의 백엔드 상태, 진단 로그 및 메트릭](application-gateway-diagnostics.md)을 참조하세요.|2018년 6월|

## <a name="next-steps"></a>다음 단계

Azure Application Gateway에 대한 자세한 내용은 [Azure Application Gateway란?](overview.md)을 참조하세요.
