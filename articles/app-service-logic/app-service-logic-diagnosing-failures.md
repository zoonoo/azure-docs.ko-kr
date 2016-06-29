<properties
   pageTitle="Logic Apps 오류 진단 | Microsoft Azure"
   description="Logic Apps이 실패하는 경우를 파악하는 일반적인 방법을 알아보기"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/18/2016"
   ms.author="jehollan"/>

# 논리 앱 오류 진단

Azure 앱 서비스에서 Logic Apps 기능에 문제가 있거나 문제가 발생할 경우, 오류의 원인을 파악하는 데 도움이 되는 방법이 몇 가지 있습니다.

## Azure 포털 도구

Azure 포털에서는 각 단계에서 각 논리 앱을 진단하는 여러 가지 도구를 제공합니다.

### 트리거 기록

각 논리 앱에는 하나 이상의 트리거가 있습니다. 앱이 실행되지 않을 경우, 가장 먼저 트리거 기록에서 자세한 정보를 찾습니다. 트리거 기록은 논리 앱 주 블레이드에서 액세스할 수 있습니다.

![트리거 기록 찾기][1]

여기에는 논리 앱의 모든 트리거 시도 목록이 포함됩니다. 각 항목을 클릭하여 다음 수준의 세부 정보를 얻을 수 있습니다(특히, 트리거 시도로 생성된 모든 입력 또는 출력). '실패(Failed)' 트리거가 있는 경우 트리거 시도를 클릭하고 **출력** 링크를 자세히 살펴 생성될 수 있는 모든 오류 메시지를 모두 파악합니다(예: 잘못된 FTP 자격 증명).

여러 상태가 표시될 수 있습니다.

* **생략**. 끝점을 폴링하여 데이터를 확인하고, 사용할 수 있는 데이터가 없다는 응답을 받았습니다.
* **성공**. 트리거가 데이터를 사용할 수 있다는 응답을 받았습니다. 수동 트리거, 되풀이 트리거 또는 폴링 트리거일 수 있습니다. **발생(Fired)**이 함께 포함될 수 있지만 코드 보기에 충족되지 않는 조건 또는 SplitOn이 있는 경우에는 포함되지 않을 수 있습니다.
* **실패**. 오류가 발생했습니다.

#### 트리거 수동 시작

논리 앱에서 사용 가능한 트리거를 즉시(다음 되풀이까지 기다리지 않고) 확인하려면 주 블레이드에서 **트리거 선택**을 클릭하여 확인합니다. 예를 들어 Dropbox 트리거로 이 단추를 클릭하면 새 파일에 대해 Dropbox를 즉시 폴링하는 워크플로가 발생합니다.

### 실행 기록

발생한 후 실행된 모든 트리거가 있습니다. 주 블레이드에서 실행 정보에 액세스할 수 있습니다. 여기에는 워크플로에서 일어난 일을 이해하는 데 유용한 정보가 많이 있습니다.

![실행 기록 찾기][2]

실행은 다음 상태 중 하나를 표시합니다.

* **성공**. 모든 작업이 성공했거나, 오류가 있을 경우 나중에 워크플로에서 발생한 작업으로 처리되었습니다. 즉, 작업이 실패하고 실행되도록 설정된 작업이 이를 처리한 것입니다.
* **실패**. 하나 이상의 작업이 실패하였고, 그중 워크플로에서 나중에 발생하여 처리하지 못한 작업이 있습니다.
* **취소**. 워크플로가 실행 중이지만 취소 요청을 받았습니다.
* **실행 중**. 워크플로가 현재 실행 중입니다. 현재 제한되는 작업 흐름 또는 현재 앱 서비스 계획으로 인해 발생할 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/app-service/plans/)의 작업 제한을 참조하세요. 진단 구성(실행 기록 아래의 차트)도 발생 중인 제한 이벤트에 관한 정보를 제공할 수 있습니다.

실행 기록에서 자세한 내용을 확인할 수 있습니다.

#### 트리거 출력

트리거 출력은 트리거에서 받은 데이터를 보여 줍니다. 모든 속성이 예상대로 반환되었는지 판단하는 데 도움이 됩니다.

>[AZURE.NOTE] 이해할 수 없는 내용을 보았을 때, Logic Apps 기능이 어떤 식으로 [다양한 콘텐츠 유형을 처리하는지](app-service-logic-content-type.md)이해하는 데 유용할 수도 있습니다.

![트리거 출력 예제][3]

#### 작업 입력 및 출력

작업에서 수신한 입력 및 출력을 드릴인할 수 있습니다. 출력 크기와 모양을 이해하고 생성될 수 있는 오류 메시지를 보는 데 유용합니다.

![작업 입력 및 출력][4]

## 워크플로 런타임 디버깅

입력, 출력 및 실행 트리거를 모니터링하는 것 외에도 워크플로 내에 디버깅에 도움이 되는 몇 가지 단계를 추가하는 데 유용할 수 있습니다. 워크플로에 단계로 추가할 수 있는 강력한 도구로는 [RequestBin](http://requestb.in)이 있습니다. RequestBin을 사용하면 HTTP 요청의 크기, 모양 및 형식을 정확하게 파악하도록 HTTP 검사기를 설정할 수 있습니다. 새 RequestBin을 만들고 URL을 테스트하려는 본문 콘텐츠(예: 식, 다른 단계 출력)와 함께 논리 앱 HTTP POST 작업에 붙여넣을 수 있습니다. Logic Apps를 실행한 후에는 RequestBin을 새로 고쳐 요청이 논리 앱 엔진에서 생성됨에 따라 어떻게 형성되는지 확인할 수 있습니다.




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG

<!---HONumber=AcomDC_0615_2016-->