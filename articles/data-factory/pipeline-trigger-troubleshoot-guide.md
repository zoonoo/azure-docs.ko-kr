---
title: Azure Data Factory 파이프라인 오케스트레이션 및 트리거 문제 해결
description: Azure Data Factory에서 파이프라인 트리거 문제를 해결 하는 다른 방법을 사용 합니다.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0e67a316b012eda61607c84edfd8e10d6aa3318d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589171"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Azure Data Factory 파이프라인 오케스트레이션 및 트리거 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory의 파이프라인 실행은 파이프라인 실행의 인스턴스를 정의합니다. 예를 들어 오전 8:00 시, 9:00 오전 및 10:00 AM에 실행 되는 파이프라인이 있습니다. 이 경우 파이프라인 또는 파이프라인 실행을 실행 하는 세 가지 별도의 실행이 있습니다. 각 파이프라인 실행에는 고유한 파이프라인 실행 ID가 있습니다. 실행 ID는 특정 파이프라인 실행을 정의 하는 GUID (Globally Unique Identifier)입니다.

파이프라인 실행은 일반적으로 파이프라인에 정의된 매개 변수에 인수를 전달하여 인스턴스화됩니다. 수동으로 또는 트리거를 사용하여 파이프라인을 실행할 수 있습니다. 자세한 내용은 [Azure Data Factory 파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md) 를 참조 하세요.

## <a name="common-issues-causes-and-solutions"></a>일반적인 문제, 원인 및 해결 방법

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>Azure Function을 사용 하는 파이프라인이 개인 끝점 연결을 사용 하 여 오류를 throw 합니다.
 
#### <a name="issue"></a>문제
일부 컨텍스트의 경우 개인 끝점에서 실행 되는 Data Factory 및 Azure 함수 앱 있습니다. 작업 하기 위해 Azure 함수 앱와 상호 작용 하는 파이프라인을 가져오려고 합니다. 3 개의 다른 메서드를 시도 했지만 하나 `Bad Request` 는 오류를 반환 하 고 다른 두 메서드는를 반환 `103 Error Forbidden` 합니다.

#### <a name="cause"></a>원인 
현재 Data Factory는 Azure 함수 앱에 대 한 개인 끝점 커넥터를 지원 하지 않습니다. 이는 Azure 함수 앱가 개인 링크의 연결만 허용 하도록 구성 되기 때문에 호출을 거부 하는 이유입니다.

#### <a name="resolution"></a>해결 방법
**PrivateLinkService** 형식의 개인 끝점을 만들고 함수 앱의 DNS를 제공할 수 있으며 연결이 작동 합니다.

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>파이프라인 실행이 종료 되었지만 모니터에 진행 상태가 계속 표시 됩니다.

#### <a name="issue"></a>문제
파이프라인 실행을 중단 하는 경우에도 파이프라인 모니터링은 진행률 상태를 표시 합니다. 이는 브라우저의 캐시 문제로 인해 발생 하며 모니터링에 대 한 올바른 필터가 없는 경우에 발생 합니다.

#### <a name="resolution"></a>해결 방법
브라우저를 새로 고치고 모니터링에 대 한 올바른 필터를 적용 합니다.
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>복사 파이프라인 오류 – 필요한 열 개수 보다 많은 열을 찾았습니다 (DelimitedTextMoreColumnsThanDefined).

#### <a name="issue"></a>문제  
복사할 특정 폴더의 파일에 가변 개수의 열, 다른 구분 기호, 따옴표 문자 설정 또는 일부 데이터 문제와 같은 다른 스키마가 있는 파일이 포함 되어 있는 경우이 오류가 발생 하면 Data Factory 파이프라인이 실행 됩니다.

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>해결 방법
데이터 복사 작업을 만드는 동안 "이진 복사" 옵션을 선택 합니다. 이러한 방식으로, **이진** 옵션을 사용 하 여 Data Lake 간에 데이터를 대량 복사 하거나 마이그레이션하는 경우에는 Data Factory 스키마를 읽기 위해 파일이 열리지 않지만 모든 파일을 이진으로 처리 하 고 다른 위치에 복사 하면 됩니다.

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>Integration runtime의 용량 제한에 도달 하면 파이프라인 실행이 실패 합니다.

#### <a name="issue"></a>문제
오류 메시지:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

이 오류는 통합 런타임 별 제한을 나타내며 현재 50입니다. 자세한 내용은 [제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) 을 참조 하세요.

동시에 동일한 통합 런타임을 사용 하 여 많은 양의 데이터 흐름을 실행 하는 경우 이러한 종류의 오류가 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법 
- 실행할 다른 트리거 시간에 대해 이러한 파이프라인을 분리 합니다.
- 새 통합 런타임을 만들고 이러한 파이프라인을 여러 통합 런타임으로 분할 합니다.

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>정기적인 간격으로 파이프라인 오류를 모니터링 하는 방법

#### <a name="issue"></a>문제
5 분 이라는 간격으로 Data Factory 파이프라인을 모니터링 해야 하는 경우가 종종 있습니다. 끝점을 사용 하 여 데이터 팩터리에서 파이프라인 실행을 쿼리하고 필터링 할 수 있습니다. 

#### <a name="recommendation"></a>권장
1. 5 분 마다 실패 한 모든 파이프라인을 쿼리하려면 Azure 논리 앱을 설정 합니다.
2. 그런 다음 [Querybyfactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)를 기준으로 티켓 시스템에 인시던트를 보고할 수 있습니다.

#### <a name="reference"></a>참조
- [외부-Data Factory에서 알림 보내기](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>파이프라인에서 활동 수준 오류 및 실패를 처리 하는 방법

#### <a name="issue"></a>문제
Azure Data Factory 오케스트레이션은 조건부 논리를 허용 하 고 사용자가 이전 작업의 결과에 따라 다른 경로를 사용할 수 있도록 합니다. 이를 통해 "성공 시 (기본 패스)", "실패 시", "완료 시", "건너뛰기 시"와 같은 네 가지 조건부 경로를 사용할 수 있습니다. 다른 경로를 사용할 수 있습니다.

Azure Data Factory은 다음과 같이 파이프라인 실행 성공 및 실패를 정의 합니다.

- 모든 리프 수준 작업에 대 한 결과를 평가 합니다. 리프 활동을 건너뛴 경우 해당 부모 활동을 대신 평가 합니다.
- 모든 작업이 성공 하는 경우에만 파이프라인 결과가 성공 합니다.

#### <a name="recommendation"></a>권장
- [파이프라인 오류 및 오류를 처리 하는 방법에](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)따라 활동 수준 검사를 구현 합니다.
- Azure 논리 앱을 사용 하 [여 DataFactory 쿼리]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)다음에 일정 한 간격으로 파이프라인을 모니터링 합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 질문 페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)