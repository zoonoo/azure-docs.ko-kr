---
title: Azure DevTest 랩을 Azure 파이프라인에 통합
description: Azure Pipelines 지속적인 통합 및 배달 파이프라인에 Azure DevTest Labs를 통합하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293218"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Azure DevTest 랩을 Azure 파이프라인 CI/CD 파이프라인에 통합

Azure DevTest Labs 작업 확장을 사용하여 Azure *DevTest Labs와* Azure 파이프라인 의 지속적인 통합 및 지속적인 전달(CI/CD) 빌드 및 릴리스 파이프라인을 통합할 수 있습니다. 확장은 다음과 같은 여러 작업을 설치합니다. 

- VM(가상 머신) 만들기
- VM에서 사용자 지정 이미지 만들기
- VM 삭제 

예를 들어 이러한 작업을 사용하면 특정 테스트 작업에 대해 *골든 이미지* VM을 신속하게 배포한 다음 테스트가 완료되면 VM을 쉽게 삭제할 수 있습니다.

이 문서에서는 Azure DevTest Labs 작업을 사용하여 VM을 만들고 배포하고, 사용자 지정 이미지를 만든 다음, VM을 모두 하나의 릴리스 파이프라인으로 삭제하는 방법을 보여 주며, 이 문서에서는 일반적으로 사용자 지정 빌드, 테스트 및 배포 파이프라인에서 작업을 개별적으로 수행합니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>사전 요구 사항

