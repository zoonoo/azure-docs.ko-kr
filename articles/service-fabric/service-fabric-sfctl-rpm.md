---
title: Azure Service Fabric CLI-sfctl rpm
description: Sfctl, Azure Service Fabric 명령줄 인터페이스에 대해 알아봅니다. 복구 관리자 서비스에 대 한 명령 목록을 포함 합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1787c0c8a8d5994ce1f1e9e4c55f155bbca6934c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76904937"
---
# <a name="sfctl-rpm"></a>sfctl rpm
복구 관리자 서비스에 쿼리하고 명령을 보냅니다.

## <a name="commands"></a>명령

|명령|Description|
| --- | --- |
| approve-force | 지정된 복구 작업의 승인을 강제 적용합니다. |
| 삭제 | 완료된 복구 작업을 삭제합니다. |
| list | 지정된 필터와 일치하는 복구 작업 목록을 가져옵니다. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm approve-force
지정된 복구 작업의 승인을 강제 적용합니다.

이 API는 Service Fabric 플랫폼을 지원하며 코드에서 직접 사용할 수 없습니다.

### <a name="arguments"></a>인수

|인수|Description|
| --- | --- |
| --task-id [필수] | 복구 작업의 ID입니다. |
| --version | 복구 작업의 현재 버전 번호입니다. 0이 아닌 경우 이 값이 복구 작업의 실제 현재 버전과 일치하는 경우에만 요청이 성공합니다. 0이면 버전 검사가 수행되지 않습니다. |

### <a name="global-arguments"></a>전역 인수

|인수|Description|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
완료된 복구 작업을 삭제합니다.

이 API는 Service Fabric 플랫폼을 지원하며 코드에서 직접 사용할 수 없습니다.

### <a name="arguments"></a>인수

|인수|Description|
| --- | --- |
| --task-id [필수] | 삭제할 완료된 복구 작업의 ID입니다. |
| --version | 복구 작업의 현재 버전 번호입니다. 0이 아닌 경우 이 값이 복구 작업의 실제 현재 버전과 일치하는 경우에만 요청이 성공합니다. 0이면 버전 검사가 수행되지 않습니다. |

### <a name="global-arguments"></a>전역 인수

|인수|Description|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-rpm-list"></a>sfctl rpm list
지정된 필터와 일치하는 복구 작업 목록을 가져옵니다.

이 API는 Service Fabric 플랫폼을 지원하며 코드에서 직접 사용할 수 없습니다.

### <a name="arguments"></a>인수

|인수|Description|
| --- | --- |
| --executor-filter | 클레임된 작업이 목록에 포함되어야 하는 복구 실행기의 이름입니다. |
| --state-filter | 결과 목록에 포함되어야 하는 작업 상태를 지정하는 다음 값의 비트 OR입니다. <ul><li>1 - 생성됨</li><li>2-요청 됨</li><li>4-준비</li><li>8-승인 됨</li><li>16-실행 중</li><li>32-복원 중</li><li>64 - 완료됨</li></ul>
| --task-id-filter | 일치 시킬 복구 작업 ID 접두사입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|Description|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |


## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.
