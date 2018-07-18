---
title: Azure Service Fabric CLI- sfctl service| Microsoft Docs
description: Service Fabric CLI sfctl service 명령을 설명합니다.
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
ms.openlocfilehash: e027bb7f61d19fc274f7eddd8f28e9e6dac099ce
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763547"
---
# <a name="sfctl-service"></a>sfctl service
서비스, 서비스 유형 및 서비스 패키지를 생성, 삭제 및 관리합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| app-name | 서비스에 대한 Service Fabric 응용 프로그램 이름을 가져옵니다. |
| code-package-list | Service Fabric 노드에 배포된 코드 패키지 목록을 가져옵니다. |
| create | 지정된 Service Fabric 서비스를 만듭니다. |
| delete | 기존 Service Fabric 서비스를 삭제합니다. |
| deployed-type | Service Fabric 클러스터의 노드에 배포된 응용 프로그램의 지정된 서비스 유형에 대한 정보를 가져옵니다. |
| deployed-type-list | Service Fabric 클러스터의 노드에 배포된 응용 프로그램의 서비스 유형에 대한 정보가 포함된 목록을 가져옵니다. |
| description | 기존 Service Fabric서비스에 대한 설명을 가져옵니다. |
| get-container-logs | Service Fabric 노드에 배포한 컨테이너에 대한 컨테이너 로그를 가져옵니다. |
| health | 지정된 Service Fabric 서비스의 상태를 가져옵니다. |
| info | Service Fabric 응용 프로그램에 속하는 특정 서비스에 대한 정보를 가져옵니다. |
| list | 응용 프로그램 ID로 지정된 응용 프로그램에 속하는 모든 서비스에 대한 정보를 가져옵니다. |
| manifest | 서비스 유형을 설명하는 매니페스트를 가져옵니다. |
| package-deploy | 지정한 서비스 매니페스트와 연결된 패키지를 지정된 노드의 이미지 캐시에 다운로드합니다. |
| package-health | Service Fabric 노드 및 응용 프로그램에 배포된 특정 응용 프로그램의 서비스 패키지 상태에 대한 정보를 가져옵니다. |
| package-info | 지정한 이름과 정확히 일치하는 Service Fabric 노드에 배포된 서비스 패키지 목록을 가져옵니다. |
| package-list | Service Fabric 노드에 배포된 서비스 패키지 목록을 가져옵니다. |
| recover | 현재 쿼럼 손실 상태에 있는 지정된 서비스를 복구하려고 시도해야 한다는 것을 Service Fabric 클러스터에 나타냅니다. |
| report-health | Service Fabric 서비스에 대한 상태 보고서를 보냅니다. |
| resolve | Service Fabric 파티션을 해결합니다. |
| type-list | Service Fabric 클러스터에 프로비저닝된 응용 프로그램 유형에서 지원하는 서비스 유형에 대한 정보가 포함된 목록을 가져옵니다. |
| update | 지정된 업데이트 설명을 사용하여 지정된 서비스를 업데이트합니다. |

## <a name="sfctl-service-app-name"></a>sfctl service app-name
서비스에 대한 Service Fabric 응용 프로그램 이름을 가져옵니다.

지정된 서비스에 대한 응용 프로그램의 이름을 가져옵니다. 제공된 서비스 ID가 있는 서비스가 존재하지 않는 경우 404 FABRIC_E_SERVICE_DOES_NOT_EXIST 오류가 반환됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --service-id [필수] | 서비스 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 서비스 이름이 "fabric\:/myapp/app1/svc1"이면 서비스 ID는 6.0 이상에서는 "myapp\~app1\~svc1"이고 이전 버전에서는 "myapp/app1/svc1"입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-code-package-list"></a>sfctl service code-package-list
Service Fabric 노드에 배포된 코드 패키지 목록을 가져옵니다.

