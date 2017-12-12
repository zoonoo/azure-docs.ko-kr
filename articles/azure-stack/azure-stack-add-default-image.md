---
title: "Azure 스택 Marketplace에 기본 VM 이미지를 추가 | Microsoft Docs"
description: "Azure 스택 마켓플레이스로 Windows Server 2016 VM 기본 이미지를 추가 합니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2849E53F-3D58-48A5-8007-3238FC39F630
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: mabrigg
ms.openlocfilehash: ed62f2f8441220eb37aea7f4c848702e9821698b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Azure 스택 마켓플레이스로 Windows Server 2016 VM 이미지를 추가 합니다.

기본적으로 없는 가상 컴퓨터 이미지는 Azure 스택 Marketplace에 있습니다. Azure 스택 연산자에 액세스 하려면 사용자에 대 한 마켓플레이스 이미지를 추가 해야 합니다. 다음 방법 중 하나를 사용 하 여 Azure 스택 마켓플레이스로 Windows Server 2016 이미지를 추가할 수 있습니다.

* [Azure 마켓플레이스의 이미지를 다운로드](#add-the-image-by-downloading-it-from-the-azure-marketplace)합니다. 연결 된 시나리오에서 작동 하는 Azure를 사용한 Azure 스택 인스턴스를 등록 한 경우이 옵션을 사용 합니다.

* [PowerShell을 사용 하 여 이미지 추가](#add-the-image-by-using-powershell)합니다. Azure 스택 연결이 끊긴 시나리오에서 또는 시나리오에서 함께 배포한 연결을 제한 하는 경우이 옵션을 사용 합니다.

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Azure 마켓플레이스에서 다운로드 하 여 이미지를 추가 합니다.

1. Azure 스택을 배포 하 고 Azure 스택 개발 키트에 로그인 합니다.

2. 선택 **더 많은 서비스** > **마켓플레이스 관리** > **Azure에서 추가**합니다. 

3. 찾기 및 검색은 **Windows Server 2016 Datacenter Eval** 이미지를 선택한 후 **다운로드**합니다.

   ![Azure에서 이미지를 다운로드 합니다.](media/azure-stack-add-default-image/download-image.png)

이미지의 크기는 아래에서 사용 가능한 다운로드가 완료 되 면 **마켓플레이스 관리**합니다. 이미지의 크기 또한 아래에서 사용할 수는 **가상 컴퓨터**합니다.

## <a name="add-the-image-by-using-powershell"></a>PowerShell을 사용 하 여 이미지를 추가 합니다.

### <a name="prerequisites"></a>필수 조건 

다음과 같은 사항을 실행는 [개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) 또는 Windows 기반 외부 클라이언트에서 있다면 [VPN을 통해 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. 설치 [Azure 스택 호환 Azure PowerShell 모듈](azure-stack-powershell-install.md)합니다.  

2. 다운로드는 [Azure 스택을 사용 하는 데 필요한 도구](azure-stack-powershell-download.md)합니다.  

3. Windows Server 평가 페이지에서 [Windows Server 2016 평가판 다운로드](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016)합니다. 대화 상자가 나타나면 다운로드 한 ISO 버전을 선택 합니다. 이 문서에 설명 된 단계에 나중에 사용 되는 다운로드 위치에 경로 기록 합니다. 이 단계에는 인터넷 연결이 필요합니다.  

### <a name="add-the-image-to-the-azure-stack-marketplace"></a>Azure 스택 마켓플레이스 이미지를 추가 합니다.
   
1. 다음 명령을 사용 하 여 Azure 스택 Connect 및 ComputeAdmin 모듈을 가져옵니다.

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Azure 스택 환경에 로그인 합니다. Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)을 사용 하 여 Azure 스택 환경 배포 여부에 따라 다음 스크립트 중 하나를 실행 합니다. (Azure AD를 대체 `tenantName`, `GraphAudience` 끝점 및 `ArmEndpoint` 환경 구성을 반영 하도록 값입니다.)  

   * **Azure Active Directory**합니다. 다음 cmdlet을 사용 합니다.

    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"
    
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```

   * **Active Directory Federation Services**합니다. 다음 cmdlet을 사용 합니다.
    
    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"

    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience `
      -EnableAdfsAuthentication:$true

   $TenantID = Get-AzsDirectoryTenantId `
     -ADFS `
     -EnvironmentName "AzureStackAdmin" 

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```
   
3. Azure 스택 마켓플레이스로 Windows Server 2016 이미지를 추가 합니다. (대체 *fully_qualified_path_to_ISO* 다운로드 한 Windows Server 2016 ISO에 경로 사용 합니다.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

Windows Server 2016 VM 이미지의 최신 누적 업데이트를 갖도록 하려면 포함 된 `IncludeLatestCU` 를 실행할 때 매개 변수는 `New-AzsServer2016VMImage` cmdlet. 에 대 한 허용 된 매개 변수에 대 한 정보에 대 한는 `New-AzsServer2016VMImage` cmdlet 참조 [매개 변수](#parameters)합니다. Azure 스택 마켓플레이스 이미지를 게시 하려면 1 시간 정도 걸립니다. 

## <a name="parameters"></a>매개 변수

|새 AzsServer2016VMImage 매개 변수|Required?|설명|
|-----|-----|------|
|ISOPath|예|다운로드 한 Windows Server 2016 ISO에 정규화 된 경로입니다.|
|net35|아니요|Windows Server 2016 이미지에.NET 3.5 runtime을 설치합니다. 기본적으로이 값 설정 **true**합니다.|
|버전|아니요|지정 **코어**, **전체**, 또는 **둘 다** Windows Server 2016 이미지입니다. 기본적으로이 값 설정 **전체**합니다.|
|VHDSizeInMB|아니요|Azure 스택 환경에 추가 될 VHD 이미지의 mb 단위로 크기를 설정 합니다. 기본적으로이 값은 40,960 MB로 설정 됩니다.|
|CreateGalleryItem|아니요|Windows Server 2016 이미지에 대 한 마켓플레이스 항목을 만들어야 하는지 여부를 지정 합니다. 기본적으로이 값 설정 **true**합니다.|
|location |아니요 |Windows Server 2016 이미지를 게시 해야 하는 위치를 지정 합니다.|
|IncludeLatestCU|아니요|새 VHD에 최신 Windows Server 2016 누적 업데이트를 적용합니다.|
|CUUri |아니요 |특정 URI에서 실행 되도록 누적 업데이트를 Windows Server 2016 설정 합니다. |
|CUPath |아니요 |로컬 경로에서 실행 되도록 누적 업데이트를 Windows Server 2016 설정 합니다. 이 옵션은 연결이 끊어진된 환경에서 Azure 스택 인스턴스에 배포한 경우에 유용 합니다.|

## <a name="next-steps"></a>다음 단계

[가상 컴퓨터 프로비전](azure-stack-provision-vm.md)
