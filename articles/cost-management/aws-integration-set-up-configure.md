---
title: Azure Cost Management를 사용 하 여 AWS 비용 및 사용 현황 보고서 통합 설정 및 구성
description: 이 문서에서는 Azure Cost Management와의 AWS 비용 및 사용 보고서 통합을 설정 하 고 구성 하는 과정을 안내 합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 66dbe45ff1a8ee773fdf7fcb0aa7cfe8e6ad6437
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219688"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>AWS 비용 및 사용 보고서 통합 설정 및 구성

Amazon Web Services (AWS) 비용 및 사용 보고서 (.CUR) 통합을 사용 하면 Azure Cost Management에서 AWS 지출을 모니터링 하 고 제어할 수 있습니다. 통합은 Azure Portal에서 Azure 및 AWS에 대 한 비용을 모니터링 하 고 제어 하는 단일 위치를 허용 합니다. 이 문서에서는 Azure Cost Management 기능을 사용 하 여 비용을 분석 하 고 예산을 검토할 수 있도록 통합을 설정 하 고 구성 하는 방법을 설명 합니다.

Cost Management는 AWS 액세스 자격 증명을 사용 하 여 보고서 정의를 가져오고 보고서 GZIP CSV 파일을 다운로드 하 여 S3 버킷에 저장 된 AWS 비용 및 사용 현황 보고서를 처리 합니다.

## <a name="create-a-cost-and-usage-report-in-aws"></a>AWS에서 비용 및 사용 현황 보고서 만들기

비용 및 사용 현황 보고서를 사용 하는 것은 AWS 비용을 수집 하 고 처리 하기 위한 AWS 권장 방법입니다. 자세한 내용은 [AWS Cost And Usage Report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) 설명서를 참조 하세요.

AWS에서 청구 및 Cost Management 콘솔의 **비용 & 사용 보고서** 페이지를 사용 하 여 다음 단계를 통해 비용 및 사용 현황 보고서를 만들 수 있습니다.

