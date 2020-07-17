---
title: Azure Service Fabric CLI-sfctl 메시 네트워크
description: Sfctl, Azure Service Fabric 명령줄 인터페이스에 대해 알아봅니다. Service Fabric 메시 네트워크 리소스를 가져오고 삭제 하는 명령 목록을 포함 합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b2b2c444bb492fa6c6b945a82090e91963fb1a8
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245863"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
메시 네트워크 리소스를 가져오고 삭제합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| 삭제 | 네트워크 리소스를 삭제합니다. |
| list | 모든 네트워크 리소스를 나열합니다. |
| 표시 | 지정된 이름의 네트워크 리소스를 가져옵니다. |

## <a name="sfctl-mesh-network-delete"></a>sfctl mesh network delete
네트워크 리소스를 삭제합니다.

이름으로 식별되는 네트워크 리소스를 삭제합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --name -n [필수] | 네트워크의 이름입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-mesh-network-list"></a>sfctl mesh network list
모든 네트워크 리소스를 나열합니다.

지정된 리소스 그룹의 모든 네트워크 리소스에 대한 정보를 가져옵니다. 정보에는 네트워크에 대한 설명과 기타 속성이 포함됩니다.

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-mesh-network-show"></a>sfctl mesh network show
지정된 이름의 네트워크 리소스를 가져옵니다.

지정된 이름의 네트워크 리소스에 대한 정보를 가져옵니다. 정보에는 네트워크에 대한 설명과 기타 속성이 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --name -n [필수] | 네트워크의 이름입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |


## <a name="next-steps"></a>다음 단계
- Service Fabric CLI를 [설정](service-fabric-cli.md) 합니다.
- [샘플 스크립트](./scripts/sfctl-upgrade-application.md)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.