지정된 응용 프로그램에 대한 Service Fabric 노드에 배포된 코드 패키지 목록을 가져옵니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 응용 프로그램의 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 “fabric\:/myapp/app1”인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --node-name [필수] | 노드의 이름입니다. |
| --code-package-name | Service Fabric 클러스터에 응용 프로그램 유형의 일부로 등록된 서비스 매니페스트에 지정된 코드 패키지의 이름입니다. |
| --service-manifest-name | Service Fabric 클러스터에 응용 프로그램 유형의 일부로 등록된 서비스 매니페스트의 이름입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-create"></a>sfctl service create
지정된 Service Fabric 서비스를 만듭니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-id       [필수] | 응용 프로그램의 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 '\~' 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 'fabric\:/myapp/app1'인 경우 응용 프로그램 ID가 6.0 이상에서는 'myapp\~app1'이고 이전 버전에서는 'myapp/app1'입니다. |
| --name         [필수] | 서비스의 이름입니다. 응용 프로그램 ID의 자식이어야 합니다. `fabric\:` URI를 포함한 전체 이름입니다. 예를 들어 서비스 `fabric\:/A/B`는 응용 프로그램 `fabric\:/A`의 자식입니다. |
| --service-type [필수] | 서비스 형식 이름입니다. |
| --activation-mode | 서비스 패키지의 활성화 모드입니다. |
| --constraints | 문자열 형태의 배치 제약 조건입니다. 배치 제약 조건은 노드 속성에 대한 부울 식이며 서비스 요구 사항을 기반으로 특정 노드에 대한 서비스 제한을 허용합니다. 예를 들어, NodeType이 파란색인 노드에 서비스를 배치하려면 "NodeColor == blue"를 지정합니다. |
| --correlated-service | 상관 관계를 지정할 대상 서비스의 이름입니다. |
| --correlation | 맞춤 선호도를 사용하여 서비스를 기존 서비스와 상호 연결합니다. |
| --dns-name | 만들 서비스의 DNS 이름입니다. 이 설정을 위해 Service Fabric DNS 시스템 서비스를 사용하도록 설정해야 합니다. |
| --instance-count | 인스턴스 수입니다. 상태 비저장 서비스에만 적용됩니다. |
| --int-scheme | 서비스가 부호 없는 정수 범위 내에서 균일하게 분할되어야 함을 나타냅니다. |
| --int-scheme-count | 균일한 정수 파티션 구성표를 사용하는 경우 생성할 정수 키 범위 내의 파티션 수입니다. |
| --int-scheme-high | 균일한 정수 파티션 구성표를 사용하는 경우 키 정수 범위의 끝입니다. |
| --int-scheme-low | 균일한 정수 파티션 구성표를 사용하는 경우 키 정수 범위의 시작입니다. |
| --load-metrics | 노드에서 서비스의 부하를 분산할 때 사용되는 메트릭의 JSON 인코딩된 목록입니다. |
| --min-replica-set-size | 숫자로 된 최소 복제본 세트 크기입니다. 상태 저장 서비스에만 적용됩니다. |
| --move-cost | 서비스에 대한 이동 비용을 지정합니다. 가능한 값\: 'Zero', 'Low', 'Medium', 'High'. |
| --named-scheme | 서비스에 여러 개의 명명된 파티션이 있어야 함을 나타냅니다. |
| --named-scheme-list | 명명된 파티션 구성표를 사용하는 경우 서비스를 분할할 이름의 JSON 인코딩된 목록입니다. |
| --no-persisted-state | true인 경우 서비스에 로컬 디스크에 저장된 영구 상태가 없거나 메모리에만 상태가 저장됨을 나타냅니다. |
| --placement-policy-list | 서비스에 대한 배치 정책 및 연결된 도메인 이름의 JSON 인코딩된 목록입니다. 정책은 다음 중 하나 이상일 수 있습니다.\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | 파티션이 쿼럼 손실 상태가 될 수 있는 최대 지속 시간(초)입니다. 상태 저장 서비스에만 적용됩니다. |
| --replica-restart-wait | 복제본이 다운된 시점과 새 복제본이 생성되는 시점 사이의 간격(초)입니다. 상태 저장 서비스에만 적용됩니다. |
| --scaling-policies | 이 서비스에 대한 크기 조정 정책의 JSON 인코딩된 목록입니다. |
| --singleton-scheme | 서비스에 단일 파티션이 있거나 분할되지 않은 서비스여야 함을 나타냅니다. |
| --stand-by-replica-keep | StandBy 복제본이 제거되기 전 유지되는 최대 기간(초)입니다. 상태 저장 서비스에만 적용됩니다. |
| --stateful | 서비스가 상태 저장 서비스임을 나타냅니다. |
| --stateless | 서비스가 상태 비저장 서비스임을 나타냅니다. |
| --target-replica-set-size | 숫자로 된 대상 복제본 세트 크기입니다. 상태 저장 서비스에만 적용됩니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-delete"></a>sfctl service delete
기존 Service Fabric 서비스를 삭제합니다.

