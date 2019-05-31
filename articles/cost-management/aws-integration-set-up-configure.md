---
title: 설정 하 고 Azure Cost Management를 사용 하 여 AWS 비용 및 사용 현황 보고서 통합 구성
description: 이 문서에서는 안내를 설정 하 고 Azure Cost Management를 사용 하 여 AWS 비용 및 사용 현황 보고서 통합을 구성 합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002118"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>설정 하 고 AWS 비용 및 사용 현황 보고서 통합 구성

Amazon Web Services (AWS)를 사용 하 여 비용 및 사용 보고서 (현재) 통합을 모니터링 하 고 Azure Cost Management에서 AWS 들이는 제어 합니다. 모니터링할 수 있습니다 하는 Azure portal 및 Azure와 AWS 둘 다에 대 한 지출 한도 컨트롤의 단일 위치를 허용 하는 통합 합니다. 이 문서에는 통합을 설정 하 여 Azure Cost Management 기능을 사용 하 여 비용을 분석 하 고 예산을 검토에 있도록 구성 하는 방법을 설명 합니다.

비용 관리 프로세스 AWS 액세스 자격 증명을 사용 하 여 보고서 정의 가져오고 보고서 GZIP CSV 파일을 다운로드 하 여를 S3 버킷에 저장 AWS 비용 및 사용 현황 보고서를 선택 합니다.

## <a name="create-a-cost-and-usage-report-in-aws"></a>AWS에서 비용 및 사용 현황 보고서를 만들려면

비용 및 사용 현황 보고서를 사용 하는 방법은 AWS 권장 수집 하 고 AWS 비용을 처리 합니다. 자세한 내용은 참조는 [AWS 비용 및 사용 현황 보고서](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) 설명서.

사용 된 **비용 및 사용 현황 보고서** 비용 및 사용 현황 보고서를 만들려면 다음 단계를 사용 하 여 AWS에서 청구 및 비용 관리 콘솔의 페이지:

