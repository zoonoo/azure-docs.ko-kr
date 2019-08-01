---
title: 'Azure AD 애플리케이션 프록시: 버전 릴리스 내역 | Microsoft Docs'
description: 이 문서에서는 Azure AD 응용 프로그램 프록시의 모든 릴리스를 나열 하 고 새로운 기능 및 해결 된 문제에 대해 설명 합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9ecd193282ed9b7333df44689530b4d057ad7f8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693893"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD 애플리케이션 프록시: 버전 릴리스 내역
이 문서에서는 릴리스된 Azure Active Directory (Azure AD) 응용 프로그램 프록시의 버전 및 기능을 나열 합니다. Azure AD 팀은 새로운 기능과 기능으로 응용 프로그램 프록시를 정기적으로 업데이트 합니다. 응용 프로그램 프록시 커넥터는 새 버전이 릴리스되면 자동으로 업데이트 됩니다. 

커넥터에 대해 자동 업데이트를 사용 하도록 설정 하 여 최신 기능 및 버그 수정이 있는지 확인 하는 것이 좋습니다. Microsoft에서는 최신 connector 버전 및 이전 버전을 직접 지원 합니다.

다음은 관련 된 리소스 목록입니다.

리소스 |  세부 정보
--------- | --------- |
응용 프로그램 프록시를 사용 하도록 설정 하는 방법 | 응용 프로그램 프록시를 사용 하도록 설정 하 고 커넥터를 설치 및 등록 하기 위한 필수 구성 요소는이 [자습서](application-proxy-add-on-premises-application.md)에 설명 되어 있습니다.
Azure AD 애플리케이션 프록시 커넥터 이해 | [커넥터 관리](application-proxy-connectors.md) 및 커넥터 [자동 업그레이드](application-proxy-connectors.md#automatic-updates)방법에 대해 자세히 알아보세요.
Azure AD 응용 프로그램 프록시 커넥터 다운로드 |  [최신 커넥터를 다운로드](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)합니다.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>릴리스 상태

2018 년 9 월 20 일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- QlikSense 응용 프로그램에 대 한 WebSocket 지원을 추가 했습니다. QlikSense를 응용 프로그램 프록시와 통합 하는 방법에 대 한 자세한 내용은이 [연습](application-proxy-qlik.md)을 참조 하세요. 
- 아웃 바운드 프록시를 쉽게 구성할 수 있도록 설치 마법사가 개선 되었습니다. 
- 커넥터의 기본 프로토콜로 TLS 1.2을 설정 합니다. 
- 새 최종 사용자 사용권 계약 (EULA)을 추가 했습니다.  

### <a name="fixed-issues"></a>해결된 문제

- 커넥터에서 약간의 메모리 누수를 일으킨 버그를 수정 했습니다.
- 커넥터 시간 제한 문제에 대 한 버그 수정을 포함 하는 Azure Service Bus 버전이 업데이트 되었습니다.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>릴리스 상태

2018 년 1 월 19 일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제

- 쿠키에서 도메인 변환이 필요한 사용자 지정 도메인에 대 한 지원이 추가 되었습니다.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>릴리스 상태 

2017 년 5 월 25 일: 다운로드용으로 릴리스됨 

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 

커넥터의 아웃 바운드 연결 제한에 대 한 제어 기능이 향상 되었습니다. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>릴리스 상태

2017 년 4 월 15 일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- 필요한 포트 수를 줄이고 등록 및 관리가 간소화 되었습니다. 이제 응용 프로그램 프록시를 사용 하려면 두 개의 표준 아웃 바운드 포트를 열어야 합니다. 443 및 80입니다. 응용 프로그램 프록시는 아웃 바운드 연결만 계속 사용 하므로 DMZ의 구성 요소는 필요 하지 않습니다. 자세한 내용은 [구성 설명서](application-proxy-add-on-premises-application.md)를 참조 하세요.  
- 외부 프록시 또는 방화벽에서 지원 되는 경우 이제 IP 범위 대신 DNS를 통해 네트워크를 열 수 있습니다. 응용 프로그램 프록시 서비스에는 *. msappproxy.net 및 *. servicebus.windows.net에 대 한 연결만 필요 합니다.


## <a name="earlier-versions"></a>이전 버전

1\.5.36.0 이전 버전의 응용 프로그램 프록시 커넥터를 사용 하는 경우 최신 버전으로 업데이트 하 여 완전히 지원 되는 최신 기능을 사용할 수 있도록 합니다.

## <a name="next-steps"></a>다음 단계
- [Azure AD 응용 프로그램 프록시을 통해 온-프레미스 응용 프로그램](application-proxy.md)에 대 한 원격 액세스에 대해 자세히 알아보세요.
- 애플리케이션 프록시를 시작하려면 [자습서: 애플리케이션 프록시를 통해 원격 액세스를 위한 온-프레미스 애플리케이션 추가](application-proxy-add-on-premises-application.md)를 참조하세요.
