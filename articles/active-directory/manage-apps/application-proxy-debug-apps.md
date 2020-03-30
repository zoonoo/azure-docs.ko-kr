---
title: 디버그 응용 프로그램 프록시 응용 프로그램 - Azure Active Directory | 마이크로 소프트 문서
description: Azure Active Directory(Azure AD) 응용 프로그램 프록시 응용 프로그램에서 문제를 디버그합니다.
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
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382066"
---
# <a name="debug-application-proxy-application-issues"></a>애플리케이션 프록시 디버그 애플리케이션 이슈 

이 문서에서는 Azure Active Directory(Azure AD) 응용 프로그램 프록시 응용 프로그램의 문제를 해결하는 데 도움이 됩니다. 온-프레미스 웹 응용 프로그램에 대한 원격 액세스를 위해 응용 프로그램 프록시 서비스를 사용하지만 응용 프로그램에 연결하는 데 문제가 있는 경우 이 순서도를 사용하여 응용 프로그램 문제를 디버깅합니다. 

## <a name="before-you-begin"></a>시작하기 전에

응용 프로그램 프록시 문제를 해결할 때커넥터로 시작하는 것이 좋습니다. 먼저 [디버그 응용 프로그램 프록시 커넥터 문제의](application-proxy-debug-connectors.md) 문제 해결 흐름을 따라 응용 프로그램 프록시 커넥터가 올바르게 구성되었는지 확인합니다. 그래도 문제가 있는 경우 이 문서로 돌아가 응용 프로그램 문제를 해결합니다.  

응용 프로그램 프록시에 대한 자세한 내용은 다음을 참조하십시오.

- [응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 대한 원격 액세스](application-proxy.md)
- [응용 프로그램 프록시 커넥터](application-proxy-connectors.md)
- [커넥터 설치 및 등록](application-proxy-add-on-premises-application.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>응용 프로그램 문제에 대한 순서도

이 순서도는 응용 프로그램에 연결할 때 보다 일반적인 문제 중 일부를 디버깅하는 단계를 안내합니다. 각 단계에 대한 자세한 내용은 순서도 다음의 표를 참조하십시오.

![응용 프로그램 디버깅 단계를 보여주는 순서도](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | 작업 | 설명 | 
|---------|---------|---------|
|1 | 브라우저를 열고, 앱에 액세스하고, 자격 증명을 입력합니다. | 자격 증명을 사용하여 앱에 로그인하고 이 회사 앱에 액세스할 수 없는 것과 같은 사용자 관련 오류를 [확인합니다.](application-proxy-sign-in-bad-gateway-timeout-error.md) |
|2 | 앱에 대한 사용자 할당 확인 | 사용자 계정에 회사 네트워크 내에서 앱에 액세스할 수 있는 권한이 있는지 확인한 다음 [응용 프로그램 테스트의](application-proxy-add-on-premises-application.md#test-the-application)단계에 따라 앱에 로그인을 테스트합니다. 로그인 문제가 지속되면 [로그인 오류 문제를 해결하는 방법을](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)참조하십시오.  |
|3 | 브라우저를 열고 앱에 액세스하려고 합니다. | 오류가 즉시 나타나면 응용 프로그램 프록시가 올바르게 구성되었는지 확인합니다. 특정 오류 메시지에 대한 자세한 내용은 [응용 프로그램 프록시 문제 및 오류 메시지 문제를 해결합니다.](application-proxy-troubleshoot.md)  |
|4 | 사용자 지정 도메인 설정 확인 또는 오류 문제 해결 | 페이지가 전혀 표시되지 않는 경우 사용자 지정 도메인 작업을 검토하여 [사용자 지정 도메인이](application-proxy-configure-custom-domain.md)올바르게 구성되었는지 확인합니다.<br></br>페이지가 로드되지 않고 오류 메시지가 나타나면 [응용 프로그램 프록시 문제 및 오류 메시지를](application-proxy-troubleshoot.md)참조하여 오류를 해결합니다. <br></br>오류 메시지가 표시되는 데 20초 이상 걸리는 경우 연결 문제가 있을 수 있습니다. [디버그 응용 프로그램 프록시 커넥터](application-proxy-debug-connectors.md) 문제 해결 문서로 이동합니다.  |
|5 | 문제가 지속되면 커넥터 디버깅으로 이동합니다. | 프록시와 커넥터 간에 또는 커넥터와 백 엔드 사이에 연결 문제가 있을 수 있습니다. [디버그 응용 프로그램 프록시 커넥터](application-proxy-debug-connectors.md) 문제 해결 문서로 이동합니다. |
|6 | 모든 리소스 게시, 브라우저 개발자 도구 확인 및 링크 수정 | 게시 경로에 응용 프로그램에 필요한 모든 이미지, 스크립트 및 스타일 시트가 포함되어 있는지 확인합니다. 자세한 내용은 [Azure AD에 온-프레미스 앱 추가를](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)참조하십시오. <br></br>브라우저의 개발자 도구(인터넷 익스플로러 또는 Microsoft Edge의 F12 도구)를 사용하고 응용 프로그램 페이지에 설명된 대로 게시 문제가 [올바르게 표시되지 않는지](application-proxy-page-appearance-broken-problem.md)확인합니다. <br></br>페이지의 링크에서 끊어진 링크를 해결하기 위한 옵션을 [검토하는 것은 작동하지 않습니다.](application-proxy-page-links-broken-problem.md) |
|7 | 네트워크 대기 시간 확인 | 페이지가 느리게 로드되는 경우 대기 시간을 [줄이기 위한 고려 사항에서](application-proxy-network-topology.md#considerations-for-reducing-latency)네트워크 대기 시간을 최소화하는 방법에 대해 알아봅니다. | 
|8 | 추가 문제 해결 도움말 보기 | 문제가 지속되면 응용 프로그램 프록시 [문제 해결 설명서에서](application-proxy-troubleshoot.md)추가 문제 해결 문서를 찾을 수 있습니다. |

## <a name="next-steps"></a>다음 단계


* [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)
* [기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)
* [애플리케이션 프록시 및 커넥터 오류 문제 해결](application-proxy-troubleshoot.md)
* [Azure AD 애플리케이션 프록시 커넥터를 자동으로 설치하는 방법](application-proxy-register-connector-powershell.md)
