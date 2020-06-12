---
title: Azure Automation Runbook을 사용하여 Amazon Web Services VM 배포
description: 이 문서에서는 Amazon Web Services VM 만들기를 자동화하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 4dcc095648111348a6935225a6aa10798109f76e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832235"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>Runbook을 사용하여 Amazon Web Services VM 배포

이 문서에서는 Azure Automation을 사용하여 AWS(Amazon Web Service) 구독에서 가상 머신을 프로비전하고 해당 VM에 특정 이름을 지정하는 방법을 설명합니다. 이는 AWS에서 VM “태그 지정”이라고 합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Automation 계정 및 AWS(Amazon Web Services) 구독이 있어야 합니다. Azure Automation 계정을 설정하고 AWS 구독 자격 증명으로 구성하는 데 대한 자세한 내용은 [Configure Authentication with Amazon Web Services(Amazon Web Services로 인증 구성)](automation-config-aws-account.md)를 참조하세요. 이 계정은 아래 섹션에서 참조되므로 먼저 AWS 구독 자격 증명으로 생성하거나 업데이트한 다음 진행해야 합니다.

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services PowerShell 모듈 배포

VM 프로비전 Runbook은 AWS PowerShell 모듈을 사용하여 작업을 수행합니다. 다음 단계를 사용하여 AWS 구독 자격 증명으로 구성된 Automation 계정에 모듈을 추가합니다.  

