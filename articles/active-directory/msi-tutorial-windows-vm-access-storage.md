---
title: "Windows VM MSI를 사용하여 Azure Storage 액세스"
description: "Windows VM MSI(관리 서비스 ID)를 사용하여 Azure Storage에 액세스하는 프로세스를 안내하는 자습서입니다."
services: active-directory
documentationcenter: 
author: elkuzmen
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 09d4f81b190329421fc9fd2ebf98b941cb033a08
ms.contentlocale: ko-kr
ms.lasthandoff: 09/20/2017

---

# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage"></a>Windows VM 관리 서비스 ID를 사용하여 Azure Storage에 액세스

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Windows 가상 컴퓨터에 대해 MSI(관리 서비스 ID)를 사용하도록 설정한 다음 해당 ID를 사용하여 저장소 키에 액세스하는 방법을 보여 줍니다. Storage SDK를 사용하는 등의 Storage 작업을 수행할 때 일반적인 방식으로 저장소 키를 사용할 수 있습니다. 이 자습서의 경우 Azure Storage PowerShell을 사용하여 Blob을 업로드하고 다운로드합니다. 다음 방법을 알게 됩니다.


> [!div class="checklist"]
> * Windows 가상 컴퓨터에서 MSI를 사용하도록 설정 
> * Resource Manager의 저장소 키에 대한 VM 액세스 권한 부여 
> * VM ID를 사용하여 액세스 토큰을 가져오고 Resource Manager에서 저장소 키를 검색하는 데 사용 


Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>새 리소스 그룹에 Windows 가상 컴퓨터 만들기

이 자습서에서는 새 Windows VM을 만듭니다. 기존 VM에서 MSI를 사용하도록 설정할 수도 있습니다.