1. AWS 관리 콘솔에 로그인 하 고 엽니다는 [청구 및 비용 관리 콘솔](https://console.aws.amazon.com/billing)합니다.
2. 탐색 창에서 선택 **비용 및 사용 현황 보고서**합니다.
3. 선택 **보고서를 만들**합니다.
4. 에 대 한 **보고서 이름**, 보고서의 이름을 입력 합니다.
5. 에 대 한 **추가 보고서 세부 정보** , 선택한 보고서의 각 리소스의 Id를 포함 하도록 **포함 리소스 Id**합니다.
6. 에 대 한 **데이터 새로 고침 설정**AWS 비용 및 사용 현황 보고서를 AWS 환불, 크레딧 적용 되는 경우 새로 고칠 수 있는지 여부를 선택 하거나 청구서를 종료 한 후 계정에 대 한 요금을 지원 합니다. 보고서를 새로 고칠 때 새 보고서를 Amazon S3에 업로드 됩니다. 이 설정을 유지 하는 것이 좋습니다.
7. **다음**을 선택합니다.
8. 에 대 한 **S3 버킷을**, 선택 **구성**합니다.
9. S3 버킷 구성 대화 상자에서 다음 중 하나를 수행 합니다.
    1. 드롭다운 목록에서 기존 버킷을 선택 하 고 선택 **다음**합니다.
    2. 버킷 이름 및 새 버킷을 만들고 선택 하려는 지역을 입력 **다음**합니다.
10. 이 정책 인지 확인 한 I 선택 수정 하 고 저장을 선택 합니다.
11. (선택 사항) 보고서 경로 접두사에 대 한 보고서의 이름 앞에 추가 하려는 보고서 경로 접두사를 입력 합니다.
접두사를 지정 하지 않으면 경우 기본 접두사는 4 단계에서 보고서와 같은 형식으로 보고서에 대 한 날짜 범위에 대 한 지정 된 이름: `/report-name/date-range/`
12. 에 대 한 **시간 단위**, 선택 **시간별**합니다.
13. 에 대 한 **보고서 버전 관리**, 각 버전의 보고서는 이전 버전의 보고서를 덮어쓸지 아니면 이전 버전 외에도 배달할 수 있는지 여부를 선택 합니다.
14. 에 대 한 **에 대 한 데이터 통합을 사용 하도록 설정**, 선택 영역이 없는 필요 합니다.
15. 에 대 한 **압축**를 선택 **GZIP**합니다.
16. **다음**을 선택합니다.
17. 보고서에 대 한 설정을 검토 한 후 선택 **검토 및 완료**합니다.

    보고서 이름을 note 합니다. 이후 단계에서 사용할 수 있습니다.

AWS에 Amazon S3 버킷에 보고서를 배달 하려면 최대 24 시간이 걸릴 수 있습니다. 배달 시작 된 후 AWS 업데이트 AWS 비용 및 사용 현황 보고서 파일 하루에 한 번 이상 시작 하는 배달 될 때까지 기다리지 않고 AWS environment 구성 작업을 계속할 수 있습니다.

## <a name="create-a-role-and-policy-in-aws"></a>AWS에서 역할 및 정책 만들기

Azure Cost Management에는 비용 및 사용 현황 보고서가 있는 하루에 여러 번 S3 버킷을 액세스 합니다. 서비스 자격 증명을 새 데이터를 확인 하는 액세스를 해야 합니다. Cost Management에 액세스할 수 있도록 하는 AWS에서 역할 및 정책을 만듭니다.

Cost Management에서 AWS 계정에 역할 기반 액세스를 활성화 하려면 AWS 콘솔에서 역할 생성 됩니다. 해야 합니다 _역할 ARN_ 하 고 _외부 ID_ AWS 콘솔에서. 나중에 사용 하에 **AWS 커넥터를 만들려면** Cost Management에서 페이지입니다.

사용 하 여 새 역할 마법사:

1. AWS 콘솔에 로그인 하 고 선택 **Services**합니다.
2. 서비스 목록에서 선택 **IAM**합니다.
3. 선택 **역할** 선택한 후 **Create Role**합니다.
4. 다음 페이지에서 선택 **다른 AWS 계정**합니다.
5. **계정 ID**를 입력 **432263259397**합니다.
6. **옵션**를 선택 **(타사는이 역할을 가정 하는 경우 모범 사례) 외부 ID 필요**합니다.
7. **외부 ID**, 외부 ID를 입력 합니다. 외부 ID는 AWS 역할와 Azure Cost Management 간에 암호를 공유 합니다. ID가 동일한 외부 에서도 사용 되는 **새 커넥터** Cost Management에서 페이지입니다. 예를 들어 외부 ID 유사 _Companyname1234567890123_합니다.

    > [!NOTE]
    > 에 대 한 선택을 변경 하지 마세요 **MFA를 요구**합니다. 선택 취소 상태로 유지 됩니다.
8. 완료되면 **다음: 사용 권한**을 클릭합니다.
9. 선택 **정책 만들기**합니다. 새 브라우저 탭이 열립니다. 정책을 만든 것이입니다.
10. 선택 **서비스를 선택**합니다.

비용 및 사용 현황 보고서에 대 한 권한을 구성 합니다.

1. 입력 **비용 및 사용 현황 보고서**합니다.
2. 선택 **액세스 수준** > **읽기** > **DescribeReportDefinitions**합니다. 이 단계에서는 비용 관리를 현재 보고서 정의 읽고 보고서 정의가 필수 구성 요소 일치 하는지 확인 합니다.
3. 선택 **사용 권한을 추가**합니다.

S3 버킷 및 개체에 대해 사용 권한을 구성 합니다.

1. 선택 **서비스를 선택**합니다.
2. 입력 **S3**합니다.
3. 선택 **액세스 수준** > **목록** > **ListBucket**합니다. 이 작업 S3 버킷에서 개체 목록을 가져옵니다.
4. 선택 **액세스 수준** > **읽기** > **GetObject**합니다. 이 작업에서는 청구 파일의 다운로드를 수 있습니다.
5. 선택 **리소스**합니다.
6. 선택 **버킷 – 추가 ARN**합니다.
7. **버킷 이름**, 현재 파일을 저장 하는 데 사용 하는 버킷에 입력 합니다.
8. 선택 **개체 – 추가 ARN**합니다.
9. **버킷 이름**, 현재 파일을 저장 하는 데 사용 하는 버킷에 입력 합니다.
10. **개체 이름**를 선택 **모든**합니다.
11. 선택 **사용 권한을 추가**합니다.

비용 탐색기에 대 한 권한을 구성 합니다.

1. 선택 **서비스를 선택**합니다.
2. 입력 **탐색기 서비스 비용**합니다.
3. 선택 **비용 탐색기의 모든 서비스 작업 (ce:\*)** 합니다. 이 작업 컬렉션 올바른지 확인 합니다.
4. 선택 **사용 권한을 추가**합니다.

AWS 조직에 대 한 권한을 추가 합니다.

1. 입력 **조직에서는**합니다.
2. 선택 **액세스 수준** > **목록** > **ListAccounts**합니다. 이 작업에는 계정의 이름을 가져옵니다.
3. **정책 검토**, 새 정책의 이름을 입력 합니다. 올바른 정보를 입력 했는지 확인 하 고 선택한 **정책 만들기**합니다.
4. 이전 탭으로 다시 이동 하 고 브라우저의 웹 페이지를 새로 고칩니다. 검색 창에서 새 정책을 검색 합니다.
5. 선택 **다음: 검토**합니다.
6. 새 역할에 대 한 이름을 입력 합니다. 올바른 정보를 입력 했는지 확인 하 고 선택한 **Create Role**합니다.

    참고 역할 ARN 및 외부 ID 사용 되는 이전 단계에서 역할을 만들 때. Azure Cost Management connector를 설정 하는 경우 나중에 사용할 수 있습니다.

정책 JSON에는 다음 예제와 유사 합니다. 바꿉니다 _bucketname_ S3 버킷의 이름입니다.

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

다음 정보를 사용 하 여 AWS 커넥터를 만들고 AWS 비용 모니터링 시작:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 로 이동 **비용 관리 + 청구** > **Cost Management**합니다.
3. 아래 **설정을**를 선택 **커넥터 (미리 보기) 클라우드**합니다.  
    ![클라우드 커넥터 (미리 보기)를 보여 주는 예제 설정을)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png)합니다.
