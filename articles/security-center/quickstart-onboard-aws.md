---
title: Azure Security Center에 AWS 계정 연결
description: Azure Security Center에서 AWS 리소스 모니터링
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 33ff6748eba9d0349df244e70f07ad178f258373
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250287"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Azure Security Center에 AWS 계정 연결

클라우드 워크 로드가 일반적으로 여러 클라우드 플랫폼을 사용 하는 경우 클라우드 보안 서비스는 동일한 작업을 수행 해야 합니다.

Azure Security Center는 Azure, Amazon Web Services (AWS) 및 Google Cloud Platform (GCP)에서 워크 로드를 보호 합니다.

AWS 계정을 Security Center에 등록 하면 AWS Security Hub와 Azure Security Center를 통합 합니다. 따라서 Security Center은 다음을 제공 하기 위해 이러한 클라우드 환경 모두에서 가시성과 보호를 제공 합니다.

- 자동 에이전트 프로 비전 (Security Center [Azure Arc](../azure-arc/servers/overview.md) 를 사용 하 여 AWS 인스턴스에 Log Analytics 에이전트를 배포)
- 정책 관리
- 취약점 관리
- 임베디드 끝점 검색 및 응답 (EDR)
- 보안 오류 감지
- Security Center 권장 사항 및 AWS 보안 허브 검색 결과를 보여 주는 단일 뷰입니다.
- Security Center의 보안 점수 계산에 AWS 리소스 통합
- AWS 리소스에 대 한 규정 준수 평가

아래 스크린샷에서 Security Center의 개요 대시보드에 표시 되는 AWS 계정을 볼 수 있습니다.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Security Center 개요 대시보드에 나열 된 GCP 프로젝트 3 개" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|미리 보기|
|결정|[서버용 Azure Defender](defender-for-servers-introduction.md) 가 필요 합니다.|
|필요한 역할 및 사용 권한:|관련 Azure 구독에 대 한 **소유자** 또는 **참가자**|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/no-icon.png) 국가/소 버린 (US Gov, 중국 .Gov, 기타 .Gov)|
|||



## <a name="connect-your-aws-account"></a>AWS 계정 연결

### <a name="step-1-set-up-aws-security-hub"></a>1단계. AWS 보안 허브 설정:

1. 여러 영역에 대 한 보안 권장 사항을 보려면 관련 된 각 지역에 대해 다음 단계를 반복 합니다.

    > [!IMPORTANT]
    > AWS 마스터 계정을 사용 하는 경우 다음 세 단계를 반복 하 여 모든 관련 지역에서 마스터 계정 및 모든 연결 된 구성원 계정을 구성 합니다.

    1. [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)를 사용 하도록 설정 합니다.
    1. [AWS 보안 허브](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html)를 사용 하도록 설정 합니다.
    1. 보안 허브로 흐르는 데이터가 있는지 확인 합니다.

        처음으로 보안 허브를 사용 하도록 설정 하는 경우 데이터를 사용 하는 데 몇 시간이 걸릴 수 있습니다.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>2단계. AWS의 Security Center에 대 한 인증 설정

Security Center에서 AWS에 인증 하도록 허용 하는 방법에는 두 가지가 있습니다.

- **Security Center에 대 한 IAM 역할을 만듭니다** . 가장 안전한 방법 이며 권장 됩니다.
- **AWS user for Security Center** -IAM을 사용 하도록 설정 하지 않은 경우 보안 수준 낮음 옵션

#### <a name="create-an-iam-role-for-security-center"></a>Security Center에 대 한 IAM 역할 만들기
1. Amazon Web Services 콘솔의 **보안, id & 준수**에서 **IAM**을 선택 합니다.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS 서비스":::

1. **역할** 을 선택 하 고 **역할을 만듭니다**.
1. **다른 AWS 계정을**선택 합니다.
1. 다음 세부 정보를 입력합니다.

    - **계정 id** -SECURITY CENTER의 AWS 커넥터 페이지에 표시 된 대로 MICROSOFT 계정 id (**158177204117**)를 입력 합니다.
    - **외부 ID 필요** -선택 해야 합니다.
    - **외부 ID** -AWS 커넥터 페이지에 표시 된 것 처럼 구독 ID를 입력 Security Center 

