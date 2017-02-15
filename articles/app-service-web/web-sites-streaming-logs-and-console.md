---
title: "스트리밍 로그 및 콘솔"
description: "스트리밍 로그 및 콘솔 개요"
author: btardif
manager: wpickett
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3e50a287-781b-4c6a-8c53-eec261889d7a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7704270d7bba54577f61f3f349dc69dc6ddf08c6


---
# <a name="streaming-logs-and-the-console"></a>스트리밍 로그 및 콘솔
## <a name="streaming-logs"></a>스트리밍 로그
**Azure Portal**은 통합 스트리밍 로그 뷰어를 제공하며, 이 뷰어에서 **App Service** 앱의 추적 이벤트를 실시간으로 볼 수 있습니다.  

이 기능을 설정하려면 몇 가지 간단한 단계를 수행해야 합니다.

* 코드에 추적 쓰기
* 앱에 대해 응용 프로그램 **진단 로그**를 사용하도록 설정
* **Azure portal**의 기본 제공 **스트리밍 로그** UI에서 스트림을 봅니다.

### <a name="how-to-write-traces-in-your-code"></a>코드에서 추적을 쓰는 방법
코드에 추적을 쓰는 작업은 쉽습니다.  C#에서는 다음 코드를 쓰기만 하면 될 정도로 쉽습니다.

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

The Trace class lives in the System.Diagnostics namespace.

node.js 앱에서는 다음 코드를 쓰면 동일한 결과를 얻을 수 있습니다.

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>스트리밍 로그를 사용하고 보는 방법
![][BrowseSitesScreenshot] 진단은 앱별로 사용하도록 설정됩니다. 이 기능을 사용하도록 설정할 사이트로 이동하여 시작합니다.  

![][DiagnosticsLogs] 설정 메뉴에서 **모니터링** 섹션으로 스크롤하고 **(1) 진단 로그**를 클릭합니다. 그런 다음 **응용 프로그램 로깅(파일 시스템)** 또는 **응용 프로그램 로깅(blob)**을 **(2) 활성화**합니다. **수준** 옵션을 통해 캡처할 추적의 심각도 수준을 변경할 수 있습니다. **세부 정보 표시**는 모든 추적 스트리밍을 수집하므로, 이 기능에 익숙해지고 싶으면 이 설정을 지정해야 합니다.

블레이드의 위에 있는 **저장** 을 클릭하면 로그를 볼 수 있습니다.

> [!NOTE]
> 더 높은 **심각도 수준**을 설정하면 로그에 더 많은 리소스를 사용하여 더 상세한 추적이 생성됩니다. **심각도 수준**이 프로덕션 또는 높은 트래픽 사이트에 대해 적절한 세부 정보 표시로 구성되어 있는지 확인합니다. 
> 
> 

![][StreamingLogsScreenshot] Azure Portal 내에서 **스트리밍 로그**를 보려면 설정 메뉴의 **모니터링** 섹션에서 **(1) 로그 스트림**을 클릭합니다. 앱에서 추적 문을 활발하게 쓰는 경우 **(2) 스트리밍 로그 UI**에서 거의 실시간으로 볼 수 있습니다.

## <a name="console"></a>콘솔
**Azure Portal**에서는 앱에 대한 콘솔 액세스를 제공합니다. 앱의 파일 시스템을 탐색하고 powershell/cmd 스크립트를 실행할 수 있습니다. 콘솔 명령을 실행할 때 실행 중인 앱 코드와 동일한 권한 집합이 적용됩니다. 보호된 디렉터리에 대한 액세스 또는 관리자 권한이 필요한 스크립트 실행은 차단됩니다.  

![][ConsoleScreenshot] 설정 메뉴에서 **개발 도구** 섹션으로 스크롤하고 **(1) 콘솔**을 클릭하면 **(2) 콘솔** UI가 오른쪽에 열립니다.

**콘솔**에 친숙해지려면 다음과 같은 기본 명령을 시도하세요.

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png



<!--HONumber=Nov16_HO3-->


