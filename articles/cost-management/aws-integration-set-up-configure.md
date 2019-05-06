---
title: 설정 하 고 Azure Cost Management를 사용 하 여 AWS 비용 및 사용 현황 보고서 통합 구성
description: 이 문서에서는 설정 하 고 Azure Cost Management를 사용 하 여 AWS 비용 및 사용 현황 보고서 통합을 구성 합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 688bcc02b14d101008afc76662fd6548446cb329
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870284"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>설정 하 고 AWS 비용 및 사용 현황 보고서 통합 구성

Amazon 웹 서비스 비용 및 사용 현황 보고서 통합 기능을 모니터링할 수 있으며 Azure Cost Management에서 AWS 소비를 제어할 수 있습니다. 모니터링할 수 있습니다 하는 Azure portal 및 Azure와 AWS 둘 다에 대 한 지출 한도 컨트롤의 단일 위치를 허용 하는 통합 합니다. 이 문서에는 통합을 설정 하 여 Cost Management 기능 비용을 분석 하 고 예산을 검토를 사용 하 여 구성 하는 방법을 설명 합니다.

Cost Management는 S3 버킷에서 AWS 액세스 자격 증명을 사용 하 여 보고서 정의 가져오고 보고서 GZIP CSV 파일을 다운로드 하 여 저장 된 AWS 비용 및 사용 현황 보고서를 읽습니다.

## <a name="create-a-cost-and-usage-report-in-aws"></a>AWS에서 비용 및 사용 현황 보고서를 만들려면

비용 및 사용 현황 보고서를 사용 하 여 수집 하 고 AWS 비용 처리 권장 AWS입니다. 자세한 내용은 참조는 [AWS 비용 및 사용 현황 보고서](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) 설명서 문서.

사용 된 **보고서** 비용 및 사용 현황 보고서를 만들려면 다음 단계를 사용 하 여 AWS에서 청구 및 비용 관리 콘솔의 페이지입니다.

1. AWS 관리 콘솔에 로그인 하 고에서 청구 및 비용 관리 콘솔을 엽니다 https://console.aws.amazon.com/billing합니다.
2. 탐색 창에서 클릭 **보고서**합니다.
3. 클릭 **보고서를 만들**합니다.
4. 에 대 한 **보고서 이름**, 보고서에 대 한 이름을 입력 합니다.
5. 에 대 한 **시간 단위**, 선택 **시간별**합니다.
6. 에 대 한 **Include**보고서의 각 리소스의 Id를 추가 하 고 선택 **리소스 Id**합니다.
7. 에 대 한 **지원할지**, 선택 영역이 없는 필요 합니다.
8. 에 대 한 **데이터 새로 고침 설정**를 선택 **비용을 자동으로 새로 고침 &amp; 사용 현황 보고서를 사용 하 여 이전 달에 대 한 요금이 감지 되 면 청구서 닫힌**합니다.
9. **다음**을 클릭합니다.
10. 에 대 한 **Amazon S3 버킷의**보고서를 배달 하려는 Amazon S3 버킷의 이름을 입력 하 고 클릭 **확인**합니다. 버킷 유효 하려면 적절 한 권한이 있어야 합니다. 버킷에 권한을 추가 하는 방법에 대 한 자세한 내용은 참조 하세요. [설정은 버킷 및 개체 액세스 권한은](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-permissions.html)합니다.
11. 에 대 한 **보고서 경로 접두사**, 보고서의 이름에 추가 하려는 보고서 경로 접두사를 입력 합니다.
12. 에 대 한 **압축**를 선택 **GZIP**합니다.
13. **다음**을 클릭합니다.
14. 보고서에서 클릭에 대 한 설정을 검토 한 후 **검토 및 완료**합니다.
    참고 합니다 **보고서 이름**합니다. 이후 단계에서 사용 됩니다.

AWS에 Amazon S3 버킷에 보고서를 배달 하려면 최대 24 시간이 걸릴 수 있습니다. 배달 시작 된 후 AWS 업데이트 AWS 비용 및 사용 현황 보고서 파일 하루에 한 번 이상

