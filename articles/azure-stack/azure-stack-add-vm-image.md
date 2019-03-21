---
title: 추가 하 고 Azure Stack에 VM 이미지를 제거 | Microsoft Docs
description: 추가 하거나 조직의 사용자 지정 Windows 또는 Linux VM 이미지를 사용 하려면 테 넌 트를 제거 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: ccf3beaacd15ad7d3e9177614bb62b0050bd8d5c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109167"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>가상 머신 이미지를 Azure Stack에서 사용할 수 있도록

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack에서 사용할 수 있습니다 가상 머신 이미지를 사용자에 게 합니다. 이러한 이미지는 Azure Resource Manager 템플릿에서 사용할 수 있습니다. 추가할 수 있습니다도 Azure Marketplace ui Marketplace 항목으로 합니다. 전역 Azure Marketplace를 이미지 형식 또는 사용자 고유의 사용자 지정 이미지를 사용 합니다. 포털 또는 Windows PowerShell을 사용 하 여 이미지를 추가할 수 있습니다.

## <a name="add-a-vm-image-through-the-portal"></a>포털을 통해 VM 이미지 추가

> [!NOTE]  
> 이 메서드를 사용 하 여 별도로 마켓플레이스 항목을 만들어야 합니다.

이미지는 blob 저장소 URI에 의해 참조 될 수 있어야 합니다. Windows 또는 Linux 운영 체제 이미지를 VHD 형식 (VHDX 아님)를 준비 하 고 Azure 또는 Azure Stack 저장소 계정에 이미지를 업로드 합니다. 이미지는 이미 Azure 또는 Azure Stack에서 blob storage에 업로드 됩니다, 경우에 1 단계를 건너뛸 수 있습니다.

