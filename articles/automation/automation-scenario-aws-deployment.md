---
title: Amazon Web Services에서 VM 배포 자동화
description: 이 문서에서는 Azure Automation을 사용하여 Amazon Web Service VM 만들기를 자동화하는 방법을 보여 줍니다.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604838"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation 시나리오 - AWS 가상 머신 프로비전
이 문서에서는 Azure Automation을 사용하여 AWS(Amazon Web Service) 구독에서 가상 머신을 프로비전하고 해당 VM에 특정 이름을 지정하는 방법을 설명합니다. 이는 AWS에서 VM “태그 지정”이라고 합니다.

## <a name="prerequisites"></a>전제 조건
Azure Automation 계정 및 Amazon Web Services (AWS) 구독이 있어야 합니다. Azure Automation 계정을 설정하고 AWS 구독 자격 증명으로 구성하는 데 대한 자세한 내용은 [Configure Authentication with Amazon Web Services(Amazon Web Services로 인증 구성)](automation-config-aws-account.md)를 참조하세요. 아래 섹션에서이 계정을 참조 하므로 계속 하기 전에 AWS 구독 자격 증명으로이 계정을 만들거나 업데이트 해야 합니다.

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services PowerShell 모듈 배포
VM 프로 비전 runbook은 AWS PowerShell 모듈을 사용 하 여 작업을 수행 합니다. AWS 구독 자격 증명으로 구성 된 Automation 계정에 모듈을 추가 하려면 다음 단계를 사용 합니다.  

