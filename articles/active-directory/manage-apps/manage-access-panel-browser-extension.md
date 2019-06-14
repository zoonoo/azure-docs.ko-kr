---
title: IE에 대 한 Azure 액세스 패널 확장 문제 해결 | Microsoft Docs
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
ms.openlocfilehash: f08a02842c97b0f4076a1b311aa918df6d83c592
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65824510"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Internet Explorer 용 액세스 패널 확장 문제 해결

이 문서는 다음과 같은 문제를 해결하는 데 도움이 됩니다.

* Internet Explorer를 사용하는 중에 My Apps 포털을 통해 응용 프로그램에 액세스할 수 없습니다.
* 소프트웨어를 이미 설치했는데도 "소프트웨어 설치" 메시지가 나타납니다.

참조 관리자 라면 [그룹 정책을 사용 하 여 Internet Explorer 용 액세스 패널 확장을 배포 하는 방법을](deploy-access-panel-browser-extension.md)합니다.

## <a name="run-the-diagnostic-tool"></a>진단 도구 실행

다운로드 및 액세스 패널 진단 도구를 실행 하 여 액세스 패널 확장을 사용 하 여 설치 문제를 진단할 수 있습니다. 

다운로드 하 고 진단 도구를 설치 합니다.

1. [진단 도구를 다운로드 하려면이 링크를 선택 합니다.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. 파일을 열고 컴퓨터에 압축을 풉니다.
   
3. 이 도구를 실행 하 라는 파일을 마우스 오른쪽 단추로 *AccessPanelExtensionDiagnosticTool.js* 선택한 **로 열기** > **Microsoft Windows 기반 스크립트 호스트** .
   
    ![열기 > Microsoft Windows 기반 스크립트 호스트](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

4. 표시 되 고 선택할 수 있는 진단 결과 검토 **예** 문제를 해결 합니다. 합니다 **검사 결과** 확장이 작동 하지 않을 경우 수행할 작업에 대 한 정보를 사용 하 여 대화 상자가 나타납니다.  

5. 선택한 메시지를 읽을 **확인**합니다.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>액세스 패널 확장을 사용할 수 있는지 확인

Internet Explorer에서 액세스 패널 확장을 사용 하도록 설정 했으면 확인:

1. Internet Explorer에서 선택 합니다 **기어 아이콘** 창과 선택의 오른쪽 위 모서리에 있는 **인터넷 옵션**합니다.
   
2. 로 이동 합니다 **프로그램** 탭을 선택한 **추가 기능 관리**합니다.
   
3. 선택 **액세스 패널 확장** 에 **Microsoft Corporation** 선택한 섹션 **사용**합니다.
   
4. Internet Explorer 브라우저 창을 모두 닫습니다 변경 내용을 저장 하려면 오픈을 해야 합니다. Internet Explorer를 열면 다음에 변경 내용을 적용 합니다.

## <a name="enable-extensions-for-inprivate-browsing"></a>InPrivate 브라우징에 대 한 확장을 사용 하도록 설정

확장을 사용 하도록 InPrivate 브라우징에 대 한 합니다.

1. Internet Explorer에서 선택 합니다 **기어 아이콘** 창과 선택의 오른쪽 위 모서리에 있는 **인터넷 옵션**합니다.
   
2. 로 이동 합니다 **개인 정보 취급** 탭 하 고 있는지 확인 합니다 **InPrivate 브라우징 시작 시 도구 모음 및 확장을 사용 하지 않도록** 확인란이 선택 취소 되어.
   
3.  Internet Explorer 브라우저 창을 모두 닫습니다 변경 내용을 저장 하려면 오픈을 해야 합니다. Internet Explorer를 열면 다음에 변경 내용을 적용 합니다.

## <a name="uninstall-the-access-panel-extension"></a>액세스 패널 확장 제거

컴퓨터에서 액세스 패널 확장 제거:

1. 제어판에서 검색할 *제거*합니다. 

2. 검색 결과에서 선택 **프로그램 제거**합니다.
   
    ![제거 프로그램을 검색 합니다.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

3. 목록에서 선택 **액세스 패널 확장이** 선택한 **제거**합니다.

    ![액세스 패널 확장을 제거 합니다.](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)
   
4. 그런 다음 확장을 다시 설치하여 문제가 해결되었는지 확인합니다.

확장을 제거 하는 문제에 봉착 한 경우 제거할 수도 있습니다를 사용 하 여 [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) 도구입니다.

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On](what-is-single-sign-on.md)
* [그룹 정책을 사용 하 여 Internet Explorer 용 액세스 패널 확장을 배포 하는 방법](deploy-access-panel-browser-extension.md)

