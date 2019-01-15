---
title: Azure Stack 개발 키트 ASDK ()를 다시 배포 | Microsoft Docs
description: 이 문서는 ASDK 다시 설치 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 91ac75f3cb41f73ea18595138969dfb7b1444173
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263514"
---
# <a name="redeploy-the-asdk"></a>ASDK를 다시 배포
이 문서는 Azure Stack 개발 키트 ASDK () 비-프로덕션 환경에서 다시 배포 하는 방법을 알아봅니다. 에서는 지원 되지 않습니다는 ASDK 업그레이드 때문에 완전히 최신 버전으로 이동 하 고 다시 배포 해야 합니다. 또한는 ASDK 처음부터 다시 시작 하려는 언제 든 다시 배포할 수 있습니다.

> [!IMPORTANT]
> ASDK를 새 버전으로 업그레이드 지원 되지 않습니다. Azure Stack의 최신 버전을 평가할 때마다 개발 키트 호스트 컴퓨터에서 ASDK를 다시 배포 해야 합니다.

## <a name="remove-azure-registration"></a>Azure 등록 제거 
Azure를 사용 하 여 이전에 ASDK 설치를 등록 하는 경우 등록 리소스는 ASDK 재배포 하려면 먼저 제거 해야 합니다. ASDK를 재배포 하는 경우 marketplace에서 항목의 가용성을 사용 하도록 설정 하려면 ASDK 다시 등록 합니다. Azure 구독에 ASDK를 이전에 등록 하지 않은 경우에이 섹션을 건너뛸 수 있습니다.

등록 리소스를 제거 하려면 사용 합니다 **제거 AzsRegistration** Azure Stack 등록을 취소 하는 cmdlet입니다. 그런 다음 사용 합니다 **Remove-azurermresourcegroup** Azure 구독에서 Azure Stack 리소스 그룹을 삭제 하는 cmdlet:

1. 권한 있는 끝점에 액세스할 수 있는 컴퓨터에서 관리자 권한으로 PowerShell 콘솔을 엽니다. ASDK에 대 한 개발 키트 호스트 컴퓨터입니다.

2. ASDK 설치의 등록을 취소 하 고 삭제 하려면 다음 PowerShell 명령을 실행 합니다 **azurestack** Azure 구독에서 리소스 그룹:

  ```Powershell    
  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Provide Azure subscription admin credentials
  Add-AzureRmAccount

  # Provide ASDK admin credentials
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

  # Unregister Azure Stack
  Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

  # Remove the Azure Stack resource group
  Remove-AzureRmResourceGroup -Name azurestack -Force
  ```

3. 스크립트를 실행 하면 로컬 ASDK 설치와 Azure 구독에 로그인 하 라는 메시지가 표시 됩니다.
4. 스크립트가 완료 되 면 다음 예제와 유사한 메시지가 표시 됩니다.

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.`
    ` Remove registration resource from Azure...`
    ` "Deleting the resource..." on target "/subscriptions/<subscription information>"`
    ` ********** End Log: Remove-AzsRegistration ********* `



Azure Stack 이제 성공적으로 취소 해야 Azure 구독에서 등록 합니다. 또한 Azure를 사용 하 여 ASDK를 등록할 때 만들어진 azurestack의 경우 리소스 그룹 에서도 삭제 하도록 합니다.

## <a name="deploy-the-asdk"></a>ASDK 배포
Azure Stack 다시 배포를 다시 시작 해야부터 아래 설명 된 대로 합니다. 단계는 Azure Stack (asdk installer.ps1) 설치 관리자 스크립트를 ASDK 설치에 사용 되는 여부에 따라 다릅니다.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>설치 관리자 스크립트를 사용 하 여 ASDK 다시 배포
1. ASDK 컴퓨터에서 관리자 권한 PowerShell 콘솔을 열고 asdk installer.ps1 스크립트에 이동 합니다 **AzureStack_Installer** 비시스템 드라이브에 있는 디렉터리입니다. 스크립트를 실행 하 고 클릭 **다시 부팅**합니다.

   ![Asdk installer.ps1 스크립트 실행](media/asdk-redeploy/1.png)

2. 기본 운영 체제 선택 (되지 **Azure Stack**)를 클릭 하 고 **다음**합니다.

   ![호스트 운영 체제로 다시 시작](media/asdk-redeploy/2.png)

3. 로컬 관리자로 개발 키트 호스트는 기본 운영 체제로 다시 부팅 한 후에 로그인 합니다. 찾아 삭제 합니다 **C:\CloudBuilder.vhdx** 이전 배포의 일부로 사용 된 파일입니다. 

4. 처음 수행한 동일한 단계를 반복 [배포를 ASDK](asdk-install.md)합니다.

### <a name="redeploy-the-asdk-without-using-the-installer"></a>설치 관리자를 사용 하지 않고는 ASDK를 다시 배포
Asdk installer.ps1 스크립트는 ASDK 설치를 사용 하지 않은 경우 수동으로 다시 구성 해야 개발 키트 호스트 컴퓨터는 ASDK 다시 배포 하기 전에 합니다.

1. 시스템 구성 유틸리티를 실행 하 여 시작 **msconfig.exe** ASDK 컴퓨터. 에 **부팅** 탭, 호스트 컴퓨터 운영 체제 (없습니다: Azure Stack)을 선택, 클릭 **기본값으로 설정**를 클릭 하 고 **확인**합니다. 클릭 **다시 시작** 메시지가 표시 되 면 합니다.

      ![부팅 구성 설정](media/asdk-redeploy/4.png)

2. 개발 키트 호스트 컴퓨터의 로컬 관리자로 개발 키트 호스트는 기본 운영 체제로 다시 부팅 한 후에 로그인 합니다. 찾아 삭제 합니다 **C:\CloudBuilder.vhdx** 이전 배포의 일부로 사용 된 파일입니다. 

3. 처음 수행한 동일한 단계를 반복 [PowerShell을 사용 하 여 ASDK 배포](asdk-deploy-powershell.md)합니다.


## <a name="next-steps"></a>다음 단계
[ASDK 배포 후 작업](asdk-post-deploy.md)