1.  Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.
2.  **계산**을 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다. 
3.  가상 컴퓨터 정보를 입력합니다. 여기서 만드는 **사용자 이름** 및 **암호**는 가상 컴퓨터에 로그인하는 데 사용하는 자격 증명입니다.
4.  드롭다운에서 가상 컴퓨터의 적절한 **구독**을 선택합니다.
5.  가상 컴퓨터를 만들 새 **리소스 그룹**을 선택하려면 **새로 만들기**를 선택합니다. 완료되면 **확인**을 클릭합니다.
6.  VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다. 설정 블레이드에서 기본값을 그대로 유지하고 **확인**을 클릭합니다.

    ![대체 이미지 텍스트](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>VM에서 MSI를 사용하도록 설정

가상 컴퓨터 MSI를 사용하면 코드에 자격 증명을 포함하지 않고도 Azure AD에서 액세스 토큰을 가져올 수 있습니다. MSI를 사용하도록 설정하는 경우 내부적으로는 두 가지 작업이 수행됩니다. 즉, VM에 MSI VM 확장이 설치되고 가상 컴퓨터에 대해 MSI가 사용하도록 설정됩니다.  

1. 새 가상 컴퓨터의 리소스 그룹을 찾고 이전 단계에서 만든 가상 컴퓨터를 선택합니다.
2. 왼쪽에 있는 VM 설정에서 **구성**을 클릭합니다.
3. MSI를 등록하고 사용하도록 설정하려면 **예**를 선택하고, 사용하지 않도록 설정하려면 아니요를 선택합니다.
4. **저장**을 클릭하여 구성을 저장합니다.

    ![대체 이미지 텍스트](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. VM에 있는 확장을 확인하려면 **확장**을 클릭합니다. MSI가 사용하도록 설정된 경우 목록에 **ManagedIdentityExtensionforWindows**가 표시됩니다.

    ![대체 이미지 텍스트](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>저장소 계정 만들기 

저장소 계정이 아직 없는 경우 이제 하나 만들게 됩니다. 또한 이 단계를 건너뛰고 기존 저장소 계정의 키에 대한 VM MSI 액세스 권한을 부여할 수 있습니다. 

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.
2. **저장소**를 클릭하면 **저장소 계정**, 새 “저장소 계정 만들기” 패널이 표시됩니다.
3. 나중에 사용할 저장소 계정에 대한 이름을 입력합니다.  
4. **배포 모델** 및 **계정 종류**는 각각 “리소스 관리자” 및 “범용”으로 설정해야 합니다. 
5. **구독** 및 **리소스 그룹**은 이전 단계에서 VM을 만들 때 지정한 것과 일치합니다.
6. **만들기**를 클릭합니다.

    ![새 저장소 계정 만들기](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>저장소 계정에 Blob 컨테이너 만들기

나중에 새 저장소 계정에 파일을 업로드 및 다운로드합니다. 파일은 Blob Storage가 필요하기 때문에 파일을 저장할 Blob 컨테이너를 만들어야 합니다.

1. 새로 만든 저장소 계정으로 다시 이동합니다.
2. 왼쪽 탐색 모음에서 “Blob service” 아래에 있는 **컨테이너** 링크를 클릭합니다.
3. 페이지 맨 위에 있는 **+ 컨테이너**를 클릭하면 “새 컨테이너” 패널이 나타납니다.
4. 컨테이너에 이름을 지정하고 액세스 수준을 선택한 다음 **확인**을 클릭합니다. 지정한 이름은 이 자습서의 뒷부분에 사용됩니다. 

    ![저장소 컨테이너 만들기](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>VM ID에 저장소 키 사용 권한 부여 

Azure Storage는 Azure AD 인증을 기본적으로 지원하지 않습니다.  그러나 MSI를 사용하여 Resource Manager에서 저장소 키를 검색하고, 해당 키를 사용하여 저장소에 액세스할 수 있습니다.  이 단계에서는 저장소 계정에 키에 대한 VM MSI 액세스를 부여합니다.   

1. **저장소**의 탭으로 이동합니다.  
2. 앞에서 만든 **저장소 계정**을 선택합니다.   
3. 왼쪽 패널의 **액세스 제어(IAM)**로 이동합니다.  
4. 그런 다음 VM에 대해 새 역할 할당을 **추가**하고 **저장소 계정 키 운영자 서비스 역할**로 **역할**을 선택합니다.  
5. 다음 드롭다운에서 **가상 컴퓨터** 리소스에 대한 **액세스를 할당**합니다.  
6. 그런 다음 **구독** 드롭다운에 적절한 구독이 나열되어 있는지 확인합니다. 그리고 **리소스 그룹**에서는 **모든 리소스 그룹**을 선택합니다.  
7. 마지막으로 **선택**의 드롭다운에서 Windows 가상 컴퓨터를 선택하고 **저장**을 클릭합니다. 

    ![대체 이미지 텍스트](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>VM ID를 사용하여 액세스 토큰을 가져온 다음 Azure Resource Manager를 호출하는 데 사용 

이 부분에서는 Azure Resource Manager **PowerShell**을 사용해야 합니다.  설치하지 않은 경우 계속하기 전에 [최신 버전을 다운로드](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1)합니다.

1. Portal에서 **Virtual Machines** -> Windows Virtual Machines로 이동한 다음 **개요**에서 **연결**을 클릭합니다. 
2. Windows VM을 만들 때 추가한 **사용자 이름**과 **암호**를 입력합니다. 
3. 이제 가상 컴퓨터에 대한 **원격 데스크톱 연결**을 만들었으므로 원격 세션에서 **PowerShell**을 엽니다. 
4. Powershell의 Invoke-WebRequest를 사용하여 로컬 MSI 끝점에 대한 요청을 수행해 Azure Resource Manager용 액세스 토큰을 가져옵니다.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > "resource" 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Resource Manager 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다.
    
    다음으로 $response 개체에서 JSON(JavaScript Object Notation) 형식의 문자열로 저장된 전체 응답을 추출합니다. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    다음으로는 응답에서 액세스 토큰을 추출합니다.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-keys-from-azure-resource-manager-to-make-storage-calls"></a>저장소 호출을 위해 Azure Resource Manager에서 저장소 키 가져오기 

이전 섹션에서 검색했던 토큰 액세스를 사용하여 Resource Manager를 호출하여 저장소 액세스 키를 검색하는 데 PowerShell을 사용합니다. 저장소 액세스 키가 있으면 저장소 업로드/다운로드 작업을 호출할 수 있습니다.

```powershell
PS C:\> $keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> URL은 대/소문자를 구분하므로 앞서 리소스 그룹의 이름을 지정할 때 사용했던 것과 정확히 동일한 대/소문자를 사용해야 하며, “resourceGroups”와 같이 대문자 “G”를 사용해야 합니다. 

```powershell
PS C:\> $keysContent = $keysResponse.Content | ConvertFrom-Json
PS C:\> $key = $keysContent.keys[0].value
```

그런 다음 “test.txt”라는 파일을 만듭니다. 저장소 키를 사용하여 Azure Storage PowerShell로 인증을 하고 Blob 컨테이너에 파일을 업로드한 다음 파일을 다운로드합니다.

```bash
echo "This is a test text file." > test.txt
```

> [!NOTE]
> 먼저 Azure Storage commandlet "Install-Module Azure.Storage"를 설치해야 합니다. 

`Set-AzureStorageBlobContent` PowerShell cmdlet을 사용하여 방금 만든 Blob을 업로드할 수 있습니다.

```powershell
PS C:\> $ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
PS C:\> Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

응답:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

`Get-AzureStorageBlobContent` PowerShell cmdlet을 사용하여 방금 업로드한 Blob을 다운로드할 수 있습니다.

```powershell
PS C:\> Get-AzureStorageBlobContent -Blob <blob name> -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

응답:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```