1. [Azure Resource Manager 배포에 대 한 Windows VM 이미지를 업로드](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) 또는 Linux 이미지에 설명 된 지침을 따릅니다 [Azure Stack의 배포 Linux virtual machines](azure-stack-linux.md)합니다. 이미지를 업로드 하기 전에 다음 사항을 고려해 야는:

   - Azure Stack 지원 세대만 고정된 디스크 VHD에서에서 VM 한 개 (1) 형식입니다. 고정 형식은 디스크 오프셋 X가 blob 오프셋 X에 저장 되도록 파일 내에 선형적으로 논리적 디스크를 구조입니다. Blob 끝의 작은 바닥글에서는 VHD의 속성을 설명합니다. 디스크는 고정 하는 경우를 확인 하려면 사용 합니다 [GET-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell 명령입니다.  

     > [!IMPORTANT]  
     >  Azure Stack에서 동적 디스크 Vhd를 지원 하지 않습니다. VM에 연결 된 동적 디스크 크기 조정 실패 상태의 VM 종료 됩니다. 이 문제를 완화 하려면 VM의 디스크를 저장소 계정에 VHD blob을 삭제 하지 않고 VM을 삭제 합니다. 동적 디스크에서 VHD 고정된 디스크와 가상 컴퓨터를 다시 만들를 변환 합니다.

   - Azure Stack 이미지 리포지토리에 이미지를 푸시 하려면 적은 시간이 걸리기 때문에 blob storage에서 Azure로 보다 Azure Stack blob storage에 이미지를 업로드 하는 것이 효율적 이며

   - 업로드 하는 경우는 [Windows VM 이미지](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/),으로 대체 해야 합니다 **Azure에 로그인** 단계를 함께 [Azure Stack 운영자의 PowerShell 환경을 구성](azure-stack-powershell-configure-admin.md) 단계입니다.  

   - Blob storage URI 이미지를 업로드 하는 위치를 기록해 둡니다. Blob 저장소 URI 형식은 다음과 같습니다: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd 합니다.

   - Blob에 익명으로 액세스할 수 있도록으로 저장소 계정 blob 컨테이너를 VM 이미지 VHD를 업로드 하는 위치입니다. 선택 **Blob**를 선택한 후 **액세스 정책**합니다. 필요에 따라 컨테이너에 대 한 공유 액세스 서명을 생성 하 고 blob URI의 일부로 포함 시킬 수 있습니다. 이 단계를 수행 하면 blob이 이미지를 추가 하기 위해 사용할 수 있습니다. Blob를 익명으로 액세스할 수 없는 경우 실패 한 상태의 VM 이미지에 만들어집니다.

     ![저장소 계정 blob으로 이동](./media/azure-stack-add-vm-image/image1.png)

     ![Public에 대 한 집합 blob 액세스](./media/azure-stack-add-vm-image/image2.png)

2. Operator 자격으로 Azure Stack에 로그인 합니다. 메뉴에서 선택 **모든 서비스** > **이미지** 아래에서 **계산** > **추가**합니다.

3. 아래 **이미지 만들기**OS 디스크, OS 유형, storage blob URI, 계정 형식, 이름, 구독, 리소스 그룹 위치를 입력 하 고 호스트 캐싱입니다. 그런 다음 선택 **만들기** VM 이미지를 만들기 시작 합니다.

   ![이미지를 만들기 시작 합니다.](./media/azure-stack-add-vm-image/image4.png)

   VM 이미지 상태 변경에 이미지를 성공적으로 만들어지면 **Succeeded**합니다.

4. 가상 머신 이미지를 더 쉽게 사용할 수 있도록 사용자 사용에 대 한 ui에서에 것이 좋습니다 [마켓플레이스 항목 만들기](azure-stack-create-and-publish-marketplace-item.md)합니다.

## <a name="remove-a-vm-image-through-the-portal"></a>포털을 통해 VM 이미지를 제거 합니다.

1. 관리 포털을 엽니다 [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external)합니다.

2. 선택 **Marketplace 관리**, 한 다음 삭제 하려는 VM을 선택 합니다.

3. **삭제**를 클릭합니다.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>PowerShell을 사용 하 여 Marketplace VM 이미지 추가

> [!Note]  
> 템플릿 및 PowerShell 배포만 사용할 수 있습니다 Azure 리소스 관리자에 대 한 이미지 기반 추가할 때 사용 합니다. 이미지를 사용할 수 있도록를 marketplace 항목으로 사용자는 문서의 단계를 사용 하 여 마켓플레이스 항목 게시 [만들기 및 마켓플레이스 항목 게시](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-and-publish-marketplace-item)

1. [Azure Stack 용 PowerShell 설치](azure-stack-powershell-install.md)합니다.  

2. 운영자로 서 Azure Stack에 로그인 합니다. 자세한 내용은 [운영자로 서 Azure Stack에 로그인](azure-stack-powershell-configure-admin.md)합니다.

3. 상승된 된 프롬프트를 사용 하 여 PowerShell을 열고 실행 합니다.

   ```PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
   ```

   합니다 **추가 AzsPlatformimage** cmdlet이 VM 이미지를 참조 하는 Azure Resource Manager 템플릿에서 사용 되는 값을 지정 합니다. 값은 다음과 같습니다.
   - **publisher**  
     예: `Canonical`  
     사용자 이미지를 배포할 때 사용 하는 VM 이미지의 게시자 이름 세그먼트입니다. 예로 **Microsoft**합니다. 이 필드에 공백 또는 기타 특수 문자가 포함 되지 않습니다.  
   - **offer**  
     예: `UbuntuServer`  
     사용자 VM 이미지를 배포할 때 사용 하는 VM 이미지의 제품 이름 세그먼트입니다. 예로 **WindowsServer**합니다. 이 필드에 공백 또는 기타 특수 문자가 포함 되지 않습니다.  
   - **sku**  
     예: `14.04.3-LTS`  
     사용자 VM 이미지를 배포할 때 사용 하는 VM 이미지의 SKU 이름 세그먼트입니다. 예로 **Datacenter2016**합니다. 이 필드에 공백 또는 기타 특수 문자가 포함 되지 않습니다.  
   - **version**  
     예: `1.0.0`  
     사용자 VM 이미지를 배포할 때 사용 하는 VM 이미지의 버전입니다. 이 버전은 형식  *\#.\#합니다. \#*. 예로 **1.0.0**합니다. 이 필드에 공백 또는 기타 특수 문자가 포함 되지 않습니다.  
   - **osType**  
     예: `Linux`  
     이미지의 osType 여야 **Windows** 하거나 **Linux**합니다.  
   - **OSUri**  
     예: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Blob 저장소 URI를 지정할 수 있습니다에 대 한는 `osDisk`합니다.  

     자세한 내용은 참조에 대 한 PowerShell 참조를 [추가 AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet 및 [새로 만들기-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) cmdlet.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>PowerShell을 사용 하 여 사용자 지정 VM 이미지를 Marketplace에 추가
 
1. [Azure Stack 용 PowerShell 설치](azure-stack-powershell-install.md)합니다.

   ```PowerShell  
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

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

2. 사용 하는 경우 **Active Directory Federation Services**, 다음 cmdlet을 사용 합니다.

   ```PowerShell
   # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAudience endpoint for your environment>"

   # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
   Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. 운영자로 서 Azure Stack에 로그인 합니다. 자세한 내용은 [운영자로 서 Azure Stack에 로그인](azure-stack-powershell-configure-admin.md)합니다.

4. 전역 Azure 또는 Azure Stack 사용자 지정 VM 이미지를 저장할 저장소 계정을 만듭니다. 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 Blob 업로드, 다운로드 및 나열](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)을 참조하세요.

5. Windows 또는 Linux 운영 체제 이미지를 VHD 형식 (VHDX 아님)를 준비, 저장소 계정에 이미지를 업로드 및 VM 이미지 PowerShell에서 검색할 수 있는 URI를 가져옵니다.  

   ```PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

6. (선택 사항) VM 이미지의 일부로 배열 데이터 디스크를 업로드할 수 있습니다. 새로 만들기-DataDiskObject cmdlet을 사용 하 여 데이터 디스크를 만듭니다. 관리자 권한 프롬프트에서 PowerShell을 열고 실행 합니다.

   ```PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
   ```

7. 상승된 된 프롬프트를 사용 하 여 PowerShell을 열고 실행 합니다.

   ```PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
   ```

    추가 AzsPlatformimage cmdlet 및 새 DataDiskObject cmdlet에 대 한 자세한 내용은 참조는 Microsoft PowerShell [모듈 설명서 Azure Stack 운영자](https://docs.microsoft.com/powershell/module/)합니다.

## <a name="remove-a-vm-image-by-using-powershell"></a>PowerShell을 사용 하 여 VM 이미지를 제거 합니다.

더 이상 가상 머신 이미지를 업로드 한 경우 다음 cmdlet을 사용 하 여 Marketplace에서 삭제할 수 없습니다.

1. [Azure Stack 용 PowerShell 설치](azure-stack-powershell-install.md)합니다.

2. 운영자로 서 Azure Stack에 로그인 합니다.

3. 상승된 된 프롬프트를 사용 하 여 PowerShell을 열고 실행 합니다.

   ```PowerShell  
   Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
   ```
   합니다 **제거 AzsPlatformImage** cmdlet이 VM 이미지를 참조 하는 Azure Resource Manager 템플릿에서 사용 되는 값을 지정 합니다. 값은 다음과 같습니다.
   - **publisher**  
     예: `Canonical`  
     사용자 이미지를 배포할 때 사용 하는 VM 이미지의 게시자 이름 세그먼트입니다. 예로 **Microsoft**합니다. 이 필드에 공백 또는 기타 특수 문자가 포함 되지 않습니다.  
   - **offer**  
     예: `UbuntuServer`  
     사용자 VM 이미지를 배포할 때 사용 하는 VM 이미지의 제품 이름 세그먼트입니다. 예로 **WindowsServer**합니다. 이 필드에 공백 또는 기타 특수 문자가 포함 되지 않습니다.  
   - **sku**  
     예: `14.04.3-LTS`  
     사용자 VM 이미지를 배포할 때 사용 하는 VM 이미지의 SKU 이름 세그먼트입니다. 예로 **Datacenter2016**합니다. 이 필드에 공백 또는 기타 특수 문자가 포함 되지 않습니다.  
   - **version**  
     예: `1.0.0`  
     사용자 VM 이미지를 배포할 때 사용 하는 VM 이미지의 버전입니다. 이 버전은 형식  *\#.\#합니다. \#*. 예로 **1.0.0**합니다. 이 필드에 공백 또는 기타 특수 문자가 포함 되지 않습니다.  
    
     제거-AzsPlatformImage cmdlet에 대 한 자세한 내용은 참조는 Microsoft PowerShell [모듈 설명서 Azure Stack 운영자](https://docs.microsoft.com/powershell/module/)합니다.

## <a name="next-steps"></a>다음 단계

[가상 머신 프로비전](azure-stack-provision-vm.md)
