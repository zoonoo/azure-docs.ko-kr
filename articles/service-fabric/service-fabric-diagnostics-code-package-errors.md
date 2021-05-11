---
title: Service Fabric을 사용한 일반적 코드 패키지 오류 진단
description: Azure Service Fabric을 사용하여 일반적인 코드 패키지 오류를 해결하는 방법을 알아보기
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 400651b240f0518a85b9deb7a7293a77a88b0861
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591700"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Service Fabric을 사용한 일반적 코드 패키지 오류 진단

이 문서에서는 코드 패키지가 예기치 않게 종료되는 경우의 의미를 설명합니다. 문제 해결 단계와 함께 일반적인 오류 코드의 가능한 원인에 대한 인사이트를 제공합니다.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>프로세스나 컨테이너가 예기치 않게 종료되는 경우는 어떤 경우인가요?

Azure Service Fabric에서 코드 패키지를 시작하는 요청을 받으면 로컬 시스템에서 앱 및 서비스 매니페스트에 설정된 옵션에 따라 환경을 준비하기 시작합니다. 관련 준비에는 네트워크 엔드포인트 또는 리소스 예약, 방화벽 규칙 구성 또는 리소스 거버넌스 제약 조건 설정이 포함될 수 있습니다. 

환경이 제대로 구성된 뒤 Service Fabric은 코드 패키지를 가져오려고 시도합니다. OS 또는 컨테이너 런타임에서 프로세스 또는 컨테이너가 성공적으로 활성화되었다고 보고하는 경우 해당 단계가 성공한 것으로 간주됩니다. 활성화에 성공하면 SFX에 다음과 같은 상태 메시지가 표시됩니다.

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

코드 패키지가 성공적으로 활성화되면 Service Fabric은 수명 모니터링을 시작합니다. 해당 시점에서 프로세스나 컨테이너는 여러 가지 이유로 언제든지 종료될 수 있습니다. 예를 들어 DLL을 초기화하지 못했거나 OS에 데스크톱 힙 공간이 부족한 것일 수 있습니다. 코드 패키지가 종료되면 SFX에서 다음 상태 메시지가 표시됩니다.

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

해당 상태 메시지의 종료 코드는 프로세스 또는 컨테이너가 종료된 이유를 제공하는 유일한 단서입니다. 관련 종료 코드는 모든 수준의 스택에 의해 생성될 수 있습니다. 예를 들어 해당 종료 코드는 OS 오류 또는 .NET 문제와 관련이 있거나 코드로 인해 발생했을 수 있습니다. 해당 문서를 시작점으로 사용하여 종료 코드의 원본 및 가능한 솔루션을 진단합니다. 그러나 해당 방법은 일반적인 시나리오에 대한 일반적 솔루션이며 표시되는 모든 오류에 공통적으로 적용되지는 않을 수 있습니다.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Service Fabric이 내 코드 패키지를 종료했는지 어떻게 알 수 있나요?

Service Fabric은 다양한 이유로 코드 패키지 종료 현상을 발생시킬 수 있습니다. 예를 들어 Service Fabric은 부하 분산을 위해 다른 노드에 코드 패키지를 위치시킬 수 있습니다. 다음 표에 나열된 종료 코드가 표시되는 경우 Service Fabric이 코드 패키지를 종료했는지 확인할 수 있습니다.

>[!NOTE]
> 다음 표에 나열된 코드가 아닌 다른 종료 코드가 표시되며 프로세스 또는 컨테이너가 종료되는 경우 Service Fabric은 해당 종료 현상과 무관합니다.

종료 코드 | 설명
--------- | -----------
7147 | 해당 코드는 Service Fabric이 Ctrl+C 신호를 전송하여 프로세스나 컨테이너를 정상적으로 종료하였음을 나타냅니다.
7148 | 해당 코드는 Service Fabric이 프로세스 또는 컨테이너를 종료했음을 나타냅니다. 경우에 따라 해당 오류 코드는 프로세스 또는 컨테이너가 Ctrl+C 신호를 보낸 후 적절한 시간 내에 응답하지 않아서 종료했음을 나타내기도 합니다.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>기타 일반적인 오류 코드 및 잠재적 수정 사항

종료 코드 | 16 진수 값 | 간단한 설명 | 근본 원인 | 잠재적 수정 사항
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | 해당 오류는 경우에 따라 머신에 데스크톱 힙 공간이 부족한 경우를 의미합니다. 해당 원인은 노드에서 실행 중인 애플리케이션에 속하는 프로세스가 너무 많은 경우 특히 발생 가능성이 높습니다. | 프로그램이 Ctrl+C 신호에 응답하도록 빌드되지 않은 경우 클러스터 매니페스트에서 **EnableActivateNoWindow** 설정을 사용하도록 설정할 수 있습니다. 해당 설정을 사용하면 코드 패키지가 GUI 창 없이 실행되며 Ctrl+C 신호를 수신하지 않습니다. 또한 해당 작업을 수행하면 각 프로세스에서 사용하는 데스크톱 힙 공간의 양이 줄어듭니다. 코드 패키지가 Ctrl+C 신호를 수신해야 하는 경우 노드의 데스크톱 힙 크기를 늘릴 수 있습니다.
3762504530 | 0xe0434352 | 해당 없음 | 해당 값은 관리 코드(.NET)에서 처리되지 않은 예외에 대한 오류 코드를 나타냅니다. | 해당 종료 코드는 애플리케이션에서 처리되지 않은 상태로 유지되는 예외가 발생하여 프로세스를 종료했음을 나타냅니다. 해당 오류를 트리거한 항목을 확인하는 첫 번째 단계로 애플리케이션의 로그 및 덤프 파일을 디버그합니다.

## <a name="next-steps"></a>다음 단계

* [다른 일반적 시나리오 진단 방법](service-fabric-diagnostics-common-scenarios.md)에 대한 자세한 정보.
* Azure Monitor 로그 및 [Azure Monitor 개요](../azure-monitor/overview.md)의 제시 사항에 대한 세부 개요를 확인하세요.
* 검색 및 진단에 도움이 되는 Azure Monitor 로그 [경고](../azure-monitor/alerts/alerts-overview.md)에 대한 자세한 정보.
* Azure Monitor 로그의 일부로 제공되는 [로그 검색 및 쿼리](../azure-monitor/logs/log-query-overview.md) 기능을 알아봅니다.
