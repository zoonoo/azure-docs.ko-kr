---
title: Azure Cost Management에서 사용량 및 AWS 비용 관리
description: 이 문서에서는 Cost Management에서 비용 분석 및 예산을 AWS 비용 및 사용량 관리를 사용 하는 방법을 이해 합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 007b6c409dde248a4dde7a15fd16b543add234bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870314"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>AWS 비용 및 Azure에서 사용 현황을 관리합니다

설정 구성 Azure Cost Management에 대 한 AWS 비용 및 사용 현황 보고서 통합 한 후에 AWS 비용 및 사용 시작 준비가 되었습니다. 이 문서에서는 Cost Management에서 비용 분석 및 예산을 AWS 비용 및 사용량 관리를 사용 하는 방법을 이해 합니다.

통합을 구성 하지 않은 경우 [설정 및 AWS 사용량 보고서 통합을 구성](aws-integration-set-up-configure.md)합니다.

_시작 하기 전에_: 비용 분석을 사용 하 여 잘 모르는 경우 참조를 [탐색 및 분석 비용 분석을 사용 하 여 비용](quick-acm-cost-analysis.md) 빠른 시작 합니다. 및 Azure에서 예산을 잘 모르는 경우 참조를 [만들기 및 Azure 예산 관리](tutorial-acm-create-budgets.md) 자습서입니다.

## <a name="view-aws-costs-in-cost-analysis"></a>비용 분석에서 AWS 비용 보기

AWS 비용 다음 범위에서 비용 분석에 사용할 수 있습니다.

- 관리 그룹에서 연결 하는 AWS 계정
- 연결 하는 AWS 계정 비용
- AWS 계정 비용 통합

다음 섹션에서는 각각에 대 한 비용 및 사용량 데이터를 표시 되도록 범위를 사용 하는 방법에 설명 합니다.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>관리 그룹에서 연결 하는 AWS 계정 보기

다른 구독에서 제공 하는 집계 된 비용을 확인 하는 유일한 방법은 관리 그룹 범위를 사용 하 여 비용을 확인 및 연결 된 계정입니다. 관리 그룹을 사용 하 여 클라우드 간 보기를 제공 합니다.

비용 분석에서 범위 선택기를 열고 연결 AWS 계정을 보유 하는 관리 그룹을 선택 합니다. Azure portal에서 예제 이미지는 다음과 같습니다.

![선택 범위 보기의 예](./media/aws-integration-manage/select-scope01.png)



비용 분석, 공급자 (Azure 및 AWS) 별로 그룹화 된 관리 그룹 비용을 보여 주는 예제는 다음과 같습니다.

