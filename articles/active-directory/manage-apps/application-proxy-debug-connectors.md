---
title: 응용 프로그램 프록시 커넥터 디버깅-Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) 응용 프로그램 프록시 커넥터를 사용 하 여 문제를 디버그 합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 7e7a1de24e5032b2dade2f325560fd6964c892d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86145688"
---
# <a name="debug-application-proxy-connector-issues"></a>애플리케이션 프록시 커넥터 디버그 이슈 

이 문서는 Azure Active Directory (Azure AD) 응용 프로그램 프록시 커넥터와 관련 된 문제를 해결 하는 데 도움이 됩니다. 응용 프로그램 프록시 서비스를 사용 하 여 온-프레미스 웹 응용 프로그램에 대 한 원격 액세스를 하지만 응용 프로그램에 연결 하는 데 문제가 있는 경우이 순서도를 사용 하 여 커넥터 문제를 디버그 하세요. 

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 응용 프로그램 프록시 커넥터를 설치 했으며 문제가 있는 것으로 가정 합니다. 응용 프로그램 프록시 문제를 해결할 때는이 문제 해결 흐름으로 시작 하 여 응용 프로그램 프록시 커넥터가 올바르게 구성 되어 있는지 확인 하는 것이 좋습니다. 응용 프로그램에 연결 하는 데 문제가 여전히 발생 하는 경우 [응용 프로그램 프록시 응용 프로그램 디버그 문제](application-proxy-debug-apps.md)에서 문제 해결 흐름을 따릅니다.  


응용 프로그램 프록시 및 해당 커넥터를 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 원격으로 액세스](application-proxy.md)
- [응용 프로그램 프록시 커넥터](application-proxy-connectors.md)
- [커넥터 설치 및 등록](application-proxy-add-on-premises-application.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>커넥터 문제에 대 한 순서도

이 순서도는 일반적인 몇 가지 커넥터 문제를 디버깅 하는 단계를 안내 합니다. 각 단계에 대 한 자세한 내용은 순서도 다음에 나오는 표를 참조 하십시오.

![커넥터 디버깅 단계를 보여 주는 순서도](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| 단계 | 작업 | 설명 |
|---------|---------|---------|
|1 | 앱에 할당 된 커넥터 그룹 찾기 | 커넥터를 여러 서버에 설치 했을 수 있습니다 .이 경우 커넥터는 커넥터 [그룹에 할당](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)되어야 합니다. 커넥터 그룹에 대한 자세한 내용은 [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)를 참조하세요. |
|2 | 커넥터 설치 및 그룹 할당 | 커넥터를 설치 하지 않은 경우에 [는 커넥터 설치 및 등록](application-proxy-add-on-premises-application.md#install-and-register-a-connector)을 참조 하세요.<br></br> 커넥터를 설치 하는 데 문제가 있는 경우 [커넥터를 설치](application-proxy-connector-installation-problem.md)하는 문제를 참조 하세요.<br></br> 커넥터가 그룹에 할당 되지 않은 경우 [그룹에 커넥터 할당](application-proxy-connector-groups.md#create-connector-groups)을 참조 하세요.<br></br>응용 프로그램이 커넥터 그룹에 할당 되지 않은 경우 [커넥터 그룹에 응용 프로그램 할당](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)을 참조 하세요.|
|3 | 커넥터 서버에서 포트 테스트 실행 | 커넥터 서버에서 [텔넷](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) 또는 기타 포트 테스트 도구를 사용 하 여 포트 [443 및 80이 열려](application-proxy-add-on-premises-application.md#open-ports)있는지 확인 하는 방식으로 포트 테스트를 실행 합니다.|
|4 | 도메인 및 포트 구성 | [도메인 및 포트가 올바르게 구성 되어 있는지 확인](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) 합니다. 커넥터가 제대로 작동 하려면 열려 있어야 하는 특정 포트와 서버에서 액세스할 수 있어야 하는 Url이 있습니다. |
|5 | 백 엔드 프록시가 사용 중인지 확인 합니다. | 커넥터에서 백 엔드 프록시 서버를 사용 하 고 있는지, 아니면 무시 하는지 확인 하십시오. 자세한 내용은 [커넥터 프록시 문제 및 서비스 연결 문제 해결](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)을 참조 하세요. |
|6 | 백 엔드 프록시를 사용 하도록 커넥터 및 업데이트 프로그램 업데이트 | 백 엔드 프록시가 사용 중인 경우 커넥터가 동일한 프록시를 사용 하 고 있는지 확인 하는 것이 좋습니다. 프록시 서버를 사용 하도록 커넥터를 구성 하 고 문제를 해결 하는 방법에 대 한 자세한 내용은 [기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)을 참조 하세요. |
|7 | 커넥터 서버에서 앱의 내부 URL을 로드 합니다. | 커넥터 서버에서 앱의 내부 URL을 로드 합니다. |
|8 | 내부 네트워크 연결 확인 | 내부 네트워크에이 디버깅 흐름이 진단할 수 없는 연결 문제가 있습니다. 커넥터를 사용 하려면 응용 프로그램이 내부적으로 액세스 가능 해야 합니다. [응용 프로그램 프록시 커넥터](application-proxy-connectors.md#under-the-hood)에 설명 된 대로 커넥터 이벤트 로그를 사용 하도록 설정 하 고 볼 수 있습니다. |
|9 | 백 엔드에서 시간 제한 값을 늘립니다. | 응용 프로그램에 대 한 **추가 설정** 에서 **백 엔드 응용 프로그램 시간 제한** 설정을 **Long**으로 변경 합니다. [AZURE AD에 온-프레미스 앱 추가를](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)참조 하세요. |
|10 | 문제가 지속 되 면 특정 흐름 문제를 해결 하 고 앱 및 SSO 디버깅 흐름을 검토 합니다. | [응용 프로그램 프록시 응용 프로그램 디버그 문제](application-proxy-debug-apps.md) 해결 흐름을 사용 합니다. |

## <a name="next-steps"></a>다음 단계


* [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)
* [기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)
* [애플리케이션 프록시 및 커넥터 오류 문제 해결](application-proxy-troubleshoot.md)
* [Azure AD 애플리케이션 프록시 커넥터를 자동으로 설치하는 방법](application-proxy-register-connector-powershell.md)
