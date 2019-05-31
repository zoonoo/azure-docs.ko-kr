---
title: Azure Active Directory 응용 프로그램 프록시 커넥터-디버그 | Microsoft Docs
description: Azure Active Directory (Azure AD) 응용 프로그램 프록시 커넥터를 사용 하 여 문제를 디버그 합니다.
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
ms.openlocfilehash: c3088ae777fe1a64be218105d36fdb9e01d7b798
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172237"
---
# <a name="debug-application-proxy-connector-issues"></a>응용 프로그램 프록시 커넥터 문제 디버그 

이 문서에서는 Azure Active Directory (Azure AD) 응용 프로그램 프록시 커넥터를 사용 하 여 문제를 해결 합니다. 온-프레미스 웹 응용 프로그램에 대 한 원격 액세스에 대 한 응용 프로그램 프록시 서비스를 사용 하는 응용 프로그램에 연결 하는 데 문제가 있는 경우,이 순서도 사용 하 여 커넥터 문제를 디버깅 합니다. 

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 응용 프로그램 프록시 커넥터를 설치 하 고 문제가 발생은 가정 합니다. 응용 프로그램 프록시 문제를 해결 하는 경우에 응용 프로그램 프록시 커넥터 올바르게 구성 되었는지 확인 하려면이 문제 해결 흐름을 시작 하는 것이 좋습니다. 여전히 문제가 있는 응용 프로그램에 연결을 하는 경우의 문제 해결 흐름을 따릅니다 [디버깅할 응용 프로그램 프록시 응용 프로그램 문제](application-proxy-debug-apps.md)합니다.  


응용 프로그램 프록시 및 해당 커넥터를 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 대 한 원격 액세스](application-proxy.md)
- [응용 프로그램 프록시 커넥터](application-proxy-connectors.md)
- [설치 하 고 커넥터를 등록 합니다.](application-proxy-add-on-premises-application.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>커넥터 문제에 대 한 흐름도

이 순서도 이용 하면 커넥터는 보다 일반적인 문제 중 일부를 디버깅 하는 단계를 안내 합니다. 각 단계에 대 한 자세한 내용은 다음에 나오는 순서도 테이블을 참조 하세요.

![커넥터를 디버깅 하기 위한 단계를 보여 주는 순서도](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | 액션(Action) | 설명 | 
|---------|---------|---------|
|1 | 앱에 할당 된 커넥터 그룹 찾기 | 커넥터가 여러 서버에 설치 되어 있을 경우이 경우 커넥터 이어야 합니다 [커넥터 그룹에 할당 된](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)합니다. 커넥터 그룹에 대한 자세한 내용은 [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)를 참조하세요. |
|2 | 커넥터를 설치 및 그룹 할당 | 설치 된 커넥터에 없는 경우 [설치 및 등록 커넥터](application-proxy-add-on-premises-application.md#install-and-register-a-connector)합니다.<br></br>커넥터 그룹에 할당 되지 경우 참조 [커넥터 그룹에 할당](application-proxy-connector-groups.md#create-connector-groups)합니다.<br></br>응용 프로그램 커넥터 그룹에 할당 되지 경우 참조 [커넥터 그룹에 응용 프로그램 할당](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)합니다.|
|3 | 커넥터 서버의 포트 테스트를 실행 합니다. | 커넥터 서버에서 사용 하 여 포트 테스트를 실행할 [텔넷](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) 또는 포트 443 및 80이 열려 있는 경우를 확인 하려면 도구를 테스트 하는 다른 포트입니다.|
|4 | 도메인 및 포트를 구성 합니다. | [도메인 및 포트에 올바르게 구성 되었는지 확인](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) 커넥터가 제대로 작동 하려면 몇 가지 열려 있어야 하는 포트 및 서버에 액세스할 수 있어야 하는 Url입니다. |
|5 | 백 엔드 프록시를 사용 중인지 확인 합니다. | 커넥터는 백 엔드 프록시 서버를 사용 하 여 또는 무시 하 고 확인 합니다. 자세한 내용은 참조 하세요 [커넥터 프록시 문제를 해결 하 고 서비스 연결 문제](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)합니다. |
|6 | 커넥터 및 업데이터 백 엔드 프록시를 사용 하도록 업데이트 | 백 엔드 프록시를 사용 하는 경우 동일한 프록시 커넥터를 사용 하는지 확인 하는 것이 좋습니다. 문제 해결 및 프록시 서버로 작업 하도록 커넥터를 구성 하는 방법에 대 한 자세한 내용은 참조 하세요 [작업을 기존 온-프레미스 프록시 서버](application-proxy-configure-connectors-with-proxy-servers.md)합니다. |
|7 | 커넥터 서버에서 앱의 내부 URL을 로드 합니다. | 커넥터 서버에서 앱의 내부 URL을 로드 합니다. |
|8 | 내부 네트워크 연결 확인 | 이 디버깅 흐름 진단할 수 없는 내부 네트워크에 연결 문제가 있습니다. 응용 프로그램 커넥터를 작업에 대해 내부적으로 액세스할 수 있어야 합니다. 사용 하도록 설정 하 고에 설명 된 대로 커넥터 이벤트 로그를 볼 수 있습니다 [응용 프로그램 프록시 커넥터](application-proxy-connectors.md#under-the-hood)합니다. |
|9 | 백 엔드에서 제한 시간 값을 늘리려면 | 에 **추가 설정** 응용 프로그램에 대 한 변경를 **백 엔드 응용 프로그램 시간 제한** 로 설정 **긴**합니다. 참조 [Azure AD에 온-프레미스 앱을 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)합니다. |
|10 | 문제가 지속 되 면 대상 특정 흐름 문제, 검토 앱 및 흐름을 디버깅 하는 SSO | 사용 된 [디버깅할 응용 프로그램 프록시 응용 프로그램 문제](application-proxy-debug-apps.md) 흐름 문제 해결. |

## <a name="next-steps"></a>다음 단계


* [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)
* [기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)
* [애플리케이션 프록시 및 커넥터 오류 문제 해결](application-proxy-troubleshoot.md)
* [Azure AD 애플리케이션 프록시 커넥터를 자동으로 설치하는 방법](application-proxy-register-connector-powershell.md)