1. **다음**을 선택합니다.
1. **권한 정책 연결** 섹션에서 다음 정책을 선택 합니다.

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. 필요에 따라 태그를 추가 합니다. 사용자에 게 태그를 추가 하면 연결에 영향을 주지 않습니다.
1. **다음**을 선택합니다.

1. 역할 목록에서 만든 역할을 선택 합니다.

1. 나중에에 대 한 ARN (Amazon Resource Name)을 저장 합니다. 

#### <a name="create-an-aws-user-for-security-center"></a>Security Center에 대 한 AWS 사용자 만들기 
1. **사용자** 탭을 열고 **사용자 추가**를 선택 합니다.
1. **세부 정보** 단계에서 Security Center에 대 한 사용자 이름을 입력 하 고 AWS access 형식에 대 한 **프로그래밍 방식 액세스** 를 선택 해야 합니다. 
1. **Next Permissions**(다음: 권한)를 클릭합니다.
1. **기존 정책 직접 연결** 을 선택 하 고 다음 정책을 적용 합니다.
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. 완료되면 **다음: 태그**를 선택합니다. 필요에 따라 태그를 추가 합니다. 사용자에 게 태그를 추가 하면 연결에 영향을 주지 않습니다.
1. **검토**를 선택 합니다.
1. 나중에 사용 하기 위해 자동으로 생성 된 **액세스 키 ID** 및 **암호 액세스 키** CSV 파일을 저장 합니다.
1. 요약을 검토 하 고 **사용자 만들기**를 클릭 합니다.


### <a name="step-3-configure-the-ssm-agent"></a>3단계. SSM 에이전트 구성

AWS Systems Manager는 AWS 리소스에서 작업을 자동화 하는 데 필요 합니다. EC2 인스턴스에 SSM 에이전트가 없는 경우 Amazon에서 관련 된 지침을 따르세요.

