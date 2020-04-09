---
title: 'Azure AD 응용 프로그램 프록시: 버전 릴리스 기록 | 마이크로 소프트 문서'
description: 이 문서에서는 Azure AD 응용 프로그램 프록시의 모든 릴리스를 나열하고 새로운 기능 및 수정된 문제에 대해 설명합니다.
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
ms.date: 04/07/2020
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: f027fbce66a73306165a0ad35d1ba3faa7a5c0bc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983894"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD 응용 프로그램 프록시: 버전 릴리스 기록
이 문서에서는 릴리스된 Azure Active Directory(Azure AD) 응용 프로그램 프록시의 버전 및 기능을 나열합니다. Azure AD 팀은 정기적으로 새로운 기능과 기능으로 응용 프로그램 프록시를 업데이트합니다. 새 버전이 릴리스되면 응용 프로그램 프록시 커넥터가 자동으로 업데이트됩니다. 

커넥터에 대한 자동 업데이트가 활성화되어 최신 기능 과 버그 수정이 있는지 확인하는 것이 좋습니다. Microsoft는 이전에 가장 마지막 커넥터 버전과 한 버전에 대한 직접 지원을 제공합니다.

관련 리소스 목록은 다음과 같습니다.

리소스 |  세부 정보
--------- | --------- |
응용 프로그램 프록시를 활성화하는 방법 | 응용 프로그램 프록시를 사용하도록 설정하고 커넥터를 설치하고 등록하기 위한 필수 구성 사항은 이 [자습서에서](application-proxy-add-on-premises-application.md)설명합니다.
Azure AD 애플리케이션 프록시 커넥터 이해 | [커넥터 관리](application-proxy-connectors.md) 및 커넥터 [자동 업그레이드](application-proxy-connectors.md#automatic-updates)방법에 대해 자세히 알아보십시오.
Azure AD 응용 프로그램 프록시 커넥터 다운로드 |  [최신 커넥터를 다운로드합니다.](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>릴리스 상태

2020년 4월 07일: 다운로드용으로 출시

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
-   커넥터는 모든 연결에 대해 TLS 1.2만 사용합니다. 자세한 내용은 [커넥터 필수 구성 조건을](application-proxy-add-on-premises-application.md#before-you-begin) 참조하십시오.
- 커넥터와 Azure 서비스 간의 시그널링이 개선되었습니다. 여기에는 커넥터와 Azure 서비스 간의 WCF 통신에 대한 신뢰할 수 있는 세션 지원과 WebSocket 통신에 대한 DNS 캐싱 개선 사항이 포함됩니다.
- 커넥터와 백 엔드 응용 프로그램 간의 프록시 구성 지원 자세한 내용은 [기존 온-프레미스 프록시 서버작업을](application-proxy-configure-connectors-with-proxy-servers.md)참조하십시오.

### <a name="fixed-issues"></a>해결된 문제
- 커넥터에서 Azure 서비스로의 통신을 위해 포트 8080으로 다시 떨어지는 것을 제거했습니다.
- WebSocket 통신에 대한 디버그 추적이 추가되었습니다. 
- 백 엔드 응용 프로그램 쿠키에 설정할 때 SameSite 특성을 유지 확인했습니다.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>릴리스 상태

2018년 9월 20일: 다운로드용으로 출시됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- QlikSense 응용 프로그램에 대한 WebSocket 지원이 추가되었습니다. QlikSense를 응용 프로그램 프록시와 통합하는 방법에 대한 자세한 내용은 이 [연습을](application-proxy-qlik.md)참조하십시오. 
- 아웃바운드 프록시를 보다 쉽게 구성할 수 있도록 설치 마법사를 개선했습니다. 
- TLS 1.2를 커넥터의 기본 프로토콜로 설정합니다. 
- EULA(최종 사용자 라이선스 계약)를 새로 추가했습니다.  

### <a name="fixed-issues"></a>해결된 문제

- 커넥터에서 일부 메모리 누수의 원인이 되는 버그를 수정했습니다.
- 커넥터 시간 시간 지정 문제에 대한 버그 수정이 포함된 Azure Service Bus 버전을 업데이트했습니다.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>릴리스 상태

2018년 1월 19일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제

- 쿠키에서 도메인 번역이 필요한 사용자 지정 도메인에 대한 지원이 추가되었습니다.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>릴리스 상태 

2017년 5월 25일: 다운로드용으로 릴리스됨 

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 

커넥터의 아웃바운드 연결 제한을 제어할 수 있습니다. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>릴리스 상태

2017년 4월 15일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- 필요한 포트수가 적어 온보딩 및 관리가 간소화되었습니다. 이제 응용 프로그램 프록시는 443 및 80이라는 두 개의 표준 아웃바운드 포트만 열면 됩니다. 응용 프로그램 프록시는 아웃바운드 연결만 계속 사용하므로 DMZ에 구성 요소가 필요하지 않습니다. 자세한 내용은 [구성 설명서를](application-proxy-add-on-premises-application.md)참조하십시오.  
- 외부 프록시 또는 방화벽에서 지원하는 경우 이제 IP 범위 대신 DNS로 네트워크를 열 수 있습니다. 응용 프로그램 프록시 서비스에는 *.msappproxy.net 및 *.servicebus.windows.net 에만 대한 연결이 필요합니다.


## <a name="earlier-versions"></a>이전 버전

1.5.36.0 이전 버전의 응용 프로그램 프록시 커넥터 버전을 사용하는 경우 최신 버전으로 업데이트하여 완전히 지원되는 최신 기능이 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계
- [Azure AD 응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에](application-proxy.md)대한 원격 액세스에 대해 자세히 알아봅니다.
- 응용 프로그램 프록시 사용을 시작하려면 [자습서: 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](application-proxy-add-on-premises-application.md)를 참조하십시오.