서비스를 삭제하려면 먼저 만들어야 합니다. 기본적으로 Service Fabric은 서비스 복제를 정상적으로 닫은 후 다음 서비스를 삭제합니다. 하지만 서비스에서 복제본을 정상적으로 닫는 데 문제가 있으면 삭제 작업이 오래 걸리거나 중단될 수 있습니다. 선택적인 ForceRemove 플래그를 사용하여 정상적인 닫기 시퀀스를 건너 뛰고 서비스를 강제로 삭제합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --service-id [필수] | 서비스 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 서비스 이름이 "fabric\:/myapp/app1/svc1"이면 서비스 ID는 6.0 이상에서는 "myapp\~app1\~svc1"이고 이전 버전에서는 "myapp/app1/svc1"입니다. |
| --force-remove | 정상적인 종료 시퀀스를 거치지 않고 강제로 Service Fabric 응용 프로그램이나 서비스를 제거합니다. 이 매개 변수는 복제본을 정상적으로 종료하지 못하게 하는 서비스 코드의 문제로 인해 삭제 시간이 초과되는 응용 프로그램이나 서비스를 강제로 삭제하는 데 사용할 수 있습니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-deployed-type"></a>sfctl service deployed-type
Service Fabric 클러스터의 노드에 배포된 응용 프로그램의 지정된 서비스 유형에 대한 정보를 가져옵니다.

Service Fabric 클러스터의 노드에 배포된 응용 프로그램의 특정 서비스 유형에 대한 정보가 포함된 목록을 가져옵니다. 응답은 서비스 유형의 이름, 해당 등록 상태, 등록한 코드 패키지 및 서비스 패키지의 활성화 ID를 포함합니다. 각 항목은 활성화 ID로 구분된 서비스 유형의 하나의 활성화를 나타냅니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id    [필수] | 응용 프로그램의 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 “fabric\:/myapp/app1”인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --node-name         [필수] | 노드의 이름입니다. |
| --service-type-name [필수] | Service Fabric 서비스 유형의 이름을 지정합니다. |
| --service-manifest-name | 배포된 서비스 형식 정보의 목록을 필터링하는 서비스 매니페스트의 이름입니다. 지정된 경우 응답은 이 서비스 매니페스트에 정의된 서비스 형식에 대한 정보만을 포함합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl service deployed-type-list
Service Fabric 클러스터의 노드에 배포된 응용 프로그램의 서비스 유형에 대한 정보가 포함된 목록을 가져옵니다.

Service Fabric 클러스터의 노드에 배포된 응용 프로그램의 서비스 유형에 대한 정보가 포함된 목록을 가져옵니다. 응답은 서비스 유형의 이름, 해당 등록 상태, 등록한 코드 패키지 및 서비스 패키지의 활성화 ID를 포함합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 응용 프로그램의 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 “fabric\:/myapp/app1”인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --node-name [필수] | 노드의 이름입니다. |
| --service-manifest-name | 배포된 서비스 형식 정보의 목록을 필터링하는 서비스 매니페스트의 이름입니다. 지정된 경우 응답은 이 서비스 매니페스트에 정의된 서비스 형식에 대한 정보만을 포함합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-description"></a>sfctl service description
기존 Service Fabric서비스에 대한 설명을 가져옵니다.

