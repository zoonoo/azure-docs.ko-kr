---
title: 데이터 흐름 문제 해결
description: Azure 데이터 팩터리에서 데이터 흐름 문제를 해결하는 방법을 알아봅니다.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.openlocfilehash: e9e9b10cc9bae029fe11fb2bd1f8b76cf120744a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417813"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Azure 데이터 팩터리에서 데이터 흐름 문제 해결

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure 데이터 팩터리에서 데이터 흐름에 대한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="error-code-df-executor-sourceinvalidpayload"></a>오류 코드: DF-실행기-소스유효페이로드
- **메시지**: 컨테이너가 없기 때문에 데이터 미리 보기, 디버그 및 파이프라인 데이터 흐름 실행이 실패했습니다.
- **원인**: 데이터 집합에 저장소에 없는 컨테이너가 포함된 경우
- **권장 사항**: 데이터 집합에서 참조되는 컨테이너가 존재하거나 액세스할 수 있는지 확인합니다.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>오류 코드: DF-실행기-시스템암묵적 카르테시안

- **메시지**: INNER 조인에 대한 암시적 카르테시안 제품은 지원되지 않으며 대신 CROSS JOIN을 사용합니다. 조인에 사용된 열은 행에 대한 고유 키를 만들어야 합니다.
- **원인**: 논리 적 계획 사이의 INNER 조인에 대한 암시적 카르테시안 제품은 지원되지 않습니다. 조인에 사용된 열이 고유 키를 만드는 경우 관계의 양쪽에서 하나 이상의 열이 필요합니다.
- **권장 사항**: 비같음 기반 조인의 경우 사용자 지정 CROSS JOIN을 선택해야 합니다.

### <a name="error-code-df-executor-systeminvalidjson"></a>오류 코드: DF-실행기-시스템유효Json

- **메시지**: JSON 구문 분석 오류, 지원되지 않는 인코딩 또는 다중 라인
- **원인**: JSON 파일에 발생할 수 있는 문제: 지원되지 않는 인코딩, 바이트 손상 또는 JSON 소스를 여러 중첩 된 줄에서 단일 문서로 사용
- **권장 사항**: JSON 파일의 인코딩이 지원되는지 확인합니다. JSON 데이터 집합을 사용하는 소스 변환에서 'JSON 설정'을 확장하고 '단일 문서'를 켭니다.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>오류 코드: DF-실행기-브로드캐스트 타임아웃

- **메시지**: 브로드캐스트 조인 시간 초과 오류, 브로드캐스트 스트림이 디버그 실행에서 60초 이내의 데이터를 생성하고 작업 실행시 300초 내에 데이터를 생성해야 합니다.
- **원인**: 브로드캐스트는 디버그 실행시 60초, 작업 실행시 300초의 기본 시간 초과를 가수 있습니다. 브로드캐스트에 대해 선택한 스트림이 이 제한 내에서 데이터를 생성하기 위해 큰 것으로 보입니다.
- **권장 사항**: 처리에 60초 이상 걸릴 수 있는 대용량 데이터 스트림을 브로드캐스트하지 마십시오. 대신 브로드캐스트할 더 작은 스트림을 선택합니다. 대용량 SQL/DW 테이블 및 원본 파일은 일반적으로 잘못된 후보입니다.

### <a name="error-code-df-executor-conversion"></a>오류 코드: DF-실행기 변환

- **메시지**: 잘못된 문자로 인해 날짜 또는 시간으로 변환하는 데 실패했습니다.
- **원인**: 데이터가 예상 형식이 아닙니다.
- **권장 사항**: 올바른 데이터 형식을 사용하십시오.

### <a name="error-code-df-executor-invalidcolumn"></a>오류 코드: DF-실행기-유효하지 않은열

- **메시지**: SQL 함수를 사용하는 경우 쿼리에 열 이름을 지정하고 별칭을 설정해야합니다.
- **원인**: 열 이름이 지정되지 않았습니다.
- **권장 사항**: 최소()/max())와 같은 SQL 함수를 사용하는 경우 별칭을 설정합니다.

### <a name="error-code-getcommand-outputasync-failed"></a>오류 코드: GetCommand OutputAsync 실패

- **메시지**: 데이터 흐름 디버그 및 데이터 미리 보기 중: GetCommand OutputAsync가 ...
- **원인**: 백 엔드 서비스 오류입니다. 작업을 다시 시도하고 디버그 세션을 다시 시작할 수도 있습니다.
- **권장 사항**: 다시 시도하고 다시 시작해도 문제가 해결되지 않으면 고객 지원에 문의하십시오.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>오류 코드: 예기치 않은 예외를 누르고 실행이 실패했습니다.

- **메시지**: 데이터 흐름 활동 실행 중: 예기치 않은 예외를 누르고 실행이 실패했습니다.
- **원인**: 백 엔드 서비스 오류입니다. 작업을 다시 시도하고 디버그 세션을 다시 시작할 수도 있습니다.
- **권장 사항**: 다시 시도하고 다시 시작해도 문제가 해결되지 않으면 고객 지원에 문의하십시오.

## <a name="general-troubleshooting-guidance"></a>일반적인 문제 해결 지침

1. 데이터 집합 연결상태를 확인합니다. 각 소스 및 싱크 변환에서 사용 중인 각 데이터 집합에 대해 연결된 서비스를 방문하고 연결을 테스트합니다.
1. 데이터 흐름 디자이너에서 파일 및 테이블 연결 상태를 확인합니다. 디버그를 켜고 소스 변환에서 데이터 미리 보기를 클릭하여 데이터에 액세스할 수 있는지 확인합니다.
1. 데이터 미리 보기에서 모든 것이 양호한 경우 파이프라인 디자이너로 이동하여 데이터 흐름을 파이프라인 활동에 넣습니다. 종단 간 테스트를 위해 파이프라인을 디버깅합니다.

## <a name="next-steps"></a>다음 단계

문제 해결에 대한 자세한 도움말을 보려면 다음 리소스를 사용해 보십시오.
*  [데이터 팩토리 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [데이터 팩터리 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [데이터 팩터리용 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [데이터 팩토리에 대한 트위터 정보](https://twitter.com/hashtag/DataFactory)
*  [ADF 매핑 데이터 흐름 성능 가이드](concepts-data-flow-performance.md)
