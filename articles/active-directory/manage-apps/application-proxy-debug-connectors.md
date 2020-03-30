---
title: 디버그 응용 프로그램 프록시 커넥터 - Azure Active Directory | 마이크로 소프트 문서
description: Azure Active Directory(Azure AD) 응용 프로그램 프록시 커넥터에 대한 디버그 문제.
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
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72311842"
---
# <a name="debug-application-proxy-connector-issues"></a>애플리케이션 프록시 커넥터 디버그 이슈 

이 문서에서는 Azure Active Directory(Azure AD) 응용 프로그램 프록시 커넥터문제를 해결하는 데 도움이 됩니다. 온-프레미스 웹 응용 프로그램에 대한 원격 액세스를 위해 응용 프로그램 프록시 서비스를 사용하지만 응용 프로그램에 연결하는 데 문제가 있는 경우 이 순서도를 사용하여 커넥터 문제를 디버깅합니다. 

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 응용 프로그램 프록시 커넥터를 설치했으며 문제가 있다고 가정합니다. 응용 프로그램 프록시 문제를 해결할 때이 문제 해결 흐름으로 시작하여 응용 프로그램 프록시 커넥터가 올바르게 구성되었는지 확인하는 것이 좋습니다. 여전히 응용 프로그램에 연결하는 데 문제가 있는 경우 [디버그 응용 프로그램 프록시 응용 프로그램 문제의](application-proxy-debug-apps.md)문제 해결 흐름을 따릅니다.  


응용 프로그램 프록시 및 커넥터 사용에 대한 자세한 내용은 다음을 참조하십시오.

- [응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 대한 원격 액세스](application-proxy.md)
- [응용 프로그램 프록시 커넥터](application-proxy-connectors.md)
- [커넥터 설치 및 등록](application-proxy-add-on-premises-application.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>커넥터 문제에 대한 순서도

이 순서도는 몇 가지 일반적인 커넥터 문제를 디버깅하는 단계를 안내합니다. 각 단계에 대한 자세한 내용은 순서도 다음의 표를 참조하십시오.

![커넥터 디버깅 단계를 보여주는 순서도](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | 작업 | 설명 | 
|---------|---------|---------|
|1 | 앱에 할당된 커넥터 그룹 찾기 | 커넥터가 여러 서버에 설치되어 있을 수 있으며, 이 경우 커넥터를 [커넥터 그룹에 할당해야](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)합니다. 커넥터 그룹에 대한 자세한 내용은 [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)를 참조하세요. |
|2 | 커넥터 를 설치하고 그룹 할당 | 커넥터가 설치되어 있지 않은 경우 [커넥터 설치 및 등록을](application-proxy-add-on-premises-application.md#install-and-register-a-connector)참조하십시오.<br></br> 커넥터 설치에 문제가 있는 경우 [커넥터 를 설치하는 데 문제가](application-proxy-connector-installation-problem.md)있음을 참조하세요.<br></br> 커넥터가 그룹에 할당되지 않은 경우 [연결선 할당을 그룹에 참조하세요.](application-proxy-connector-groups.md#create-connector-groups)<br></br>응용 프로그램이 커넥터 그룹에 할당되지 않은 경우 [커넥터 그룹에 응용 프로그램 할당을](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)참조하세요.|
|3 | 커넥터 서버에서 포트 테스트 실행 | 커넥터 서버에서 [텔넷](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) 또는 기타 포트 테스트 도구를 사용하여 포트 443과 80이 열려 있는지 확인하여 포트 테스트를 실행합니다.|
|4 | 도메인 및 포트 구성 | [도메인과 포트가 올바르게 구성되었는지 확인](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) 커넥터가 제대로 작동하려면 열려 있어야 하는 특정 포트와 서버가 액세스할 수 있어야 하는 URL이 있습니다. |
|5 | 백 엔드 프록시가 사용 중인지 확인 | 커넥터가 백 엔드 프록시 서버를 사용하고 있는지 또는 이를 우회하는지 확인합니다. 자세한 내용은 [커넥터 프록시 문제 및 서비스 연결 문제를 해결합니다.](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues) |
|6 | 백 엔드 프록시를 사용하도록 커넥터 및 업데이터 업데이트 | 백 엔드 프록시가 사용 중이면 커넥터가 동일한 프록시를 사용하고 있는지 확인해야 합니다. 프록시 서버에서 작동하도록 커넥터 문제 해결 및 구성에 대한 자세한 내용은 [기존 온-프레미스 프록시 서버에서 작업참조를](application-proxy-configure-connectors-with-proxy-servers.md)참조하세요. |
|7 | 커넥터 서버에서 앱의 내부 URL로드 | 커넥터 서버에서 앱의 내부 URL을 로드합니다. |
|8 | 내부 네트워크 연결 확인 | 내부 네트워크에 이 디버깅 흐름을 진단할 수 없는 연결 문제가 있습니다. 커넥터가 작동하려면 응용 프로그램에 내부적으로 액세스할 수 있어야 합니다. [응용 프로그램 프록시 커넥터에](application-proxy-connectors.md#under-the-hood)설명된 대로 커넥터 이벤트 로그를 활성화하고 볼 수 있습니다. |
|9 | 백 엔드의 시간 시간 값 길이 를 길게 합니다. | 응용 프로그램의 **추가 설정에서** **백 엔드 응용 프로그램 시간 설정** 설정을 **Long으로**변경합니다. [Azure AD에 온-프레미스 앱 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조하십시오. |
|10 | 문제가 지속되면 특정 흐름 문제, 검토 앱 및 SSO 디버깅 흐름을 대상으로 지정합니다. | [디버그 응용 프로그램 프록시 응용 프로그램 문제](application-proxy-debug-apps.md) 해결 흐름을 사용 합니다. |

## <a name="next-steps"></a>다음 단계


* [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)
* [기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)
* [애플리케이션 프록시 및 커넥터 오류 문제 해결](application-proxy-troubleshoot.md)
* [Azure AD 애플리케이션 프록시 커넥터를 자동으로 설치하는 방법](application-proxy-register-connector-powershell.md)