1. AWS 관리 콘솔에 로그인 하 고 [청구 및 Cost Management 콘솔](https://console.aws.amazon.com/billing)을 엽니다.
2. 탐색 창에서 **비용 & 사용 보고서**를 선택 합니다.
3. **보고서 만들기**를 선택 합니다.
4. **보고서 이름**에 보고서 이름을 입력 합니다.
5. **추가 보고서 세부 정보**에서 **리소스 id 포함**을 선택 합니다.
6. **데이터 새로 고침 설정**의 경우 청구서를 마친 후 AWS가 환불, 크레딧 또는 지원 요금을 계정에 적용 하는 경우 AWS 비용 및 사용 현황 보고서를 새로 고칠지 여부를 선택 합니다. 보고서를 새로 고치면 Amazon s 3에 새 보고서가 업로드 됩니다. 설정을 선택 된 상태로 두는 것이 좋습니다.
7. **다음**을 선택합니다.
8. **S3 버킷에**대해 **구성**을 선택 합니다.
9. S3 버킷 구성 대화 상자에서 다음 작업 중 하나를 수행 합니다.
    1. 드롭다운 목록에서 기존 버킷을 선택 하 고 **다음**을 선택 합니다.
    2. 버킷 이름과 새 버킷을 만들 지역을 입력 하 고 **다음**을 선택 합니다.
10. **이 정책이 올바른지 확인**합니다 .를 선택한 다음 **저장**을 클릭 합니다.
11. 필드 보고서 경로 접두사에 보고서 이름 앞에 추가할 보고서 경로 접두사를 입력 합니다.
접두사를 지정 하지 않는 경우 기본 접두사는 보고서에 대해 지정한 이름입니다. 날짜 범위에는 `/report-name/date-range/` 형식이 있습니다.
12. **시간 단위**에 대해 **매시간**을 선택 합니다.
13. **보고서 버전 관리**를 위해 각 버전의 보고서에서 이전 버전을 덮어쓸지 아니면 새 보고서를 추가로 사용할지 여부를 선택 합니다.
14. **에 데이터 통합을 사용 하도록 설정**하는 경우에는 아무 것도 선택 하지 않아도 됩니다.
15. **압축**의 경우 **GZIP**을 선택 합니다.
16. **다음**을 선택합니다.
17. 보고서에 대 한 설정을 검토 한 후 **검토 및 완료**를 선택 합니다.

    보고서 이름을 확인 합니다. 이후 단계에서 사용 합니다.

AWS는 Amazon S3 버킷에 보고서를 배달 하기 시작 하는 데 최대 24 시간이 걸릴 수 있습니다. 배달이 시작 된 후 AWS는 AWS 비용 및 사용 보고서 파일을 하루에 한 번 이상 업데이트 합니다. 배달이 시작 될 때까지 기다리지 않고 AWS 환경을 계속 구성할 수 있습니다.

## <a name="create-a-role-and-policy-in-aws"></a>AWS에서 역할 및 정책 만들기

Azure Cost Management는 비용 및 사용 보고서가 하루에 여러 번 위치한 S3 버킷에 액세스 합니다. 서비스는 새 데이터를 확인 하기 위해 자격 증명에 액세스 해야 합니다. AWS에서 역할 및 정책을 만들어 Cost Management에 액세스할 수 있도록 합니다.

Cost Management의 AWS 계정에 대 한 역할 기반 액세스를 사용 하도록 설정 하기 위해 AWS 콘솔에 역할이 만들어집니다. AWS 콘솔에서 _역할 ARN_ 및 _외부 ID_ 가 있어야 합니다. 나중에 Cost Management의 **AWS 커넥터 만들기** 페이지에서 사용 합니다.

새 역할 만들기 마법사를 사용 합니다.

1. AWS 콘솔에 로그인 하 고 **서비스**를 선택 합니다.
2. 서비스 목록에서 **IAM**을 선택 합니다.
3. **역할** 을 선택한 다음 **역할 만들기**를 선택 합니다.
4. 다음 페이지에서 **다른 AWS 계정을**선택 합니다.
5. **계정 ID**에 **432263259397**을 입력 합니다.
6. **옵션**에서 **외부 ID 필요 (타사에서이 역할을 가정 하는 경우 모범 사례)** 를 선택 합니다.
7. **외부 id**에 외부 id를 입력 합니다. 외부 ID는 AWS 역할과 Azure Cost Management 간의 공유 암호입니다. Cost Management의 **새 커넥터** 페이지 에서도 동일한 외부 ID를 사용 합니다. 예를 들어 외부 ID는 _Companyname1234567890123_와 비슷합니다.

    > [!NOTE]
    > **MFA 요구**에 대 한 선택 항목을 변경 하지 마세요. 지워야 합니다.
8. **다음: 사용 권한**을 선택 합니다.
9. **Create policy**(정책 만들기)를 선택합니다. 새 브라우저 탭이 열립니다. 여기에서 정책을 만들 수 있습니다.
10. **서비스 선택을**선택 합니다.

비용 및 사용 현황 보고서에 대 한 사용 권한 구성:

1. **비용 및 사용 현황 보고서**를 입력 합니다.
2. **액세스 수준** > **읽기** > **DescribeReportDefinitions**를 선택 합니다. 이 단계를 통해 Cost Management에서 정의 된 모든 보고서를 읽고 보고서 정의 필수 구성 요소와 일치 하는지 확인할 수 있습니다.
3. 추가 **권한 추가**를 선택 합니다.

S3 버킷 및 개체에 대 한 사용 권한 구성:

1. **서비스 선택을**선택 합니다.
2. **S3**을 입력 합니다.
3. **액세스 수준** > **목록** > **listbucket**선택 합니다. 이 작업은 S3 버킷의 개체 목록을 가져옵니다.
4. **액세스 수준** > **읽기** > **GetObject**를 선택 합니다. 이 작업을 통해 대금 청구 파일을 다운로드할 수 있습니다.
5. **리소스**를 선택 합니다.
6. **버킷 – ADD ARN**을 선택 합니다.
7. **버킷 이름**에 .cur 파일을 저장 하는 데 사용 된 버킷을 입력 합니다.
8. **개체 – 추가 ARN**을 선택 합니다.
9. **버킷 이름**에 .cur 파일을 저장 하는 데 사용 된 버킷을 입력 합니다.
10. **개체 이름**에서 **Any**를 선택 합니다.
11. 추가 **권한 추가**를 선택 합니다.

Cost Explorer에 대 한 사용 권한 구성:

1. **서비스 선택을**선택 합니다.
2. **Cost Explorer 서비스**를 입력 합니다.
3. **모든 비용 탐색기 서비스 작업 (ce:\*)** 을 선택 합니다. 이 작업은 컬렉션이 올바른지 유효성을 검사 합니다.
4. 추가 **권한 추가**를 선택 합니다.

AWS 조직에 대 한 권한 추가:

1. **조직**을 입력 합니다.
2. **액세스 수준** > **목록** > **listaccounts**를 선택 합니다. 이 작업은 계정 이름을 가져옵니다.
3. **정책 검토**에서 새 정책의 이름을 입력 합니다. 올바른 정보를 입력 했는지 확인 한 다음 **정책 만들기**를 선택 합니다.
4. 이전 탭으로 돌아가서 브라우저의 웹 페이지를 새로 고칩니다. 검색 창에서 새 정책을 검색 합니다.
5. **다음: 검토**를 선택합니다.
6. 새 역할의 이름을 입력 합니다. 올바른 정보를 입력 했는지 확인 한 후 **역할 만들기**를 선택 합니다.

    역할을 만들 때 이전 단계에서 사용한 역할 ARN 및 외부 ID를 확인 합니다. 나중에 Azure Cost Management 커넥터를 설정할 때 사용 합니다.

정책 JSON은 다음 예제와 유사 합니다. _Bucketname_ 을 S3 버킷의 이름으로 바꿉니다.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Azure에서 새 AWS 커넥터 설정

다음 정보를 사용 하 여 AWS 커넥터를 만들고 AWS 비용 모니터링을 시작 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Cost Management + 청구** > **Cost Management**으로 이동 합니다.
3. **설정**아래에서 **클라우드 커넥터 (미리 보기)** 를 선택 합니다.  
    클라우드 커넥터 (미리 보기) 설정을 보여 주는 ![예제](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. 페이지 위쪽에서 **+ 추가** 를 선택 하 여 커넥터를 만듭니다.
5. **AWS 커넥터 만들기** 페이지의 **표시 이름**에 커넥터의 이름을 입력 합니다.  
    AWS 커넥터를 만들기 위한 페이지 ![예제](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. 필요에 따라 기본 관리 그룹을 선택 합니다. 검색 된 모든 연결 된 계정이 저장 됩니다. 나중에 설정할 수 있습니다.
7. 미리 보기가 만료 될 때 연속 작업을 보장 하려면 **청구** 섹션에서 **일반 공급 시 1% 자동 요금 청구** 를 선택 합니다. 자동 옵션을 선택 하는 경우 청구 구독을 선택 해야 합니다.
8. **역할 ARN**에 AWS에서 역할을 설정할 때 사용한 값을 입력 합니다.
9. **외부 ID**의 경우 AWS에서 역할을 설정할 때 사용한 값을 입력 합니다.
10. **보고서 이름**에 AWS에서 만든 이름을 입력 합니다.
11. **다음** 을 선택 하 고 **만들기**를 선택 합니다.

새 AWS 범위, AWS 통합 계정, AWS 연결 된 계정 및 해당 비용 데이터가 표시 되는 데 몇 시간이 걸릴 수 있습니다.

커넥터를 만든 후 액세스 제어를 할당 하는 것이 좋습니다. 사용자에 게 새로 검색 된 범위에 대 한 사용 권한이 할당 됩니다. AWS 통합 계정 및 AWS 연결 된 계정. 커넥터를 만드는 사용자는 커넥터의 소유자, 통합 계정 및 모든 연결 된 계정입니다.

검색 후 사용자에 게 커넥터 권한을 할당 하는 것은 기존 AWS 범위에 대 한 사용 권한을 할당 하지 않습니다. 대신, 새 연결 된 계정에만 권한이 할당 됩니다.

## <a name="take-additional-steps"></a>추가 단계 수행

- 아직 없는 경우 [관리 그룹을 설정](../governance/management-groups/overview.md#initial-setup-of-management-groups)합니다.
- 새 범위가 범위 선택기에 추가 되었는지 확인 합니다. 최신 데이터를 보려면 **새로 고침** 을 선택 합니다.
- **클라우드 커넥터** 페이지에서 커넥터를 선택 하 고 **청구 계정으로 이동** 을 선택 하 여 관리 그룹에 연결 된 계정을 할당 합니다.

## <a name="manage-cloud-connectors"></a>클라우드 커넥터 관리

**클라우드 커넥터** 페이지에서 커넥터를 선택 하면 다음을 수행할 수 있습니다.

- AWS 통합 계정에 대 한 정보를 보려면 **청구 계정으로 이동** 을 선택 합니다.
- 커넥터에 대 한 역할 할당을 관리 하려면 **Access Control** 을 선택 합니다.
- **편집** 을 선택 하 여 커넥터를 업데이트 합니다. AWS 계정 번호는 역할 ARN에 표시 되기 때문에 변경할 수 없습니다. 하지만 새 커넥터를 만들 수 있습니다.
- 확인 테스트를 다시 실행 하 여 Cost Management에서 커넥터 설정을 사용 하 여 데이터를 수집할 수 있는지 확인 하려면 **확인** 을 선택 합니다.

![만든 AWS 커넥터의 예제 목록](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Azure 관리 그룹 설정

Azure 구독과 AWS 연결 된 계정을 동일한 관리 그룹에 배치 하 여 클라우드 간 공급자 정보를 볼 수 있는 단일 위치를 만듭니다. 관리 그룹을 사용 하 여 Azure 환경을 아직 구성 하지 않은 경우 [관리 그룹의 초기 설정](../governance/management-groups/overview.md#initial-setup-of-management-groups)을 참조 하세요.

비용을 구분 하려면 AWS 연결 된 계정만 포함 하는 관리 그룹을 만들 수 있습니다.

## <a name="set-up-an-aws-consolidated-account"></a>AWS 통합 계정 설정

AWS 통합 계정은 여러 AWS 계정에 대 한 청구 및 지불을 결합 합니다. 또한 AWS 연결 된 계정으로 작동 합니다.

![AWS 통합 계정에 대 한 예제 세부 정보](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

페이지에서 다음을 수행할 수 있습니다.

- **업데이트** 를 선택 하 여 AWS 연결 된 계정의 연결을 관리 그룹과 대량으로 업데이트 합니다.
- **Access Control** 를 선택 하 여 범위에 대 한 역할 할당을 설정 합니다.

### <a name="permissions-for-an-aws-consolidated-account"></a>AWS 통합 계정에 대 한 사용 권한

기본적으로 AWS 통합 계정에 대 한 사용 권한은 AWS 커넥터 권한에 따라 계정 만들기에 대해 설정 됩니다. 커넥터 작성자는 소유자입니다.

AWS 통합 계정의 **액세스 수준** 페이지를 사용 하 여 액세스 수준을 관리할 수 있습니다. 그러나 AWS 연결 된 계정은 AWS 통합 계정에 대 한 사용 권한을 상속 하지 않습니다.

## <a name="set-up-an-aws-linked-account"></a>AWS 연결 된 계정 설정

AWS 연결 된 계정은 AWS 리소스를 만들고 관리 하는 위치입니다. 또한 연결 된 계정은 보안 경계 역할을 합니다.

이 페이지에서 다음을 수행할 수 있습니다.

- **업데이트** 를 선택 하 여 AWS 연결 된 계정과 관리 그룹의 연결을 업데이트 합니다.
- 범위에 대 한 역할 할당을 설정 하려면 **Access Control** 을 선택 합니다.

![AWS 연결 된 계정 페이지의 예](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>AWS 연결 된 계정에 대 한 사용 권한

기본적으로 AWS 연결 된 계정에 대 한 사용 권한은 생성 시 AWS 커넥터 권한에 따라 설정 됩니다. 커넥터 작성자는 소유자입니다. AWS 연결 된 계정의 **액세스 수준** 페이지를 사용 하 여 액세스 수준을 관리할 수 있습니다. AWS 연결 된 계정은 AWS 통합 계정에서 사용 권한을 상속 하지 않습니다.

AWS 연결 된 계정은 항상 속한 관리 그룹에서 사용 권한을 상속 합니다.

## <a name="next-steps"></a>다음 단계

- 이제 AWS 비용 및 사용 보고서 통합을 설정 하 고 구성 했으므로 [AWS 비용 및 사용량](aws-integration-manage.md)을 계속 관리 합니다.
- 비용 분석에 익숙하지 않은 경우 비용 분석 빠른 시작을 [사용 하 여 비용 탐색 및 분석](quick-acm-cost-analysis.md) 을 참조 하세요.
- Azure의 예산을 잘 모르는 경우 [azure 예산 만들기 및 관리](tutorial-acm-create-budgets.md)를 참조 하세요.
