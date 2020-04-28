---
title: IE 용 Azure 액세스 패널 확장 문제 해결 | Microsoft Docs
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
ms.locfileid: "67723913"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Internet Explorer 용 액세스 패널 확장 문제 해결

이 문서는 다음과 같은 문제를 해결하는 데 도움이 됩니다.

* Internet Explorer를 사용하는 중에 My Apps 포털을 통해 응용 프로그램에 액세스할 수 없습니다.
* 소프트웨어를 이미 설치했는데도 "소프트웨어 설치" 메시지가 나타납니다.

관리자 인 경우 [그룹 정책를 사용 하 여 Internet Explorer 용 액세스 패널 확장을 배포 하는 방법](deploy-access-panel-browser-extension.md)을 참조 하세요.

## <a name="run-the-diagnostic-tool"></a>진단 도구 실행

액세스 패널 진단 도구를 다운로드 하 고 실행 하 여 액세스 패널 확장으로 설치 문제를 진단할 수 있습니다. 

진단 도구를 다운로드 하 여 설치 하려면:

1. [진단 도구를 다운로드 하려면이 링크를 선택 합니다.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. 파일을 열고 컴퓨터에 콘텐츠를 추출 합니다.
1. 도구를 실행 하려면 *AccessPanelExtensionDiagnosticTool* 이라는 파일을 마우스 오른쪽 단추로 클릭 하 고 > **Microsoft Windows 기반 스크립트 호스트** **를 사용 하 여 열기**를 선택 합니다.

    ![열기 > Microsoft Windows 기반 스크립트 호스트](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. 표시 되는 진단 결과를 검토 하 고 **예** 를 선택 하 여 문제를 해결 합니다. 확장이 작동 하지 않는 경우 수행할 작업에 대 한 정보가 포함 된 **확인 결과** 대화 상자가 나타납니다.  
1. 메시지를 읽고 **확인**을 선택 합니다.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>액세스 패널 확장을 사용할 수 있는지 확인

Internet Explorer에서 액세스 패널 확장을 사용 하도록 설정 했는지 확인 하려면:

1. Internet Explorer에서 창의 오른쪽 위 모퉁이에 있는 **기어 아이콘** 을 선택 하 고 **인터넷 옵션**을 선택 합니다.
1. **프로그램** 탭으로 이동 하 여 **추가 기능 관리**를 선택 합니다.
1. **Microsoft Corporation** 섹션에서 **액세스 패널 확장** 을 선택 하 고 **사용**을 선택 합니다.
1. 변경 내용을 저장 하려면 열려 있는 Internet Explorer 브라우저 창을 모두 닫습니다. 변경 내용은 다음에 Internet Explorer를 열 때 적용 됩니다.

## <a name="enable-extensions-for-inprivate-browsing"></a>InPrivate 브라우징에 대 한 확장 사용

InPrivate 브라우징에 대 한 확장을 사용 하도록 설정 하려면:

1. Internet Explorer에서 창의 오른쪽 위 모퉁이에 있는 **기어 아이콘** 을 선택 하 고 **인터넷 옵션**을 선택 합니다.
1. **개인 정보** 탭으로 이동 하 고 **InPrivate 브라우징 시작 시 도구 모음 및 확장 프로그램 사용 안 함** 확인란이 선택 취소 되어 있는지 확인 합니다.
1. 변경 내용을 저장 하려면 열려 있는 Internet Explorer 브라우저 창을 모두 닫습니다. 변경 내용은 다음에 Internet Explorer를 열 때 적용 됩니다.

## <a name="uninstall-the-access-panel-extension"></a>액세스 패널 확장 제거

컴퓨터에서 액세스 패널 확장을 제거 하려면 다음을 수행 합니다.

1. 제어판에서 *제거*를 검색 합니다.
1. 검색 결과에서 **프로그램 제거**를 선택 합니다.

    ![제어판에서 프로그램 제거 옵션을 선택 합니다.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. 목록에서 **액세스 패널 확장** 을 선택 하 고 **제거**를 선택 합니다.

    ![액세스 패널 확장 제거](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. 그런 다음 확장을 다시 설치하여 문제가 해결되었는지 확인합니다.

확장을 제거 하는 동안 문제가 발생 하는 경우 [Microsoft Fix it](https://go.microsoft.com/?linkid=9779673) 도구를 사용 하 여 제거할 수도 있습니다.

## <a name="related-articles"></a>관련된 문서

* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On](what-is-single-sign-on.md)
* [그룹 정책를 사용 하 여 Internet Explorer 용 액세스 패널 확장을 배포 하는 방법](deploy-access-panel-browser-extension.md)
