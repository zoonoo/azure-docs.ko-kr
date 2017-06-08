---
title: "오류 진단 - Azure Logic Apps | Microsoft Docs"
description: "Logic Apps이 실패하는 경우를 이해하는 일반적인 방법"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 25f4520bae3089ce9f11efcf6acd176255043e70
ms.contentlocale: ko-kr
ms.lasthandoff: 03/10/2017


---
# <a name="diagnose-logic-app-failures"></a>논리 앱 오류 진단
Logic Apps에서 문제 또는 오류가 발생하는 경우 오류의 원인을 파악하는 데 도움이 되는 방법이 몇 가지 있습니다.  

## <a name="azure-portal-tools"></a>Azure 포털 도구
Azure 포털에서는 각 단계에서 각 논리 앱을 진단하는 여러 가지 도구를 제공합니다.

### <a name="trigger-history"></a>트리거 기록

각 논리 앱에는 하나 이상의 트리거가 있습니다. 앱이 실행되지 않는다는 점을 알아챈 경우 자세한 정보에 대한 트리거 기록을 먼저 확인합니다. 트리거 기록은 논리 앱의 주 블레이드에서 액세스할 수 있습니다.

![트리거 기록 찾기][1]

트리거 기록은 만든 논리 앱의 모든 트리거 시도를 나열합니다. 각 트리거 시도를 클릭하여 세부 정보 특히, 트리거 시도로 생성된 모든 입력 또는 출력을 자세히 찾아볼 수 있습니다. 실패한 트리거를 찾는 경우 트리거 시도를 선택하고 **출력** 링크를 선택하여 유효하지 않은 FTP 자격 증명과 같은 생성된 오류 메시지를 검토합니다.

여러 상태가 표시될 수 있습니다.

* **생략**. 끝점을 폴링하여 데이터를 확인하고, 사용할 수 있는 데이터가 없다는 응답을 받았습니다.
* **성공**. 트리거가 데이터를 사용할 수 있다는 응답을 받았습니다. 이 상태는 수동 트리거, 되풀이 트리거 또는 폴링 트리거로 발생할 수 있습니다. 이 상태는 일반적으로 **발생(Fired)** 상태와 함께 발생하지만 코드 보기에서 충족되지 않는 조건 또는 SplitOn 명령이 있는 경우에는 발생하지 않을 수 있습니다.
* **실패**. 오류가 발생했습니다.

#### <a name="start-a-trigger-manually"></a>트리거 수동 시작

논리 앱에서 사용 가능한 트리거를 즉시(다음 되풀이까지 기다리지 않고) 확인하려면 주 블레이드에서 **트리거 선택** 을 클릭합니다. 예를 들어 Dropbox 트리거로 이 단추를 클릭하면 새 파일에 대해 Dropbox를 즉시 폴링하는 워크플로가 발생합니다.

### <a name="run-history"></a>실행 기록

발생한 트리거는 모두 실행 중에 발생합니다. 주 블레이드에서 실행 정보에 액세스할 수 있습니다. 여기에는 워크플로에서 일어난 일을 이해할 수 있는 세부 정보가 많이 있습니다.

![실행 기록 찾기][2]

실행은 다음 상태 중 하나를 표시합니다.

* **성공**. 모든 작업에 성공했습니다. 오류가 발생한 경우 나중에 워크플로에서 발생한 작업으로 처리되었습니다. 즉, 작업이 실패하고 실행되도록 설정된 작업에서 오류를 처리했습니다.
* **실패**. 하나 이상의 작업이 실패하였고, 그중 워크플로에서 나중에 발생하여 처리하지 못한 작업이 있습니다.
* **취소**. 워크플로가 실행 중이지만 취소 요청을 받았습니다.
* **실행 중**. 워크플로가 현재 실행 중입니다. 이 상태는 제한되는 워크플로 또는 현재 요금제로 인해 발생할 수 있습니다. 자세한 내용은 [가격 책정 페이지의 작업 제한](https://azure.microsoft.com/pricing/details/app-service/plans/)을 참조하세요. 진단 구성(실행 기록 아래에 나타나는 차트)은 발생한 제한 이벤트에 관한 정보를 제공할 수도 있습니다.

실행 기록에서 자세한 내용을 확인할 수 있습니다.  

#### <a name="trigger-outputs"></a>트리거 출력

트리거 출력은 트리거에서 온 데이터를 보여 줍니다. 이러한 출력은 모든 속성이 예상대로 반환되었는지 확인하는 데 도움이 됩니다.

> [!NOTE]
> 이해할 수 없는 콘텐츠가 있는 경우 Azure Logic Apps에서 [다양한 콘텐츠 유형을 처리](../logic-apps/logic-apps-content-type.md)하는 방법을 알아봅니다.
> 

![트리거 출력 예제][3]

#### <a name="action-inputs-and-outputs"></a>작업 입력 및 출력

작업에서 수신한 입력 및 출력을 드릴인할 수 있습니다. 이 데이터는 출력 크기와 셰이프를 이해하고 생성될 수 있는 오류 메시지를 찾는 데에도 유용합니다.

![작업 입력 및 출력][4]

## <a name="debug-workflow-runtime"></a>워크플로 런타임 디버그

입력, 출력 및 실행 트리거를 모니터링하는 것 외에도 워크플로에 디버깅에 도움이 되는 몇 가지 단계를 추가할 수 있습니다. 
[RequestBin](http://requestb.in) 이 있습니다. RequestBin을 사용하면 HTTP 요청의 크기, 모양 및 형식을 정확하게 파악하도록 HTTP 검사기를 설정할 수 있습니다. RequestBin을 만들고 URL을 테스트하려는 본문 콘텐츠(예: 식, 다른 단계 출력)와 논리 앱 HTTP POST 작업에 붙여넣을 수 있습니다. 논리 앱을 실행한 후에는 RequestBin을 새로 고쳐 Logic Apps 엔진에서 생성되는 경우 요청이 어떻게 형성되는지 확인할 수 있습니다.

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png

