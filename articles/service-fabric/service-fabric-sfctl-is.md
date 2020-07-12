---
title: Azure Service Fabric CLI-sfctl이
description: Azure Service Fabric 명령줄 인터페이스인 sfctl에 대해 알아봅니다. 인프라를 관리 하기 위한 명령 목록을 포함 합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 349f70c32ea4ebb4559f053d5ef05b4b37b6480f
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260371"
---
# <a name="sfctl-is"></a>sfctl is
쿼리하고 인프라 서비스에 명령을 보냅니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| 명령을 사용합니다. | 지정된 인프라 서비스 인스턴스에 대한 관리 명령을 호출합니다. |
| Query | 지정된 인프라 서비스 인스턴스에 대한 읽기 전용 쿼리를 호출합니다. |

## <a name="sfctl-is-command"></a>sfctl is 명령
지정된 인프라 서비스 인스턴스에 대한 관리 명령을 호출합니다.

하나 이상의 인프라 서비스 인스턴스를 구성한 클러스터의 경우 이 API는 인프라 서비스의 특정 인스턴스에 인프라 관련 명령을 전송하는 방법을 제공합니다. 사용 가능한 명령 및 해당 응답 형식은 클러스터가 실행되는 인프라에 따라 달라집니다. 이 API는 Service Fabric 플랫폼을 지원하며 코드에서 직접 사용할 수 없습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --command[필수] | 호출할 명령의 텍스트입니다. 명령의 콘텐츠는 인프라에 따라 다릅니다. |
| --service-id | 인프라 서비스의 ID입니다. <br><br> 'fabric\:' URI 스키마가 없는 인프라 서비스의 전체 이름입니다. 이 매개 변수는 실행 중인 하나 이상의 인프라 서비스 인스턴스가 있는 클러스터에만 필요합니다. |
| --timeout -t | 기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-is-query"></a>sfctl is 쿼리
지정된 인프라 서비스 인스턴스에 대한 읽기 전용 쿼리를 호출합니다.

하나 이상의 인프라 서비스 인스턴스를 구성한 클러스터의 경우 이 API는 인프라 서비스의 특정 인스턴스에 인프라 관련 쿼리를 전송하는 방법을 제공합니다. 사용 가능한 명령 및 해당 응답 형식은 클러스터가 실행되는 인프라에 따라 달라집니다. 이 API는 Service Fabric 플랫폼을 지원하며 코드에서 직접 사용할 수 없습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --command[필수] | 호출할 명령의 텍스트입니다. 명령의 콘텐츠는 인프라에 따라 다릅니다. |
| --service-id | 인프라 서비스의 ID입니다. <br><br> 'fabric\:' URI 스키마가 없는 인프라 서비스의 전체 이름입니다. 이 매개 변수는 실행 중인 하나 이상의 인프라 서비스 인스턴스가 있는 클러스터에만 필요합니다. |
| --timeout -t | 기본값\: 60. |

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
