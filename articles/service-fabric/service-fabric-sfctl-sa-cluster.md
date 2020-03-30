---
title: Azure 서비스 패브릭 CLI-sfctl sa-클러스터
description: Azure 서비스 패브릭 명령줄 인터페이스인 sfctl에 대해 알아봅니다. 독립 실행형 클러스터 관리를 위한 명령 목록을 포함합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 233148c04fb1a9c6cf1d6c7042c12c54eebd0205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904934"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
독립 실행형 Service Fabric 클러스터를 관리합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| config | Service Fabric 독립 실행형 클러스터 구성을 가져옵니다. |
| config-upgrade | Service Fabric 독립 실행형 클러스터의 구성 업그레이드를 시작합니다. |
| upgrade-status | Service Fabric 독립 실행형 클러스터의 클러스터 구성 업그레이드 상태를 가져옵니다. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Service Fabric 독립 실행형 클러스터 구성을 가져옵니다.

클러스터 구성에는 클러스터, 보안 구성, 오류 및 업그레이드 도메인 토폴로지 등의 다양한 노드 형식을 포함하는 클러스터의 속성이 들어 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --configuration-api-version [필수] | 독립 실행형 클러스터 json 구성의 API 버전입니다. |
| --timeout -t | 작업을 수행하는 서버 시간 시간이 몇 초 입니다. 이 시간 아웃은 클라이언트가 요청된 작업이 완료될 때까지 기다릴 수 있는 기간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Service Fabric 독립 실행형 클러스터의 구성 업그레이드를 시작합니다.

제공된 구성 업그레이드 매개 변수의 유효성을 검사하고 매개 변수가 유효한 경우 클러스터 구성 업그레이드를 시작합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --cluster-config            [필수] | 클러스터 구성입니다. |
| --application-health-policies | 오류를 발생시키기 전의 비정상 애플리케이션 형식 이름 및 최대 비율 쌍의 JSON 인코딩된 사전. |
| --delta-unhealthy-nodes | 업그레이드 중 허용되는 델타 상태 저하의 최대 비율입니다. 허용되는 값은 0~100의 정수 값입니다. |
| --health-check-retry | 애플리케이션 또는 클러스터가 정상이 아닌지 상태 확인을 수행하는 시도의 시간 간격입니다.  기본값\: PT0H0M0S. |
| --health-check-stable | 업그레이드가 다음 업그레이드 도메인으로 진행되기 전에 애플리케이션 또는 클러스터가 정상 상태로 유지되어야 하는 시간입니다.  기본값\: PT0H0M0S. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --health-check-wait | 업그레이드 도메인을 완료한 후 상태 확인 프로세스를 시작하기 전에 대기하는 시간입니다.  기본값\: PT0H0M0S. |
| --timeout -t | 기본값\: 60. |
| --unhealthy-applications | 업그레이드 중 허용되는 비정상 애플리케이션의 최대 백분율입니다. 허용되는 값은 0~100의 정수 값입니다. |
| --unhealthy-nodes | 업그레이드 중 허용되는 비정상 노드의 최대 백분율입니다. 허용되는 값은 0~100의 정수 값입니다. |
| --upgrade-domain-delta-unhealthy-nodes | 업그레이드 중 허용되는 업그레이드 도메인 델타 상태 저하의 최대 비율입니다. 허용되는 값은 0~100의 정수 값입니다. |
| --upgrade-domain-timeout | FailureAction이 실행되기 전에 각 업그레이드 도메인이 완료해야 하는 시간입니다.  기본값\: PT0H0M0S. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --upgrade-timeout | FailureAction이 실행되기 전에 전체 업그레이드를 완료해야 하는 시간입니다.  기본값\: PT0H0M0S. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

### <a name="examples"></a>예

클러스터 구성 업데이트 시작
```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Service Fabric 독립 실행형 클러스터의 클러스터 구성 업그레이드 상태를 가져옵니다.

Service Fabric 독립 실행형 클러스터의 클러스터 구성 업그레이드 상태 정보를 가져옵니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --timeout -t | 작업을 수행하는 서버 시간 시간이 몇 초 입니다. 이 시간 아웃은 클라이언트가 요청된 작업이 완료될 때까지 기다릴 수 있는 기간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |


## <a name="next-steps"></a>다음 단계
- 서비스 패브릭 [CLI를 설정합니다.](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.