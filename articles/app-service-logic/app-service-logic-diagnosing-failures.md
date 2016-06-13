<properties
   pageTitle="논리 앱 진단 앱 | Microsoft Azure"
   description="앱이 실패하는 경우를 파악하는 일반적인 방법을 알아봅니다."
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
   
# 논리 앱 진단

논리 앱에서 어떤 문제 또는 오류가 발생하는 경우 어느 곳에서 오류가 발생했는지 잘 파악할 수 있는 몇 가지 방법이 있습니다.

## 관리 포털 도구

Azure 포털에서는 각 단계에서 각 논리 앱을 진단하는 여러 가지 도구를 제공합니다.

### 트리거 기록

각 논리 앱에는 하나 이상의 트리거가 있습니다. 실행되지 않은 앱을 발견한 경우 트리거 기록부터 시작합니다. 트리거 기록은 논리 앱 주 블레이드에서 액세스할 수 있습니다.

![][1]

여기에는 논리 앱에서 트리거한 모든 시도가 나열됩니다. 각 항목을 클릭하여 다음 수준의 세부 정보를 얻을 수 있습니다(특히 트리거 시도로 생성된 모든 입력 또는 출력). '실패(Failed)' 트리거가 있는 경우 트리거 시도를 클릭하고 출력 링크를 자세히 살펴 생성될 수 있는 모든 오류 메시지를 파악합니다(예: 잘못된 FTP 자격 증명).

여러 상태가 표시될 수 있습니다.

* 생략(Skipped) - 데이터를 사용할 수 있는지 확인하기 위해 끝점을 폴링했으며 데이터를 사용할 수 없다는 응답을 다시 받았습니다.
* 성공(Succeeded) - 트리거가 데이터를 사용할 수 있다는 응답을 수신했습니다. 수동 트리거, 되풀이 트리거 또는 폴링 트리거일 수 있습니다. '발생(Fired)'이 함께 포함될 수 있지만 코드 보기에 충족되지 않는 조건 또는 splitOn이 있는 경우에는 포함되지 않을 수 있습니다.
* 실패(Failed) - 오류가 발생했습니다.

#### 트리거 수동 시작

논리 앱에서 사용 가능한 트리거를 즉시(다음 되풀이까지 기다리지 않고) 확인하려면 주 블레이드에서 항상 **트리거 선택** 단추를 클릭하여 확인합니다. 예를 들어 Dropbox 트리거로 이 단추를 클릭하면 새 파일에 대해 Dropbox를 즉시 폴링하는 워크플로가 발생합니다.

### 실행 기록

트리거가 발생할 때마다 하나의 실행 기록이 생깁니다. 주 블레이드에서 실행에 액세스할 수 있으며 실행에는 흐름 중에 어떤 일이 발생했는지 이해하는 데 도움이 되는 많은 정보가 포함됩니다.

![][2]

실행은 다음과 같은 상태를 포함할 수 있습니다.

* 성공(Succeeded) - 모든 작업이 성공했거나 오류가 있는 경우 워크플로에서 이후 작업으로 처리되었습니다(즉, 작업이 '실패' 작업 후에 실행되도록 설정됨).
* 실패(Failed) - 하나 이상의 작업에 워크플로에서 이후 작업으로 처리되지 않은 오류가 있습니다.
* 취소됨(Cancelled) - 워크플로가 실행 중이지만 취소 요청을 수신했습니다.
* 실행 중(Running) - 워크플로가 현재 실행 중인 경우입니다. 현재 앱 서비스 계획으로도 제한되는 흐름에 대해 발생할 수 있습니다. 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/app-service/plans/)의 작업 제한을 참조하세요. 진단 구성(차트 아래 실행 기록)을 통해서도 발생하는 제한 이벤트에 대해 알 수 있습니다.

실행 내에 있다면 드릴인하여 세부 정보를 볼 수 있습니다.

#### 트리거 출력

트리거 출력은 트리거로부터 받은 데이터를 보여 줍니다. 모든 속성이 예상대로 반환되는지 파악하는 데 유용할 수 있습니다.

>[AZURE.NOTE] 이해할 수 없는 콘텐츠가 있는 경우 논리 앱에서 [다양한 콘텐츠 형식을 어떻게 처리](app-service-logic-content-type.md)하는지 이해하는 데 도움이 될 수 있습니다.

![][3]

#### 작업 입력 및 출력

작업에서 수신한 입력 및 출력을 드릴인할 수 있습니다. 출력 크기와 모양을 이해하고 생성될 수 있는 오류 메시지를 보는 데 유용합니다.

![][4]

## 워크플로 런타임 디버깅

입력, 출력 및 실행 트리거를 모니터링하는 것 외에도 워크플로 내에 디버깅에 도움이 되는 몇 가지 단계를 추가하는 데 유용할 수 있습니다. 워크플로에 단계로 추가할 수 있는 한 가지 강력한 도구는 [RequestBin](http://requestb.in)입니다. RequestBin을 사용하면 HTTP 요청의 크기, 모양 및 형식을 정확하게 파악하도록 HTTP 검사기를 설정할 수 있습니다. 새 RequestBin을 만들고 URL을 테스트하려는 본문 콘텐츠(식, 다른 단계 출력 등)와 함께 논리 앱 HTTP POST 작업에 붙여넣을 수 있습니다. 논리 앱을 실행한 후에는 RequestBin을 새로 고쳐 요청이 논리 앱 엔진에서 생성됨에 따라 어떻게 형성되는지 확인할 수 있습니다.




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG

<!---HONumber=AcomDC_0601_2016-->