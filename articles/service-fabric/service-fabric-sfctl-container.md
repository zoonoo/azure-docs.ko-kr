---
title: Azure Service Fabric CLI- sfctl container | Microsoft Docs
description: Service Fabric CLI sfctl container 명령을 설명합니다.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 27108d27ee27346e4cba44e6778faff56df70a36
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495131"
---
# <a name="sfctl-container"></a>sfctl container
클러스터 노드에서 컨테이너 관련 명령을 실행합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| invoke-api | 컨테이너 REST API를 호출합니다. |
| 로그 | 컨테이너 로그 검색 |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
컨테이너 REST API를 호출합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id           [필수] | 응용 프로그램 ID. |
| --code-package-instance-id [필수] | 'service code-package-list'에서 검색할 수 있는 코드 패키지 인스턴스 ID입니다. |
| --code-package-name        [필수] | 코드 패키지 이름입니다. |
| --container-api-uri-path   [필수] | 컨테이너 REST API의 URI 경로입니다. 컨테이너 이름/ID 대신 '{ID}'를 사용합니다. |
| --node-name                [필수] | 노드의 이름입니다. |
| --service-manifest-name    [필수] | 서비스 매니페스트 이름입니다. |
| --container-api-body | 컨테이너 REST API에 대한 HTTP 요청 본문입니다. |
| --container-api-content-type | 컨테이너 REST API에 대한 콘텐츠 형식입니다. 'application/json'으로 기본값을 지정합니다. |
| --container-api-http-verb | 컨테이너 REST API에 대한 HTTP 동사입니다. GET으로 기본값을 지정합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다. |

## <a name="sfctl-container-logs"></a>sfctl container logs
컨테이너 로그 검색

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id           [필수] | 응용 프로그램 ID. |
| --code-package-instance-id [필수] | 'service code-package-list'에서 검색할 수 있는 코드 패키지 인스턴스 ID입니다. |
| --code-package-name        [필수] | 코드 패키지 이름입니다. |
| --node-name                [필수] | 노드의 이름입니다. |
| --service-manifest-name    [필수] | 서비스 매니페스트 이름입니다. |
| --tail | 로그의 끝에서 이 로그 줄의 수를 반환합니다. 모든 로그 줄을 출력하도록 정수 또는 모두로 지정합니다. 기본값은 '모두'로 지정합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

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