4. 선택 **+ 추가** 커넥터 만들기 페이지의 맨 위에 있는 합니다.
5. 에 **AWS 커넥터를 만드는** 페이지에서 **표시 이름**, 커넥터의 이름을 입력 합니다.  
    ![AWS 커넥터를 만들기 위한 페이지의 예](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. 필요에 따라 기본 관리 그룹을 선택 합니다. 검색 된 모든 연결 된 계정에 저장 됩니다. 설정할 수 이상.
7. 에 **청구** 섹션에서 **자동으로 1%에서 일반 공급 요금을 청구** 미리 보기에 만료 되 면 연속 작업을 확인 하려는 경우. 자동 옵션을 선택 하면 청구 구독을 선택 해야 합니다.
8. 에 대 한 **역할 ARN**, AWS에서 역할을 설정할 때 사용 하는 값을 입력 합니다.
9. 에 대 한 **외부 ID**, AWS에서 역할을 설정할 때 사용 하는 값을 입력 합니다.
10. 에 대 한 **보고서 이름**을 AWS에서 생성 된 이름을 입력 합니다.
11. 선택 **다음** 선택한 후 **만들기**합니다.

새 AWS 범위에 대 한 몇 시간이 걸릴 수 있습니다, 그리고 계정 및 AWS 연결 된 계정 및 해당 비용 데이터를 표시 하는 AWS 통합 합니다.

커넥터를 만든 후에 액세스 제어를 할당 하는 것이 좋습니다. 사용자가 새로 검색 된 범위에 권한이 할당 됩니다. AWS 계정과 연결 하는 AWS 계정 통합 합니다. 커넥터를 만드는 사용자에 게는 커넥터, 통합된 계정 및 모든 연결 된 계정 소유자입니다.

검색 실행 후 커넥터 사용 권한을 사용자에 게 할당 기존 AWS 범위에 포함 된 사용 권한을 할당 하지 않습니다. 대신, 새 연결 된 계정에 권한이 할당 됩니다.

## <a name="take-additional-steps"></a>추가 단계

- [관리 그룹 설정](../governance/management-groups/index.md#initial-setup-of-management-groups), 아직 없는 경우.
- 새 범위 범위 선택기에 추가 되어 있는지 확인 합니다. 선택 **새로 고침** 최신 데이터를 볼 수 있습니다.
- 에 **클라우드 커넥터** 페이지, 커넥터를 선택 하 고, 선택 **대금 청구 계정으로 이동** 관리 그룹에 연결 된 계정을 할당 하려면.

## <a name="manage-cloud-connectors"></a>클라우드 커넥터 관리

커넥터를 선택 하는 **커넥터 클라우드** 페이지 수:

- 선택 **대금 청구 계정으로 이동** 는 AWS에 대 한 정보를 보려는 통합 계정.
- 선택 **Access Control** 커넥터에 대 한 역할 할당을 관리 합니다.
- 선택 **편집** 커넥터를 업데이트 합니다. 역할 ARN 표시 되기 때문에 AWS 계정 번호를 변경할 수 없습니다. 하지만 새 커넥터를 만들 수 있습니다.
- 선택 **확인** Cost Management 커넥터 설정을 사용 하 여 데이터를 수집할 수 있는지 확인 테스트를 다시 실행 합니다.

![만든된 AWS 커넥터의 예제 목록](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Azure 관리 그룹 설정

클라우드 간 공급자 정보를 볼 수 있는 단일 위치를 만들려면 Azure 구독 및 연결 하는 AWS 계정에서 같은 관리 그룹에 배치 해야 합니다. 관리 그룹을 사용 하 여 Azure 환경의 구성 하지 않은 경우 [관리 그룹의 설치를 초기](../governance/management-groups/index.md#initial-setup-of-management-groups)합니다.

비용을 분리 하려는 경우에 연결 하는 AWS 계정 보유 하는 관리 그룹을 만들 수 있습니다.

## <a name="set-up-an-aws-consolidated-account"></a>AWS 설정 통합 계정

AWS 통합 계정에 청구 및 지불 여러 AWS 계정에 대 한 결합합니다. AWS 연결 계정의 역할도 수행 합니다.

![예제에서는 자세한 AWS 계정 통합](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

페이지에서 다음을 수행할 수 있습니다.

- 선택 **업데이트** AWS의 연결이 연결 된 관리 그룹을 사용 하 여 계정 일괄 업데이트 합니다.
- 선택 **Access Control** 범위에 대 한 역할 할당을 설정 합니다.

### <a name="permissions-for-an-aws-consolidated-account"></a>AWS에 대 한 권한을 계정 통합

기본적으로 통합 하는 AWS 계정에 대 한 권한은 만들어지면 계정 AWS 커넥터 권한을 기반으로 설정 됩니다. 커넥터 작성자 소유자입니다.

액세스 수준을 사용 하 여 관리 하는 **액세스 수준** 페이지는 AWS의 통합 계정. 그러나 AWS 연결 계정 통합 AWS 계정에 권한을 상속 하지 않습니다.

## <a name="set-up-an-aws-linked-account"></a>연결 하는 AWS 계정 설정

AWS 연결 계정은 AWS 리소스를 만들고 관리 하는 위치입니다. 연결된 된 계정 보안 경계로 서도 작동합니다.

이 페이지에서 다음을 수행할 수 있습니다.

- 선택 **업데이트** AWS의 연결을 업데이트 하려면 관리 그룹 계정과 연결 합니다.
- 선택 **Access Control** 범위에 대 한 역할 할당을 설정 합니다.

![AWS 연결 된 계정 페이지의 예](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>AWS에 대 한 권한을 계정 연결

기본적으로 연결 하는 AWS 계정에 대 한 권한은 만들면 AWS 커넥터 권한을 기반으로 설정 됩니다. 커넥터 작성자 소유자입니다. 액세스 수준을 사용 하 여 관리 하는 **액세스 수준** 는 AWS의 페이지에 계정을 연결 합니다. 연결 하는 AWS 계정 통합 AWS 계정에서 사용 권한을 상속 하지 않습니다.

AWS 계정 연결 항상 자신이 속한 관리 그룹에서 사용 권한을 상속 합니다.

## <a name="next-steps"></a>다음 단계

- 이제는 설정 하 고 AWS 비용 및 사용 현황 보고서 통합을 구성 했으므로, 진행 [AWS 관리 비용 및 사용량](aws-integration-manage.md)합니다.
- 비용 분석을 사용 하 여 잘 모르는 경우 [탐색 및 분석 비용 분석을 사용 하 여 비용](quick-acm-cost-analysis.md) 빠른 시작 합니다.
- Azure의 예산에 익숙하지 참조 [만들기 및 관리 Azure 예산](tutorial-acm-create-budgets.md).
