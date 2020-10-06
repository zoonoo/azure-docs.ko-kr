---
title: 일반적인 오류 문제 해결
description: Kubernetes에 대 한 정책 정의, 다양 한 SDK 및 추가 기능 만들기와 관련 된 문제를 해결 하는 방법에 대해 알아봅니다.
ms.date: 10/05/2020
ms.topic: troubleshooting
ms.openlocfilehash: 6026dc75187c8a70203a2484380eed70d519599d
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743440"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Azure Policy를 사용 하 여 오류 해결

정책 정의를 만들거나 SDK로 작업 하거나 Kubernetes 추가 기능 [에 대 한 Azure Policy](../concepts/policy-for-kubernetes.md) 를 설정 하는 경우 오류가 발생할 수 있습니다. 이 문서에서는 발생할 수 있는 다양한 오류와 이러한 오류를 해결하는 방법에 대해 설명합니다.

## <a name="finding-error-details"></a>오류 세부 정보 찾기

오류 정보 위치는 오류를 발생 시키는 동작에 따라 달라 집니다.

- 사용자 지정 정책을 사용 하 여 작업 하는 경우 스키마에 대 한 lint 피드백을 얻거나 결과 [준수 데이터](../how-to/get-compliance-data.md) 를 검토 하 여 리소스 평가 방법을 확인 하려면 Azure Portal에서 시도 하세요.
- 다양 한 SDK로 작업 하는 경우 SDK는 함수가 실패 한 이유에 대 한 세부 정보를 제공 합니다.
- Kubernetes에 대 한 추가 기능을 사용 하는 경우 클러스터의 [로깅을](../concepts/policy-for-kubernetes.md#logging) 시작 합니다.

## <a name="general-errors"></a>일반 오류

### <a name="scenario-alias-not-found"></a>시나리오: 별칭을 찾을 수 없음

#### <a name="issue"></a>문제

Azure Policy [별칭](../concepts/definition-structure.md#aliases) 을 사용 하 여 Azure Resource Manager 속성에 매핑합니다.

#### <a name="cause"></a>원인

정책 정의에 잘못 되었거나 존재 하지 않는 별칭이 사용 되었습니다.

#### <a name="resolution"></a>해결 방법

먼저 리소스 관리자 속성에 별칭이 있는지 확인 합니다. Visual Studio Code, [Azure 리소스 그래프](../../resource-graph/samples/starter.md#distinct-alias-values)또는 SDK [에 대 한 Azure Policy 확장](../how-to/extension-for-vscode.md)을 사용 하 여 사용 가능한 별칭을 조회 합니다. 리소스 관리자 속성에 대 한 별칭이 없으면 지원 티켓을 만듭니다.

### <a name="scenario-evaluation-details-not-up-to-date"></a>시나리오: 평가 세부 정보가 최신 상태가 아님

#### <a name="issue"></a>문제

리소스가 "시작 되지 않음" 상태 이거나 호환성 정보가 최신이 아닙니다.

#### <a name="cause"></a>원인

새 정책 또는 이니셔티브 할당을 적용 하는 데 약 30 분이 걸립니다. 기존 할당 범위 내에 있는 새 리소스 또는 업데이트 된 리소스는 15 분 후에 사용할 수 있게 됩니다. 표준 준수 검사는 24 시간 마다 수행 됩니다. 자세한 내용은 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)를 참조 하세요.

#### <a name="resolution"></a>해결 방법

먼저 평가를 완료 하는 데 적절 한 시간을 기다린 후 Azure Portal 또는 SDK에서 준수 결과를 사용할 수 있게 합니다. Azure PowerShell 또는 REST API를 사용 하 여 새 평가 검색을 시작 하려면 [주문형 평가 검사](../how-to/get-compliance-data.md#on-demand-evaluation-scan)를 참조 하세요.

### <a name="scenario-compliance-not-as-expected"></a>시나리오: 호환성이 예상과 일치 하지 않음

#### <a name="issue"></a>문제

리소스가 해당 리소스에 대해 _준수_ 되거나 _비규격_인 평가 상태에 있지 않습니다.

#### <a name="cause"></a>원인

리소스가 정책 할당에 대 한 올바른 범위에 속하지 않거나 정책 정의가 의도 한 대로 작동 하지 않습니다.

#### <a name="resolution"></a>해결 방법

정책 정의 문제를 해결 하려면 다음 단계를 따르세요.

1. 먼저 평가를 완료 하는 데 적절 한 시간을 기다린 후 Azure Portal 또는 SDK에서 준수 결과를 사용할 수 있게 합니다. Azure PowerShell 또는 REST API를 사용 하 여 새 평가 검색을 시작 하려면 [주문형 평가 검사](../how-to/get-compliance-data.md#on-demand-evaluation-scan)를 참조 하세요.
1. 할당 매개 변수와 할당 범위가 올바르게 설정 되어 있는지 확인 하십시오.
1. [정책 정의 모드](../concepts/definition-structure.md#mode)를 확인 합니다.
   - 모든 리소스 종류의 ' 모두 ' 모드입니다.
   - 정책 정의가 태그나 위치를 확인 하는 경우 ' 인덱싱된 ' 모드입니다.
1. 리소스의 범위가 [제외](../concepts/assignment-structure.md#excluded-scopes) [되거나 제외](../concepts/exemption-structure.md)되지 않았는지 확인 합니다.
1. 정책 할당에 대 한 규정 준수 `0/0` 에서 리소스를 표시 하는 경우 할당 범위 내에서 적용 가능한 리소스가 결정 되지 않습니다. 정책 정의와 할당 범위를 모두 확인 합니다.
1. 호환 되는 것으로 예상 되는 비준수 리소스의 경우 [비준수의 원인을](../how-to/determine-non-compliance.md)확인 하세요. 정의를 평가 된 속성 값과 비교 하면 리소스가 비규격 인 이유를 알 수 있습니다.
   - **대상 값** 이 잘못 된 경우 정책 정의를 수정 합니다.
   - **현재 값** 이 잘못 된 경우을 통해 리소스 페이로드의 유효성을 검사 `resources.azure.com` 합니다.
1. 문제 해결: 다른 일반적인 문제 및 해결 방법에 대해 [예상 대로 적용 되지 않습니다](#scenario-enforcement-not-as-expected) .

중복 되 고 사용자 지정 된 기본 제공 정책 정의 또는 사용자 지정 정의에 문제가 계속 발생 하는 경우 **정책을 작성** 하 여 지원 티켓을 만들어 문제를 올바르게 라우팅합니다.

### <a name="scenario-enforcement-not-as-expected"></a>시나리오: 예상 대로 적용 되지 않음

#### <a name="issue"></a>문제

Azure Policy에 의해 처리 될 것으로 예상 되는 리소스는 [Azure 활동 로그](../../../azure-monitor/platform/platform-logs-overview.md)에 항목이 없습니다.

#### <a name="cause"></a>원인

정책 할당이 [EnforcementMode](../concepts/assignment-structure.md#enforcement-mode) _사용 하지 않도록_구성 되었습니다. 적용 모드를 사용 하지 않도록 설정 하는 동안 정책 효과가 적용 되지 않으며 활동 로그에 항목이 없습니다.

#### <a name="resolution"></a>해결 방법

정책 할당의 적용 문제를 해결 하려면 다음 단계를 따르세요.

1. 먼저 평가를 완료 하는 데 적절 한 시간을 기다린 후 Azure Portal 또는 SDK에서 준수 결과를 사용할 수 있게 합니다. Azure PowerShell 또는 REST API를 사용 하 여 새 평가 검색을 시작 하려면 [주문형 평가 검사](../how-to/get-compliance-data.md#on-demand-evaluation-scan)를 참조 하세요.
1. 할당 매개 변수 및 할당 범위를 올바르게 설정 하 고 **enforcementMode** 를 _사용 하도록_설정 했는지 확인 합니다. 
1. [정책 정의 모드](../concepts/definition-structure.md#mode)를 확인 합니다.
   - 모든 리소스 종류의 ' 모두 ' 모드입니다.
   - 정책 정의가 태그나 위치를 확인 하는 경우 ' 인덱싱된 ' 모드입니다.
1. 리소스의 범위가 [제외](../concepts/assignment-structure.md#excluded-scopes) [되거나 제외](../concepts/exemption-structure.md)되지 않았는지 확인 합니다.
1. 리소스 페이로드가 정책 논리와 일치 하는지 확인 합니다. [HAR 추적을 캡처하거나](../../../azure-portal/capture-browser-trace.md) ARM 템플릿 속성을 검토 하 여이 작업을 수행할 수 있습니다.
1. 문제 해결 확인: 다른 일반적인 문제 및 해결 방법에 대해 [예상 대로 준수 하지 않습니다](#scenario-compliance-not-as-expected) .

중복 되 고 사용자 지정 된 기본 제공 정책 정의 또는 사용자 지정 정의에 문제가 계속 발생 하는 경우 **정책을 작성** 하 여 지원 티켓을 만들어 문제를 올바르게 라우팅합니다.

### <a name="scenario-denied-by-azure-policy"></a>시나리오: Azure Policy에 의해 거부 됨

#### <a name="issue"></a>문제

리소스 만들기 또는 업데이트가 거부 되었습니다.

#### <a name="cause"></a>원인

새 리소스 또는 업데이트 된 리소스 범위에 대 한 정책 할당은 [거부](../concepts/effects.md#deny) 효과가 있는 정책 정의의 조건을 충족 합니다. 리소스 모임 이러한 정의를 만들거나 업데이트할 수 없습니다.

#### <a name="resolution"></a>해결 방법

거부 정책 할당의 오류 메시지에는 정책 정의 및 정책 할당 Id가 포함 됩니다. 메시지의 오류 정보가 누락 된 경우에도 [활동 로그](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)에서 사용할 수 있습니다. 이 정보를 사용 하 여 리소스 제한을 이해 하 고 허용 되는 값과 일치 하도록 요청에서 리소스 속성을 조정 하는 방법에 대 한 자세한 내용을 확인 하세요.

## <a name="template-errors"></a>템플릿 오류

### <a name="scenario-policy-supported-functions-processed-by-template"></a>시나리오: 템플릿에서 처리 된 정책 지원 함수

#### <a name="issue"></a>문제

Azure Policy는 정책 정의 에서만 사용할 수 있는 여러 가지 Azure Resource Manager 템플릿 (ARM 템플릿) 함수 및 함수를 지원 합니다. 리소스 관리자은 이러한 기능을 정책 정의의 일부가 아닌 배포의 일부로 처리 합니다.

#### <a name="cause"></a>원인

또는와 같은 지원 되는 함수를 사용 하면 `parameter()` `resourceGroup()` 정책 정의 및 Azure Policy 엔진에서 처리할 함수를 그대로 유지 하는 대신 배포 시 함수의 처리 된 결과가 발생 합니다.

#### <a name="resolution"></a>해결 방법

를 사용 하 여 함수를 정책 정의의 일부로 전달 하려면 속성의 모양을로 전체 문자열을 이스케이프 `[` `[[resourceGroup().tags.myTag]` 합니다. 이스케이프 문자를 통해 리소스 관리자은 템플릿을 처리할 때 값을 문자열로 처리 합니다. 그런 다음 함수를 정책 정의에 배치 하 여 예상 대로 동적으로 Azure Policy 합니다. 자세한 내용은 [Azure Resource Manager 템플릿의 구문 및 식](../../../azure-resource-manager/templates/template-expressions.md)을 참조 하세요.

## <a name="add-on-installation-errors"></a>추가 기능 설치 오류

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>시나리오: 암호에서 투구 차트를 사용 하 여 설치 실패

#### <a name="issue"></a>문제

`helm install azure-policy-addon`다음 메시지 중 하나를 사용 하 여 명령이 실패 합니다.

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>원인

생성 된 암호에는 `,` 투구 차트를 분할 하는 쉼표 ()가 포함 되어 있습니다.

#### <a name="resolution"></a>해결 방법

`,` `helm install azure-policy-addon` 백슬래시 ()를 사용 하 여 실행할 때 암호 값에서 쉼표 ()를 이스케이프 `\` 합니다.

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>시나리오: 이름이 이미 있으므로 투구 차트를 사용 하는 설치가 실패 함

#### <a name="issue"></a>문제

`helm install azure-policy-addon`다음 메시지와 함께 명령이 실패 합니다.

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>원인

이름이 인 투구 차트가 `azure-policy-addon` 이미 설치 되었거나 부분적으로 설치 되었습니다.

#### <a name="resolution"></a>해결 방법

지침에 따라 [Kubernetes 추가 기능에 대 한 Azure Policy를 제거한](../concepts/policy-for-kubernetes.md#remove-the-add-on)후 명령을 다시 실행 `helm install azure-policy-addon` 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- [Microsoft Q&A를](/answers/topics/azure-policy.html)통해 전문가의 답변을 받으세요.
- [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
- 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
