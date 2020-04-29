---
title: Azure Service Fabric CLI-sfctl 컨테이너
description: Sfctl, Azure Service Fabric 명령줄 인터페이스에 대해 알아봅니다. 컨테이너에 대 한 명령 목록을 포함 합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 07861e2b67eea67740b341cbea994de978973664
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906122"
---
# <a name="sfctl-container"></a>sfctl container
클러스터 노드에서 컨테이너 관련 명령을 실행합니다.

## <a name="commands"></a>명령

|명령|Description|
| --- | --- |
| invoke-api | 지정된 코드 패키지에 대해 Service Fabric 노드에 배포한 컨테이너에 대한 컨테이너 API를 호출합니다. |
| 로그 | Service Fabric 노드에 배포한 컨테이너에 대한 컨테이너 로그를 가져옵니다. |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
지정된 코드 패키지에 대해 Service Fabric 노드에 배포한 컨테이너에 대한 컨테이너 API를 호출합니다.

### <a name="arguments"></a>인수

|인수|Description|
| --- | --- |
| --application-id           [필수] | 애플리케이션 ID입니다. <br><br> 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --code-package-instance-id [필수] | Service Fabric 노드에 배포된 코드 패키지 인스턴스를 고유하게 식별하는 ID입니다. <br><br> 'service code-package-list'로 검색할 수 있습니다. |
| --code-package-name        [필수] | Service Fabric 클러스터에 애플리케이션 유형의 일부로 등록된 서비스 매니페스트에 지정된 코드 패키지의 이름입니다. |
| --container-api-uri-path   [필수] | 컨테이너 REST API의 URI 경로입니다. 컨테이너 이름/ID 대신 '{ID}'를 사용합니다. |
| --node-name                [필수] | 노드의 이름입니다. |
| --service-manifest-name    [필수] | Service Fabric 클러스터에 애플리케이션 유형의 일부로 등록된 서비스 매니페스트의 이름입니다. |
| --container-api-body | 컨테이너 REST API에 대한 HTTP 요청 본문입니다. |
| --container-api-content-type | 컨테이너 REST API에 대한 콘텐츠 형식입니다. 'application/json'으로 기본값을 지정합니다. |
| --container-api-http-verb | 컨테이너 REST API에 대한 HTTP 동사입니다. GET으로 기본값을 지정합니다. |
| --timeout -t | 기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|Description|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-container-logs"></a>sfctl container logs
Service Fabric 노드에 배포한 컨테이너에 대한 컨테이너 로그를 가져옵니다.

### <a name="arguments"></a>인수

|인수|Description|
| --- | --- |
| --application-id           [필수] | 애플리케이션 ID입니다. <br><br> 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --code-package-instance-id [필수] | 'service code-package-list'에서 검색할 수 있는 코드 패키지 인스턴스 ID입니다. |
| --code-package-name        [필수] | Service Fabric 클러스터에 애플리케이션 유형의 일부로 등록된 서비스 매니페스트에 지정된 코드 패키지의 이름입니다. |
| --node-name                [필수] | 노드의 이름입니다. |
| --service-manifest-name    [필수] | Service Fabric 클러스터에 애플리케이션 유형의 일부로 등록된 서비스 매니페스트의 이름입니다. |
| --tail | 로그의 끝에서 표시할 줄의 수입니다. 기본값은 100입니다. 전체 로그를 표시하는 'all'입니다. |
| --timeout -t | 기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|Description|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |


## <a name="next-steps"></a>다음 단계
- Service Fabric CLI를 [설정](service-fabric-cli.md) 합니다.
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.