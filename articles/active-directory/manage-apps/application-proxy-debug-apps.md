---
title: Azure Active Directory 응용 프로그램 프록시 응용 프로그램 디버그 | Microsoft Docs
description: Azure Active Directory (Azure AD) 응용 프로그램 프록시 응용 프로그램 문제를 디버그 합니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: d0a12bde119e9dae3f950603fac4bce060bb5f91
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172267"
---
# <a name="debug-application-proxy-application-issues"></a>응용 프로그램 프록시 응용 프로그램 문제 디버그 

이 문서에서는 Azure Active Directory (Azure AD) 응용 프로그램 프록시 응용 프로그램을 사용 하 여 문제를 해결 합니다. 온-프레미스 웹 응용 프로그램에 대 한 원격 액세스에 대 한 응용 프로그램 프록시 서비스를 사용 하는 응용 프로그램에 연결 하는 데 문제가 있는 경우,이 순서도 사용 하 여 응용 프로그램 문제를 디버깅 합니다. 

## <a name="before-you-begin"></a>시작하기 전에

응용 프로그램 프록시 문제를 해결 하는 경우에 커넥터를 사용 하 여 시작 하는 것이 좋습니다. 먼저 문제 해결 흐름에 따라 [응용 프로그램 프록시 커넥터 디버그 문제](application-proxy-debug-connectors.md) 응용 프로그램 프록시 커넥터는 올바르게 구성 되었는지 확인 합니다. 여전히 문제가 발생 하는, 응용 프로그램 문제를 해결 하려면이 문서를 반환 합니다.  

응용 프로그램 프록시에 대 한 자세한 내용은 다음을 참조 하세요.

- [응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 대 한 원격 액세스](application-proxy.md)
- [응용 프로그램 프록시 커넥터](application-proxy-connectors.md)
- [설치 하 고 커넥터를 등록 합니다.](application-proxy-add-on-premises-application.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>응용 프로그램 문제에 대 한 흐름도

이 순서도 응용 프로그램에 연결 된 일반적인 문제 중 일부를 디버깅 하는 단계를 안내 합니다. 각 단계에 대 한 자세한 내용은 다음에 나오는 순서도 테이블을 참조 하세요.

![응용 프로그램을 디버깅 하기 위한 단계를 보여 주는 순서도](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | 액션(Action) | 설명 | 
|---------|---------|---------|
|1 | 브라우저를 열고 앱에 액세스 하 고 자격 증명 입력 | 같은 앱 및 사용자 관련 오류를 확인 하려면 로그인 자격 증명을 사용해 보세요 [이 회사 앱에 액세스할 수 없습니다](application-proxy-sign-in-bad-gateway-timeout-error.md)합니다. |
|2 | 앱에 사용자 할당을 확인 | 사용자 계정에 회사 네트워크 내에서 앱에 액세스할 권한이 있는지 확인 한 다음의 단계를 수행 하 여 앱에 로그인을 테스트 [응용 프로그램을 테스트](application-proxy-add-on-premises-application.md#test-the-application)합니다. 로그인 문제가 지속 되 면 참조 [로그인 오류를 해결 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-troubleshoot-sign-in-errors)합니다.  |
|3 | 브라우저를 열고 앱에 액세스 하려고 합니다. | 오류가 즉시 표시 되 면 응용 프로그램 프록시를 올바르게 구성 되어 있는지를 확인 합니다. 특정 오류 메시지에 대 한 자세한 내용은 참조 하세요 [문제를 해결 하는 응용 프로그램 프록시 문제 및 오류 메시지](application-proxy-troubleshoot.md)합니다.  |
|4 | 사용자 지정 도메인 설정을 확인 하거나 문제 해결 | 페이지가 전혀 표시 되지 않으면, 사용자 지정 도메인을 검토 하 여 올바르게 구성 되어 있는지 확인 하십시오 [사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)합니다.<br></br>페이지 로드 되지 않습니다. 오류 메시지가 나타나면를 참조 하 여 오류를 해결 [문제를 해결 하는 응용 프로그램 프록시 문제 및 오류 메시지](application-proxy-troubleshoot.md)합니다. <br></br>표시할 오류 메시지에 대해 20 초 보다 오래 걸리는 경우 연결 문제가 있을 수 있습니다. 로 이동 합니다 [디버깅할 응용 프로그램 프록시 커넥터](application-proxy-debug-connectors.md) 문제 해결 문서.  |
|5 | 문제가 지속 되 면 커넥터 디버깅으로 이동 | 또는 커넥터와 백 엔드 프록시와 커넥터 사이의 연결 문제가 있을 수 있습니다. 로 이동 합니다 [디버깅할 응용 프로그램 프록시 커넥터](application-proxy-debug-connectors.md) 문제 해결 문서. |
|6 | 모든 리소스를 게시할 브라우저 개발자 도구를 확인 하 고 링크를 수정 합니다. | 게시 경로 모든 필요한 이미지, 스크립트 및 응용 프로그램에 대 한 스타일 시트에 포함 되어 있는지 확인 합니다. 자세한 내용은 참조 하세요 [Azure AD에 온-프레미스 앱을 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)합니다. <br></br>에 설명 된 대로 문제를 게시 하는 것에 대 한 브라우저의 개발자 도구 (Microsoft Edge 또는 Internet Explorer의 F12 도구) 및 검사를 사용 하 여 [응용 프로그램 페이지가 올바르게 표시 되지 않음](application-proxy-page-appearance-broken-problem.md)합니다. <br></br>끊어진된 링크 문제 해결 옵션을 검토 [페이지의 링크가 작동 하지 않습니다](application-proxy-page-links-broken-problem.md)합니다. |
|7 | 네트워크 대기 시간 확인 | 느린 페이지를 로드 하는 경우 네트워크 대기 시간을 최소화 하는 방법을 알아봅니다 [대기 시간이 감소 하는 것에 대 한 고려 사항](application-proxy-network-topology.md#considerations-for-reducing-latency)합니다. | 
|8 | 추가 문제 해결 정보를 참조 하세요. | 문제가 지속 되 면에서 추가 문제 해결 문서를 검색 합니다 [응용 프로그램 프록시 문제 해결 설명서](application-proxy-page-appearance-broken-problem.md)합니다. |

## <a name="next-steps"></a>다음 단계


* [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)
* [기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)
* [애플리케이션 프록시 및 커넥터 오류 문제 해결](application-proxy-troubleshoot.md)
* [Azure AD 애플리케이션 프록시 커넥터를 자동으로 설치하는 방법](application-proxy-register-connector-powershell.md)
