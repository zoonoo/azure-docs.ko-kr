---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164870"
---
#### <a name="built-in-log-streaming"></a>기본 제공 로그 스트리밍

다음 예제와 같이 `logstream` 옵션을 사용하여 Azure에서 실행 중인 특정 함수 앱의 스트리밍 로그 수신을 시작합니다.

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>기본 제공 로그 스트리밍은 사용 계획의 Linux에서 실행되는 함수 앱의 핵심 도구에서 아직 사용하도록 설정되지 않습니다. 이러한 호스팅 계획의 경우 라이브 메트릭 스트림을 대신 사용하여 로그를 거의 실시간으로 볼 수 있어야 합니다.

#### <a name="live-metrics-stream"></a>라이브 메트릭 스트림

다음 예제와 같이 `--browser` 옵션을 포함하여 새 브라우저 창에서 함수 앱에 대한 [라이브 메트릭 스트림](../articles/azure-monitor/app/live-stream.md)을 볼 수도 있습니다.

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
