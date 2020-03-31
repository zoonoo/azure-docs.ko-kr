---
title: 서비스 패브릭을 사용하여 일반적인 코드 패키지 오류 진단
description: Azure 서비스 패브릭을 사용하여 일반적인 코드 패키지 오류 문제를 해결하는 방법 알아보기
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 344fef70522240da2236a020c96308c472c9c545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463103"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>서비스 패브릭을 사용하여 일반적인 코드 패키지 오류 진단

이 문서에서는 코드 패키지가 예기치 않게 종료되는 것이 무엇을 의미하는지 설명합니다. 문제 해결 단계와 함께 일반적인 오류 코드의 가능한 원인에 대한 통찰력을 제공합니다.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>프로세스 또는 컨테이너가 예기치 않게 종료되는 시기는 언제입니까?

Azure Service Fabric에서 코드 패키지 시작 요청을 받으면 앱 및 서비스 매니페스트에 설정된 옵션에 따라 로컬 시스템에서 환경을 준비하기 시작합니다. 이러한 준비에는 네트워크 끝점 또는 리소스 예약, 방화벽 규칙 구성 또는 리소스 거버넌스 제약 조건 설정이 포함될 수 있습니다. 

환경이 올바르게 구성되면 Service Fabric은 코드 패키지를 가져오려고 시도합니다. 이 단계는 OS 또는 컨테이너 런타임이 프로세스 또는 컨테이너가 성공적으로 활성화되었다고 보고하는 경우 성공한 것으로 간주됩니다. 정품 인증에 실패하면 SFX에 다음과 유사한 상태 메시지가 표시됩니다.

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

코드 패키지가 성공적으로 활성화되면 Service Fabric이 수명 모니터링을 시작합니다. 이 시점에서 프로세스 또는 컨테이너는 여러 가지 이유로 언제든지 종료될 수 있습니다. 예를 들어 DLL을 초기화하지 못했거나 OS에 데스크톱 힙 공간이 부족했을 수 있습니다. 코드 패키지가 종료된 경우 SFX에 다음과 같은 상태 메시지가 표시됩니다.

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

이 상태 메시지의 종료 코드는 프로세스 또는 컨테이너가 종료된 이유에 대해 제공하는 유일한 단서입니다. 스택의 모든 수준에서 생성될 수 있습니다. 예를 들어 이 엑시트 코드는 OS 오류 또는 .NET 문제와 관련이 있거나 코드에서 발생했을 수 있습니다. 이 문서를 종료 종료 종료 코드 및 가능한 솔루션의 소스를 진단하기 위한 시작점으로 사용합니다. 그러나 이러한 솔루션은 일반적인 시나리오에 대한 일반적인 솔루션이며 표시되는 오류에는 적용되지 않을 수 있습니다.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>서비스 패브릭이 코드 패키지를 종료했는지 어떻게 알 수 있습니까?

서비스 패브릭은 다양한 이유로 코드 패키지를 종료해야 할 수 있습니다. 예를 들어 로드 밸런싱을 위해 코드 패키지를 다른 노드에 배치할 수 있습니다. 다음 표에 종료 코드가 표시되면 Service Fabric에서 코드 패키지를 종료했는지 확인할 수 있습니다.

>[!NOTE]
> 프로세스 또는 컨테이너가 다음 표의 코드 이외의 종료 코드로 종료되는 경우 Service Fabric은 종료에 대한 책임을 지지 않습니다.

종료 코드 | 설명
--------- | -----------
7147 | Service Fabric이 Ctrl+C 신호를 전송하여 프로세스 또는 컨테이너를 정상적으로 종료했음을 나타냅니다.
7148 | 서비스 패브릭이 프로세스 또는 컨테이너를 종료했음을 나타냅니다. 경우에 따라 이 오류 코드는 Ctrl+C 신호를 보낸 후 프로세스 또는 컨테이너가 적시에 응답하지 않아 종료되어야 했음을 나타냅니다.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>기타 일반적인 오류 코드 및 잠재적수정

종료 코드 | 16 진수 값 | 간단한 설명 | 근본 원인 | 잠재적인 수정
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | 이 오류는 때때로 컴퓨터가 데스크톱 힙 공간이 부족했다는 것을 의미합니다. 이 원인은 노드에서 실행 중인 응용 프로그램에 속하는 프로세스가 많은 경우 특히 발생할 수 있습니다. | 프로그램이 Ctrl+C 신호에 응답하도록 빌드되지 않은 경우 클러스터 매니페스트에서 **EnableActivateNoWindow** 설정을 사용하도록 설정할 수 있습니다. 이 설정을 사용하도록 설정하면 코드 패키지가 GUI 창 없이 실행되고 Ctrl+C 신호가 수신되지 않습니다. 이 작업을 수행하면 각 프로세스가 사용하는 데스크톱 힙 공간도 줄어듭니다. 코드 패키지에서 Ctrl+C 신호를 수신해야 하는 경우 노드의 데스크톱 힙 크기를 늘릴 수 있습니다.
3762504530 | 0xe0434352 | 해당 없음 | 이 값은 관리 코드(즉,.NET)에서 처리되지 않은 예외에 대한 오류 코드를 나타냅니다. | 이 종료 코드는 응용 프로그램이 처리되지 않은 상태로 남아 있고 프로세스를 종료한 예외를 발생시켰음을 나타냅니다. 이 오류를 트리거한 원인을 확인하는 첫 번째 단계로 응용 프로그램의 로그 및 덤프 파일을 디버깅합니다.

## <a name="next-steps"></a>다음 단계

* [다른 일반적인 시나리오 진단에](service-fabric-diagnostics-common-scenarios.md)대해 자세히 알아봅니다.
* Azure Monitor 개요를 [읽고](../operations-management-suite/operations-management-suite-overview.md)Azure Monitor 로그와 제공되는 에 대한 자세한 개요를 확인합니다.
* 검색 및 진단에 대한 지원을 [알리는](../log-analytics/log-analytics-alerts.md) Azure Monitor 로그에 대해 자세히 알아봅니다.
* Azure Monitor [로그의](../log-analytics/log-analytics-log-searches.md) 일부로 제공되는 로그 검색 및 쿼리 기능에 대해 잘 알고 있습니다.