1. 웹 브라우저를 열고 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AWSPowerShell/)로 이동하여 **Azure Automation에 배포** 단추를 클릭합니다.<br><br> ![AWS PS 모듈 가져오기](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Azure 로그인 페이지가 열리고 인증되면 Azure Portal로 이동하여 다음 페이지가 표시됩니다.<br><br> ![모듈 가져오기 페이지](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. 사용할 Automation 계정을 선택 하 고 **확인** 을 클릭 하 여 배포를 시작 합니다.

   > [!NOTE]
   > Azure Automation PowerShell 모듈을 가져올 때 cmdlet을 추출 합니다. Automation에서 모듈 가져오기 및 cmdlet 추출이 완전히 완료 될 때까지 활동이 표시 되지 않습니다. 이 프로세스는 몇 분 정도 걸릴 수 있습니다.  
   > <br>

1. Azure Portal에서 Automation 계정을 엽니다.
2. **자산** 타일을 클릭 하 고 자산 창에서 **모듈**을 선택 합니다.
3. 모듈 페이지의 목록에 **AWSPowerShell** 모듈이 표시됩니다.

## <a name="create-aws-deploy-vm-runbook"></a>AWS 배포 VM Runbook 만들기
AWS PowerShell 모듈을 배포한 후에는 Runbook을 작성하여 PowerShell 스크립트를 통해 AWS에서 가상 머신을 프로비전하는 작업을 자동화할 수 있습니다. 아래 단계에서는 Azure Automation에서 네이티브 PowerShell 스크립트를 사용 하는 방법을 보여 줍니다.  

> [!NOTE]
> 이 스크립트와 관련된 추가 옵션 및 정보를 보려면 [PowerShell 갤러리](https://www.powershellgallery.com/packages/New-AwsVM/)를 방문하세요.
> 

1. PowerShell 세션을 열고 다음 명령을 입력 하 여 PowerShell 갤러리에서 PowerShell 스크립트 새-AwsVM을 다운로드 합니다.<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Azure Portal에서 Automation 계정을 열고 **프로세스 자동화**아래에서 **runbook** 을 선택 합니다.  
3. Runbook 페이지에서 **Runbook 추가**를 선택합니다.
4. Runbook 추가 창에서 **빨리 만들기** 를 선택 하 여 새 runbook을 만듭니다.
5. Runbook 속성 창에서 runbook의 이름을 입력 합니다.
6. **Runbook 유형** 드롭다운 목록에서 **PowerShell**을 선택 하 고 **만들기**를 클릭 합니다.<br><br> ![Runbook 만들기 창](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. PowerShell Runbook 편집 페이지가 나타나면 PowerShell 스크립트를 복사하여 Runbook 작성 캔버스에 붙여넣습니다.<br><br> ![Runbook PowerShell 스크립트](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > 예제 PowerShell 스크립트 작업을 할 때는 다음 사항에 주의하세요.
    > 
    > * Runbook에는 많은 기본 매개 변수 값이 포함되어 있습니다. 모든 기본 값을 평가하고 필요한 경우 업데이트합니다.
    > * AWS 자격 증명을와 다른 `AWScred`자격 증명 자산으로 저장 한 경우에는 줄 57의 스크립트를 업데이트 하 여 적절 하 게 일치 해야 합니다.  
    > * PowerShell에서 AWS CLI 명령을 사용하여 작업할 때 특히 이 예제 Runbook에서는 AWS 영역을 지정해야 합니다. 그러지 않으면 cmdlet이 실패합니다. 자세한 내용은 PowerShell용 AWS 도구 설명서의 AWS 항목 [AWS 영역 지정](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) 을 참조하세요.  
    >

7. AWS 구독에서 이미지 이름 목록을 검색하려면 PowerShell ISE를 시작하고 AWS PowerShell 모듈을 가져옵니다. ISE 환경을로 `Get-AutomationPSCredential` `AWScred = Get-Credential`바꿔서 AWS에 대해 인증 합니다. 이 문은 자격 증명을 묻는 메시지를 표시 하 고 사용자 이름 및 암호에 대 한 암호 액세스 키에 대 한 액세스 키 ID를 제공할 수 있습니다. 

        ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
        ```
        
    다음과 같은 출력이 반환됩니다.<br><br>
   ![AWS 이미지 가져오기](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Runbook에서 참조 되는 것 처럼 자동화 변수에 이미지 이름 중 하나를 복사 하 여 붙여넣습니다 `$InstanceType`. 이 예제에서는 무료 AWS 계층화 된 구독을 사용 하므로 runbook 예제 **에 대해** 를 사용 합니다.  
9. Runbook을 저장하고 **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.

### <a name="testing-the-aws-vm-runbook"></a>AWS VM Runbook 테스트
Runbook 테스트를 진행 하기 전에 다음 몇 가지 사항을 확인 해야 합니다.

* AWS에 대 `AWScred` 한 인증을 위해 호출 된 자산이 만들어지거나 자격 증명 자산 이름을 참조 하도록 스크립트가 업데이트 되었습니다.    
* Azure Automation에서 AWS PowerShell 모듈을 가져왔습니다.  
* 새 runbook을 만들고 매개 변수 값을 확인 하 고 필요한 경우 업데이트 합니다.  
* **자세한 정보 표시 레코드를 기록** 하 고 Runbook 작업 **로깅 및 추적** 에서 **진행률 레코드를 기록** 합니다 ( **On**으로 설정 됨).<br><br> ![Runbook 로깅 및 추적](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png).  

1. **시작** 을 클릭 하 여 runbook을 시작 하 고 runbook 시작 창이 열리면 **확인** 을 클릭 합니다.
2. Runbook 시작 창에서 VM 이름을 제공 합니다. 스크립트에 미리 구성 된 다른 매개 변수에 대 한 기본값을 적용 합니다. **확인** 을 클릭 하 여 runbook 작업을 시작 합니다.<br><br> ![New-AwsVM Runbook 시작](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. 만든 Runbook 작업에 대한 작업 창이 열립니다. 이 창을 닫습니다.
4. Runbook 작업 창에서 **모든 로그** 를 선택 하 여 작업의 진행 상황을 확인 하 고 출력 스트림을 볼 수 있습니다.<br><br> ![스트림 출력](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. VM을 프로 비전 하 고 있는지 확인 하려면 AWS 관리 콘솔에 로그인 합니다 (현재 로그인 하지 않은 경우).<br><br> ![AWS 콘솔에서 VM 배포](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>다음 단계
* 그래픽 runbook을 시작 하려면 [내 첫 번째 그래픽 runbook](automation-first-runbook-graphical.md)을 참조 하세요.
* PowerShell 워크플로 runbook을 시작 하려면 [내 첫 번째 powershell 워크플로 runbook](automation-first-runbook-textual.md)을 참조 하세요.
* Runbook의 형식, 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 형식](automation-runbook-types.md)을 참조하세요.
* PowerShell 스크립트 지원 기능에 대 한 자세한 내용은 [Azure Automation의 네이티브 PowerShell 스크립트 지원](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)을 참조 하세요.