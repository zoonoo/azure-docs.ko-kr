---
title: Azure Cost Management에 Amazon Web Services 계정 연결 | Microsoft Docs
description: Cost Management 보고서에서 비용 및 사용 현황 데이터를 보려면 Amazon Web Services 계정을 연결합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/08/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 4a0280420132aad9f1e0b17d5998ec225bb0eaa1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Amazon Web Services 계정 연결

AWS(Amazon Web Services)를 Azure Cost Management에 연결하는 옵션은 두 가지입니다. IAM 역할 또는 읽기 전용 IAM 사용자 계정으로 연결할 수 있습니다. 정의된 권한이 포함된 액세스 권한을 신뢰할 수 있는 엔터티에 위임할 수 있으므로 IAM 역할을 사용하는 것이 좋습니다. IAM 역할은 장기 액세스 키를 공유하지 않아도 됩니다. AWS 계정을 Cost Management에 연결하면 Cost Management 보고서에서 비용 및 사용 현황 데이터를 볼 수 있습니다. 이 문서에서는 두 가지 옵션을 모두 안내합니다.

AWS IAM ID에 대한 자세한 내용은 [ID(사용자, 그룹 및 역할)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)를 참조하세요.

## <a name="aws-role-based-access"></a>AWS 역할 기반 액세스

다음 섹션에서는 읽기 전용 IAM 역할을 만들어서 Cost Management에 대한 액세스 권한을 제공하는 과정을 안내합니다.

### <a name="get-your-cost-management-account-external-id"></a>Cost Management 계정 외부 ID 확보하기

첫 번째 단계는 Azure Cost Management 포털에서 고유한 연결 암호를 확보하는 것입니다. AWS에서 **외부 ID**로 사용됩니다.

