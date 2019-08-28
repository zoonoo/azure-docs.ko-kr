---
title: Azure Cost Management에서 AWS 비용과 사용량 관리
description: 이 문서는 Cost Management에서 비용 분석 및 예산을 사용 하 여 AWS 비용과 사용량을 관리 하는 방법을 이해 하는 데 도움이 됩니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 6767644e037c6abb3e3044c8707e923a9137a01c
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019540"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Azure에서 AWS 비용 및 사용량 관리

Azure Cost Management에 대 한 비용 및 사용 현황 보고서 통합을 설정 하 고 구성한 후에는 AWS 비용 및 사용량을 AWS 관리할 준비가 되었습니다. 이 문서는 Cost Management에서 비용 분석 및 예산을 사용 하 여 AWS 비용과 사용량을 관리 하는 방법을 이해 하는 데 도움이 됩니다.

통합을 아직 구성 하지 않은 경우 [AWS 사용 보고서 통합 설정 및 구성](aws-integration-set-up-configure.md)을 참조 하세요.

_시작 하기 전에_: 비용 분석에 익숙하지 않은 경우 비용 분석 빠른 시작을 사용 하 여 [비용 탐색 및 분석](quick-acm-cost-analysis.md) 을 참조 하세요. Azure의 예산을 잘 모르는 경우 [azure 예산 만들기 및 관리](tutorial-acm-create-budgets.md) 자습서를 참조 하세요.

## <a name="view-aws-costs-in-cost-analysis"></a>비용 분석에서 AWS 비용 보기

AWS 비용은 다음 범위의 비용 분석에서 제공 됩니다.

- 관리 그룹에서 연결 된 계정 AWS
- AWS 연결 된 계정 비용
- AWS 통합 계정 비용

다음 섹션에서는 각 항목에 대 한 비용 및 사용 현황 데이터를 볼 수 있도록 범위를 사용 하는 방법을 설명 합니다.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>관리 그룹에서 AWS 연결 된 계정 보기

관리 그룹 범위를 사용 하 여 비용을 보는 것은 다른 구독 및 연결 된 계정에서 발생 하는 집계 된 비용을 확인 하는 유일한 방법입니다. 관리 그룹을 사용 하면 클라우드 간 보기가 제공 됩니다.

비용 분석에서 범위 선택기를 열고 AWS 연결 된 계정이 포함 된 관리 그룹을 선택 합니다. Azure Portal의 예제 이미지는 다음과 같습니다.

![Select 범위 뷰의 예](./media/aws-integration-manage/select-scope01.png)



공급자 (Azure 및 AWS) 별로 그룹화 된 비용 분석의 관리 그룹 비용을 보여 주는 예제는 다음과 같습니다.