기존 Service Fabric서비스에 대한 설명을 가져옵니다. 서비스 설명을 가져올 수 있으려면 먼저 서비스를 만들어야 합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --service-id [필수] | 서비스 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 서비스 이름이 "fabric\:/myapp/app1/svc1"이면 서비스 ID는 6.0 이상에서는 "myapp\~app1\~svc1"이고 이전 버전에서는 "myapp/app1/svc1"입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-get-container-logs"></a>sfctl service get-container-logs
Service Fabric 노드에 배포한 컨테이너에 대한 컨테이너 로그를 가져옵니다.

지정된 코드 패키지에 대해 Service Fabric 노드에 배포한 컨테이너에 대한 컨테이너 로그를 가져옵니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id        [필수] | 응용 프로그램의 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 “fabric\:/myapp/app1”인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --code-package-name     [필수] | Service Fabric 클러스터에 응용 프로그램 유형의 일부로 등록된 서비스 매니페스트에 지정된 코드 패키지의 이름입니다. |
| --node-name             [필수] | 노드의 이름입니다. |
| --service-manifest-name [필수] | Service Fabric 클러스터에 응용 프로그램 유형의 일부로 등록된 서비스 매니페스트의 이름입니다. |
| --previous | 코드 패키지 인스턴스의 종료된/배달 못 한 컨테이너에서 컨테이너 로그를 가져올지 여부를 지정합니다. |
| --tail | 로그의 끝에서 표시할 줄의 수입니다. 기본값은 100입니다. 전체 로그를 표시하는 'all'입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-health"></a>sfctl service health
지정된 Service Fabric 서비스의 상태를 가져옵니다.

지정된 서비스의 상태 정보를 가져옵니다. 상태에 따라 서비스에 보고된 상태 이벤트의 컬렉션을 필터링하려면 EventsHealthStateFilter를 사용합니다. 반환된 파티션 컬렉션을 필터링하려면 PartitionsHealthStateFilter를 사용합니다. Health 스토어에 없는 서비스를 지정하면 이 요청이 오류를 반환합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --service-id          [필수] | 서비스 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 서비스 이름이 "fabric\:/myapp/app1/svc1"이면 서비스 ID는 6.0 이상에서는 "myapp\~app1\~svc1"이고 이전 버전에서는 "myapp/app1/svc1"입니다. |
| --events-health-state-filter | 상태에 따라 반환된 HealthEvent 개체의 컬렉션을 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 이벤트만 반환됩니다. 모든 이벤트는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 모든 이벤트가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --exclude-health-statistics | 상태 통계가 쿼리 결과의 일부로 반환되어야 하는지 여부를 나타냅니다. False(기본값). 통계는 Ok, Warning 및 Error 상태의 자식 엔터티 수를 보여줍니다. |
| --partitions-health-state-filter | 상태에 따라 서비스 상태 쿼리의 결과로 반환된 파티션 상태 개체를 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 파티션만 반환됩니다. 모든 파티션은 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 파티션의 상태가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-info"></a>sfctl service info
Service Fabric 응용 프로그램에 속하는 특정 서비스에 대한 정보를 가져옵니다.

지정된 Service Fabric 응용 프로그램에 속하는 지정된 서비스에 대한 정보를 반환합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 응용 프로그램의 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 “fabric\:/myapp/app1”인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --service-id     [필수] | 서비스 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 서비스 이름이 "fabric\:/myapp/app1/svc1"이면 서비스 ID는 6.0 이상에서는 "myapp\~app1\~svc1"이고 이전 버전에서는 "myapp/app1/svc1"입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-list"></a>sfctl service list
응용 프로그램 ID로 지정된 응용 프로그램에 속하는 모든 서비스에 대한 정보를 가져옵니다.

