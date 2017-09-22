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
ms.date: 09/14/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 86d88e3d44f442171f69d0baea5e7d689b963277
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---

# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-storage"></a>Windows VM MSI(관리 서비스 ID)를 사용하여 Azure Storage 액세스

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Linux 가상 컴퓨터에 대해 MSI(관리 서비스 ID)를 사용하도록 설정한 다음 해당 ID를 사용하여 저장소 키에 액세스하는 방법을 보여 줍니다. Storage SDK를 사용하는 등의 Storage 작업을 수행할 때 일반적인 방식으로 저장소 키를 사용할 수 있습니다. 이 자습서에서는 Azure CLI를 사용하여 BLOB를 업로드하고 다운로드합니다. 다음 방법을 알게 됩니다.


> [!div class="checklist"]
> * Windows 가상 컴퓨터에서 MSI를 사용하도록 설정 
> * 새 저장소 계정 만들기
> * VM에 저장소 액세스 권한 부여 
> * VM ID를 사용하여 저장소 계정용 액세스 토큰 가져오기 


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

1. MSI를 사용하도록 설정할 **가상 컴퓨터**를 선택합니다.
2. 왼쪽 탐색 모음에서 **구성**을 클릭합니다.
3. **관리 서비스 ID**가 표시됩니다. MSI를 등록하고 사용하도록 설정하려면 **예**를 선택하고, 사용하지 않도록 설정하려면 아니요를 선택합니다.
4. **저장**을 클릭하여 구성을 저장합니다.

    ![대체 이미지 텍스트](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 이 **Windows VM**에 있는 확장을 확인하려면 **확장**을 클릭합니다. MSI가 사용하도록 설정된 경우 목록에 **ManagedIdentityExtensionforWindows**가 표시됩니다.

    ![대체 이미지 텍스트](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-new-storage-account"></a>새 저장소 계정 만들기 

저장소 작업을 수행할 때 일반적인 방식으로 저장소 키를 사용할 수 있습니다. 이 예제에서는 Azure CLI를 사용하여 BLOB를 업로드하고 다운로드하는 방법을 중점적으로 살펴봅니다. 

1. 사이드바로 이동하여 **저장소**를 선택합니다.  
2. 새 **저장소 계정**을 만듭니다.  
3. **배포 모델**에서 **Resource Manager** 및 **계정 종류**(**범용**)를 입력합니다.  
4. **구독** 및 **리소스 그룹**이 위 단계에서 **Linux 가상 컴퓨터**를 만들 때 사용했던 항목인지 확인합니다.

    ![대체 이미지 텍스트](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>VM ID에 저장소 키 사용 권한 부여 

코드는 MSI를 사용하여 Azure AD 인증을 지원하는 리소스에 인증하기 위한 액세스 토큰을 가져올 수 있습니다.   

1. **저장소**의 탭으로 이동합니다.  
2. 앞에서 만든 **저장소 계정**을 선택합니다.   
3. 왼쪽 패널의 **액세스 제어(IAM)**로 이동합니다.  
4. 그런 다음 VM에 대해 새 역할 할당을 **추가**하고 **저장소 계정 키 운영자 서비스 역할**로 **역할**을 선택합니다.  
5. 다음 드롭다운에서 **가상 컴퓨터** 리소스에 대한 **액세스를 할당**합니다.  
6. 그런 다음 **구독** 드롭다운에 적절한 구독이 나열되어 있는지 확인합니다. 그리고 **리소스 그룹**에서는 **모든 리소스 그룹**을 선택합니다.  
7. 마지막으로 **선택**의 드롭다운에서 Windows 가상 컴퓨터를 선택하고 **저장**을 클릭합니다. 

    ![대체 이미지 텍스트](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>VM ID를 사용하여 액세스 토큰을 가져온 다음 Azure Resource Manager를 호출하는 데 사용 

이 부분에서는 **PowerShell**을 사용해야 합니다.  PowerShell이 설치되어 있지 않으면 [여기](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1)서 다운로드하세요. 

1. Portal에서 **Virtual Machines** -> Windows Virtual Machines로 이동한 다음 **개요**에서 **연결**을 클릭합니다. 
2. Windows VM을 만들 때 추가한 **사용자 이름**과 **암호**를 입력합니다. 
3. 이제 가상 컴퓨터에 대한 **원격 데스크톱 연결**을 만들었으므로 원격 세션에서 **PowerShell**을 엽니다. 
4. Powershell의 Invoke-WebRequest를 사용하여 로컬 MSI 끝점에 대한 요청을 수행해 Azure Resource Manager용 액세스 토큰을 가져옵니다.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost/50342/oauth2/token -Method GET -Body @resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > "resource" 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Resource Manager 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다.
    
    다음으로 $response 개체에서 JSON(JavaScript Object Notation) 형식의 문자열로 저장된 전체 응답을 추출합니다. 
    
    ```powershell
    $content = $repsonse.Content | ConvertFrom-Json
    ```
    다음으로는 응답에서 액세스 토큰을 추출합니다.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    마지막으로 액세스 토큰을 사용하여 Azure Resource Manager를 호출합니다. 또한 이 예제에서는 PowerShell의; Invoke-WebRequest를 사용하여 Azure Resource Manager를 호출한 다음 인증 헤더에 액세스 토큰을 포함합니다.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE]
    > URL은 대/소문자를 구분하므로 앞에서 리소스 그룹의 이름을 지정할 때 사용했던 것과 정확히 동일한 대/소문자를 사용해야 하며, "resourceGroup"과 같이 대문자 "G"를 사용해야 합니다.
    
## <a name="get-the-storage-keys-from-azure-resource-manager"></a>Azure Resource Manager에서 저장소 키 가져오기 

```powershell
PS C:\> $keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/97f51385-2edc-4b69-bed8-7778dd4cb761/resourceGroups/SKwan_Test/providers/Microsoft.Storage/storageAccounts/skwanteststorage/listKeys/?api-version=2016-12-01 -Method POST$ -Headers @{Authorization="Bearer $ARMToken"}
```

```powershell
PS C:\> $keysContent = $keysResponse.Content | ConvertFrom-Json
```

```powershell
PS C:\> $key = $keysContent.keys[0].value
```

**Azure CLI를 사용하여 업로드할 파일 만들기**

```bash
echo "This is a test text file." > test.txt
```

**Azure CLI를 사용하여 파일을 업로드하고 저장소 키를 사용하여 인증**

> [!NOTE]
> 먼저 Azure Storage commandlet "Install-Module Azure.Storage"를 설치해야 합니다. 

PowerShell 요청은 다음과 같습니다.


```powershell
PS C:\> $ctx = New-AzureStorageContext -StorageAccountName skwanteststorage -StorageAccountKey $key
PS C:\> Set-AzureStorageBlobContent -File test.txt -Container testcontainer -Blob testblob -Context $ctx
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

**Azure CLI를 사용하여 파일을 다운로드하고 저장소 키를 사용하여 인증**

PowerShell 요청은 다음과 같습니다.

```powershell
PS C:\> Get-AzureStorageBlobContent -Blob <blob name> -Container <container name> -Destination <file> -Context $ctx
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





