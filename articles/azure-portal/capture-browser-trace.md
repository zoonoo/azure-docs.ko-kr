---
title: 문제 해결을 위한 브라우저 추적 캡처 | 마이크로 소프트 문서
description: 브라우저 추적에서 네트워크 정보를 캡처하여 Azure 포털의 문제를 해결하는 데 도움이 됩니다.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310699"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>문제 해결을 위한 브라우저 추적 캡처

Azure 포털에서 문제를 해결하는 데 Microsoft 지원에 문의해야 하는 경우 먼저 브라우저 추적 및 몇 가지 추가 정보를 캡처하는 것이 좋습니다. 수집하는 정보는 문제가 발생했을 때 포털에 대한 중요한 세부 정보를 제공할 수 있습니다. 당신이 사용하는 브라우저에서 개발자 도구에 대한이 문서의 단계를 따르십시오 : 구글 크롬이나 마이크로 소프트 에지 (크롬), 마이크로 소프트 에지 (EdgeHTML), 또는 애플 사파리.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>구글 크롬과 마이크로 소프트 에지 (크롬)

구글 크롬과 마이크로 소프트 에지 (크롬)는 모두 [크롬 오픈 소스 프로젝트를](https://www.chromium.org/Home)기반으로합니다. 다음 단계는 두 브라우저에서 매우 유사한 개발자 도구를 사용하는 방법을 보여 준다. 자세한 내용은 [크롬 개발자 도구](https://developers.google.com/web/tools/chrome-devtools) 및 마이크로소프트 [에지 (크롬) 개발자 도구를](/microsoft-edge/devtools-guide-chromium)참조 하십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 추적에 로그인과 관련된 중요한 정보가 포함되지 않도록 추적을 시작하기 _전에_ 로그인하는 것이 중요합니다. 

1. [단계 레코더를](https://support.microsoft.com/help/22878/windows-10-record-steps)사용하여 포털에서 수행한 단계를 기록하기 시작합니다.

1. 포털에서 문제가 발생하기 직전의 단계로 이동합니다.

1. F12를 ![누르거나 브라우저 설정](media/capture-browser-trace/chromium-icon-settings.png) > 아이콘의 스크린 샷을 선택**하십시오 더 많은 도구** > **개발자 도구**.

1. 기본적으로 브라우저는 현재 로드된 페이지에 대해서만 추적 정보를 유지합니다. 브라우저가 두 개 이상의 페이지로 이동해야하는 경우에도 브라우저가 모든 추적 정보를 유지하도록 다음 옵션을 설정합니다.

    1. **네트워크** 탭을 선택한 다음 **로그 보존을**선택합니다.

          !["로그 보존" 스크린샷](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. **콘솔** 탭을 선택하고 **콘솔 설정을**선택한 다음 **로그 보존을**선택합니다. 설정 창을 닫기 위해 **콘솔 설정을** 다시 선택합니다.

          !["로그 보존" 스크린샷](media/capture-browser-trace/chromium-console-preserve-log.png)

1. **네트워크** 탭을 선택한 다음 **네트워크 로그 기록 중지** 및 **지우기**를 선택합니다.

    !["네트워크 로그 녹화 중지" 및 "지우기" 스크린샷](media/capture-browser-trace/chromium-stop-clear-session.png)

1. **네트워크 로그 기록을**선택한 다음 포털에서 문제를 재현합니다.

    !["프로파일링 세션 시작" 스크린샷](media/capture-browser-trace/chromium-start-session.png)

    다음 이미지와 유사한 세션 출력이 표시됩니다.

    ![브라우저 추적 결과의 스크린샷](media/capture-browser-trace/chromium-browser-trace-results.png)

1. 예기치 않은 포털 동작을 재현한 후 **네트워크 로그 기록 중지를**선택한 다음 **HAR 내보내기를** 선택하고 파일을 저장합니다.

    !["내보내기 HAR"의 스크린 샷](media/capture-browser-trace/chromium-network-export-har.png)

1. 단계 레코더를 중지하고 파일을 저장합니다.

1. 브라우저 개발자 도구 창에서 **콘솔** 탭을 마우스 오른쪽 단추로 클릭한 다음 **저장을**선택하여 콘솔 출력을 텍스트 파일에 저장합니다.

    ![콘솔 출력스크린샷](media/capture-browser-trace/chromium-console-select.png)

1. HAR 파일, 콘솔 출력 및 화면 녹화를 .zip과 같은 압축 된 형식으로 패키징하고 Microsoft 지원과 공유하십시오.

## <a name="microsoft-edge-edgehtml"></a>마이크로소프트 에지 (에지HTML)

다음 단계는 Microsoft Edge(EdgeHTML)에서 개발자 도구를 사용하는 방법을 보여 주며, 자세한 내용은 [Microsoft Edge(EdgeHTML) 개발자 도구를](/microsoft-edge/devtools-guide)참조하십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 추적에 로그인과 관련된 중요한 정보가 포함되지 않도록 추적을 시작하기 _전에_ 로그인하는 것이 중요합니다. 

1. [단계 레코더를](https://support.microsoft.com/help/22878/windows-10-record-steps)사용하여 포털에서 수행한 단계를 기록하기 시작합니다.

1. 포털에서 문제가 발생하기 직전의 단계로 이동합니다.

1. F12를 ![누르거나 브라우저 설정](media/capture-browser-trace/edge-icon-settings.png) > 아이콘의 스크린 샷을 선택**하십시오 더 많은 도구** > **개발자 도구**.

1. 기본적으로 브라우저는 현재 로드된 페이지에 대해서만 추적 정보를 유지합니다. 브라우저가 두 개 이상의 페이지로 이동해야하는 경우에도 브라우저가 모든 추적 정보를 유지하도록 다음 옵션을 설정합니다.

    1. **네트워크** 탭을 선택한 다음 **탐색에서 항목 지우기**옵션을 선택 취소합니다.

          !["탐색 시 항목 지우기" 스크린샷](media/capture-browser-trace/edge-network-clear-entries.png)

    1. **콘솔** 탭을 선택한 다음 **로그 보존을**선택합니다.

          !["로그 보존" 스크린샷](media/capture-browser-trace/edge-console-preserve-log.png)

1. **네트워크** 탭을 선택한 다음 **프로파일링 세션 중지** 및 **세션 지우기**를 선택합니다.

    !["프로파일링 세션 중지" 및 "세션 지우기" 스크린샷](media/capture-browser-trace/edge-stop-clear-session.png)

1. **프로파일링 세션 시작을**선택한 다음 포털에서 문제를 재현합니다.

    !["프로파일링 세션 시작" 스크린샷](media/capture-browser-trace/edge-start-session.png)

    다음 이미지와 유사한 세션 출력이 표시됩니다.

    ![브라우저 추적 결과의 스크린샷](media/capture-browser-trace/edge-browser-trace-results.png)

1. 예기치 않은 포털 동작을 재현한 후 **프로파일링 세션 중지를**선택한 다음 **내보내기를 HAR로** 선택하고 파일을 저장합니다.

    !["HAR로 내보내기"의 스크린 샷](media/capture-browser-trace/edge-network-export-har.png)

1. 단계 레코더를 중지하고 파일을 저장합니다.

1. 브라우저 개발자 도구 창에서 **콘솔** 탭을 선택하고 창을 확장합니다. 콘솔 출력의 시작 부분에 커서를 배치한 다음 출력의 전체 내용을 드래그하고 선택합니다. 마우스 오른쪽 단추로 클릭한 다음 **복사**를 선택하고 콘솔 출력을 텍스트 파일에 저장합니다.

    ![콘솔 출력스크린샷](media/capture-browser-trace/edge-console-select.png)

1. HAR 파일, 콘솔 출력 및 화면 녹화를 .zip과 같은 압축 된 형식으로 패키징하고 Microsoft 지원과 공유하십시오.

## <a name="apple-safari"></a>Apple Safari

다음 단계는 Apple Safari에서 개발자 도구를 사용하는 방법을 보여 준다. 자세한 내용은 [사파리 개발자 도구 개요를](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)참조하십시오.

1. Apple Safari에서 개발자 도구를 사용하도록 설정합니다.

    1. **사파리를**선택한 다음 **환경 설정을 선택합니다.**

        ![사파리 환경 설정의 스크린 샷](media/capture-browser-trace/safari-preferences.png)

    1. **고급** 탭을 선택한 다음 **메뉴 모음에서 메뉴 개발 표시를**선택합니다.

        ![사파리 고급 환경 설정의 스크린 샷](media/capture-browser-trace/safari-show-develop-menu.png)

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 추적에 로그인과 관련된 중요한 정보가 포함되지 않도록 추적을 시작하기 _전에_ 로그인하는 것이 중요합니다. 

1. 포털에서 수행한 단계를 기록하기 시작합니다. 자세한 내용은 [Mac에서 화면을 기록하는 방법을 참조하세요.](https://support.apple.com/HT208721)

1. 포털에서 문제가 발생하기 직전의 단계로 이동합니다.

1. **개발을**선택한 다음 **웹 검사기 표시를**선택합니다.

    !["웹 검사기 표시" 스크린샷](media/capture-browser-trace/safari-show-web-inspector.png)

1. 기본적으로 브라우저는 현재 로드된 페이지에 대해서만 추적 정보를 유지합니다. 브라우저가 두 개 이상의 페이지로 이동해야하는 경우에도 브라우저가 모든 추적 정보를 유지하도록 다음 옵션을 설정합니다.

    1. **네트워크** 탭을 선택한 다음 **로그 보존을**선택합니다.

          !["로그 보존" 스크린샷](media/capture-browser-trace/safari-network-preserve-log.png)

    1. **콘솔** 탭을 선택한 다음 **로그 보존을**선택합니다.

          !["로그 보존" 스크린샷](media/capture-browser-trace/safari-console-preserve-log.png)

1. **네트워크** 탭을 선택한 다음 네트워크 항목 지우기 선택을 **선택합니다.**

    !["네트워크 항목 지우기" 스크린샷](media/capture-browser-trace/safari-clear-session.png)

1. 포털에서 문제를 재현합니다. 다음 이미지와 유사한 세션 출력이 표시됩니다.

    ![브라우저 추적 결과의 스크린샷](media/capture-browser-trace/safari-browser-trace-results.png)

1. 예기치 않은 포털 동작을 재현한 후 **내보내기를** 선택하고 파일을 저장합니다.

    !["내보내기" 스크린샷](media/capture-browser-trace/safari-network-export-har.png)

1. 화면 레코더를 중지하고 파일을 저장합니다.

1. 브라우저 개발자 도구 창에서 **콘솔** 탭을 선택하고 창을 확장합니다. 콘솔 출력의 시작 부분에 커서를 배치한 다음 출력의 전체 내용을 드래그하고 선택합니다. 명령-C를 사용하여 출력을 복사하고 텍스트 파일에 저장합니다.

    ![콘솔 출력스크린샷](media/capture-browser-trace/safari-console-select.png)

1. HAR 파일, 콘솔 출력 및 화면 녹화를 .zip과 같은 압축 된 형식으로 패키징하고 Microsoft 지원과 공유하십시오.

## <a name="next-steps"></a>다음 단계

[Azure 포털 개요](azure-portal-overview.md)