응용 프로그램 ID로 지정된 응용 프로그램에 속하는 모든 서비스에 대한 정보를 반환합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 응용 프로그램의 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 “fabric\:/myapp/app1”인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --continuation-token | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다. |
| --service-type-name | 쿼리할 서비스를 필터링하는 데 사용되는 서비스 유형 이름입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-manifest"></a>sfctl service manifest
서비스 유형을 설명하는 매니페스트를 가져옵니다.

서비스 유형을 설명하는 매니페스트를 가져옵니다. 응답에 서비스 매니페스트 XML이 문자열로 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-type-name    [필수] | 응용 프로그램 유형의 이름입니다. |
| --application-type-version [필수] | 응용 프로그램 유형의 버전입니다. |
| --service-manifest-name    [필수] | Service Fabric 클러스터에 응용 프로그램 유형의 일부로 등록된 서비스 매니페스트의 이름입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-package-deploy"></a>sfctl service package-deploy
지정한 서비스 매니페스트와 연결된 패키지를 지정된 노드의 이미지 캐시에 다운로드합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-type-name         [필수] | 해당하는 요청된 서비스 매니페스트에 대한 응용 프로그램 매니페스트의 이름입니다. |
| --app-type-version      [필수] | 해당하는 요청된 서비스 매니페스트에 대한 응용 프로그램 매니페스트의 버전입니다. |
| --node-name             [필수] | 노드의 이름입니다. |
| --service-manifest-name [필수] | 다운로드할 패키지와 연결된 서비스 매니페스트의 이름입니다. |
| --share-policy | 공유 정책의 JSON 인코딩된 목록입니다. 각 공유 정책 요소는 '이름' 및 '범위'로 구성됩니다. 이름은 공유할 코드, 구성 또는 데이터 패키지의 이름에 해당합니다. 범위는 'None', 'All', 'Code', 'Config' 또는 'Data'일 수 있습니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-package-health"></a>sfctl service package-health
Service Fabric 노드 및 응용 프로그램에 배포된 특정 응용 프로그램의 서비스 패키지 상태에 대한 정보를 가져옵니다.

Service Fabric 노드에 배포된 특정 응용 프로그램의 서비스 패키지 상태에 대한 정보를 가져옵니다. EventsHealthStateFilter를 사용하여 필요에 따라 상태에 따라 배포된 서비스 패키지에 보고된 HealthEvent 개체의 컬렉션에 대해 필터링합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id      [필수] | 응용 프로그램의 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 “fabric\:/myapp/app1”인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --node-name            [필수] | 노드의 이름입니다. |
| --service-package-name [필수] | 서비스 패키지의 이름입니다. |
| --events-health-state-filter | 상태에 따라 반환된 HealthEvent 개체의 컬렉션을 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 이벤트만 반환됩니다. 모든 이벤트는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 모든 이벤트가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-package-info"></a>sfctl service package-info
지정한 이름과 정확히 일치하는 Service Fabric 노드에 배포된 서비스 패키지 목록을 가져옵니다.

지정된 응용 프로그램에 대한 Service Fabric 노드에 배포된 서비스 패키지에 대한 정보를 반환합니다. 이러한 결과는 해당 이름이 매개 변수로 지정된 서비스 패키지 이름과 정확히 일치하는 서비스 패키지입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id      [필수] | 응용 프로그램의 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 “fabric\:/myapp/app1”인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --node-name            [필수] | 노드의 이름입니다. |
| --service-package-name [필수] | 서비스 패키지의 이름입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-package-list"></a>sfctl service package-list
Service Fabric 노드에 배포된 서비스 패키지 목록을 가져옵니다.

지정된 응용 프로그램에 대한 Service Fabric 노드에 배포된 서비스 패키지에 대한 정보를 반환합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 응용 프로그램의 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 “fabric\:/myapp/app1”인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --node-name [필수] | 노드의 이름입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-recover"></a>sfctl service recover
현재 쿼럼 손실 상태에 있는 지정된 서비스를 복구하려고 시도해야 한다는 것을 Service Fabric 클러스터에 나타냅니다.

