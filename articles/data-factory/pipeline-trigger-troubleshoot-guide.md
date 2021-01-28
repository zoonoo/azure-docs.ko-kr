---
title: Azure Data Factory 파이프라인 오케스트레이션 및 트리거 문제 해결
description: Azure Data Factory에서 파이프라인 트리거 문제를 해결 하는 다른 방법을 사용 합니다.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 1a5f665627da1b08ec57b04863a58f227c673af4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944910"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Azure Data Factory 파이프라인 오케스트레이션 및 트리거 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory의 파이프라인 실행은 파이프라인 실행의 인스턴스를 정의합니다. 예를 들어 오전 8:00 시, 9:00 오전 및 10:00 AM에 실행 되는 파이프라인이 있다고 가정해 보겠습니다. 이 경우 세 개의 별도 파이프라인이 실행 됩니다. 각 파이프라인 실행에는 고유한 파이프라인 실행 ID가 있습니다. 실행 ID는 특정 파이프라인 실행을 정의 하는 GUID (globally unique identifier)입니다.

파이프라인 실행은 일반적으로 파이프라인에 정의된 매개 변수에 인수를 전달하여 인스턴스화됩니다. 수동으로 또는 트리거를 사용 하 여 파이프라인을 실행할 수 있습니다. 자세한 내용은 [Azure Data Factory 파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md) 를 참조 하세요.

## <a name="common-issues-causes-and-solutions"></a>일반적인 문제, 원인 및 해결 방법

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Azure Functions 앱 파이프라인이 개인 끝점 연결을 사용 하 여 오류를 throw 합니다.
 
개인 끝점에서 실행 되는 Data Factory 및 Azure 함수 앱이 있습니다. 함수 앱과 상호 작용 하는 파이프라인을 실행 하려고 합니다. 세 가지 다른 방법을 시도 했지만 하나는 오류 "잘못 된 요청"을 반환 하 고 다른 두 메서드는 "103 오류를 사용할 수 없습니다."를 반환 합니다.

**원인**: 현재 Data Factory는 함수 앱에 대 한 개인 끝점 커넥터를 지원 하지 않습니다. Azure Functions은 개인 링크의 연결만 허용 하도록 구성 되어 있기 때문에 호출을 거부 합니다.

**해결** 방법: **PrivateLinkService** 끝점을 만들고 함수 앱의 DNS를 제공 합니다.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>파이프라인 실행이 취소 되었지만 모니터는 계속 진행 상태를 표시 합니다.

파이프라인 실행을 취소 하면 파이프라인 모니터링에서 여전히 진행 상태를 표시 하는 경우가 많습니다. 이는 브라우저 캐시 문제로 인해 발생 합니다. 또한 올바른 모니터링 필터를 사용할 수 없습니다.

**해결** 방법: 브라우저를 새로 고치고 올바른 모니터링 필터를 적용 합니다.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>파이프라인을 복사할 때 "DelimitedTextMoreColumnsThanDefined" 오류가 표시 됩니다.
 
복사 하는 폴더에 다양 한 수의 열, 서로 다른 구분 기호, 따옴표 문자 설정, 일부 데이터 문제 등의 스키마가 있는 파일이 포함 되어 있으면 Data Factory 파이프라인이이 오류를 throw 할 수 있습니다.

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**해결** 방법: 복사 작업을 만드는 동안 **이진 복사** 옵션을 선택 합니다. 이러한 방식으로 data lake에서 다른 data lake로 데이터를 대량 복사 하거나 마이그레이션하면 스키마를 읽을 수 Data Factory 파일이 열리지 않습니다. 대신, Data Factory는 각 파일을 이진으로 처리 하 고 다른 위치에 복사 합니다.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime"></a>통합 런타임의 용량 제한에 도달 하면 파이프라인 실행이 실패 합니다.

오류 메시지:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**원인**: 통합 런타임의 용량 제한에 도달 했습니다. 동시에 동일한 통합 런타임을 사용 하 여 많은 양의 데이터 흐름을 실행 하 고 있을 수 있습니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) 을 참조 하세요.

**해결 방법**:
 
- 여러 트리거 시간에 파이프라인을 실행 합니다.
- 새 integration runtime을 만들고 여러 통합 런타임으로 파이프라인을 분할 합니다.

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>파이프라인에서 활동 수준 오류 및 오류가 발생 합니다.

Azure Data Factory 오케스트레이션은 조건부 논리를 허용 하 고 사용자가 이전 작업의 결과에 따라 다른 경로를 사용할 수 있도록 합니다. **성공** 시 (기본 패스), **실패 시**, **완료** 시, **건너뛸** 때 네 가지 조건부 경로를 사용할 수 있습니다. 

Azure Data Factory은 모든 리프 수준 활동의 결과를 평가 합니다. 모든 작업이 성공 하는 경우에만 파이프라인 결과가 성공 합니다. 리프 활동을 건너뛴 경우 해당 부모 활동을 대신 평가 합니다. 

**해결 방법**

1. [파이프라인 오류 및 오류를 처리 하는 방법에](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)따라 활동 수준 검사를 구현 합니다.
1. Azure Logic Apps를 사용 하 여 [팩터리에서 쿼리](/rest/api/datafactory/pipelineruns/querybyfactory)다음에 정기적인 간격으로 파이프라인을 모니터링할 수 있습니다.

## <a name="monitor-pipeline-failures-in-regular-intervals"></a>정기적으로 파이프라인 오류 모니터링

5 분 이라는 간격으로 실패 한 Data Factory 파이프라인을 모니터링 해야 할 수도 있습니다. 끝점을 사용 하 여 데이터 팩터리에서 파이프라인 실행을 쿼리하고 필터링 할 수 있습니다. 

[팩터리에 쿼리](/rest/api/datafactory/pipelineruns/querybyfactory)에 설명 된 대로 5 분 마다 실패 한 모든 파이프라인을 쿼리하려면 Azure 논리 앱을 설정 합니다. 그런 다음 티켓 시스템에 인시던트를 보고할 수 있습니다.

자세한 내용은 [Data Factory에서 알림 보내기, 파트 2](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 질문 페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)