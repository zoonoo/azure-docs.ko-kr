---
title: VM 이미지를 Azure 스택에 추가 | Microsoft Docs
description: 조직의 사용자 지정 Windows 또는 Linux VM 이미지를 사용 하도록 테 넌 트를 추가 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/05/2018
ms.author: mabrigg
ms.openlocfilehash: 3680777439678a93b1283cf94b9f8a173965feeb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>이미지를 사용자 지정 가상 컴퓨터에서에서 사용할 수 있도록 Azure 스택

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

에서는 Azure 스택 연산자 수의 사용자 지정 가상 컴퓨터 이미지 사용할 수 있도록 사용자에 게 있습니다. Azure 리소스 관리자 템플릿을 이러한 이미지를 참조할 수 있습니다 또는 마켓플레이스 항목으로 Azure 마켓플레이스 UI에 추가할 수 있습니다.

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>PowerShell을 사용 하 여 마켓플레이스 VM 이미지 추가

다음과 같은 사항을 실행는 [개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) 또는 Windows 기반 외부 클라이언트에서 있다면 [VPN을 통해 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. [Azure 스택에 대 한 PowerShell을 설치](azure-stack-powershell-install.md)합니다.  

2. 다운로드는 [Azure 스택을 사용 하는 데 필요한 도구](azure-stack-powershell-download.md)합니다.  

3. Windows 또는 Linux 운영 체제 가상 하드 디스크 이미지 준비 VHD 형식에서 (VHDX 형식을 사용 하지 않는).

   * 준비 이미지에 대 한 지침은 Windows 이미지 참조 [리소스 관리자 배포에 대 한 Windows VM 이미지를 Azure에 업로드](../virtual-machines/windows/upload-generalized-managed.md)합니다.

   * Linux 이미지를 참조 하십시오. [배포 Linux 가상 컴퓨터 Azure 스택에](azure-stack-linux.md)합니다. 이미지를 준비 하거나 문서에 설명 된 대로 기존 Azure 스택 Linux 이미지를 사용 하는 단계를 완료 합니다.    

   Azure 스택 VHD 형식의 고정된 디스크를 지원합니다. 고정된 형식은 디스크 오프셋 X가 blob 오프셋 X에 저장 되므로 파일 내에 선형적으로 논리적 디스크를 구조 합니다. Blob 끝에는 작은 바닥글에서는 VHD의 속성을 설명합니다. 디스크의 해결 여부를 확인 하려면 사용 하 여는 [GET-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell 명령입니다.  

   > [!IMPORTANT]
   >  Azure 스택 동적 디스크 Vhd를 지원 하지 않습니다. 동적 디스크는 VM에 연결 하는 크기 조정 실패 상태에 VM이 종료 됩니다. 이 문제를 해결 하려면 VM의 디스크를 저장소 계정에 VHD blob을 삭제 하지 않고 VM을 삭제 합니다. 다음 동적 디스크에서 VHD를 고정된 디스크로 변환 하 고 가상 컴퓨터를 다시 만듭니다.

Azure 스택 마켓플레이스 이미지를 추가 하려면 다음 단계를 수행 합니다.

1. 연결 및 ComputeAdmin 모듈을 가져옵니다.

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

      Connect-AzureRmAccount `
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
          -EnvironmentName AzureStackAdmin

        Connect-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID
        ```

3. VM 이미지를 호출 하 여 추가 된 `Add-AzsVMImage` cmdlet. 에 `Add-AzsVMImage` cmdlet 지정 `osType` Windows 또는 Linux. 게시자, 제품, SKU 및 VM 이미지에 대 한 버전을 포함 합니다. 허용 된 매개 변수에 대 한 정보를 참조 하십시오. [매개 변수](#parameters)합니다. 매개 변수는 VM 이미지를 참조 하 여 Azure 리소스 관리자 템플릿을 사용 됩니다. 다음 예제에서는 스크립트를 호출합니다.

  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```


이 명령은 다음 작업을 수행 합니다.

* Azure 스택 환경으로 인증합니다.
* 새로 만든된 임시 저장소 계정에 로컬 VHD를 업로드합니다.
* VM 이미지 리포지토리에 VM 이미지를 추가합니다.
* 마켓플레이스 항목을 만듭니다.

포털에서이 명령은 성공적으로 실행 했는지 확인 하려면는 마켓플레이스로 이동 합니다. VM 이미지에서 사용할 수 있는지 확인은 **계산** 범주입니다.

![성공적으로 추가 하는 VM 이미지](./media/azure-stack-add-vm-image/verify-vm.png)

## <a name="remove-a-vm-image-by-using-powershell"></a>PowerShell을 사용 하 여 VM 이미지를 제거 합니다.

가상 컴퓨터 이미지 업로드 필요 없는 경우 다음 cmdlet을 사용 하 여 시장에서 삭제할 수 없습니다.

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>매개 변수

| 매개 변수 | 설명 |
| --- | --- |
| **publisher** |사용자가 이미지를 배포할 때 사용 되는 VM 이미지의 게시자 이름 세그먼트입니다. 예로 **Microsoft**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오. |
| **offer** |사용자가 VM 이미지를 배포할 때 사용 되는 VM 이미지의 제안 이름 세그먼트입니다. 예로 **windows Server**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오. |
| **sku** |사용자가 VM 이미지를 배포할 때 사용 되는 VM 이미지의 SKU 이름 세그먼트입니다. 예로 **Datacenter2016**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오. |
| **version** |사용자가 VM 이미지를 배포할 때 사용 되는 VM 이미지의 버전입니다. 이 버전은 형식에서  *\#.\#합니다. \#*. 예로 **1.0.0**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오. |
| **osType** |이미지의 osType 납입이 되어야 **Windows** 또는 **Linux**합니다. |
| **osDiskLocalPath** |OS 디스크 VHD 스택에 Azure VM 이미지로 업로드 하는 로컬 경로입니다. |
| **dataDiskLocalPaths** |선택적 배열 VM 이미지의 일환으로 업로드할 수 있는 데이터 디스크에 대 한 로컬 경로입니다. |
| **CreateGalleryItem** |Marketplace에서 항목을 만들 것인지를 결정 하는 부울 플래그입니다. 기본적으로 설정은 **true**합니다. |
| **title** |마켓플레이스 항목의 표시 이름입니다. 기본적으로 설정은 `Publisher-Offer-Sku` VM 이미지의 값입니다. |
| **description** |마켓플레이스 항목의 설명입니다. |
| **위치** |VM 이미지를 게시할 위치입니다. 기본적으로이 값 설정 **로컬**합니다.|
| **osDiskBlobURI** |(선택 사항) 이 스크립트를 받기도 Blob 저장소 URI에 대 한 `osDisk`합니다. |
| **dataDiskBlobURIs** |(선택 사항) 이 스크립트는 또한 이미지에 데이터 디스크를 추가 하기 위한 Blob 저장소 Uri의 배열을 허용 합니다. |

## <a name="add-a-vm-image-through-the-portal"></a>포털을 통해 VM 이미지를 추가 합니다.

> [!NOTE]
> 이 메서드를 별도로 마켓플레이스 항목을 만들 해야 합니다.

이미지를 Blob 저장소 URI에서 참조할 수 있어야 합니다. VHD 형식 (하지 VHDX) Windows 또는 Linux 운영 체제 이미지를 준비 하 고 이미지를 Azure 또는 Azure 스택의 저장소 계정에 업로드 합니다. 이미지에 이미 Azure 또는 Azure 스택에서 Blob 저장소에 업로드, 1 단계를 건너뛸 수 있습니다.

1. [리소스 관리자 배포에 대 한 Windows VM 이미지를 Azure에 업로드](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) 또는 Linux 이미지에 대 한에 설명 된 지침에 따라 [배포 Linux 가상 컴퓨터 Azure 스택에](azure-stack-linux.md)합니다. 이미지를 업로드 하기 전에 다음 사항을 고려해 야 할 주요은:

   * Azure 스택 VHD 형식의 고정된 디스크를 지원합니다. 고정된 형식은 디스크 오프셋 X가 blob 오프셋 X에 저장 되므로 파일 내에 선형적으로 논리적 디스크를 구조 합니다. Blob 끝에는 작은 바닥글에서는 VHD의 속성을 설명합니다. 디스크의 해결 여부를 확인 하려면 사용 하 여는 [GET-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell 명령입니다.  

    > [!IMPORTANT]
   >  Azure 스택 동적 디스크 Vhd를 지원 하지 않습니다. 동적 디스크는 VM에 연결 하는 크기 조정 실패 상태에 VM이 종료 됩니다. 이 문제를 해결 하려면 VM의 디스크를 저장소 계정에 VHD blob을 삭제 하지 않고 VM을 삭제 합니다. 동적 디스크에서 VHD 고정된 디스크와 가상 컴퓨터를 다시 만들를 변환 합니다.

   * 더 적은 시간이 이미지 스택 Azure 이미지 리포지토리에 푸시를 사용 하기 때문에 Azure Blob 저장소에 보다 Azure 스택 Blob 저장소에 이미지를 업로드 하려면 더 효율적입니다.

   * 업로드 하는 경우는 [Windows VM 이미지](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), 대체 해야는 **Azure에 로그인** 단계와 함께 [Azure 스택 운영자의 PowerShell 환경을 구성](azure-stack-powershell-configure-admin.md) 단계입니다.  

   * Blob 저장소 URI 이미지를 업로드 하기를 기록해 둡니다. Blob 저장소 URI 형식은 다음과 같습니다: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd 합니다.

   * Blob를 익명으로 액세스할 수 있도록로 저장소 계정 blob 컨테이너는 VM 이미지의 VHD 업로드 되었습니다. 선택 **Blob**를 선택한 후 **액세스 정책**합니다. 필요에 따라 대신는 컨테이너에 대 한 공유 액세스 서명 생성 및 blob URI의 일부분으로 포함 수 있습니다.

   ![저장소 계정 blob로 이동](./media/azure-stack-add-vm-image/image1.png)

   ![Public에 대 한 집합 blob 액세스](./media/azure-stack-add-vm-image/image2.png)

2. 연산자와 Azure 스택에 로그인 합니다. 메뉴에서 선택 **더 많은 서비스** > **리소스 공급자**합니다. 그런 다음 선택 **계산** > **VM 이미지** > **추가**합니다.

3. 아래 **VM 이미지를 추가할**, 게시자, 제품, SKU, 가상 컴퓨터 이미지의 버전을 입력 합니다. 이러한 이름 세그먼트는 리소스 관리자 템플릿에서 VM 이미지를 참조 하십시오. 선택 하는 **osType** 올바르게 값입니다. 에 대 한 **OS 디스크 Blob URI**를 이미지 업로드 된 Blob URI를 입력 합니다. 그런 다음 선택 **만들기** VM 이미지 만들기를 시작할 수 있습니다.

   ![이미지를 만들기 시작 합니다.](./media/azure-stack-add-vm-image/image4.png)

   이미지는 성공적으로 만들어지면로 VM 이미지 상태 변경 **Succeeded**합니다.

4. 가상 컴퓨터 이미지 보다 쉽게를 사용 하려면 사용자가 사용할 수 있도록 UI에는 것이 좋습니다를 [마켓플레이스 항목을 만듭니다.](azure-stack-create-and-publish-marketplace-item.md)합니다.

## <a name="next-steps"></a>다음 단계

[가상 머신 프로비전](azure-stack-provision-vm.md)