1. Azure Portal에서 Cloudyn 포털을 열거나 [https://azure.cloudyn.com](https://azure.cloudyn.com)으로 이동하여 로그인합니다.
2. **설정**(톱니바퀴 아이콘)을 클릭한 다음, **클라우드 계정**을 선택합니다.
3. 계정 관리에서 **AWS 계정** 탭을 클릭한 다음, **새로 추가 +**를 클릭합니다.
4. **Add AWS Account**(AWS 계정 추가) 대화 상자에서 **외부 ID**를 복사하여 다음 섹션의 AWS 역할 만들기 단계를 위해 저장합니다. 다음 이미지에서 예제 ID는 _Cloudyn_입니다. 사용자의 ID는 다릅니다.  
    ![외부 ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>AWS 읽기 전용 역할 기반 액세스 추가

1. https://console.aws.amazon.com/iam/home에서 AWS 콘솔에 로그인하고 **역할**을 선택합니다.
2. **역할 만들기**를 클릭한 다음, **Another AWS account**(다른 AWS 계정)을 선택합니다.
3. **계정 ID** 필드에 ID `432263259397`을 붙여 넣습니다. 이 계정 ID는 반드시 사용해야 하는 Cost Management 데이터 수집기입니다.
4. **옵션** 옆의 **외부 ID 필요**를 선택한 다음, **외부 ID** 필드에서 이전에 복사한 값을 붙여 넣은 후 **Next: Permissions**(다음: 권한)를 클릭합니다.  
    ![역할 만들기](./media/connect-aws-account/create-role01.png)
5. **Attach permissions policies**(권한 연결 정책) 아래 **정책 유형** 필터 상자 검색에 `ReadOnlyAccess`를 입력하고, **ReadOnlyAccess**를 선택한 후 **다음: 검토**를 클릭합니다.  
    ![읽기 전용 액세스](./media/connect-aws-account/readonlyaccess.png)
6. 검토 페이지에서 선택 내용이 올바른지 확인하고 **역할 이름**을 입력합니다. 예: *Azure-Cost-Mgt*. **역할 설명**을 입력합니다. 예를 들어 _Azure Cost Management를 위한 역할 할당_을 입력한 다음, **역할 만들기**를 클릭합니다.
7. 만들어진 역할을 **역할** 목록에서 클릭하고 요약 페이지에서 **역할 ARN** 값을 복사합니다. 역할 ARN 값은 나중에 Azure Cost Management에서 구성을 등록할 때 사용합니다.  
    ![역할 ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Cost Management에서 AWS IAM 역할 액세스 구성

1. Azure Portal에서 Cloudyn 포털을 열거나 https://azure.cloudyn.com/으로 이동하여 로그인합니다.
2. **설정**(톱니바퀴 아이콘)을 클릭한 다음, **클라우드 계정**을 선택합니다.
3. 계정 관리에서 **AWS 계정** 탭을 클릭한 다음, **새로 추가 +**를 클릭합니다.
4. **계정 이름**에 계정의 이름을 입력합니다.
5. **액세스 유형** 옆에서 **IAM 역할**을 선택합니다.
6. **역할 ARN** 필드에 이전에 복사한 값을 붙여 넣은 다음, **저장**을 클릭합니다.  
    ![AWS 계정 상태](./media/connect-aws-account/aws-account-status01.png)

AWS 계정은 계정 목록에 나타납니다. 나열된 **소유자 ID**는 역할 ARN 값과 일치합니다. **계정 상태**에는 녹색 확인 표시 기호가 있어야 합니다.

Cost Management에서 데이터를 수집하고 보고서를 채우기 시작합니다. 하지만 정확한 권장 사항이 생성되기 전에 일부 최적화 보고서에는 몇 일 동안의 데이터가 필요할 수도 있습니다.

## <a name="aws-user-based-access"></a>AWS 사용자 기반 액세스

다음 섹션에서는 읽기 전용 사용자를 만들어서 Cost Management에 대한 액세스 권한을 제공하는 과정을 안내합니다.

### <a name="add-aws-read-only-user-based-access"></a>AWS 읽기 전용 사용자 기반 액세스 추가

1. https://console.aws.amazon.com/iam/home에서 AWS 콘솔에 로그인하고 **사용자**를 선택합니다.
2. **사용자 추가**를 클릭합니다.
3. **사용자 이름** 필드에 사용자 이름을 입력합니다.
4. **액세스 유형**에 **프로그래밍 방식 액세스**를 선택하고 **Next: Permissions**(다음: 권한)을 클릭합니다.  
    ![사용자 추가](./media/connect-aws-account/add-user01.png)
5. 권한에 대해 **Attach existing policies directly**(기존 정책 직접 연결)을 선택합니다.
6. **Attach permissions policies**(권한 연결 정책) 아래 **정책 유형** 필터 상자 검색에 `ReadOnlyAccess`를 입력하고, **ReadOnlyAccess**를 선택한 후 **다음: 검토**를 클릭합니다.  
    ![사용자에 대한 권한 설정](./media/connect-aws-account/set-permission-for-user.png)
7. 검토 페이지에서 선택 내용이 올바른지 확인한 다음, **사용자 만들기**를 입력합니다.
8. 완료 페이지에 액세스 키 ID 및 비밀 액세스 키가 표시됩니다. 이 정보를 사용하여 Cost Management에서 등록을 구성합니다.
9. **Download .csv**를 클릭하고 credentials.csv 파일을 안전한 위치에 저장합니다.  
    ![자격 증명 다운로드](./media/connect-aws-account/download-csv.png)


### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>Cost Management에서 AWS IAM 사용자 기반 액세스 구성

1. Azure Portal에서 Cloudyn 포털을 열거나 https://azure.cloudyn.com/으로 이동하여 로그인합니다.
2. **설정**(톱니바퀴 아이콘)을 클릭한 다음, **클라우드 계정**을 선택합니다.
3. 계정 관리에서 **AWS 계정** 탭을 클릭한 다음, **새로 추가 +**를 클릭합니다.
4. **계정 이름**에 계정 이름을 입력합니다.
5. **액세스 유형** 옆에서 **IAM 사용자**를 선택합니다.
6. **액세스 키**에 credentials.csv 파일의 **액세스 키 ID** 값을 붙여넣습니다.
7. **비밀 키**에 credentials.csv 파일의 **비밀 액세스 키** 값을 붙여넣은 다음, **저장**을 클릭합니다.  
    ![AWS 사용자 계정 상태](./media/connect-aws-account/aws-user-account-status.png)

AWS 계정은 계정 목록에 나타납니다. **계정 상태**에는 녹색 확인 표시 기호가 있어야 합니다.

Cost Management에서 데이터를 수집하고 보고서를 채우기 시작합니다. 하지만 정확한 권장 사항이 생성되기 전에 일부 최적화 보고서에는 몇 일 동안의 데이터가 필요할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Cost Management에 대한 자세한 내용은 Cost Management에 대한 [사용량 및 비용 검토](tutorial-review-usage.md) 자습서를 참조하세요.