1. 웹 브라우저를 열고 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AWSPowerShell/)로 이동하여 **Azure Automation에 배포** 단추를 클릭합니다.<br><br> ![AWS PS 모듈 가져오기](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Azure 로그인 페이지가 열리고 인증되면 Azure Portal로 이동하여 다음 페이지가 표시됩니다.<br><br> ![모듈 가져오기 페이지](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. 사용할 Automation 계정을 선택하고 **확인**을 클릭하여 배포를 시작합니다.

   > [!NOTE]
   > Azure Automation이 PowerShell 모듈을 가져와 cmdlet을 추출합니다. Automation에서 모듈 가져오기 및 cmdlet 추출을 완전히 완료할 때까지 활동이 표시되지 않습니다. 이 프로세스는 몇 분 정도 걸릴 수 있습니다.  
   > <br>

1. Azure Portal에서 Automation 계정을 엽니다.
2. **자산** 타일을 클릭하고 자산 창에서 **모듈**을 선택합니다.
3. 모듈 페이지의 목록에 **AWSPowerShell** 모듈이 표시됩니다.

## <a name="create-aws-deploy-vm-runbook"></a>AWS 배포 VM Runbook 만들기

AWS PowerShell 모듈을 배포한 후에는 Runbook을 작성하여 PowerShell 스크립트를 통해 AWS에서 가상 머신을 프로비전하는 작업을 자동화할 수 있습니다. 아래 단계에서는 Azure Automation에서 네이티브 PowerShell 스크립트를 사용하는 방법을 보여 줍니다.  

> [!NOTE]
> 이 스크립트와 관련된 추가 옵션 및 정보를 보려면 [PowerShell 갤러리](https://www.powershellgallery.com/packages/New-AwsVM/)를 방문하세요.
> 

1. PowerShell 세션을 열고 다음 명령을 입력하여 PowerShell 갤러리에서 PowerShell 스크립트 New-AwsVM을 다운로드합니다.<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Azure Portal에서 Automation 계정을 열고 **프로세스 자동화** 아래에서 **Runbook**을 선택합니다.  
3. Runbook 페이지에서 **Runbook 추가**를 선택합니다.
4. Runbook 추가 창에서 **빨리 만들기**를 선택하여 새 Runbook을 만듭니다.
5. Runbook 속성 창에서 Runbook의 이름을 입력합니다.
6. **Runbook 형식** 드롭다운 목록에서 **PowerShell**을 선택한 다음 **만들기**를 클릭합니다.<br><br> ![Runbook 만들기 창](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
7. PowerShell Runbook 편집 페이지가 나타나면 PowerShell 스크립트를 복사하여 Runbook 작성 캔버스에 붙여넣습니다.<br><br> ![Runbook PowerShell 스크립트](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    예제 PowerShell 스크립트 작업을 할 때는 다음 사항에 주의하세요.

    * Runbook에는 많은 기본 매개 변수 값이 포함되어 있습니다. 모든 기본 값을 평가하고 필요한 경우 업데이트합니다.
    * AWS 자격 증명을 `AWScred`와 다른 이름의 자격 증명 자산으로 저장한 경우 이에 맞게 스크립트의 57번째 줄을 업데이트해야 합니다.  
    * PowerShell에서 AWS CLI 명령을 사용하여 작업할 때 특히 이 예제 Runbook에서는 AWS 영역을 지정해야 합니다. 그러지 않으면 cmdlet이 실패합니다. 자세한 내용은 PowerShell용 AWS 도구 설명서의 AWS 항목 [AWS 영역 지정](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) 을 참조하세요.  

8. AWS 구독에서 이미지 이름 목록을 검색하려면 PowerShell ISE를 시작하고 AWS PowerShell 모듈을 가져옵니다. ISE 환경의 `Get-AutomationPSCredential`을 `AWScred = Get-Credential`로 바꿔 AWS에 인증합니다. 이 문은 자격 증명을 요청하는 메시지를 표시합니다. 그러면 사용자 이름으로 액세스 키 ID, 암호로 보안 액세스 키를 입력하면 됩니다. 

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
9. 이미지 이름 중 하나를 복사하여 Runbook에서 `$InstanceType`으로 참조되는 Automation 변수에 붙여넣습니다. 이 예제에서는 AWS 프리 티어 구독을 사용하므로 Runbook 예제에 **t2.micro**를 사용합니다.  
10. Runbook을 저장하고 **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.

### <a name="test-the-aws-vm-runbook"></a>AWS VM Runbook 테스트

1. Runbook이 AWS에 인증하기 위해 `AWScred`라는 자산을 생성하는지 확인하거나, 자격 증명 자산 이름을 참조하도록 스크립트를 업데이트합니다.    
2. 새 Runbook을 확인하고 모든 매개 변수 값이 필수로 업데이트되었는지 확인합니다.
AWS PowerShell 모듈을 Azure Automation으로 가져왔는지 확인합니다.  
3. Azure Automation에서 **상세 레코드 기록**을 설정하고, 선택적으로 Runbook 작업 **로깅 및 추적** 아래의 **진행률 레코드 기록**을 **켜짐**으로 설정합니다.<br><br> ![Runbook 로깅 및 추적](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png).  
4. **시작**을 클릭하여 Runbook을 시작한 다음 Runbook 시작 창이 열리면 **확인**을 클릭합니다.
5. Runbook 시작 창에서 VM 이름을 제공합니다. 스크립트에서 미리 구성한 다른 매개 변수의 기본값을 수락합니다. **확인**을 클릭하여 Runbook 작업을 시작합니다.<br><br> ![New-AwsVM Runbook 시작](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. 만든 Runbook 작업에 대한 작업 창이 열립니다. 이 창을 닫습니다.
7. Runbook 작업 창에서 **모든 로그** 타일을 선택하여 작업 진행 상태 및 출력 스트림을 볼 수 있습니다.<br><br> ![스트림 출력](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. VM이 프로비전 중인지 확인하려면 AWS Management Console에 로그인합니다(현재 로그인되지 않은 경우).<br><br> ![AWS 콘솔에서 VM 배포](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>다음 단계
 
* 지원되는 Runbook을 확인하려면 [Azure Automation Runbook 형식](automation-runbook-types.md)을 참조하세요.
* Runbook을 사용하려면 [Azure Automation에서 Runbook 관리](manage-runbooks.md)를 참조하세요.
* PowerShell에 대한 자세한 내용은 [PowerShell 문서](https://docs.microsoft.com/powershell/scripting/overview)를 참조하세요.
* 스크립트 지원은 [Azure Automation에서 네이티브 PowerShell 스크립트 지원](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)을 참조하세요.
* PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조하세요.