![비용 분석의 사분기에 대 한 Azure 및 AWS 비용을 보여 주는 예제](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>AWS 연결 된 계정 비용 보기

AWS 링크 계정 비용을 보려면 범위 선택기를 열고 AWS 연결 된 계정을 선택 합니다. 연결 된 계정은 AWS 커넥터에 정의 된 관리 그룹에 연결 됩니다.

AWS 연결 된 계정 범위를 선택 하는 방법을 보여 주는 예제는 다음과 같습니다.

![Select 범위 뷰의 예](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>AWS 통합 계정 비용 보기

AWS 통합 계정 비용을 보려면 범위 선택기를 열고 AWS 통합 계정을 선택 합니다. AWS 통합 계정 범위를 선택 하는 방법을 보여 주는 예제는 다음과 같습니다.

![Select 범위 뷰의 예](./media/aws-integration-manage/select-scope03.png)



이 범위는 AWS 통합 계정에 연결 된 모든 AWS 연결 된 계정에 대 한 집계 보기를 제공 합니다. 서비스 이름별로 그룹화 된 AWS 통합 계정에 대 한 비용을 보여 주는 예제는 다음과 같습니다.

![비용 분석의 AWS 통합 비용을 보여 주는 예제](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>필터링 및 그룹화에 사용할 수 있는 차원

다음 표에서는 그룹화 및 비용 분석에서 필터링 할 수 있는 차원을 설명 합니다.

| 차원 | Amazon .CUR 헤더 | 범위 | 주석 |
| --- | --- | --- | --- |
| 가용성 영역 | lineitem/AvailabilityZone | All |   |
| 위치 | 제품/지역 | All |   |
| 미터 |   | All |   |
| 미터 범주 | lineItem/ProductCode | All |   |
| 미터 하위 범주 | lineitem/UsageType | All |   |
| 작업(Operation) | lineItem/작업 | All |   |
| Resource | lineItem/ResourceId | All |   |
| 리소스 형식 | product/instanceType | All | Product/instanceType가 null 이면 lineItem/UsageType이 사용 됩니다. |
| ResourceGuid | 해당 사항 없음 | All | Azure 측정기 GUID입니다. |
| 서비스 이름 | product/ProductName | All | Product/ProductName이 null 이면 lineItem/ProductCode가 사용 됩니다. |
| 서비스 계층 |   |   |   |
| 구독 ID | lineItem/UsageAccountId | 통합 계정 및 관리 그룹 |   |
| 구독 이름 | 해당 사항 없음 | 통합 계정 및 관리 그룹 | 계정 이름은 AWS 조직 API를 사용 하 여 수집 됩니다. |
| 태그 | resourceTags/\* | All | _사용자:_ 접두사는 사용자 정의 태그에서 제거 되어 클라우드 간 태그를 허용 합니다. _Aws:_ 접두사는 그대로 남아 있습니다. |
| 청구 계정 ID | 청구서/PayerAccountId | 관리 그룹 |   |
| 청구 계정 이름 | 해당 사항 없음 | 관리 그룹 | 계정 이름은 AWS 조직 API를 사용 하 여 수집 됩니다. |
| 공급자 | 해당 사항 없음 | 관리 그룹 | AWS 또는 Azure 중 하나입니다. |

## <a name="set-budgets-on-aws-scopes"></a>AWS 범위에서 예산 설정

예산을 사용 하 여 조직의 비용을 사전에 관리 하 고 책임을 추진 합니다. 예산은 AWS 통합 계정 및 AWS 연결 된 계정 범위에 대해 설정 됩니다. Cost Management에 표시 된 AWS 통합 계정에 대 한 예산 예는 다음과 같습니다.

![AWS 통합 계정의 예산을 보여 주는 예제](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS 데이터 수집 프로세스

AWS 커넥터를 설정한 후 데이터 수집 및 검색 프로세스가 시작 됩니다. 모든 사용 현황 데이터를 수집 하는 데 몇 시간 정도 걸릴 수 있습니다. 기간은 다음에 따라 달라 집니다.

- AWS S3 버킷에 있는 .CUR 파일을 처리 하는 데 필요한 시간입니다.
- AWS 통합 계정을 만들고 연결 된 계정 범위를 AWS 하는 데 필요한 시간입니다.
- AWS 시간 및 빈도는 S3 버킷에 비용 및 사용 보고서 파일을 기록 합니다.

## <a name="aws-integration-pricing"></a>AWS 통합 가격 책정

각 AWS 커넥터는 90의 무료 평가판을 얻습니다. 공개 미리 보기 중에는 요금이 부과 되지 않습니다.

정가는 AWS 월별 비용의 1%입니다. 매월 이전 달의 송장이 발부 된 비용을 기준으로 요금이 청구 됩니다.

AWS Api에 액세스 하면 추가 비용이 발생할 수 있습니다.

## <a name="aws-integration-limitations"></a>AWS 통합 제한 사항

- Cost Management는 여러 통화 유형을 포함 하는 비용 보고서를 지원 하지 않습니다. 여러 통화를 포함 하는 범위를 선택 하면 오류 메시지가 표시 됩니다.
- 클라우드 커넥터는 AWS GovCloud (US), AWS .Gov 또는 AWS 중국을 지원 하지 않습니다.
- Cost Management AWS _사용 비용도_ 표시 됩니다. 세금, 지원, 환불, RI, 크레딧 또는 기타 요금 유형도 아직 지원 되지 않습니다.

## <a name="troubleshooting-aws-integration"></a>AWS 통합 문제 해결

다음 문제 해결 정보를 사용 하 여 일반적인 문제를 해결할 수 있습니다.

### <a name="no-permission-to-aws-linked-accounts"></a>AWS 연결 된 계정에 대 한 사용 권한 없음

**오류 코드:** _상태로_

AWS 연결 된 계정 비용에 액세스할 수 있는 권한을 얻는 방법에는 두 가지가 있습니다.

- AWS 연결 된 계정이 있는 관리 그룹에 대 한 액세스 권한을 얻습니다.
- AWS 연결 된 계정에 대 한 사용 권한을 사용자에 게 부여 합니다.

기본적으로 AWS 커넥터 작성자는 커넥터가 만든 모든 개체의 소유자입니다. AWS 통합 계정 및 AWS 연결 된 계정을 포함 합니다.

커넥터 설정을 확인할 수 있도록 하려면 참가자 역할이 하나 이상 필요 하며, 판독기는 커넥터 설정을 확인할 수 없습니다.

### <a name="collection-failed-with-assumerole"></a>AssumeRole으로 수집 하지 못했습니다.

**오류 코드:** _FailedToAssumeRole_

이 오류는 Cost Management에서 AWS AssumeRole API를 호출할 수 없음을 의미 합니다. 이 문제는 역할 정의 문제로 인해 발생할 수 있습니다. 다음 조건이 충족 되는지 확인 합니다.

- 외부 ID는 역할 정의 및 커넥터 정의의 ID와 동일 합니다.
- 역할 유형이 **사용자 또는 타사에 속하는 다른 AWS 계정** 으로 설정 되어 있습니다.
- **MFA 필요** 를 선택 취소 합니다.
- AWS 역할의 신뢰할 수 있는 AWS 계정은 _432263259397_입니다.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>액세스 거부로 인해 수집 실패-현재 보고서 정의

**오류 코드:** _AccessDeniedReportDefinitions_ 

이 오류는 Cost Management에서 비용 및 사용 보고서 정의를 볼 수 없음을 의미 합니다. 이 사용 권한은 Azure Cost Management에 의해 c u r n이 예상 대로 정의 되었는지 확인 하는 데 사용 됩니다. [AWS에서 비용 및 사용 현황 보고서 만들기](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)를 참조 하세요.

### <a name="collection-failed-with-access-denied---list-reports"></a>액세스 거부로 인해 수집 하지 못했습니다. 목록 보고서

**오류 코드:** _AccessDeniedListReports_ 

이 오류는 Cost Management가 .CUR가 있는 S3 버킷의 개체를 나열할 수 없음을 의미 합니다. AWS IAM 정책에는 버킷 및 버킷의 개체에 대 한 권한이 필요 합니다. [AWS에서 역할 및 정책 만들기](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)를 참조 하세요.

### <a name="collection-failed-with-access-denied---download-report"></a>액세스 거부로 인해 수집 실패-보고서 다운로드 

**오류 코드:** _AccessDeniedDownloadReport_ 

이 오류는 Cost Management에서 Amazon S3 버킷에 저장 된 파일을 액세스 하 여 다운로드할 수 없음을 의미 합니다. 역할에 연결 된 AWS JSON 정책이 [AWS에서 역할 및 정책 만들기](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) 섹션의 아래쪽에 표시 된 예제와 유사 해야 합니다.

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>비용 및 사용 현황 보고서를 찾을 수 없어 수집 하지 못했습니다.

**오류 코드:** _FailedToFindReport_

이 오류는 Cost Management에서 커넥터에 정의 된 비용 및 사용 현황 보고서를 찾을 수 없음을 의미 합니다. 삭제 되지 않았는지 확인 하 고 역할에 연결 된 AWS JSON 정책이 [AWS에서 역할 및 정책 만들기](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) 섹션의 아래쪽에 표시 된 예제와 비슷합니다.

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>비용 및 사용 보고서 정의 불일치로 인해 커넥터를 만들거나 확인할 수 없습니다.

**오류 코드:** _ReportIsNotValid_

이 오류는 AWS Cost and Usage Report의 정의와 관련이 있습니다 .이 보고서에 대 한 특정 설정이 필요 합니다. [AWS에서 비용 및 사용 보고서 만들기](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws) 의 요구 사항을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 관리 그룹을 사용 하 여 Azure 환경을 아직 구성 하지 않은 경우 [관리 그룹의 초기 설정](../governance/management-groups/index.md#initial-setup-of-management-groups)을 참조 하세요.
