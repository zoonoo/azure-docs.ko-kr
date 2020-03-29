---
title: IE에 대한 Azure 액세스 패널 확장 문제 해결 | 마이크로 소프트 문서
description: 그룹 정책을 사용하여 My Apps 포털용 Internet Explorer 추가 기능을 배포하는 방법
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723913"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>인터넷 익스플로러를 위한 액세스 패널 확장 문제 해결

이 문서는 다음과 같은 문제를 해결하는 데 도움이 됩니다.

* Internet Explorer를 사용하는 중에 My Apps 포털을 통해 응용 프로그램에 액세스할 수 없습니다.
* 소프트웨어를 이미 설치했는데도 "소프트웨어 설치" 메시지가 나타납니다.

관리자인 경우 [그룹 정책을 사용하여 Internet Explorer에 대한 액세스 패널 확장 프로그램을 배포하는 방법을](deploy-access-panel-browser-extension.md)참조하세요.

## <a name="run-the-diagnostic-tool"></a>진단 도구 실행

액세스 패널 진단 도구를 다운로드하고 실행하여 액세스 패널 확장의 설치 문제를 진단할 수 있습니다. 

진단 도구를 다운로드하여 설치하려면 다음을 수행하십시오.

1. [진단 도구를 다운로드하려면 이 링크를 선택합니다.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. 파일을 열고 내용을 컴퓨터에 추출합니다.
1. 도구를 실행하려면 *AccessPanelExtensionDiagnosticTool.js라는* 파일을 마우스 오른쪽 단추로 클릭하고**Microsoft Windows 기반 스크립트 호스트로** >  **열기를**선택합니다.

    ![열기 > Microsoft Windows 기반 스크립트 호스트](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. 나타나는 진단 결과를 검토하고 **예를** 선택하여 문제를 해결합니다. **결과 확인** 대화 상자에 확장이 작동하지 않는 경우 수행할 작업에 대한 정보가 표시됩니다.  
1. 메시지를 읽고 **확인을**선택합니다.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>액세스 패널 확장을 사용할 수 있는지 확인

인터넷 익스플로러에서 액세스 패널 확장을 사용하도록 설정했는지 확인하려면 다음 을 수행합니다.

1. 인터넷 익스플로러에서 창의 오른쪽 상단에 있는 **기어 아이콘을** 선택하고 **인터넷 옵션을**선택합니다.
1. **프로그램** 탭으로 이동하여 **추가 기능 관리를 선택합니다.**
1. **Microsoft 회사** 섹션에서 액세스 패널 **확장 프로그램을** 선택하고 사용 **옵션을**선택합니다.
1. 변경 내용을 저장하려면 열려 있는 Internet Explorer 브라우저 창을 모두 닫습니다. 변경 사항은 다음에 Internet Explorer를 열 때 적용됩니다.

## <a name="enable-extensions-for-inprivate-browsing"></a>비공개 브라우징에 대한 확장 사용

InPrivate 브라우징에 대한 확장을 사용하려면 다음을 수행합니다.

1. 인터넷 익스플로러에서 창의 오른쪽 상단에 있는 **기어 아이콘을** 선택하고 **인터넷 옵션을**선택합니다.
1. **개인 정보** 탭으로 이동하여 **InPrivate 브라우징이 시작될 때 도구 모음 및 확장 해제** 가 확인란이 명확한지 확인합니다.
1. 변경 내용을 저장하려면 열려 있는 Internet Explorer 브라우저 창을 모두 닫습니다. 변경 사항은 다음에 Internet Explorer를 열 때 적용됩니다.

## <a name="uninstall-the-access-panel-extension"></a>액세스 패널 확장 제거

컴퓨터에서 액세스 패널 확장 프로그램을 제거하려면 다음을 수행하십시오.

1. 제어판에서 *제거를*검색합니다.
1. 검색 결과에서 **프로그램 제거를 선택합니다.**

    ![제어판에서 프로그램 제거 옵션 선택](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. 목록에서 **액세스 패널 확장 확장을** 선택하고 **제거를**선택합니다.

    ![액세스 패널 확장 제거](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. 그런 다음 확장을 다시 설치하여 문제가 해결되었는지 확인합니다.

확장을 제거 하는 문제가 실행 하는 경우, 당신은 또한 [마이크로소프트 수정 도구를](https://go.microsoft.com/?linkid=9779673) 사용 하 여 제거할 수 있습니다.

## <a name="related-articles"></a>관련 문서

* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On](what-is-single-sign-on.md)
* [그룹 정책을 사용하여 인터넷 익스플로러에 대한 액세스 패널 확장을 배포하는 방법](deploy-access-panel-browser-extension.md)