![사분기에 비용 분석에 대 한 Azure 및 AWS 비용을 보여 주는 예제](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>연결 하는 AWS 계정 비용 보기

AWS 링크 계정 비용을 볼 범위 선택 연 선택의 AWS 계정을 연결 합니다. AWS connector에 정의 된 대로 연결 된 계정 관리 그룹에 연결 되는 note 합니다.

AWS 선택 표시는 계정 범위를 연결 하는 예제는 다음과 같습니다.

![선택 범위 보기의 예](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>보기 AWS 계정 비용 통합

AWS를 보려는 통합된 계정 비용 범위 선택기를 열고 선택한는 AWS 계정을 통합 합니다. 통합 계정 범위를 AWS 선택 표시는 예는 다음과 같습니다.

![선택 범위 보기의 예](./media/aws-integration-manage/select-scope03.png)



이 범위의 모든 AWS의 집계 보기를 통합 하는 AWS 계정에 연결 된 계정 연결을 제공 합니다. AWS에 대 한 비용 통합 서비스 이름별로 그룹화 된 계정을 보여 주는 예제는 다음과 같습니다.

![AWS를 보여 주는 예제 비용 분석 비용 통합](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>필터링 및 그룹화에 사용할 수 있는 차원

다음 표에 차원 그룹화 및 필터링 하 여 비용 분석에 사용할 수 있습니다.

| 차원 | Amazon CUR 헤더 | 범위 | 설명 |
| --- | --- | --- | --- |
| 가용성 영역 | lineitem/AvailabilityZone | 모두 |   |
| Location | 제품/지역 | 모두 |   |
| 미터 |   | 모두 |   |
| 미터 범주 | lineItem/ProductCode | 모두 |   |
| 미터 하위 범주 | lineitem/UsageType | 모두 |   |
| 작업(Operation) | lineItem/Operation | 모두 |   |
| Resource | lineItem/ResourceId | 모두 |   |
| 리소스 종류 | product/instanceType | 모두 | 제품/instanceType null 인 경우 lineItem/UsageType 사용 됩니다. |
| ResourceGuid | N/A | 모두 | Azure 미터 GUID입니다. |
| 서비스 이름 | product/ProductName | 모두 | 제품/ProductName null 인 경우 lineItem/ProductCode 사용 됩니다. |
| 서비스 계층 |   |   |   |
| 구독 ID | lineItem/UsageAccountId | 통합된 계정 및 관리 그룹 |   |
| 구독 이름 | N/A | 통합된 계정 및 관리 그룹 | 계정 이름은 AWS 조직 API를 사용 하 여 수집 됩니다. |
| 태그 | resourceTags/\* | 모두 | 합니다 _사용자:_ 접두사 클라우드 간 태그 수 있도록 사용자 정의 태그에서 제거 됩니다. 합니다 _aws:_ 접두사는 그대로 유지 됩니다. |
| 대금 청구 계정 ID | bill/PayerAccountId | 관리 그룹 |   |
| 대금 청구 계정 이름 | N/A | 관리 그룹 | 계정 이름은 AWS 조직 API를 사용 하 여 수집 됩니다. |
| 공급자 | N/A | 관리 그룹 | AWS 또는 Azure입니다. |

## <a name="set-budgets-on-aws-scopes"></a>AWS 범위 집합 예산

예산을 사용 하 여 사전에 조직에서 비용을 줄이고 책임을 관리 합니다. 예산은 연결 된 계정 범위를 통합 하는 AWS 계정 및 AWS에 설정 됩니다. Cost Management에 표시 된 것을 통합 하는 AWS 계정에 대 한 예산의 예는 다음과 같습니다.

![AWS에 대 한 예산을 보여 주는 예제 통합 계정](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS 데이터 수집 프로세스

AWS 커넥터를 설정한 후 데이터 수집 및 검색 프로세스를 시작 합니다. 모든 사용 현황 데이터를 수집 하는 데 몇 시간 걸릴 수 있습니다. 기간에 따라 달라 집니다.

- AWS S3 버킷에 현재 파일을 처리 하는 데 필요한 시간입니다.
- AWS 통합 계정 및 AWS 연결 된 계정 범위를 만드는 데 필요한 시간입니다.
- 시간 및 AWS의 빈도 작성 하는 비용 및 사용 현황 보고서 파일을 S3 버킷의

## <a name="aws-integration-pricing"></a>AWS 통합 가격

각 AWS 커넥터 90 무료 평가판 일을 가져옵니다. 공개 미리 보기 동안에 무료입니다.

가격에는 1 %AWS 월별 비용입니다. 이전 달의 송장 발부 된 비용을 기반으로 청구는 매월 합니다.

AWS Api에 액세스 하면 추가 비용이 발생할 수 있습니다.

## <a name="aws-integration-limitations"></a>AWS 통합 제한 사항

- Cost Management는 여러 통화 형식을 포함 하는 비용 보고서를 지원 하지 않습니다. 오류 메시지를 범위에 포함 된 여러 통화를 선택 하는 경우 표시 됩니다.
- AWS GovCloud (미국), AWS Gov 또는 AWS 중국 클라우드 커넥터 지원 하지 않습니다.
- Cost Management에 AWS 보여 줍니다 _사용량 비용_ 만 합니다. 세금, 환불 "," RI "," 크레딧 "또는" 형식이 아직 지원 되지 않음 다른 요금을 지원 합니다.

## <a name="troubleshooting-aws-integration"></a>AWS 통합 문제 해결

다음 문제 해결 정보를 사용 하 여 일반적인 문제를 해결 합니다.

### <a name="no-permission-to-aws-linked-accounts"></a>AWS 연결 된 계정에 권한 없음

두 가지 방법으로 연결 하는 AWS 계정 비용을 액세스 하는 권한을 얻으려면:

- AWS 연결 된 계정에 있는 관리 그룹에 대 한 액세스를 가져옵니다.
- 사람이 연결 AWS 계정에 대 한 권한은 부여 합니다.

기본적으로 AWS 커넥터 작성자는 커넥터에서 만든 모든 개체의 소유자입니다. 통합된 계정 및는 AWS 계정 연결을 AWS 등.

### <a name="collection-failed-with-assumerole"></a>AssumeRole 하지 못했으며 컬렉션

이 오류는 Cost Management AWS AssumeRole API를 호출할 수 없는 것을 의미 합니다. 이 문제는 역할 정의 사용 하 여 문제로 인해 발생할 수 있습니다. 다음 조건이 충족 되는지 확인 합니다.

- 외부 ID 역할 정 및 연결선 정의에 있는 것과 같습니다.
- 역할 유형이 설정 되어 **다른 AWS 또는 제 3 자에 속하는 계정.**
- 합니다 **MFA를 요구** 선택 지워집니다.
- AWS 역할에 있는 신뢰할 수 있는 AWS 계정이 _432263259397_합니다.

### <a name="collection-failed-with-access-denied"></a>컬렉션 액세스 거부로 인해 실패 했습니다

이 오류 메시지 Cost Management에 Amazon S3 버킷에 저장 된 현재 파일에 액세스할 수 없는 것을 의미 합니다. 역할에 연결 된 AWS JSON 정책 맨 아래에 표시 된 예제와 유사 하 고 있는지 확인 합니다 [AWS에서 역할 및 정책을 만들기](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) 섹션입니다.

### <a name="connector-error-with-failedtofindreport"></a>FailedToFindReport 사용 하 여 커넥터 오류

이 오류는 Cost Management connector에 정의 된 비용 및 사용 현황 보고서를 찾을 수 없습니다를 의미 합니다. 삭제 되지 않습니다 및 역할에 연결 된 AWS JSON 정책 맨 아래에 표시 된 예제 유사한 지 확인 합니다 [AWS에서 역할 및 정책을 만들기](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) 섹션입니다.

## <a name="next-steps"></a>다음 단계

- 관리 그룹을 사용 하 여 Azure 환경의 구성 하지 않은 경우 [관리 그룹의 설치를 초기](../governance/management-groups/index.md#initial-setup-of-management-groups)합니다.