현재 쿼럼 손실 상태에 있는 지정된 서비스를 복구하려고 시도해야 한다는 것을 Service Fabric 클러스터에 나타냅니다. 이 작업은 다운된 복제본을 복구하지 못할 경우에만 수행해야 합니다. 이 API를 잘못 사용하면 잠재적인 데이터 손실이 발생할 수 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --service-id [필수] | 서비스 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 서비스 이름이 "fabric\:/myapp/app1/svc1"이면 서비스 ID는 6.0 이상에서는 "myapp\~app1\~svc1"이고 이전 버전에서는 "myapp/app1/svc1"입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-report-health"></a>sfctl service report-health
Service Fabric 서비스에 대한 상태 보고서를 보냅니다.

지정된 Service Fabric 서비스의 상태를 보고합니다. 보고서는 상태 보고서의 원본 및 보고되는 속성에 대한 정보를 포함해야 합니다. 보고서는 Health 스토어에 전달하는 Service Fabric 게이트웨이 서비스에 전송됩니다. 보고서는 게이트웨이에 의해 허용되지만 추가 유효성 검사 후 Health 스토어에 의해 거부될 수 있습니다. 예를 들어 Health 스토어는 부실 시퀀스 번호와 같은 잘못된 매개 변수로 인해 보고서를 거부할 수도 있습니다. 보고서가 Health 스토어에서 적용되었는지 여부를 확인하려면 해당 보고서가 서비스의 상태 이벤트에 표시되는지 확인합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --health-property [필수] | 상태 정보의 속성입니다. <br><br> 엔터티는 서로 다른 속성에 대한 상태 보고서를 가질 수 있습니다. 속성은 문자열이며 보고자 유연성에서 보고서를 트리거하는 상태 조건을 분류하도록 허용하는 고정된 열거형이 아닙니다. 예를 들어 SourceId "LocalWatchdog"가 있는 보고자는 노드에서 사용 가능한 디스크의 상태를 모니터링할 수 있으므로 해당 노드에서 "AvailableDisk" 속성을 보고할 수 있습니다. 동일한 보고자는 노드 연결을 모니터링할 수 있으므로 동일한 노드에서 "Connectivity" 속성을 보고할 수 있습니다. Health 스토어에서 이러한 보고서는 지정된 노드에 대한 별도의 상태 이벤트로 처리됩니다. SourceId와 함께 속성은 상태 정보를 고유하게 식별합니다. |
| --health-state    [필수] | 가능한 값\: 'Invalid', 'Ok', 'Warning', 'Error', 'Unknown'. |
| --service-id      [필수] | 서비스 id입니다. <br><br> 일반적으로 'fabric\:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 '\~' 문자로 구분됩니다. 예를 들어 서비스 이름이 'fabric\:/myapp/app1/svc1'이면 서비스 ID는 6.0 이상에서는 'myapp\~app1\~svc1'이고 이전 버전에서는 'myapp/app1/svc1'입니다. |
| --source-id       [필수] | 상태 정보를 생성한 클라이언트/watchdog/시스템 구성 요소를 식별하는 원본 이름입니다. |
| --description | 상태 정보의 설명입니다. <br><br> 보고서에 대한 사람이 읽을 수 있는 정보를 추가하는 데 사용되는 일반 텍스트를 나타냅니다. 설명의 최대 문자열 길이는 4096자입니다. 제공된 문자열이 긴 경우 자동으로 잘립니다. 잘린 경우 설명의 마지막 문자는 "[잘림]" 표식을 포함하고, 총 문자열 크기는 4096자입니다. 표식은 사용자에게 잘림이 발생했음을 나타냅니다. 잘린 경우 설명은 원래 문자열의 4096자보다 작은 문자를 갖습니다. |
| --immediate | 보고서를 즉시 보낼지 여부를 나타내는 플래그입니다. <br><br> 상태 보고서는 Health 스토어에 전달하는 Service Fabric 게이트웨이 응용 프로그램에 전송됩니다. Immediate가 true로 설정된 경우 보고서는 HTTP 게이트웨이 응용 프로그램이 사용 중인 패브릭 클라이언트 설정에 관계 없이 HTTP 게이트웨이에서 Health 스토어로 즉시 전송됩니다. 이는 가능한 한 빨리 보내야 하는 중요한 보고서에 유용합니다. 타이밍 및 기타 조건에 따라 보고서 전송이 여전히 실패할 수 있습니다. 예를 들어, HTTP 게이트웨이가 닫혀 있거나 메시지가 게이트웨이에 도달하지 않는 경우입니다. Immediate가 false로 설정된 경우 보고서는 HTTP 게이트웨이의 상태 클라이언트 설정에 따라 전송됩니다. 따라서 HealthReportSendInterval 구성에 따라 일괄처리됩니다. 상태 클라이언트가 상태 보고서 처리뿐만 아니라 Health 스토어로 메시지를 보고하는 상태를 최적화하도록 허용하므로 이는 권장 설정입니다. 기본적으로 보고서는 즉시 전송되지 않습니다. |
| --remove-when-expired | 보고서가 만료될 때 Health 스토어에서 제거할지 여부를 나타내는 값입니다. <br><br> true로 설정된 경우 보고서는 만료된 후 Health 스토어에서 제거됩니다. false로 설정된 경우 보고서는 만료될 때 오류로 처리됩니다. 이 속성의 값은 기본적으로 false입니다. 클라이언트가 주기적으로 보고하는 경우 RemoveWhenExpired를 false(기본값)로 설정해야 합니다. 이러한 방식은 문제가 있고(예: 교착 상태) 보고할 수 없는 보고자이며, 엔터티는 상태 보고서가 만료되는 경우 오류로 평가됩니다. 엔터티를 오류 성능 상태에 있는 것으로 플래그 지정합니다. |
| --sequence-number | 숫자 문자열의 이 상태 보고서에 대한 일련 번호입니다. <br><br> 보고서 일련 번호는 Health 스토어에서 유효하지 않은 보고서를 검색하는 데 사용됩니다. 지정되지 않은 경우 일련 번호는 보고서가 추가될 때 상태 클라이언트에서 자동으로 생성됩니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |
| --ttl | 이 상태 보고서가 유효한 기간입니다. 이 필드는 기간을 지정하기 위해 ISO8601 형식을 사용합니다. <br><br> 클라이언트가 주기적으로 보고하는 경우 TTL(Time to Live)보다 높은 빈도로 보고서를 보내야 합니다. 클라이언트가 전환 시 보고하는 경우 TTL(Time to live)을 무한으로 설정할 수 있습니다. TTL(Time to live)이 만료되면 상태 정보를 포함하는 상태 이벤트는 RemoveWhenExpired가 true인 경우 Health 스토어에서 제거되거나 RemoveWhenExpired가 false인 경우 오류로 평가됩니다. 지정되지 않은 경우 TTL(Time to live)은 무한 값으로 기본 설정됩니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Service Fabric 파티션을 해결합니다.

