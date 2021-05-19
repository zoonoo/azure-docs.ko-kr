---
title: Azure Service Fabric CLI - sfctl 메시 배포
description: Azure Service Fabric 명령줄 인터페이스인 sfctl에 대해 자세히 알아봅니다. Service Fabric Mesh 리소스를 만들기 위한 명령 목록을 포함합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fb2adafab88eb1d3855cdec8268601fb4e15dcbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257276"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Service Fabric Mesh 리소스를 만듭니다.

## <a name="commands"></a>명령

|명령|Description|
| --- | --- |
| create | Service Fabric Mesh 리소스의 배포를 만듭니다. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Service Fabric Mesh 리소스의 배포를 만듭니다.

### <a name="arguments"></a>인수

|인수|Description|
| --- | --- |
| --input-yaml-files[필수] | 모든 yaml 파일의 쉼표로 구분된 상대 또는 절대 파일 경로 또는 yaml 파일을 포함하는 디렉터리(재귀)의 상대 또는 절대 경로입니다. |
| --parameters | 재정의해야 하는 매개 변수를 포함하는 yaml 파일 또는 json 개체의 상대 또는 절대 경로입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|Description|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

### <a name="examples"></a>예

yaml 파일에 언급된 매개 변수를 재정의하여 모든 리소스를 클러스터에 통합하고 배포합니다.
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

yaml 파일에 언급된 매개 변수를 재정의하여 디렉터리의 모든 리소스를 클러스터에 통합하고 배포합니다.

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

json 개체로 직접 전달된 매개 변수를 재정의하여 디렉터리의 모든 리소스를 클러스터에 통합하고 배포합니다.
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](./scripts/sfctl-upgrade-application.md)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.
