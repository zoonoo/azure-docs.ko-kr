---
title: 데이터 흐름 문제 해결
description: Azure Data Factory에서 데이터 흐름 문제를 해결 하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: c9ac8d7ea465a26d29bf8f8fbc15dcefaf9d7575
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187282"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Azure Data Factory 데이터 흐름 문제 해결

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure Data Factory의 데이터 흐름에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="error-code-df-executor-sourceinvalidpayload"></a>오류 코드: DF-실행자-SourceInvalidPayload
- **메시지**: 컨테이너가 없으므로 데이터 미리 보기, 디버그 및 파이프라인 데이터 흐름을 실행 하지 못했습니다.
- **원인**: 데이터 집합에 저장소에 없는 컨테이너가 포함 된 경우
- **권장 사항**: 데이터 집합에서 참조 되는 컨테이너가 존재 하거나 액세스할 수 있는지 확인 합니다.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>오류 코드: DF-SystemImplicitCartesian

- **메시지**: 내부 조인에 대 한 암시적 데카르트 제품은 지원 되지 않습니다. 대신 CROSS join을 사용 하십시오. 조인에 사용 되는 열은 행에 대 한 고유 키를 만들어야 합니다.
- **원인**: 논리 요금제 간의 내부 조인에 대 한 암시적 직교 곱을 지원 하지 않습니다. 조인에 사용 된 열이 고유 키를 만드는 경우 관계의 양쪽에 있는 하나 이상의 열이 필요 합니다.
- **권장 사항**: 같지 않음 기반 조인의 경우 사용자 지정 크로스 조인을 옵트인 해야 합니다.

### <a name="error-code-df-executor-systeminvalidjson"></a>오류 코드: DF-SystemInvalidJson

- **메시지**: JSON 구문 분석 오류, 지원 되지 않는 인코딩 또는 여러 줄
- **원인**: json 파일의 가능한 문제: 지원 되지 않는 인코딩, 손상 된 바이트 또는 여러 중첩 된 줄에서 단일 문서로 json 원본 사용
- **권장 사항**: JSON 파일의 인코딩이 지원 되는지 확인 합니다. JSON 데이터 집합을 사용 하는 원본 변환에서 ' JSON 설정 '을 확장 하 고 ' 단일 문서 '를 설정 합니다.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>오류 코드: DF-BroadcastTimeout

- **메시지**: 브로드캐스트 조인 시간 초과 오류, 브로드캐스트 스트림이 디버그 실행에서 60 초 내에 데이터를 생성 하 고 작업 실행에서 300 초 내에 데이터를 생성 하는지 확인 합니다.
- **원인**: 브로드캐스트는 디버그 실행에서 기본 시간 제한이 60 초이 고 작업 실행에서 300 초입니다. 브로드캐스트에 대해 선택한 스트림은이 한도 내에 데이터를 생성 하는 데 너무 많은 것 같습니다.
- **권장 사항**: 조인, 존재 및 조회에 대 한 데이터 흐름 변환의 최적화 탭을 선택 합니다. 브로드캐스트의 기본 옵션은 "Auto"입니다. 이 설정이 설정 된 경우 또는 "고정" 아래에서 브로드캐스트를 수동으로 왼쪽 또는 오른쪽으로 설정 하는 경우 더 큰 Azure Integration Runtime 구성을 설정 하거나 브로드캐스트를 해제할 수 있습니다. 데이터 흐름의 최상의 성능을 위해 Spark에서 "Auto"를 사용 하 여 브로드캐스트할 수 있도록 하 고 메모리 최적화 Azure IR를 사용 하는 것이 좋습니다.

### <a name="error-code-df-executor-conversion"></a>오류 코드: DF-변환

- **메시지**: 잘못 된 문자로 인해 날짜 또는 시간으로 변환 하지 못했습니다.
- **원인**: 데이터가 예상 된 형식이 아닙니다.
- **권장 사항**: 올바른 데이터 형식 사용

### <a name="error-code-df-executor-invalidcolumn"></a>오류 코드: DF-실행-InvalidColumn

- **메시지**: 쿼리에 열 이름을 지정 해야 합니다. SQL 함수를 사용 하는 경우 별칭을 설정 합니다.
- **원인**: 열 이름이 지정 되지 않았습니다.
- **권장 사항**: min ()/max () 등의 SQL 함수를 사용 하는 경우 별칭을 설정 합니다.

### <a name="error-code-getcommand-outputasync-failed"></a>오류 코드: GetCommand OutputAsync가 실패 했습니다.

- **메시지**: 데이터 흐름 디버그 및 데이터 미리 보기 중: Getcommand outputasync가 다음을 사용 하 여 실패 했습니다.
- **원인**: 백 엔드 서비스 오류입니다. 작업을 다시 시도 하 고 디버그 세션을 다시 시작할 수도 있습니다.
- **권장 사항**: 다시 시도 및 다시 시작 해도 문제가 해결 되지 않으면 고객 지원에 문의 하세요.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>오류 코드: 예기치 않은 예외에 도달 하 여 실행 하지 못했습니다.

- **메시지**: 데이터 흐름 작업 실행 중: 예기치 않은 예외를 누르고 실행이 실패 했습니다.
- **원인**: 백 엔드 서비스 오류입니다. 작업을 다시 시도 하 고 디버그 세션을 다시 시작할 수도 있습니다.
- **권장 사항**: 다시 시도 및 다시 시작 해도 문제가 해결 되지 않으면 고객 지원에 문의 하세요.

## <a name="general-troubleshooting-guidance"></a>일반 문제 해결 지침

1. 데이터 집합 연결의 상태를 확인 합니다. 각 원본 및 싱크 변환에서 사용 중인 각 데이터 집합에 대 한 연결 된 서비스를 방문 하 고 연결을 테스트 합니다.
1. 데이터 흐름 디자이너에서 파일 및 테이블 연결의 상태를 확인 합니다. 디버그를 전환 하 고 원본 변환에서 데이터 미리 보기를 클릭 하 여 데이터에 액세스할 수 있는지 확인 합니다.
1. 모든 항목이 데이터 미리 보기에서 양호 하면 파이프라인 디자이너로 이동 하 여 파이프라인 활동에 데이터 흐름을 배치 합니다. 종단 간 테스트를 위한 파이프라인을 디버깅 합니다.

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.
*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory에 대 한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대 한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
*  [ADF 매핑 데이터 흐름 성능 가이드](concepts-data-flow-performance.md)
