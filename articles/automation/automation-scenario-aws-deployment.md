---
title: Amazon Web Services에서 VM 배포 자동화
description: 이 문서에서는 Azure Automation을 사용하여 Amazon Web Service VM 만들기를 자동화하는 방법을 보여 줍니다.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604838"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation 시나리오 - AWS 가상 머신 프로비전
이 문서에서는 Azure Automation을 사용하여 AWS(Amazon Web Service) 구독에서 가상 머신을 프로비전하고 해당 VM에 특정 이름을 지정하는 방법을 설명합니다. 이는 AWS에서 VM “태그 지정”이라고 합니다.

## <a name="prerequisites"></a>사전 요구 사항
Azure 자동화 계정과 Amazon 웹 서비스(AWS) 구독이 있어야 합니다. Azure Automation 계정을 설정하고 AWS 구독 자격 증명으로 구성하는 데 대한 자세한 내용은 [Configure Authentication with Amazon Web Services(Amazon Web Services로 인증 구성)](automation-config-aws-account.md)를 참조하세요. 아래 섹션에서 이 계정을 참조할 때 이 계정을 계속 진행하기 전에 AWS 구독 자격 증명으로 계정을 만들거나 업데이트해야 합니다.

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services PowerShell 모듈 배포
VM 프로비저닝 런북은 AWS PowerShell 모듈을 사용하여 작업을 수행합니다. 다음 단계를 사용하여 AWS 구독 자격 증명으로 구성된 자동화 계정에 모듈을 추가합니다.  

