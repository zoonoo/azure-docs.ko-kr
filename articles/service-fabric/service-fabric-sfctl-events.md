---
title: Azure Service Fabric CLI-sfctl 이벤트 | Microsoft Docs
description: CLI sfctl events 명령 Service Fabric에 대해 설명 합니다.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 858fd1971a22b1db2d243838558c3792d3a60cc9
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901977"
---
# <a name="sfctl-events"></a>sfctl 이벤트
이벤트 저장소에서 이벤트를 검색 합니다 (EventStore 서비스가 이미 설치 된 경우).

EventStore 시스템 서비스는 > = 6.4를 실행 하는 모든 SFRP 클러스터에 구성 업그레이드를 통해 추가할 수 있습니다. Https\://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore url\: 다음 url을 확인 하세요.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| 모든 응용 프로그램-목록 | 모든 응용 프로그램 관련 이벤트를 가져옵니다. |
| 모든 노드 목록 | 모든 노드 관련 이벤트를 가져옵니다. |
| 모든-파티션-목록 | 모든 파티션 관련 이벤트를 가져옵니다. |
| 모든 서비스-목록 | 모든 서비스 관련 이벤트를 가져옵니다. |
| 응용 프로그램 목록 | 응용 프로그램 관련 이벤트를 가져옵니다. |
| 클러스터 목록 | 모든 클러스터 관련 이벤트를 가져옵니다. |
| 노드 목록 | 노드 관련 이벤트를 가져옵니다. |
| 파티션-전체 복제본-목록 | 파티션에 대 한 모든 복제본 관련 이벤트를 가져옵니다. |
| 파티션 목록 | 파티션 관련 이벤트를 가져옵니다. |
| 분할-복제본 목록 | 파티션 복제본 관련 이벤트를 가져옵니다. |
| 서비스 목록 | 서비스 관련 이벤트를 가져옵니다. |

## <a name="sfctl-events-all-applications-list"></a>sfctl 이벤트 모든-응용 프로그램 목록
모든 응용 프로그램 관련 이벤트를 가져옵니다.

응답은 ApplicationEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --최종 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 종료 시간입니다. |
| --시작 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 시작 시간입니다. |
| --events-types-필터 | 응답에만 포함 되어야 하는 FabricEvents 유형을 지정 하는 쉼표로 구분 된 문자열입니다. |
| --제외-분석-이벤트 | True가 전달 되 면이 매개 변수는 AnalysisEvents 검색을 사용 하지 않도록 설정 합니다. |
| --skip-상관 관계-조회 | True가 전달 되는 경우이 매개 변수는 CorrelatedEvents 정보 검색을 사용 하지 않도록 설정 합니다. 그렇지 않으면 모든 FabricEvent의 CorrelationEvents get 처리 및 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행 하기 위한 서버 제한 시간 (초)입니다. 이 시간 제한은 요청 된 작업이 완료 될 때까지 클라이언트가 대기 하는 기간을 지정 합니다. 이 매개 변수의 기본값은 60 초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, 테이블, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl 이벤트 모든-노드 목록
모든 노드 관련 이벤트를 가져옵니다.

응답은 NodeEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --최종 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 종료 시간입니다. |
| --시작 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 시작 시간입니다. |
| --events-types-필터 | 응답에만 포함 되어야 하는 FabricEvents 유형을 지정 하는 쉼표로 구분 된 문자열입니다. |
| --제외-분석-이벤트 | True가 전달 되 면이 매개 변수는 AnalysisEvents 검색을 사용 하지 않도록 설정 합니다. |
| --skip-상관 관계-조회 | True가 전달 되는 경우이 매개 변수는 CorrelatedEvents 정보 검색을 사용 하지 않도록 설정 합니다. 그렇지 않으면 모든 FabricEvent의 CorrelationEvents get 처리 및 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행 하기 위한 서버 제한 시간 (초)입니다. 이 시간 제한은 요청 된 작업이 완료 될 때까지 클라이언트가 대기 하는 기간을 지정 합니다. 이 매개 변수의 기본값은 60 초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, 테이블, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl 이벤트 모든 파티션-목록
모든 파티션 관련 이벤트를 가져옵니다.

응답은 파티션 이벤트 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --최종 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 종료 시간입니다. |
| --시작 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 시작 시간입니다. |
| --events-types-필터 | 응답에만 포함 되어야 하는 FabricEvents 유형을 지정 하는 쉼표로 구분 된 문자열입니다. |
| --제외-분석-이벤트 | True가 전달 되 면이 매개 변수는 AnalysisEvents 검색을 사용 하지 않도록 설정 합니다. |
| --skip-상관 관계-조회 | True가 전달 되는 경우이 매개 변수는 CorrelatedEvents 정보 검색을 사용 하지 않도록 설정 합니다. 그렇지 않으면 모든 FabricEvent의 CorrelationEvents get 처리 및 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행 하기 위한 서버 제한 시간 (초)입니다. 이 시간 제한은 요청 된 작업이 완료 될 때까지 클라이언트가 대기 하는 기간을 지정 합니다. 이 매개 변수의 기본값은 60 초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, 테이블, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-events-all-services-list"></a>sfctl 이벤트 모든-서비스 목록
모든 서비스 관련 이벤트를 가져옵니다.