Service Fabric 서비스 파티션을 확인하여 서비스 복제본의 끝점을 가져옵니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --service-id [필수] | 서비스 id입니다. 일반적으로 'fabric\:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 서비스 이름이 "fabric\:/myapp/app1/svc1"이면 서비스 ID는 6.0 이상에서는 "myapp\~app1\~svc1"이고 이전 버전에서는 "myapp/app1/svc1"입니다. |
| --partition-key-type | 파티션의 키 유형입니다. 서비스의 파티션 구성표가 Int64Range 또는 Named인 경우 이 매개 변수가 필요합니다. 가능한 값은 다음과 같습니다. - None(1) - PartitionKeyValue 매개 변수가 지정되지 않았음을 나타냅니다. 파티션 구성표가 싱글톤인 파티션에 유효합니다. 기본값입니다. 값은 1입니다. - Int64Range(2) - PartitionKeyValue 매개 변수가 int64 파티션 키임을 나타냅니다. 파티션 구성표가 Int64Range인 파티션에 유효합니다. 값은 2입니다. - Named(3) - PartitionKeyValue 매개 변수가 파티션의 이름임을 나타냅니다. 파티션 구성표가 Named인 파티션에 유효합니다. 값은 3입니다. |
| --partition-key-value | 파티션 키입니다. 서비스의 파티션 구성표가 Int64Range 또는 Named인 경우 필요합니다. |
| --previous-rsp-version | 이전에 수신한 응답의 버전 필드 값입니다. 이전에 얻은 결과가 오래되었다는 것을 사용자가 아는 경우에 필요합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-type-list"></a>sfctl service type-list
Service Fabric 클러스터에 프로비저닝된 응용 프로그램 유형에서 지원하는 서비스 유형에 대한 정보가 포함된 목록을 가져옵니다.