## <a name="create-a-role-and-policy-in-aws"></a>AWS에서 역할 및 정책 만들기

Azure Cost Management에는 비용 및 사용 현황 보고서가 있는 하루에 여러 번 S3 버킷을 액세스 합니다. Cost Management에는 자격 증명을 새 데이터를 확인 하는 액세스를 해야 합니다. Cost Management를 통해 액세스할 수 있도록 하는 AWS에서 역할 및 정책을 만듭니다.

Azure Cost Management에서 AWS 계정에 역할 기반 액세스를 활성화 하려면 AWS 콘솔에서 역할 생성 됩니다. 해야 합니다 _역할 ARN_ 하 고 _외부 ID_ AWS 콘솔에서. 나중에 사용할 있습니다 만들기에서 Azure Cost Management에서 AWS 커넥터 페이지를 합니다.

1. AWS 콘솔에 로그인 하 고 선택 **Services**합니다.
2. 서비스 목록에서 선택 **IAM**합니다.
3. 선택 **역할** 을 클릭 한 다음 **Create Role**합니다.
4. 다음 페이지에서 선택 **다른 AWS 계정**합니다.
5. **계정 ID** 를 입력 _432263259397_합니다.
6. **옵션**를 선택 **(타사는이 역할을 가정 하는 경우 모범 사례) 외부 ID 필요**합니다.
7. **외부 ID**, 외부 ID 입력 외부 ID는 AWS 역할와 Azure Cost Management 간에 암호를 공유 합니다. Cost Management에서 새 커넥터 페이지에서 동일한 외부 ID도 사용 됩니다. 예를 들어 외부 ID 유사 _Companyname1234567890123_합니다.
    에 대 한 선택을 변경 하지 마세요 **MFA를 요구**합니다. 선택 취소 상태로 유지 됩니다.
8. **다음: 사용 권한**을 클릭합니다.
9. **정책 만들기**를 클릭합니다. 새 브라우저 탭을 새 정책을 만든 것이 열려 있습니다.
10. 클릭 **서비스를 선택**합니다.
11. 형식 **비용 및 사용 현황 보고서**합니다.
12. 선택 **액세스 수준**하십시오 **읽기** > **DescribeReportDefinitions**합니다. 이렇게 하면 Cost Management는 현재 정의 되어 있고 보고서 정의 필수 구성 요소 일치 하는지 확인 보고서 읽기 수 있습니다.
13. 클릭 **사용 권한을 추가**합니다.
14. 클릭 **서비스를 선택**합니다.
15. 형식 _S3_합니다.
16. 선택 **액세스 수준**하십시오 **목록** > **ListBucket**합니다. 이 작업 S3 버킷에서 개체 목록을 가져옵니다.
17. 선택 **액세스 수준**하십시오 **읽기** > **GetObject**합니다. 이 작업을 사용 하면 파일 다운로드를 청구 합니다.
18. 선택 **리소스**합니다.
19. 선택 **버킷 – 추가 ARN**합니다.
20. **버킷 이름**, 현재 파일을 저장 하는 데 사용 하는 버킷에 입력 합니다.
21. 선택 **개체 – 추가 ARN**합니다.
22. **버킷 이름**, 현재 파일을 저장 하는 데 사용 하는 버킷에 입력 합니다.
23. **개체 이름**를 선택 **모든**합니다.
24. 클릭 **사용 권한을 추가**합니다.
25. 클릭 **서비스를 선택**합니다.
26. 형식 _탐색기 서비스 비용_합니다.
27. 선택 **비용 탐색기의 모든 서비스 작업 (ce:\*)** 합니다. 이 작업 컬렉션 올바른지 확인 합니다.
28. 클릭 **사용 권한을 추가**합니다.
29. 형식 **조직에서는**합니다.
30. 선택 **액세스 수준, 목록** > **ListAccounts**합니다. 이 작업에는 계정의 이름을 가져옵니다.
31. **정책 검토**, 새 정책의 이름을 입력 합니다. 올바른 정보를 입력 했는지 확인 한 다음 클릭 확인 **정책 만들기**합니다.
32. 이전 탭으로 돌아가서 브라우저의 웹 페이지를 새로 고칩니다. 검색 창에서 새 정책을 검색 합니다.
33. 선택 **다음: 검토**합니다.
34. 새 역할에 대 한 이름을 입력 합니다. 올바른 정보를 입력 했는지 확인 한 다음 클릭 확인 **Create Role**합니다.
    참고 합니다 **역할 ARN** 하며 **외부 ID** 역할을 만들 때 이전 단계에서 사용 합니다. Azure Cost Management connector를 설정 하는 경우 나중에 사용할가 있습니다.

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

