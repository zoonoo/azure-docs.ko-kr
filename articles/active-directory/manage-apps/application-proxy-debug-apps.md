---
title: 응용 프로그램 프록시 응용 프로그램 디버그-Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) 응용 프로그램 프록시 응용 프로그램의 문제를 디버그 합니다.
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
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382066"
---
# <a name="debug-application-proxy-application-issues"></a>응용 프로그램 프록시 응용 프로그램 문제 디버그 

이 문서는 Azure Active Directory (Azure AD) 응용 프로그램 프록시 응용 프로그램의 문제를 해결 하는 데 도움이 됩니다. 응용 프로그램 프록시 서비스를 사용 하 여 온-프레미스 웹 응용 프로그램에 대 한 원격 액세스를 하지만 응용 프로그램에 연결 하는 데 문제가 있는 경우이 순서도를 사용 하 여 응용 프로그램 문제를 디버그 하세요. 

## <a name="before-you-begin"></a>시작하기 전에

응용 프로그램 프록시 문제를 해결할 때 커넥터를 사용 하 여 시작 하는 것이 좋습니다. 먼저 응용 프로그램 프록시 [커넥터 문제 디버그](application-proxy-debug-connectors.md) 에서 문제 해결 흐름을 따라 응용 프로그램 프록시 커넥터가 올바르게 구성 되어 있는지 확인 합니다. 문제가 여전히 발생 하는 경우이 문서로 돌아와서 응용 프로그램 문제를 해결 하세요.  

응용 프로그램 프록시에 대 한 자세한 내용은 다음을 참조 하세요.

- [응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 원격으로 액세스](application-proxy.md)
- [응용 프로그램 프록시 커넥터](application-proxy-connectors.md)
- [커넥터 설치 및 등록](application-proxy-add-on-premises-application.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>응용 프로그램 문제에 대 한 순서도

이 순서도는 응용 프로그램에 연결 하는 것과 관련 된 몇 가지 일반적인 문제를 디버깅 하는 단계를 안내 합니다. 각 단계에 대 한 자세한 내용은 순서도 다음에 나오는 표를 참조 하십시오.

![응용 프로그램 디버깅 단계를 보여 주는 순서도](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | 작업 | 설명 | 
|---------|---------|---------|
|1 | 브라우저를 열고 앱에 액세스 한 다음 자격 증명을 입력 합니다. | 자격 증명을 사용 하 여 앱에 로그인 하 고, [이 회사 앱에 액세스할 수 없는](application-proxy-sign-in-bad-gateway-timeout-error.md)경우와 같은 사용자 관련 오류를 확인 하세요. |
|2 | 앱에 대 한 사용자 할당 확인 | 사용자 계정에 회사 네트워크 내부에서 앱에 액세스할 수 있는 권한이 있는지 확인 한 다음 [응용 프로그램 테스트](application-proxy-add-on-premises-application.md#test-the-application)의 단계를 수행 하 여 앱에 대 한 로그인을 테스트 합니다. 로그인 문제가 지속 되 [는 경우 로그인 오류 문제를 해결 하는 방법](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)을 참조 하세요.  |
|3 | 브라우저를 열고 앱에 액세스를 시도 합니다. | 오류가 즉시 표시 되 면 응용 프로그램 프록시가 올바르게 구성 되어 있는지 확인 하십시오. 특정 오류 메시지에 대 한 자세한 내용은 [응용 프로그램 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)을 참조 하세요.  |
|4 | 사용자 지정 도메인 설정을 확인 하거나 오류를 해결 하십시오. | 페이지가 전혀 표시 되지 않으면 사용자 지정 도메인에 대 한 [작업](application-proxy-configure-custom-domain.md)을 검토 하 여 사용자 지정 도메인이 올바르게 구성 되어 있는지 확인 합니다.<br></br>페이지가 로드 되지 않고 오류 메시지가 표시 되 면 [응용 프로그램 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)을 참조 하 여 오류를 해결 하십시오. <br></br>오류 메시지를 표시 하는 데 20 초 이상이 소요 되는 경우 연결 문제가 있을 수 있습니다. [디버그 응용 프로그램 프록시 커넥터](application-proxy-debug-connectors.md) 문제 해결 문서로 이동 합니다.  |
|5 | 문제가 지속 되 면 커넥터 디버깅으로 이동 합니다. | 프록시와 커넥터 사이 또는 커넥터와 백 엔드 사이에 연결 문제가 있을 수 있습니다. [디버그 응용 프로그램 프록시 커넥터](application-proxy-debug-connectors.md) 문제 해결 문서로 이동 합니다. |
|6 | 모든 리소스를 게시 하 고, 브라우저 개발자 도구를 확인 하 고, 링크를 수정 합니다. | 응용 프로그램에 필요한 모든 이미지, 스크립트 및 스타일 시트가 게시 경로에 포함 되어 있는지 확인 합니다. 자세한 내용은 [AZURE AD에 온-프레미스 앱 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조 하세요. <br></br>브라우저의 개발자 도구 (Internet Explorer 또는 Microsoft Edge의 F12 도구)를 사용 하 여 [응용 프로그램 페이지가 제대로 표시 되지 않음](application-proxy-page-appearance-broken-problem.md)에 설명 된 대로 게시 문제를 확인 합니다. <br></br>페이지의 링크에서 끊어진 링크를 확인 [하는 옵션은 작동 하지 않습니다](application-proxy-page-links-broken-problem.md). |
|7 | 네트워크 대기 시간 확인 | 페이지가 느리게 로드 되는 경우 [대기 시간을 줄이도록](application-proxy-network-topology.md#considerations-for-reducing-latency)네트워크 대기 시간을 최소화 하는 방법에 대해 알아보세요. | 
|8 | 추가 문제 해결 도움말을 참조 하세요. | 문제가 지속 되 면 [응용 프로그램 프록시 문제 해결 설명서](application-proxy-troubleshoot.md)에서 추가 문제 해결 문서를 찾습니다. |

## <a name="next-steps"></a>다음 단계


* [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)
* [기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)
* [애플리케이션 프록시 및 커넥터 오류 문제 해결](application-proxy-troubleshoot.md)
* [Azure AD 애플리케이션 프록시 커넥터를 자동으로 설치하는 방법](application-proxy-register-connector-powershell.md)
