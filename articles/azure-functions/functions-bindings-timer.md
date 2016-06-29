<properties
	pageTitle="Azure Functions 타이머 트리거 | Microsoft Azure"
	description="Azure Functions에서 타이머 트리거를 사용하는 방법을 파악합니다."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure 함수, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/16/2016"
	ms.author="chrande"/>

# Azure Functions 타이머 트리거

이 문서에서는 Azure Functions에서 타이머 트리거를 구성하는 방법을 설명합니다. 타이머 트리거는 일정에 따라, 한 번만 또는 반복해서 함수를 호출합니다.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## 타이머 트리거에 대한 function.json

*function.json* 파일은 예약 식을 제공합니다.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

타이머 트리거는 다중 인스턴스 확장을 자동으로 처리합니다. 특정 타이머 함수의 단일 인스턴스만이 모든 인스턴스에 실행됩니다.

## 일정 식의 서식

일정 식은 {초} {분} {시간} {일} {월} {요일}과 같은 6개의 필드를 포함하는 [CRON 식](http://en.wikipedia.org/wiki/Cron#CRON_expression)입니다. 온라인에서 찾은 cron 식 문서는 대부분 {두 번째} 필드를 생략하므로 해당 필드 중 하나를 복사하면 추가 필드에 대해 조정해야 합니다.

다음은 몇 가지 일정 식의 예입니다.

5분마다 한 번씩 트리거하려면:

```json
"schedule": "0 */5 * * * *"
```

2시간마다 한 번씩 트리거하려면:

```json
"schedule": "0 0 */2 * * *",
```

## 타이머 트리거 C# 코드 예제

이 C# 코드 예제에서는 함수가 트리거될 때마다 단일 로그를 작성합니다.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## 다음 단계

[AZURE.INCLUDE [다음 단계](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0615_2016-->