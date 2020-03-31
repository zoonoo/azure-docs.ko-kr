---
title: Azure 서비스 패브릭 CLI- sfctl 메쉬 코드 패키지 로그
description: Azure 서비스 패브릭 명령줄 인터페이스인 sfctl에 대해 알아봅니다. 지정된 코드 패키지에 대한 로그를 얻기 위한 명령 목록을 포함합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906056"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
지정된 서비스 복제본에 대해 지정된 코드 패키지의 컨테이너 관련 로그를 가져옵니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| Get | 컨테이너에서 로그를 가져옵니다. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
컨테이너에서 로그를 가져옵니다.

서비스 복제본의 지정된 코드 패키지에서 컨테이너 관련 로그를 가져옵니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-name --application-name[필수] | 애플리케이션 이름입니다. |
| --code-package-name           [필수] | 서비스의 코드 패키지 이름입니다. |
| --replica-name                [필수] | Service Fabric 복제본 이름입니다. |
| --service-name[필수] | 서비스의 이름입니다. |
| --tail | 로그의 끝에서 표시할 줄의 수입니다. 기본값은 100입니다. 전체 로그를 표시하는 'all'입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |


## <a name="next-steps"></a>다음 단계
- 서비스 패브릭 [CLI를 설정합니다.](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.