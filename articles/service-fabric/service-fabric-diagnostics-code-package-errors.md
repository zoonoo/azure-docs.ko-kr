---
title: 코드 패키지 오류를 진단 하는 azure Service Fabric | Microsoft Docs
description: Azure Service Fabric을 사용 하 여 일반적인 코드 패키지 오류를 해결 하는 방법에 알아봅니다.
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 235952388d2c044cc141b3020c67944c4250ea3d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276952"
---
# <a name="diagnose-common-code-package-errors-with-service-fabric"></a>Service Fabric을 사용 하 여 일반적인 코드 패키지 오류를 진단 합니다.

이 문서에서는 예기치 않게 종료 하는 코드 패키지에 대 한 의미를 보여 줍니다. 하 고 문제를 완화 될 수 있는 문제 해결 단계 뿐만 아니라 일반적인 오류 코드의 가능한 원인에 대 한 정보를 제공 합니다.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>경우에 프로세스 또는 컨테이너 예기치 않게 종료?

Service Fabric 코드 패키지를 시작 하는 요청을 받으면 앱 및 서비스 매니페스트 설정 구성 옵션을 기반으로 하는 로컬 시스템에서 환경을 준비 하기 시작 합니다. 이러한 준비 작업은 네트워크 끝점 또는 리소스를 예약, 방화벽 규칙 구성 또는 리소스 거 버 넌 스 제약 조건을 설정 포함할 수 있습니다. 환경을 제대로 구성 되 면 Service Fabric 코드 패키지를 표시 하려고 합니다. 이 단계는 OS 또는 컨테이너 런타임에서 프로세스 또는 컨테이너를 활성화 된 성공적으로 보고 하는 경우 성공한 것으로 간주 됩니다. 정품 인증에 성공 하지 않으면 SFX 되었다는 상태 메시지가 표시 됩니다.

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

코드 패키지 성공적으로 가져온 후에, Service Fabric 수명 기간 동안 모니터링을 시작 합니다. 이 시점에서 프로세스 또는 컨테이너를 위해 다양 한 이유로 언제 든 지 종료할 수 있습니다. DLL을 초기화 하지 못한 없습니다 OS 등 데스크톱 힙 공간이 부족 해질 있을 수 있습니다. 코드 패키지 종료 되었다는 SFX 상태 메시지가 표시 됩니다.

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

종료 코드를이 상태에 제공 되는 메시지는 종료 이유에 대 한 프로세스/컨테이너에서 제공 되는 유일한 단서 및 스택의 모든 수준에서 생성 된이 없습니다. 이 종료 코드와 관련 되어, 예를 들어, OS 오류로.NET 문제를 또는 코드에서 발생 하는 경우를 이해 하는 것이 어렵습니다. 결과적으로, 종료 종료 코드의 소스를 진단 하기 위한 시작 점으로 사용할 수 있습니다이 문서 및 이러한은 일반적인 시나리오에 대 한 일반 솔루션을 적용할 수 없는 오류가 있습니다 염두에서 갖도록 가능한 솔루션을 표시 합니다.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Service Fabric 코드 패키지를 종료 하는 경우 어떻게 알 수 있나요?

Service Fabric에 대 한 다양 한 이유로 코드 패키지를 종료 해야 할 수 있습니다. 예를 들어, 부하 분산을 위해 다른 노드에서 코드 패키지를 배치를 결정할 수 목적으로 합니다. 다음 표에서 종료 코드 중 하나를 표시 하는 경우 코드 패키지 Service Fabric에 의해 종료 되었습니다 경우 알 수 있습니다.

>[!NOTE]
> 다음 테이블에이 아닌 종료 코드를 사용 하 여 프로세스/컨테이너 종료 되 면 Service Fabric는 종료 하기 처리 하지 않습니다.

종료 코드 | 설명
--------- | -----------
7147 | 이 오류 코드는 Service Fabric 정상적으로 종료 프로세스/컨테이너 Ctrl + C 신호를 보내 나타냅니다.
7148 | 이러한 오류 코드는 Service Fabric 프로세스/컨테이너를 종료 되었음을 나타냅니다. 경우에 따라이 오류 코드를 종료 해야 하므로 Ctrl + C 신호를 보낸 후 프로세스/컨테이너를 적시에 응답 하지 않은 것을 나타낼 수 있습니다.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>다른 일반적인 오류 코드 및 해당 잠재적 수정 사항

종료 코드 | 16 진수 값 | 간단한 설명 | 근본 원인 | 잠재적 수정
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | 이 오류는 컴퓨터에 데스크톱 힙 공간이 부족 한지를 잠재적으로 의미 합니다. 이 원인은 노드에서 실행 중인 응용 프로그램에 속하는 프로세스로 수가 많은 경우 것입니다. | Ctrl + C 신호에 응답 하는 프로그램 작성 되지 않은, 경우에 클러스터 매니페스트에서 "EnableActivateNoWindow" 설정을 사용할 수 있습니다. 이 설정을 사용 하면 코드 패키지는 GUI 창 없이 실행 하 고 Ctrl + C 신호를 받지는 되지만 각 프로세스에 사용 되는 데스크톱 힙 공간 양을 줄이는 것을 의미 합니다. 코드 패키지를 Ctrl + C 신호를 수신 해야 하는 경우 노드의 데스크톱 힙 크기를 늘릴 수 있습니다.
3762504530 | 0xe0434352 | N/A | 이 값은 관리 코드 (즉,.NET)에서 처리 되지 않은 예외에 대 한 오류 코드입니다. | 이 종료 코드를 표시 하는 경우 응용 프로그램 처리 되지 않은 유지 하 고 프로세스를 종료 하는 예외 발생 함을 의미 합니다. 응용 프로그램의 로그 및 덤프를 디버깅 오류를 일으킨 원인을 확인 하려면 먼저 있어야 합니다.

## <a name="next-steps"></a>다음 단계

* 자세한 내용은 [기타 일반적인 시나리오를 진단 합니다.](service-fabric-diagnostics-common-scenarios.md)
* Azure Monitor 로그와 읽어 제공의 자세한 개요 [Azure Monitor 로그 란?](../operations-management-suite/operations-management-suite-overview.md)
* Azure Monitor 로그에 자세히 알아보려면 [경고](../log-analytics/log-analytics-alerts.md) 감지 및 진단에 도움이 되 합니다.
* 알아보기 합니다 [로그 검색 및 쿼리](../log-analytics/log-analytics-log-searches.md) Azure Monitor 로그의 일부로 제공 하는 기능
