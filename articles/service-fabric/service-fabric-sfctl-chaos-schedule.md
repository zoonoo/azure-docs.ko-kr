---
title: Azure 서비스 패브릭 CLI- sfctl 혼돈 일정
description: Azure 서비스 패브릭 명령줄 인터페이스인 sfctl에 대해 알아봅니다. 혼돈 일정에 대한 명령 목록을 포함합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 29b365c48e405830e238945f1d94156f477c15b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906179"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
chaos schedule을 가져오고 설정합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| Get | Chaos를 실행하는 시기 및 방법을 정의하는 Chaos Schedule을 가져옵니다. |
| set | Chaos에서 사용되는 일정을 설정합니다. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
Chaos를 실행하는 시기 및 방법을 정의하는 Chaos Schedule을 가져옵니다.

사용 중인 Chaos Schedule과 Chaos를 실행하는 시기 및 방법을 정의하는 Chaos Schedule의 버전을 가져옵니다.

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

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Chaos에서 사용되는 일정을 설정합니다.

Chaos는 Chaos Schedule에 따라 실행을 자동으로 예약합니다. 제공된 버전이 서버의 버전과 일치하는 경우 카오스 일정이 업데이트됩니다. 혼돈 일정을 업데이트할 때 서버의 버전은 1씩 증가합니다. 서버의 버전은 많은 수에 도달하면 0으로 다시 래핑됩니다. 이 호출이 만들어질 때 Chaos가 실행되는 경우 호출에 실패합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --chaos-parameters-dictionary | 작업에서 사용할 ChaosParameters로 문자열 이름의 매핑을 나타내는 JSON 인코딩된 목록입니다. |
| --expiry-date-utc | Chaos를 예약하는 데 Schedule을 사용하여 중지할 시기에 대한 날짜 및 시간입니다.  기본값\: 9999-12-31T23\:59\:59.999Z. |
| --jobs | Chaos를 실행할 시기 및 Chaos와 함께 실행할 매개 변수를 나타내는 ChaosScheduleJobs의 JSON 인코딩된 목록입니다. |
| --start-date-utc | Chaos를 예약하는 데 Schedule을 사용하여 시작할 시기에 대한 날짜 및 시간입니다.  기본값\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | 기본값\: 60. |
| --version | Schedule의 버전 번호입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

### <a name="examples"></a>예

다음 명령은 Chaos를 1일 24시간, 주 7일 실행하는 2016-01-01에 시작하여 2038-01-01에 만료되는 일정을 설정합니다(현재 일정에 버전 0이 있다고 가정).
Chaos는 해당 시간에 대해 클러스터에서 예약됩니다.
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>다음 단계
- 서비스 패브릭 [CLI를 설정합니다.](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.
