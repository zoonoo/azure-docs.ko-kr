---
title: Azure Service Fabric Mesh 응용 프로그램의 모니터링 및 진단 | Microsoft Docs
description: Azure의 Service Fabric Mesh에서 응용 프로그램을 모니터링하고 진단하는 방법을 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 79e5622dd73e53854204675b435e99d187a3ab26
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075885"
---
# <a name="monitoring-and-diagnostics"></a>모니터링 및 진단
Azure Service Fabric Mesh는 개발자가 가상 머신, 저장소 또는 네트워킹을 관리하지 않고 마이크로 서비스 응용 프로그램을 배포할 수 있는 완전히 관리되는 서비스입니다. Service Fabric Mesh의 모니터링 및 진단은 다음 세 가지 주요 진단 데이터 유형으로 분류됩니다.

- 응용 프로그램 로그 - 응용 프로그램을 계측한 방법에 따라 컨테이너화된 응용 프로그램의 로그로 정의됩니다(예: docker 로그).
- 플랫폼 이벤트 - 현재 컨테이너 활성화, 비활성화 및 종료를 포함하는 컨테이너 작업에 관련된 Mesh 플랫폼의 이벤트입니다.
- 컨테이너 메트릭 - 컨테이너에 대한 리소스 사용률 및 성능 메트릭입니다(docker 통계).

이 문서에서는 사용 가능한 최신 미리 보기 버전의 모니터링 및 진단 옵션을 설명합니다.

## <a name="application-logs"></a>응용 프로그램 로그

배포된 컨테이너의 docker 로그를 컨테이너별로 볼 수 있습니다. Service Fabric Mesh 응용 프로그램 모델에서 각 컨테이너는 응용 프로그램의 코드 패키지입니다. 코드 패키지와 연결된 로그를 보려면 다음 명령을 사용합니다.

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> “az mesh servicereplica” 명령을 사용하여 복제본 이름을 가져올 수 있습니다. 복제본 이름은 0부터 증분하는 숫자입니다.*

투표 응용 프로그램에서 VotingWeb.Code 컨테이너의 로그를 확인하면 다음과 같이 표시됩니다.

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>다음 단계
Service Fabric Mesh에 대한 자세한 내용은 다음 개요를 참조하세요.
- [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)