1. 웹 브라우저를 열고 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AWSPowerShell/)로 이동하여 **Azure Automation에 배포** 단추를 클릭합니다.<br><br> ![AWS PS 모듈 가져오기](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Azure 로그인 페이지가 열리고 인증되면 Azure Portal로 이동하여 다음 페이지가 표시됩니다.<br><br> ![모듈 가져오기 페이지](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. 사용할 자동화 계정을 선택하고 **확인을** 클릭하여 배포를 시작합니다.

   > [!NOTE]
   > Azure 자동화가 PowerShell 모듈을 가져오면 cmdlet을 추출합니다. 자동화가 모듈 가져오기 및 cmdlet 추출을 완전히 완료할 때까지 활동이 나타나지 않습니다. 이 프로세스는 몇 분 정도 걸릴 수 있습니다.  
   > <br>

1. Azure Portal에서 Automation 계정을 엽니다.
2. **자산** 타일을 클릭하고 자산 창에서 **모듈 을 선택합니다.**
3. 모듈 페이지의 목록에 **AWSPowerShell** 모듈이 표시됩니다.

## <a name="create-aws-deploy-vm-runbook"></a>AWS 배포 VM Runbook 만들기
AWS PowerShell 모듈을 배포한 후에는 Runbook을 작성하여 PowerShell 스크립트를 통해 AWS에서 가상 머신을 프로비전하는 작업을 자동화할 수 있습니다. 아래 단계는 Azure 자동화에서 네이티브 PowerShell 스크립트를 사용하는 방법을 보여 줍니다.  

> [!NOTE]
> 이 스크립트와 관련된 추가 옵션 및 정보를 보려면 [PowerShell 갤러리](https://www.powershellgallery.com/packages/New-AwsVM/)를 방문하세요.
> 

1. PowerShell 세션을 열고 다음 명령을 입력하여 PowerShell 갤러리에서 PowerShell 스크립트 New-AwsVM을 다운로드합니다.<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Azure 포털에서 자동화 계정을 열고 프로세스 자동화 에서 **Runbook을** **선택합니다.**  
3. Runbook 페이지에서 **Runbook 추가**를 선택합니다.
4. Runbook 추가 창에서 빠른 **만들기를** 선택하여 새 Runbook을 만듭니다.
5. Runbook 속성 창에서 Runbook의 이름을 입력합니다.
6. **Runbook 유형** 드롭다운 목록에서 **PowerShell을**선택한 다음 **을 클릭합니다.**<br><br> ![Runbook 만들기 창](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. PowerShell Runbook 편집 페이지가 나타나면 PowerShell 스크립트를 복사하여 Runbook 작성 캔버스에 붙여넣습니다.<br><br> ![Runbook PowerShell 스크립트](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > 예제 PowerShell 스크립트 작업을 할 때는 다음 사항에 주의하세요.
    > 
    > * Runbook에는 많은 기본 매개 변수 값이 포함되어 있습니다. 모든 기본 값을 평가하고 필요한 경우 업데이트합니다.
    > * AWS 자격 증명을 다른 `AWScred`이름의 자격 증명 자산으로 저장한 경우 57호선의 스크립트를 업데이트하여 그에 따라 일치시켜야 합니다.  
    > * PowerShell에서 AWS CLI 명령을 사용하여 작업할 때 특히 이 예제 Runbook에서는 AWS 영역을 지정해야 합니다. 그러지 않으면 cmdlet이 실패합니다. 자세한 내용은 PowerShell용 AWS 도구 설명서의 AWS 항목 [AWS 영역 지정](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) 을 참조하세요.  
    >

7. AWS 구독에서 이미지 이름 목록을 검색하려면 PowerShell ISE를 시작하고 AWS PowerShell 모듈을 가져옵니다. ISE 환경에서 를 로 `Get-AutomationPSCredential` 대체하여 AWS에 `AWScred = Get-Credential`대해 인증합니다. 이 명령문은 자격 증명을 묻는 메시지를 표시하며 사용자 이름에 대한 액세스 키 ID와 암호에 대한 비밀 액세스 키를 제공할 수 있습니다. 

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
8. Runbook에서 참조한 대로 자동화 변수에 이미지 이름 중 하나를 `$InstanceType`복사하여 붙여넣습니다. 이 예제에서는 무료 AWS 계층 구독을 사용하므로 runbook 예제에 **t2.micro를** 사용합니다.  
9. Runbook을 저장하고 **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.

### <a name="testing-the-aws-vm-runbook"></a>AWS VM Runbook 테스트
Runbook 테스트를 진행하기 전에 몇 가지 사항을 확인해야 합니다.

* `AWScred` AWS에 대한 인증을 요구하는 에셋이 만들어졌거나 자격 증명 자산 이름을 참조하도록 스크립트가 업데이트되었습니다.    
* AWS PowerShell 모듈은 Azure 자동화에서 가져왔습니다.  
* 새 Runbook이 만들어졌으며 필요한 경우 매개 변수 값이 확인되고 업데이트되었습니다.  
* **로그 자세한 레코드** 및 선택적으로 Runbook 작업 **로깅 및 추적에서** **진행률 레코드를 로그** 온으로 설정했습니다. **On**<br><br> ![Runbook 로깅](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)및 추적.  

1. 실행책을 시작하려면 **시작을** 클릭한 다음 Runbook 시작 창이 열릴 때 **확인을** 클릭합니다.
2. 실행 책 시작 창에서 VM 이름을 제공합니다. 스크립트에서 미리 구성한 다른 매개 변수에 대한 기본값을 수락합니다. **확인을** 클릭하여 Runbook 작업을 시작합니다.<br><br> ![New-AwsVM Runbook 시작](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. 만든 Runbook 작업에 대해 작업 창이 열립니다. 이 창을 닫습니다.
4. Runbook 작업 창에서 **모든 로그를** 선택하여 작업의 진행 률을 보고 출력 스트림을 볼 수 있습니다.<br><br> ![스트림 출력](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. VM이 프로비전되고 있는지 확인하려면 현재 로그인하지 않은 경우 AWS 관리 콘솔에 로그인합니다.<br><br> ![AWS 콘솔에서 VM 배포](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>다음 단계
* 그래픽 런북을 시작하려면 [내 첫 번째 그래픽 실행북을](automation-first-runbook-graphical.md)참조하십시오.
* PowerShell 워크플로 런북을 시작하려면 [내 첫 번째 PowerShell 워크플로 런북을](automation-first-runbook-textual.md)참조하십시오.
* Runbook 유형 및 해당 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 유형을](automation-runbook-types.md)참조하십시오.
* PowerShell 스크립트 지원 기능에 대한 자세한 내용은 [Azure 자동화의 네이티브 PowerShell 스크립트 지원을](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)참조하십시오.