응답은 ServiceEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --최종 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 종료 시간입니다. |
| --시작 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 시작 시간입니다. |
| --events-types-필터 | 응답에만 포함 되어야 하는 FabricEvents 유형을 지정 하는 쉼표로 구분 된 문자열입니다. |
| --제외-분석-이벤트 | True가 전달 되 면이 매개 변수는 AnalysisEvents 검색을 사용 하지 않도록 설정 합니다. |
| --skip-상관 관계-조회 | True가 전달 되는 경우이 매개 변수는 CorrelatedEvents 정보 검색을 사용 하지 않도록 설정 합니다. 그렇지 않으면 모든 FabricEvent의 CorrelationEvents get 처리 및 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행 하기 위한 서버 제한 시간 (초)입니다. 이 시간 제한은 요청 된 작업이 완료 될 때까지 클라이언트가 대기 하는 기간을 지정 합니다. 이 매개 변수의 기본값은 60 초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, 테이블, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-events-application-list"></a>sfctl 이벤트 응용 프로그램 목록
응용 프로그램 관련 이벤트를 가져옵니다.

응답은 ApplicationEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --최종 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 종료 시간입니다. |
| --시작 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 시작 시간입니다. |
| --events-types-필터 | 응답에만 포함 되어야 하는 FabricEvents 유형을 지정 하는 쉼표로 구분 된 문자열입니다. |
| --제외-분석-이벤트 | True가 전달 되 면이 매개 변수는 AnalysisEvents 검색을 사용 하지 않도록 설정 합니다. |
| --skip-상관 관계-조회 | True가 전달 되는 경우이 매개 변수는 CorrelatedEvents 정보 검색을 사용 하지 않도록 설정 합니다. 그렇지 않으면 모든 FabricEvent의 CorrelationEvents get 처리 및 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행 하기 위한 서버 제한 시간 (초)입니다. 이 시간 제한은 요청 된 작업이 완료 될 때까지 클라이언트가 대기 하는 기간을 지정 합니다. 이 매개 변수의 기본값은 60 초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, 테이블, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-events-cluster-list"></a>sfctl 이벤트 클러스터 목록
모든 클러스터 관련 이벤트를 가져옵니다.

응답은 ClusterEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --최종 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 종료 시간입니다. |
| --시작 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 시작 시간입니다. |
| --events-types-필터 | 응답에만 포함 되어야 하는 FabricEvents 유형을 지정 하는 쉼표로 구분 된 문자열입니다. |
| --제외-분석-이벤트 | True가 전달 되 면이 매개 변수는 AnalysisEvents 검색을 사용 하지 않도록 설정 합니다. |
| --skip-상관 관계-조회 | True가 전달 되는 경우이 매개 변수는 CorrelatedEvents 정보 검색을 사용 하지 않도록 설정 합니다. 그렇지 않으면 모든 FabricEvent의 CorrelationEvents get 처리 및 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행 하기 위한 서버 제한 시간 (초)입니다. 이 시간 제한은 요청 된 작업이 완료 될 때까지 클라이언트가 대기 하는 기간을 지정 합니다. 이 매개 변수의 기본값은 60 초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, 테이블, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-events-node-list"></a>sfctl 이벤트 노드 목록
노드 관련 이벤트를 가져옵니다.

응답은 NodeEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --최종 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 종료 시간입니다. |
| --node-name [필수] | 노드의 이름입니다. |
| --시작 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 시작 시간입니다. |
| --events-types-필터 | 응답에만 포함 되어야 하는 FabricEvents 유형을 지정 하는 쉼표로 구분 된 문자열입니다. |
| --제외-분석-이벤트 | True가 전달 되 면이 매개 변수는 AnalysisEvents 검색을 사용 하지 않도록 설정 합니다. |
| --skip-상관 관계-조회 | True가 전달 되는 경우이 매개 변수는 CorrelatedEvents 정보 검색을 사용 하지 않도록 설정 합니다. 그렇지 않으면 모든 FabricEvent의 CorrelationEvents get 처리 및 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행 하기 위한 서버 제한 시간 (초)입니다. 이 시간 제한은 요청 된 작업이 완료 될 때까지 클라이언트가 대기 하는 기간을 지정 합니다. 이 매개 변수의 기본값은 60 초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, 테이블, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl 이벤트 파티션-전체 복제본-목록
파티션에 대 한 모든 복제본 관련 이벤트를 가져옵니다.