- [Azure DevOps](https://dev.azure.com) 조직에 등록하거나 로그인하고 조직에서 [프로젝트를 만듭니다.](/vsts/organizations/projects/create-project) 
  
- 비주얼 스튜디오 마켓플레이스에서 Azure DevTest 랩 작업 확장을 설치합니다.
  
  1. [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)으로 이동합니다.
  1. **무료 받기를 선택합니다.**
  1. 드롭다운에서 Azure DevOps 조직을 선택하고 **설치를**선택합니다. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Azure VM을 빌드하는 템플릿 만들기 

이 섹션에서는 요청 시 Azure VM을 만드는 데 사용하는 Azure 리소스 관리자 템플릿을 만드는 방법에 대해 설명합니다.

1. 구독에서 리소스 관리자 템플릿을 만들려면 [리소스 관리자 템플릿 사용의](devtest-lab-use-resource-manager-template.md)절차를 따릅니다.
   
1. Resource Manager 템플릿을 생성하기 전에, VM 만들기의 일부로 [WinRM 아티팩트](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)를 추가합니다. 대상 컴퓨터에서 *Azure 파일 복사본* 및 *PowerShell과* 같은 배포 작업에는 WinRM 액세스가 필요합니다. WinRM 아티팩트에는 VM의 정규화된 도메인 이름(FQDN)이어야 하는 매개 변수로 호스트 이름이 필요합니다. 
   
   > [!NOTE]
   > WinRM을 공유 IP 주소와 함께 사용하는 경우 NAT 규칙을 추가하여 외부 포트를 WinRM 포트에 매핑해야 합니다. 공용 IP 주소로 VM을 만드는 경우 NAT 규칙이 필요하지 않습니다.
   
   
1. *CreateVMTemplate.json이라는*파일로 컴퓨터에 템플릿을 저장합니다.
   
1. 소스 제어 시스템에 템플릿을 체크 인합니다.

## <a name="create-a-script-to-get-vm-properties"></a>VM 속성을 얻기 위한 스크립트 만들기

릴리스 파이프라인에서 대상 컴퓨터에서 *Azure 파일 복사* 또는 *PowerShell과* 같은 작업 단계를 실행하는 경우 다음 스크립트는 VM에 앱을 배포하는 데 필요한 값을 수집합니다. 일반적으로 이러한 작업을 사용하여 Azure VM에 앱을 배포합니다. 작업에는 VM 리소스 그룹 이름, IP 주소 및 FQDN과 같은 값이 필요합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

스크립트 파일을 만들려면 다음을 수행하십시오.

1. 텍스트 편집기를 열고 다음 스크립트를 붙여넣습니다.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. *GetLabVMParams.ps1과*같은 이름으로 파일을 저장하고 소스 제어 시스템에 체크 인합니다. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Azure Pipelines에서 릴리스 파이프라인 만들기

새 릴리스 파이프라인을 만들려면 다음을 수행합니다.

1. Azure DevOps 프로젝트 페이지에서 왼쪽 탐색에서 **파이프라인 릴리스를** > **Releases** 선택합니다.
1. **새 파이프라인을**선택합니다.
1. **템플릿 선택에서**아래로 스크롤하여 **빈 작업을**선택한 다음 **적용을**선택합니다.

### <a name="add-and-set-variables"></a>변수 추가 및 설정

파이프라인 태스크는 Azure 포털에서 리소스 관리자 템플릿을 만들 때 VM에 할당한 값을 사용합니다. 

값에 대한 변수를 추가하려면 다음을 수행합니다. 

1. 파이프라인 페이지에서 **변수 탭을 선택합니다.**
   
1. 각 변수에 대해 **추가를** 선택하고 이름과 값을 입력합니다.
   
   |이름|값|
   |---|---|
   |*vmName*|리소스 관리자 템플릿에 할당한 VM 이름|
   |*사용자*|VM에 액세스하는 사용자 이름|
   |*다시 설정*|사용자 이름에 대한 암호입니다. 암호를 숨기고 고정하려면 잠금 아이콘을 선택합니다.

### <a name="create-a-devtest-labs-vm"></a>개발자 테스트 랩 VM 만들기

다음 단계는 향후 배포에 사용할 황금 이미지 VM을 만드는 것입니다. Azure DevTest 랩 만들기 VM 작업을 사용 하 여 Azure DevTest 연구소 인스턴스 내에서 *VM을 만듭니다.*

1. 릴리스 **파이프라인** 탭에서 **스테이지 1에서** 하이퍼링크된 텍스트를 선택하여 **스테이지 작업을**보고 **+** **에이전트 작업**옆에 있는 더하기 기호를 선택합니다. 
   
1. **작업 추가에서** **Azure DevTest 랩 만들기 VM을**선택하고 **추가**를 선택합니다. 
   
1. 왼쪽 창에서 **Azure DevTest 랩 VM 만들기를** 선택합니다. 

1. 오른쪽 창에서 다음과 같이 양식을 작성합니다.
   
   |필드|값|
   |---|---|
   |**Azure RM 구독**|드롭다운에서 **사용 가능한 Azure 서비스 연결** 또는 사용 가능한 Azure 구독에서 서비스 연결 또는 **구독을** 선택하고 필요한 경우 **권한 부여를** 선택합니다.<br /><br />**참고:** Azure 구독에 대한 보다 제한된 사용 권한 연결을 만드는 자세한 내용은 [Azure 리소스 관리자 서비스 끝점을](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)참조하십시오.|
   |**랩 이름**|랩 VM을 만들 기존 랩의 이름을 선택합니다.|
   |**템플릿 이름**|소스 코드 리포지토리에 저장한 템플릿 파일의 전체 경로 및 이름을 입력합니다. 예를 들어 기본 제공 속성을 사용하여 경로를 단순화할 수 있습니다.<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**템플릿 매개 변수**|이전에 정의한 변수에 대한 매개 변수를 입력합니다.<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**출력 변수** > **랩 VM ID**|생성된 랩 VM ID에 대한 변수를 입력합니다. 기본 **labVMId를**사용하는 경우 후속 작업의 변수를 *$(labVMId)로*참조할 수 있습니다.<br /><br />기본값 이외의 이름을 만들 수 있지만 후속 작업에서 올바른 이름을 사용해야 합니다. 다음 형식으로 랩 VM ID를 작성할 수 있습니다.<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>데브테스트 랩 VM에 대한 세부 정보 수집

이전에 만든 스크립트를 실행하여 DevTest Labs VM의 세부 정보를 수집합니다. 

1. 릴리스 **파이프라인** 탭에서 **스테이지 1에서** 하이퍼링크된 텍스트를 선택하여 **스테이지 작업을**보고 **+** **에이전트 작업**옆에 있는 더하기 기호를 선택합니다. 
   
1. **작업 추가에서** **Azure PowerShell을**선택하고 **에 추가를**선택합니다. 
   
1. **Azure PowerShell 스크립트:** 왼쪽 창의 FilePath를 선택합니다. 
   
1. 오른쪽 창에서 다음과 같이 양식을 작성합니다.
   
   |필드|값|
   |---|---|
   |**Azure 연결 유형**|**Azure 리소스 관리자를**선택합니다.|
   |**Azure 구독**|서비스 연결 또는 구독을 선택합니다.| 
   |**스크립트 유형**|**스크립트 파일 경로**선택 .|
   |**스크립트 경로**|소스 코드 리포지토리에 저장한 PowerShell 스크립트의 전체 경로 및 이름을 입력합니다. 예를 들어 기본 제공 속성을 사용하여 경로를 단순화할 수 있습니다.<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**스크립트 인수**|예를 들어 이전 작업에서 채워진 *labVmId* 변수의 이름을 입력합니다.<br /><br />`-labVmId '$(labVMId)'`|

스크립트는 필요한 값을 수집하고 릴리스 파이프라인 내의 환경 변수에 저장하므로 후속 단계에서 쉽게 참조할 수 있습니다.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>개발자 테스트 랩 VM에서 VM 이미지 만들기

다음 작업은 Azure DevTest Labs 인스턴스에서 새로 배포된 VM의 이미지를 만드는 것입니다. 그런 다음, 개발 작업을 실행하거나 일부 테스트를 실행하고자 할 때마다 필요에 따라 이미지를 사용하여 VM의 복사본을 만듭니다. 

1. 릴리스 **파이프라인** 탭에서 **스테이지 1에서** 하이퍼링크된 텍스트를 선택하여 **스테이지 작업을**보고 **+** **에이전트 작업**옆에 있는 더하기 기호를 선택합니다. 
   
1. **작업 추가에서** **Azure DevTest 랩에서 사용자 지정 이미지 만들기를**선택하고 **추가**를 선택합니다. 
   
1. 아래와 같이 작업을 구성합니다.
   
   |필드|값|
   |---|---|
   |**Azure RM 구독**|서비스 연결 또는 구독을 선택합니다.|
   |**랩 이름**|이미지를 만들 기존 랩의 이름을 선택합니다.|
   |**사용자 지정 이미지 이름**|사용자 지정 이미지의 이름을 입력합니다.|
   |**설명(선택** 사항)|나중에 올바른 이미지를 쉽게 선택할 수 있도록 설명을 입력합니다.|
   |**소스 랩 VM** > **소스 랩 VM ID**|LabVMId 변수의 기본 이름을 변경한 경우 여기에 입력합니다. 기본값은 **$(labVMId)** 입니다.|
   |**출력 변수** > **사용자 정의 이미지 ID**|필요한 경우 변수의 기본 이름을 편집할 수 있습니다.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>DevTest 랩 VM에 앱을 배포(선택 사항)

새로운 DevTest Labs VM에 앱을 배포하는 작업을 추가할 수 있습니다. 일반적으로 앱을 배포하는 데 사용하는 작업은 대상 컴퓨터에서 *Azure 파일 복사* 및 *PowerShell*입니다.

이러한 작업의 매개 변수에 필요한 VM 정보는 릴리스 파이프라인 내에서 **labVmRgName,** **labVMIpAddress**및 **labVMFqdn이라는** 세 가지 구성 변수에 저장됩니다. 앱을 배포하지 않고 DevTest Labs VM 및 사용자 지정 이미지를 만들어 시험하려는 경우 이 단계를 건너뛸 수 있습니다.

### <a name="delete-the-vm"></a>VM 삭제

마지막 작업은 Azure DevTest Labs 인스턴스에 배포한 VM을 삭제하는 것입니다. 일반적으로 배포된 VM에서 필요한 개발 작업을 실행하거나 테스트를 실행한 후 VM을 삭제합니다. 

1. 릴리스 **파이프라인** 탭에서 **스테이지 1에서** 하이퍼링크된 텍스트를 선택하여 **스테이지 작업을**보고 **+** **에이전트 작업**옆에 있는 더하기 기호를 선택합니다. 
   
1. **작업 추가에서** **Azure DevTest 랩에서 VM 삭제를**선택하고 **에 추가를**선택합니다. 
   
1. 아래와 같이 작업을 구성합니다.
   
   - **Azure RM 구독에서**서비스 연결 또는 구독을 선택합니다. 
   - **랩 VM ID의**경우 LabVMId 변수의 기본 이름을 변경한 경우 여기에 입력합니다. 기본값은 **$(labVMId)** 입니다.
   
### <a name="save-the-release-pipeline"></a>릴리스 파이프라인 저장

새 릴리스 파이프라인을 저장하려면 다음을 수행합니다.

1. 릴리스 파이프라인 페이지에서 **새 릴리스 파이프라인** 이름을 선택하고 파이프라인에 대한 새 이름을 입력합니다. 
   
1. 오른쪽 상단에 있는 **저장** 아이콘을 선택합니다. 

## <a name="create-and-run-a-release"></a>릴리스 만들기 및 실행

새 파이프라인을 사용하여 릴리스를 만들고 실행하려면 다음을 수행합니다.

1. 릴리스 파이프라인 페이지에서 오른쪽 상단에 있는 **릴리스 만들기를** 선택합니다. 
   
1. **아티팩트에서**최신 빌드를 선택한 다음 **을**선택합니다.
   
1. 각 릴리스 단계에서 Azure 포털에서 DevTest Labs 인스턴스보기를 새로 고쳐 VM 생성, 이미지 생성 및 VM 삭제를 봅니다.

사용자 지정 이미지를 사용하여 필요할 때마다 VM을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Resource Manager 템플릿으로 다중 VM 환경을 만드는](devtest-lab-create-environment-from-arm.md) 방법을 알아봅니다.
- [공개 DevTest Labs GitHub 리포지토리에서](https://github.com/Azure/azure-quickstart-templates)DevTest Labs 자동화에 대한 더 빠른 시작 리소스 관리자 템플릿을 살펴보십시오.
- 필요한 경우 [Azure DevOps 문제 해결 페이지를 참조하세요.](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)
 