다음 정보를 사용 하 여 새 AWS 커넥터를 만들고 AWS 비용 모니터링을 시작 합니다.

1. Azure portal에 로그인 https://portal.azure.com합니다.
2. 이동할 **비용 관리 + 청구** > **Cost Management**합니다.
3. 아래 **설정을**, 클릭 **커넥터 (미리 보기) 클라우드**합니다.  
    ![클라우드 커넥터 (미리 보기 설정)를 보여 주는 예제](./media/aws-integration-setup-configure/cloud-connectors-preview01.png)합니다.
4. 클릭 **+ 추가** 새 커넥터 만들기 페이지의 맨 위에 있는 합니다.
5. AWS 커넥터 페이지를 만들기, 입력을 **표시 이름** 커넥터 이름을 지정 합니다.  
    ![만들기는 AWS 커넥터 페이지의 예](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. 필요에 따라 기본 관리 그룹을 선택 합니다. 검색 된 모든 연결 된 계정에 저장 됩니다. 설정할 수 이상.
7. 아래는 **청구** 섹션에서 **자동으로 1%에서 일반 공급 요금을 청구** 미리 보기에 만료 되 면 연속 작업을 확인 하려는 경우. 자동 옵션을 선택 하는 경우 대금 청구 선택 해야 합니다 **구독**합니다.
8. 입력 된 **역할 ARN**합니다. AWS에서 역할을 설정 하는 경우는 값입니다.
9. 입력 된 **외부 ID**합니다. AWS에서 역할을 설정 하는 경우는 값입니다.
10. 입력 된 **보고서 이름** AWS에서 생성 합니다.
11. 클릭 **다음** 을 클릭 한 다음 **만들기**합니다.

표시할 새 AWS 범위, AWS 통합 계정 및 AWS 연결 된 계정 및 비용 데이터는 몇 시간이 걸릴 수 있습니다.

커넥터를 만든 후 커넥터에 대 한 액세스 제어를 지정 하는 것이 좋습니다. 사용자가 새로 검색 된 범위에 권한이 할당 됩니다. AWS 계정 및 AWS 통합 계정을 연결 합니다. 커넥터를 만드는 사용자는 커넥터, 통합된 계정 및 연결 된 모든 계정 소유자를입니다.

검색 실행 후 커넥터 사용 권한을 사용자에 게 할당 기존 AWS 범위에 포함 된 사용 권한을 할당 하지 않습니다. 대신, 새 연결 된 계정에 권한이 할당 됩니다.

## <a name="additional-steps"></a>추가 단계

- [관리 그룹 설정](../governance/management-groups/index.md#initial-setup-of-management-groups), 아직 없는 경우.
- 새 범위 범위 선택기에 추가 되어 있는지 확인 합니다. 클릭 잊지 **새로 고침** 최신 데이터를 볼 수 있습니다.
- 클라우드 커넥터 페이지에서 커넥터를 선택 하 고 클릭 **대금 청구 계정으로 이동** 에 연결 된 관리 그룹에 할당 합니다.

## <a name="manage-cloud-connectors"></a>클라우드 커넥터 관리

클라우드 커넥터 페이지에서 커넥터를 선택 하면 다음 작업을 수행할 수 있습니다.

- 클릭 **대금 청구 계정으로 이동** AWS 통합 계정 정보를 볼 수 있습니다.
- 클릭 **Access Control** 커넥터에 대 한 역할 할당을 관리 합니다.
- 클릭 **편집** 커넥터를 업데이트 합니다. 역할 ARN 나타나는 AWS 계정 번호를 변경할 수 없습니다. 그러나 새 커넥터를 만들 수 있습니다.
- 클릭 **확인** Cost Management가 커넥터 설정을 사용 하 여 데이터를 수집할 수 있는지 확인 테스트를 다시 실행 합니다.

![만든된 AWS 커넥터 목록을 보여 주는 예제](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="role-of-azure-management-groups"></a>Azure 관리 그룹의 역할

클라우드 간 공급자 정보를 볼 수 있는 단일 위치를 만들려면 Azure 구독 및 연결 하는 AWS 계정에서 같은 관리 그룹에 배치 해야 합니다. 관리 그룹을 사용 하 여 Azure 환경의 구성 하지 않은 경우 [관리 그룹의 설치를 초기](../governance/management-groups/index.md#initial-setup-of-management-groups)합니다.

비용을 분리 하려는 경우에 연결 하는 AWS 계정 보유 하는 관리 그룹을 만들 수 있습니다.

## <a name="aws-consolidated-account"></a>AWS 통합 계정

AWS 통합 계정 청구 및 지불 여러 AWS 계정에 대 한 통합 됩니다. AWS 연결 된 계정으로 AWS 통합 계정의 역할도 합니다.

![AWS 통합 계정 세부 정보의 예제](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

페이지에서 다음을 수행할 수 있습니다.

- 클릭 **업데이트** 대량 업데이트 관리 그룹을 사용 하 여 AWS 연결 된 계정 연결 합니다.
- 클릭 **Access Control** 범위에 대 한 역할 할당을 설정 합니다.

### <a name="aws-consolidated-account-permissions"></a>AWS 통합 계정 사용 권한

기본적으로 AWS 계정 AWS 커넥터 권한을 기반으로 작성 시 사용 권한이 설정 되어를 통합 합니다. 커넥터 작성자 소유자입니다.

AWS 통합 계정의 액세스 수준 페이지를 사용 하 여 액세스 수준 관리. 그러나는 AWS 권한을 통합 계정 연결 AWS 계정에서 상속 되지 않습니다.

## <a name="aws-linked-account"></a>AWS 연결 계정

AWS 연결 AWS 리소스를 만들고 관리 하는 위치입니다. 연결된 된 계정 보안 경계로 서도 작동합니다.

이 페이지에서 다음을 수행할 수 있습니다.

- 클릭 **업데이트** 관리 그룹을 사용 하 여 연결 된 AWS 계정 연결을 업데이트 합니다.
- 클릭 **Access Control** 범위에 대 한 역할 할당을 설정 합니다.

![AWS 연결 된 계정 페이지의 예](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="aws-linked-account-permissions"></a>AWS 연결 된 계정 권한

기본적으로 만들면 AWS 커넥터 권한을 기반으로 AWS 연결 된 계정 권한 설정 됩니다. 커넥터 작성자 소유자입니다. AWS 연결 된 계정의 액세스 수준 페이지를 사용 하 여 액세스 수준 관리. AWS 연결 된 계정으로 AWS 통합 계정에 대 한 사용 권한은 상속 되지 않습니다.

AWS 연결 된 계정은 항상 자신이 속한 관리 그룹에서 권한을 상속 합니다.

## <a name="next-steps"></a>다음 단계

- 이제는 설정 하 고 AWS 비용 및 사용 현황 보고서 통합을 구성 했으므로, 진행 [AWS 관리 비용 및 사용량](aws-integration-manage.md)합니다.
- 비용 분석을 사용 하 여 잘 모르는 경우 [탐색 및 분석 비용 분석을 사용 하 여 비용](quick-acm-cost-analysis.md) 빠른 시작 합니다.
- Azure의 예산에 익숙하지 참조 [만들기 및 관리 Azure 예산](tutorial-acm-create-budgets.md) 자습서.