응답은 ReplicaEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --최종 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 종료 시간입니다. |
| --partition-id   [필수] | 파티션의 id입니다. |
| --시작 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 시작 시간입니다. |
| --events-types-필터 | 응답에만 포함 되어야 하는 FabricEvents 유형을 지정 하는 쉼표로 구분 된 문자열입니다. |
| --제외-분석-이벤트 | True가 전달 되 면이 매개 변수는 AnalysisEvents 검색을 사용 하지 않도록 설정 합니다. |
| --skip-상관 관계-조회 | True가 전달 되는 경우이 매개 변수는 CorrelatedEvents 정보 검색을 사용 하지 않도록 설정 합니다. 그렇지 않으면 모든 FabricEvent의 CorrelationEvents get 처리 및 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행 하기 위한 서버 제한 시간 (초)입니다. 이 시간 제한은 요청 된 작업이 완료 될 때까지 클라이언트가 대기 하는 기간을 지정 합니다. 이 매개 변수의 기본값은 60 초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, 테이블, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-events-partition-list"></a>sfctl 이벤트 파티션 목록
파티션 관련 이벤트를 가져옵니다.

응답은 파티션 이벤트 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --최종 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 종료 시간입니다. |
| --partition-id   [필수] | 파티션의 id입니다. |
| --시작 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 시작 시간입니다. |
| --events-types-필터 | 응답에만 포함 되어야 하는 FabricEvents 유형을 지정 하는 쉼표로 구분 된 문자열입니다. |
| --제외-분석-이벤트 | True가 전달 되 면이 매개 변수는 AnalysisEvents 검색을 사용 하지 않도록 설정 합니다. |
| --skip-상관 관계-조회 | True가 전달 되는 경우이 매개 변수는 CorrelatedEvents 정보 검색을 사용 하지 않도록 설정 합니다. 그렇지 않으면 모든 FabricEvent의 CorrelationEvents get 처리 및 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행 하기 위한 서버 제한 시간 (초)입니다. 이 시간 제한은 요청 된 작업이 완료 될 때까지 클라이언트가 대기 하는 기간을 지정 합니다. 이 매개 변수의 기본값은 60 초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, 테이블, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl 이벤트 파티션-복제본 목록
파티션 복제본 관련 이벤트를 가져옵니다.

응답은 ReplicaEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --최종 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 종료 시간입니다. |
| --partition-id   [필수] | 파티션의 id입니다. |
| --복제본-id [필수] | 복제본의 식별자입니다. |
| --시작 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 시작 시간입니다. |
| --events-types-필터 | 응답에만 포함 되어야 하는 FabricEvents 유형을 지정 하는 쉼표로 구분 된 문자열입니다. |
| --제외-분석-이벤트 | True가 전달 되 면이 매개 변수는 AnalysisEvents 검색을 사용 하지 않도록 설정 합니다. |
| --skip-상관 관계-조회 | True가 전달 되는 경우이 매개 변수는 CorrelatedEvents 정보 검색을 사용 하지 않도록 설정 합니다. 그렇지 않으면 모든 FabricEvent의 CorrelationEvents get 처리 및 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행 하기 위한 서버 제한 시간 (초)입니다. 이 시간 제한은 요청 된 작업이 완료 될 때까지 클라이언트가 대기 하는 기간을 지정 합니다. 이 매개 변수의 기본값은 60 초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, 테이블, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-events-service-list"></a>sfctl 이벤트 서비스 목록
서비스 관련 이벤트를 가져옵니다.

응답은 ServiceEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --최종 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 종료 시간입니다. |
| --service-id     [필수] | 서비스 id입니다. 이 ID는 일반적으로 'fabric\:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 서비스 이름이 "fabric\:/myapp/app1/svc1"이면 서비스 ID는 6.0 이상에서는 "myapp\~app1\~svc1"이고 이전 버전에서는 "myapp/app1/svc1"입니다. |
| --시작 시간-utc [필수] | ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ에서 조회 쿼리의 시작 시간입니다. |
| --events-types-필터 | 응답에만 포함 되어야 하는 FabricEvents 유형을 지정 하는 쉼표로 구분 된 문자열입니다. |
| --제외-분석-이벤트 | True가 전달 되 면이 매개 변수는 AnalysisEvents 검색을 사용 하지 않도록 설정 합니다. |
| --skip-상관 관계-조회 | True가 전달 되는 경우이 매개 변수는 CorrelatedEvents 정보 검색을 사용 하지 않도록 설정 합니다. 그렇지 않으면 모든 FabricEvent의 CorrelationEvents get 처리 및 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행 하기 위한 서버 제한 시간 (초)입니다. 이 시간 제한은 요청 된 작업이 완료 될 때까지 클라이언트가 대기 하는 기간을 지정 합니다. 이 매개 변수의 기본값은 60 초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, 테이블, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