- [Windows 인스턴스에 SSM Agent 설치 및 구성](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Amazon EC2 Linux 인스턴스에서 SSM Agent 설치 및 구성](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-create-a-service-principal-for-onboarding-at-scale"></a>4단계. 대규모 온보딩을 위한 서비스 주체 만들기

온 보 딩에 사용할 구독에 대 한 **소유자** 는 [대규모로 온 보 딩에 대 한 서비스 주체 만들기](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) 에 설명 된 대로 Azure Arc 온 보 딩에 대 한 서비스 주체를 만듭니다.


### <a name="step-5-connect-aws-to-security-center"></a>5단계. Security Center에 AWS 연결

1. Security Center의 메뉴에서 **다중 클라우드 커넥터**를 선택 합니다.
1. **AWS 계정 추가**를 선택 합니다.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Security Center의 다중 클라우드 커넥터 페이지에서 AWS 계정 추가 단추":::
1. **AWS 인증** 탭에서 옵션을 구성 합니다.
    1. 커넥터에 대 한 **표시 이름을** 입력 합니다.
    1. 구독이 올바른지 확인 합니다. 커넥터 및 AWS 보안 허브 권장 사항을 포함 하는 구독입니다.
    1. 2 단계에서 선택한 인증 옵션에 따라 결정 [됩니다. AWS의 Security Center에 대 한 인증을 설정 합니다](#step-2-set-up-authentication-for-security-center-in-aws).
        - :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Azure Portal에서 AWS 연결 마법사의 관련 필드에 arn 파일을 붙여 넣는"::: [Security Center에 대 한 IAM 역할 만들기](#create-an-iam-role-for-security-center) **를 선택 하** 고 arn을 붙여넣습니다.

            또는

        - **자격 증명** 을 선택 하 고 [AWS 사용자 만들기](#create-an-aws-user-for-security-center)에서 저장 한 .csv 파일의 **액세스 키** 및 **비밀 키** 를 Security Center에 붙여넣습니다.
1. **다음**을 선택합니다.
1. **Azure Arc 구성** 탭에서 옵션을 구성 합니다.

    Security Center는 연결 된 AWS 계정에서 EC2 인스턴스를 검색 하 고 SSM를 사용 하 여 Azure Arc에 등록 합니다. 

    > [!TIP]
    > 지원 되는 운영 체제 목록은 아래 FAQ에 나와 있습니다.

    1. 선택한 구독에서 검색 된 AWS EC2s가 등록 되는 **리소스 그룹** 및 **Azure 지역을** 선택 합니다.
    1. 여기에 설명 된 대로 Azure Arc의 서비스 사용자 **ID** 및 **서비스 주체 클라이언트 암호** 를 입력 합니다. 여기에 설명 된 대로 [온 보 딩에 대 한 서비스 주체 만들기](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. 컴퓨터에서 프록시 서버를 통해 인터넷에 연결 하는 경우 프록시 서버 IP 주소 또는 컴퓨터에서 프록시 서버와 통신 하는 데 사용 하는 이름 및 포트 번호를 지정 합니다. 형식으로 값을 입력 합니다. ```http://<proxyURL>:<proxyport>```
    1. **검토 + 만들기**를 선택합니다.

        요약 정보 검토

        태그 섹션에는 Azure에서 쉽게 인식할 수 있도록 각 등록 EC2에 대해 자동으로 생성 되는 모든 Azure 태그가 나열 됩니다. 

        Azure [리소스 및 관리 계층 구조를 구성 하기 위해 사용 중인 태그](../azure-resource-manager/management/tag-resources.md)에서 azure 태그에 대해 자세히 알아보세요.

### <a name="step-7-confirmation"></a>7단계. 확인

커넥터가 성공적으로 만들어지고 AWS 보안 허브가 올바르게 구성 된 경우:

- Security Center는 환경을 검색 하 여 Azure Arc에 온 보 딩 하 고, Log Analytics 에이전트를 설치 하 고, 위협 방지 및 보안 권장 사항을 제공 합니다. 
- ASC 서비스는 6 시간 마다 새로운 AWS EC2 인스턴스를 검색 하 고 구성에 따라 등록 합니다.
- AWS CIS 표준은 Security Center의 규정 준수 대시보드에 표시 됩니다.
- 보안 허브 정책을 사용 하도록 설정 하면 Security Center 포털에 권장 사항이 표시 되 고, 등록을 완료 한 후 규정 준수 대시보드 5-10 분이 표시 됩니다.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Security Center의 권장 사항 페이지에서 리소스 및 권장 사항 AWS":::



## <a name="monitoring-your-aws-resources"></a>AWS 리소스 모니터링

위와 같이 Azure Security Center의 보안 권장 사항 페이지에는 진정한 다중 클라우드 보기를 위해 Azure 및 GCP 리소스와 함께 AWS 리소스가 표시 됩니다.

리소스 유형별로 리소스에 대 한 모든 활성 권장 사항을 보려면 Security Center의 자산 인벤토리 페이지를 사용 하 여 관심 있는 AWS 리소스 종류를 필터링 합니다.

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="AWS 옵션을 보여 주는 자산 인벤토리 페이지의 리소스 종류 필터"::: 


## <a name="aws-in-security-center-faq"></a>Security Center FAQ의 AWS

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>EC2 인스턴스에 사용할 수 있는 운영 체제는 무엇 인가요?

AWS 컴퓨터용 Azure Arc 자동 등록을 지 원하는 OS

- Ubuntu 16.04-SSM 에이전트는 기본적으로 미리 설치 되어 있습니다.
- Ubuntu 18.04-SSM 에이전트는 기본적으로 미리 설치 되어 있습니다.
- Windows server-SSM 에이전트는 기본적으로 미리 설치 되어 있습니다.
- CentOS Linux 7 – SSM를 수동으로 설치 하거나 별도로 등록 해야 함
- SUSE Linux Enterprise Server (SLES) 15 (x64)-SSM를 수동으로 설치 하거나 별도로 등록 해야 합니다.
- Red Hat Enterprise Linux (RHEL) 7 (x64)-SSM를 수동으로 설치 하거나 별도로 등록 해야 합니다.


## <a name="next-steps"></a>다음 단계

AWS 계정을 연결 하는 것은 Azure Security Center에서 사용할 수 있는 다중 클라우드 환경의 일부입니다. 관련 정보는 다음 페이지를 참조 하세요.

- [Azure Security Center에 GCP 계정 연결](quickstart-onboard-gcp.md)
