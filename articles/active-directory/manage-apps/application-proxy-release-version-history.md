---
title: 'Azure AD 애플리케이션 프록시: 버전 릴리스 내역 | Microsoft Docs'
description: 이 문서에서는 Azure AD Application Proxy의 모든 릴리스를 나열 하 고 새로운 기능과 수정 된 문제에 설명 합니다.
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
ms.openlocfilehash: 97b54d57a13f0f2b47633a25f1a44efdfa17d11a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595051"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD 애플리케이션 프록시: 버전 릴리스 내역
이 문서는 버전 및 출시 된 Azure Active Directory (Azure AD) 응용 프로그램 프록시의 기능을 나열 합니다. Azure AD 팀 새로운 특징과 기능을 사용 하 여 응용 프로그램 프록시를 정기적으로 업데이트합니다. 응용 프로그램 프록시 커넥터는 새 버전이 릴리스되면 자동으로 업데이트 됩니다.

관련 된 리소스의 목록을 다음과 같습니다.

리소스 |  세부 정보
--------- | --------- |
응용 프로그램 프록시를 사용 하는 방법 | 필수 구성 요소 및 응용 프로그램 프록시를 사용 하도록 설정 하 고, 설치 하 고, 커넥터 등록에 대 한이 설명 되어 있습니다 [자습서](application-proxy-add-on-premises-application.md)합니다.
Azure AD 애플리케이션 프록시 커넥터 이해 | 자세히 알아보세요 [커넥터 관리](application-proxy-connectors.md) 방법과 커넥터 [자동 업그레이드](application-proxy-connectors.md#automatic-updates)합니다.
Azure AD 응용 프로그램 프록시 커넥터 다운로드 |  [최신 커넥터 다운로드](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)합니다.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>릴리스 상태

2018 년 9 월 20 년: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- QlikSense 응용 프로그램에 대 한 WebSocket 지원이 추가 되었습니다. 이 참조 응용 프로그램 프록시를 사용 하 여 QlikSense를 통합 하는 방법에 대 한 자세한 내용은 [연습](application-proxy-qlik.md)합니다. 
- 설치 마법사는 아웃 바운드 프록시를 구성 하는 쉽게 수행할 수 있도록 향상 되었습니다. 
- 커넥터에 대 한 기본 프로토콜로 TLS 1.2를 설정 합니다. 
- 새 최종 사용자 사용권 계약 (EULA)을 추가 합니다.  

### <a name="fixed-issues"></a>해결된 문제

- 커넥터에서 일부 메모리 누수를 발생 시킨 버그가 수정 되었습니다.
- 커넥터 제한 문제에 대 한 버그 수정을 포함 하는 Azure Service Bus 버전을 업데이트 합니다.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>릴리스 상태

2018 년 1 월 19 일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제

- 쿠키의 도메인 번역 해야 하는 사용자 지정 도메인에 대 한 지원이 추가 되었습니다.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>릴리스 상태 

2017 년 5 월 25 일: 다운로드용으로 릴리스됨 

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 

향상 된 커넥터의 아웃 바운드 연결 제한 제어 합니다. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>릴리스 상태

2017 년 4 월 15: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- 온 보 딩 및 적은 수의 필요한 포트를 사용 하 여 관리를 간소화 합니다. 응용 프로그램 프록시는 이제 두 개의 표준 아웃 바운드 포트를 열어 필요 합니다. 443 및 80입니다. 응용 프로그램 프록시를 DMZ에 모든 구성 요소가 여전히 필요 하지 않습니다만 아웃 바운드 연결을 사용 하 여 계속 합니다. 세부 정보를 참조 하세요. 당사의 [구성 설명서](application-proxy-add-on-premises-application.md)합니다.  
- 외부 프록시 또는 방화벽에서 지원할 경우, 이제 IP 범위 대신 DNS에서 네트워크를 열 수 있습니다. 응용 프로그램 프록시 서비스에 대 한 연결을 필요한 *. msappproxy.net 및 *. servicebus.windows.net만 합니다.


## <a name="earlier-versions"></a>이전 버전

1\.5.36.0 이전의 응용 프로그램 프록시 커넥터 버전을 사용 중인 경우 최신 확인 하기 위해 최신 버전으로 업데이트 기능을 완벽 하 게 지원 합니다.

## <a name="next-steps"></a>다음 단계
- 에 대해 자세히 알아보세요 [Azure AD 응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 대 한 원격 액세스](application-proxy.md)합니다.
- 애플리케이션 프록시를 시작하려면 [자습서: 애플리케이션 프록시를 통해 원격 액세스를 위한 온-프레미스 애플리케이션 추가](application-proxy-add-on-premises-application.md)를 참조하세요.
