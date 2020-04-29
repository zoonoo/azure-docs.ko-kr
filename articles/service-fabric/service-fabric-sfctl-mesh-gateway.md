---
title: Azure Service Fabric CLI-sfctl 메시 게이트웨이
description: Sfctl, Azure Service Fabric 명령줄 인터페이스에 대해 알아봅니다. Service Fabric 메시 gateway 리소스를 가져오고 삭제 하는 명령 목록을 포함 합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3e398ff8a1a0a28fd9d0650a7c9d18ba04817d33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906006"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
메시 게이트웨이 리소스를 가져오고 삭제합니다.

## <a name="commands"></a>명령

|명령|Description|
| --- | --- |
| 삭제 | 게이트웨이 리소스를 삭제합니다. |
| list | 모든 게이트웨이 리소스를 나열합니다. |
| show | 지정된 이름의 게이트웨이 리소스를 가져옵니다. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl mesh gateway delete
게이트웨이 리소스를 삭제합니다.

이름으로 식별되는 게이트웨이 리소스를 삭제합니다.

### <a name="arguments"></a>인수

|인수|Description|
| --- | --- |
| --name -n [필수] | 게이트웨이 리소스의 이름입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|Description|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-mesh-gateway-list"></a>sfctl mesh gateway list
모든 게이트웨이 리소스를 나열합니다.

지정된 리소스 그룹의 모든 게이트웨이 리소스에 대한 정보를 가져옵니다. 정보에는 게이트웨이에 대한 설명과 기타 속성이 포함됩니다.

### <a name="global-arguments"></a>전역 인수

|인수|Description|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl mesh gateway show
지정된 이름의 게이트웨이 리소스를 가져옵니다.

지정된 이름의 게이트웨이 리소스에 대한 정보를 가져옵니다. 정보에는 게이트웨이에 대한 설명과 기타 속성이 포함됩니다.

### <a name="arguments"></a>인수

|인수|Description|
| --- | --- |
| --name -n [필수] | 게이트웨이 리소스의 이름입니다. |

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