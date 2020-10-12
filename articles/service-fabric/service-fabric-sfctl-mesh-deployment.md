---
title: Azure Service Fabric CLI-sfctl 메시 배포
description: Azure Service Fabric 명령줄 인터페이스인 sfctl에 대해 알아봅니다. Service Fabric 메시 리소스를 만들기 위한 명령 목록을 포함 합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fb2adafab88eb1d3855cdec8268601fb4e15dcbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257276"
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
| --input-yaml-files[필수] | Yaml 파일을 포함 하는 디렉터리의 상대 또는 절대 경로 또는 모든 yaml 파일의 쉼표로 구분 된 상대 또는 절대 파일 경로입니다. |
| --parameters | Yaml 파일에 대 한 상대 또는 절대 경로 또는 재정의 해야 하는 매개 변수를 포함 하는 json 개체입니다. |

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

Json 개체로 직접 전달 되는 매개 변수를 재정의 하 여 디렉터리의 모든 리소스를 통합 하 고 클러스터에 배포 합니다.
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>다음 단계
- Service Fabric CLI를 [설정](service-fabric-cli.md) 합니다.
- [샘플 스크립트](./scripts/sfctl-upgrade-application.md)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.
