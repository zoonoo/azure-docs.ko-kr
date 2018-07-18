---
title: Azure Service Fabric CLI- sfctl sa-cluster | Microsoft Docs
description: Service Fabric CLI sfctl standalone cluster 명령을 설명합니다.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: ffdbff7edc5af187071615c8b1e61790b3a38429
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763958"
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

Service Fabric 독립 실행형 클러스터 구성을 가져옵니다. 클러스터 구성에는 클러스터, 보안 구성, 오류 및 업그레이드 도메인 토폴로지 등의 다양한 노드 형식을 포함하는 클러스터의 속성이 들어 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --configuration-api-version [필수] | 독립 실행형 클러스터 json 구성의 API 버전입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Service Fabric 독립 실행형 클러스터의 구성 업그레이드를 시작합니다.

제공된 구성 업그레이드 매개 변수의 유효성을 검사하고 매개 변수가 유효한 경우 클러스터 구성 업그레이드를 시작합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --cluster-config            [필수] | 클러스터 구성입니다. |
| --delta-unhealthy-nodes | 업그레이드 중 허용되는 델타 상태 저하의 최대 비율입니다. 허용되는 값은 0~100의 정수 값입니다. |
| --health-check-retry | 응용 프로그램 또는 클러스터가 정상이 아닌지 상태 확인을 수행하는 시도 사이의 시간입니다.  기본값\: PT0H0M0S. |
| --health-check-stable | 응용 프로그램 또는 클러스터에서 정상을 유지해야 하는 시간입니다.  기본값\: PT0H0M0S. |
| --health-check-wait | 업그레이드 도메인을 완료한 후 상태 확인 프로세스를 시작하기 전에 대기하는 시간입니다.  기본값\: PT0H0M0S. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |
| --unhealthy-applications | 업그레이드 중 허용되는 비정상 응용 프로그램의 최대 백분율입니다. 허용되는 값은 0~100의 정수 값입니다. |
| --unhealthy-nodes | 업그레이드 중 허용되는 비정상 노드의 최대 백분율입니다. 허용되는 값은 0~100의 정수 값입니다. |
| --upgrade-domain-delta-unhealthy-nodes | 업그레이드 중 허용되는 업그레이드 도메인 델타 상태 저하의 최대 비율입니다. 허용되는 값은 0~100의 정수 값입니다. |
| --upgrade-domain-timeout | 업그레이드 도메인에 대한 시간 제한입니다.  기본값\: PT0H0M0S. |
| --upgrade-timeout | 업그레이드 시간 제한입니다.  기본값\: PT0H0M0S. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Service Fabric 독립 실행형 클러스터의 클러스터 구성 업그레이드 상태를 가져옵니다.

Service Fabric 독립 실행형 클러스터의 클러스터 구성 업그레이드 상태 정보를 가져옵니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.