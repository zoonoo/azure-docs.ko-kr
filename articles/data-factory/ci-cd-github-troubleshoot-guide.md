---
title: ADF의 CI-CD, Azure DevOps 및 GitHub 문제 해결
description: 다른 방법을 사용 하 여 ADF에서 CI-CD 문제를 해결 합니다.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 11/27/2020
ms.openlocfilehash: e0b4b31a1d732cbd5cbfaa9b6e5e021caa3adf01
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301959"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>ADF의 CI-CD, Azure DevOps 및 GitHub 문제 해결 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 CI (지속적인 Integration-Continuous 배포), Azure DevOps 및 GitHub 문제에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

원본 제어 또는 DevOps 기술 사용에 대 한 질문이 나 문제가 있는 경우 다음과 같은 몇 가지 문서를 유용 하 게 사용할 수 있습니다.

- Adf에서 원본 제어를 연습 하는 방법을 알아보려면 [adf의 원본 제어](source-control.md) 를 참조 하세요. 
- Adf에서 DevOps CI-CD를 연습 하는 방법에 대 한 자세한 내용은  [adf의 ci-cd](continuous-integration-deployment.md) 를 참조 하세요.
 
## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>다른 테 넌 트 때문에 Git 리포지토리에 연결 하지 못했습니다.

#### <a name="issue"></a>문제
    
경우에 따라 HTTP 상태 401 등의 인증 문제가 발생할 수 있습니다. 특히 게스트 계정을 사용 하는 테 넌 트가 여러 개 있는 경우 더 복잡 해질 수 있습니다.

#### <a name="cause"></a>원인

토큰은 원래 테 넌 트에서 가져오지만 ADF는 게스트 테 넌 트에 있으며 토큰을 사용 하 여 게스트 테 넌 트에서 DevOps를 방문 하려고 합니다. 이는 예상 된 동작이 아닙니다.

#### <a name="recommendation"></a>권장

대신 게스트 테 넌 트에서 발급 된 토큰을 사용 해야 합니다. 예를 들어, 동일한 Azure Active Directory를 게스트 테 넌 트 뿐만 아니라 DevOps에 할당 해야 합니다. 따라서 토큰 동작을 올바르게 설정 하 고 올바른 테 넌 트를 사용할 수 있습니다.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>매개 변수 파일의 템플릿 매개 변수가 잘못 되었습니다.

#### <a name="issue"></a>문제

**동일한** 구성 (빈도 및 간격)을 사용 하는 테스트 또는 프로덕션 분기에서 이미 사용할 수 있는 Dev 분기에서 트리거를 삭제 하면 릴리스 파이프라인 배포가 성공 하 고 해당 하는 트리거가 해당 환경에서 삭제 됩니다. 그러나 테스트/프로덕션 환경에서 트리거에 대해 **다른** 구성 (빈도 및 간격)을 사용 하 고 Dev에서 동일한 트리거를 삭제 하면 배포에 실패 하 고 오류가 발생 합니다.

#### <a name="cause"></a>원인

다음 오류가 발생 하 여 CI/CD 파이프라인이 실패 합니다.

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>권장

이 오류는 일반적으로 매개 변수화 된 트리거를 삭제 하기 때문에 발생 합니다. 따라서 트리거는 더 이상 존재 하지 않기 때문에 ARM 템플릿에서는 매개 변수를 사용할 수 없습니다. 매개 변수가 ARM 템플릿에 더 이상 포함 되어 있지 않으므로 DevOps 파이프라인에서 재정의 된 매개 변수를 업데이트 해야 합니다. 그렇지 않고 ARM 템플릿의 매개 변수가 변경 될 때마다 배포 작업에서 DevOps 파이프라인의 재정의 된 매개 변수를 업데이트 해야 합니다.

### <a name="updating-property-type-is-not-supported"></a>속성 유형 업데이트가 지원 되지 않습니다.

#### <a name="issue"></a>문제

다음 오류로 인해 CI/CD 릴리스 파이프라인이 실패 합니다.

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>원인

이는 대상 팩터리에 이름이 같지만 형식이 다른 Integration Runtime 때문입니다. Integration Runtime를 배포할 때 동일한 유형 이어야 합니다.

#### <a name="recommendation"></a>권장

- 아래의 CI/CD에 대 한 모범 사례를 참조 하세요.

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Integration runtime은 자주 변경 되지 않으며 CI/CD의 모든 단계에서 유사 하므로 Data Factory는 CI/CD의 모든 단계에서 동일한 이름 및 유형의 통합 런타임을 사용할 것으로 예상 합니다. 이름 및 형식 & 속성이 다르면 원본 및 대상 IR 구성과 일치 하는지 확인 하 고 릴리스 파이프라인을 배포 합니다.
- 모든 단계에서 통합 런타임을 공유하려면 공유 통합 런타임을 포함하기 위해 3개로 구성된 팩터리를 사용하는 것이 좋습니다. 모든 환경에서 이 공유 팩터리를 연결된 통합 런타임 형식으로 사용할 수 있습니다.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>잘못 된 참조로 인해 문서를 만들거나 업데이트 하지 못했습니다.

#### <a name="issue"></a>문제

Data Factory에 변경 내용을 게시 하려고 하면 다음과 같은 오류 메시지가 표시 됩니다.

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>증상

Git 구성을 분리 하 고 "리소스 가져오기" 플래그를 선택 하 여 다시 설정 하 여 Data Factory를 "동기화 된"로 설정 합니다. 이는 게시할 변경 내용이 없음을 의미 합니다.

**해결 방법**

Git 구성을 분리 하 고 다시 설정 하 고 "기존 리소스 가져오기" 확인란을 선택 하지 않았는지 확인 합니다.

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>한 리소스 그룹에서 다른 리소스 그룹으로 이동 실패 Data Factory

#### <a name="issue"></a>문제

한 리소스 그룹에서 다른 리소스 그룹으로 Data Factory 이동할 수 없습니다. 다음 오류를 표시 하며 실패 합니다.

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>해결 방법

이동 작업을 허용 하려면 SSIS-IR 및 공유 IRs를 삭제 해야 합니다. IRs를 삭제 하지 않으려는 경우, 복사 및 복제 문서에 따라 복사를 수행 하 고 완료 된 후 이전 데이터 팩터리를 삭제 하는 것이 가장 좋습니다.

###  <a name="unable-to-export-and-import-arm-template"></a>ARM 템플릿을 내보내고 가져올 수 없습니다.

#### <a name="issue"></a>문제

ARM 템플릿을 내보내고 가져올 수 없습니다. 포털에 오류가 없지만 브라우저 추적에서 다음 오류를 확인할 수 있습니다.

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>원인

사용자로 서 고객 역할을 만들었으며 필요한 권한이 없습니다. 팩터리가 UI에서 로드 되 면 팩터리에 대 한 일련의 노출 제어 값이 확인 됩니다. 이 경우 사용자의 액세스 역할에는 *queryFeaturesValue* API에 액세스할 수 있는 권한이 없습니다. 이 API에 액세스 하기 위해 전역 매개 변수 기능이 꺼집니다. ARM 내보내기 코드 경로는 부분적으로 전역 매개 변수 기능에 의존 합니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결 하려면 *DataFactory/factory/queryFeaturesValue/action* 역할에 다음 권한을 추가 해야 합니다. 이 권한은 기본적으로 "Data Factory 참여자" 역할에 포함 되어야 합니다.

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory에 대 한 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
