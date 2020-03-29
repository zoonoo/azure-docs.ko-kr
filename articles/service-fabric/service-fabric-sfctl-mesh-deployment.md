---
title: Azure 서비스 패브릭 CLI-sfctl 메시 배포
description: Azure 서비스 패브릭 명령줄 인터페이스인 sfctl에 대해 알아봅니다. 서비스 패브릭 메시 리소스를 만들기 위한 명령 목록을 포함합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906033"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Service Fabric Mesh 리소스를 만듭니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| create | Service Fabric Mesh 리소스의 배포를 만듭니다. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Service Fabric Mesh 리소스의 배포를 만듭니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --input-yaml-files[필수] | yaml 파일을 포함하는 모든 yaml 파일또는 디렉터리(재귀)의 상대적 또는 절대 경로의 쉼표 분리된 상대 또는 절대 파일 경로입니다. |
| --parameters | yaml 파일 또는 재정의해야 하는 매개 변수를 포함하는 json 개체에 대한 상대 또는 절대 경로입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
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

json 개체로 직접 전달되는 매개 변수를 재정의하여 디렉터리에서 모든 리소스를 클러스터에 통합하고 배포합니다.
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>다음 단계
- 서비스 패브릭 [CLI를 설정합니다.](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.
