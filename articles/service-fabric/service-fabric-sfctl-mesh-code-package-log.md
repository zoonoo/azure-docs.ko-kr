---
title: Azure Service Fabric CLI- sfctl mesh code-package-log | Microsoft Docs
description: Service Fabric CLI sfctl mesh code-package-log 명령에 대해 설명합니다.
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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 81ddcc8c5685a839afabc1e82ecf4246cb813c21
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283339"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
지정된 서비스 복제본에 대해 지정된 코드 패키지의 컨테이너 관련 로그를 가져옵니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| get | 컨테이너에서 로그를 가져옵니다. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
컨테이너에서 로그를 가져옵니다.

서비스 복제본의 지정된 코드 패키지의 컨테이너 관련 로그를 가져옵니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-name --application-name[필수] | 응용 프로그램 이름입니다. |
| --code-package-name           [필수] | 서비스의 코드 패키지 이름입니다. |
| --replica-name                [필수] | Service Fabric 복제본 이름입니다. |
| --service-name                [필수] | 서비스의 이름입니다. |
| --tail | 로그의 끝에서 표시할 줄의 수입니다. 기본값은 100입니다. 전체 로그를 표시하는 'all'입니다. |

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