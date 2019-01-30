---
title: Azure Service Fabric CLI- sfctl mesh service | Microsoft Docs
description: Service Fabric CLI sfctl mesh service 명령을 설명합니다.
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
ms.openlocfilehash: 6c40cf2340568e704a5c76ad367c07c85826943b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53282919"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
애플리케이션 리소스의 서비스를 나열하고 서비스 세부 정보를 가져옵니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| list | 모든 서비스 리소스를 나열합니다. |
| 표시 | 지정된 이름의 서비스 리소스를 가져옵니다. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
모든 서비스 리소스를 나열합니다.

애플리케이션 리소스의 모든 서비스에 대한 정보를 가져옵니다. 정보에는 서비스에 대한 설명과 기타 속성이 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-name --application-name[필수] | 응용 프로그램 이름입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
지정된 이름의 서비스 리소스를 가져옵니다.

지정된 이름의 서비스 리소스에 대한 정보를 가져옵니다. 정보에는 서비스에 대한 설명과 기타 속성이 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-name --application-name[필수] | 응용 프로그램 이름입니다. |
| --name -n[필수] | 서비스의 이름입니다. |

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