Service Fabric 클러스터에 프로비저닝된 응용 프로그램 유형에서 지원하는 서비스 유형에 대한 정보가 포함된 목록을 가져옵니다. 제공된 응용 프로그램 유형이 존재해야 합니다. 그렇지 않으면 404 상태가 반환됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-type-name    [필수] | 응용 프로그램 유형의 이름입니다. |
| --application-type-version [필수] | 응용 프로그램 유형의 버전입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-service-update"></a>sfctl service update
지정된 업데이트 설명을 사용하여 지정된 서비스를 업데이트합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --service-id   [필수] | 서비스의 ID입니다. 이 ID는 일반적으로 'fabric\:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 서비스 이름이 'fabric\:/myapp/app1/svc1'이면 서비스 ID는 6.0 이상에서는 'myapp\~app1\~svc1'이고 이전 버전에서는 'myapp/app1/svc1'입니다. |
| --constraints | 문자열 형태의 배치 제약 조건입니다. 배치 제약 조건은 노드 속성에 대한 부울 식이며 서비스 요구 사항을 기반으로 특정 노드에 대한 서비스 제한을 허용합니다. 예를 들어, NodeType이 파란색인 노드에 서비스를 배치하려면 "NodeColor == blue"를 지정합니다. |
| --correlated-service | 상관 관계를 지정할 대상 서비스의 이름입니다. |
| --correlation | 맞춤 선호도를 사용하여 서비스를 기존 서비스와 상호 연결합니다. |
| --instance-count | 인스턴스 수입니다. 상태 비저장 서비스에만 적용됩니다. |
| --load-metrics | 노드에서 부하를 분산할 때 사용되는 메트릭의 JSON 인코딩된 목록입니다. |
| --min-replica-set-size | 숫자로 된 최소 복제본 세트 크기입니다. 이 집합 크기는 상태 저장 서비스에만 적용됩니다. |
| --move-cost | 서비스에 대한 이동 비용을 지정합니다. 가능한 값\: 'Zero', 'Low', 'Medium', 'High'. |
| --placement-policy-list | 서비스에 대한 배치 정책 및 연결된 도메인 이름의 JSON 인코딩된 목록입니다. 정책은 다음 중 하나 이상일 수 있습니다.\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | 파티션이 쿼럼 손실 상태가 될 수 있는 최대 지속 시간(초)입니다. 이 기간은 상태 저장 서비스에만 적용됩니다. |
| --replica-restart-wait | 복제본이 다운된 시점과 새 복제본이 생성되는 시점 사이의 간격(초)입니다. 이 기간은 상태 저장 서비스에만 적용됩니다. |
| --scaling-policies | 이 서비스에 대한 크기 조정 정책의 JSON 인코딩된 목록입니다. |
| --stand-by-replica-keep | StandBy 복제본이 제거되기 전 유지되는 최대 기간(초)입니다. 이 기간은 상태 저장 서비스에만 적용됩니다. |
| --stateful | 대상 서비스가 상태 저장 서비스임을 나타냅니다. |
| --stateless | 대상 서비스가 상태 비저장 서비스임을 나타냅니다. |
| --target-replica-set-size | 숫자로 된 대상 복제본 세트 크기입니다. 이 집합 크기는 상태 저장 서비스에만 적용됩니다. |
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
