---
title: "Linux VM MSI를 사용하여 Azure Storage 액세스"
description: "Linux VM MSI(관리 서비스 ID)를 사용하여 Azure Storage에 액세스하는 프로세스를 안내하는 자습서입니다."
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
ms.openlocfilehash: 9fec51528d1203a7107558408ced42fa2b2b884a
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---


# <a name="use-managed-service-identity-msi-with-a-linux-vm-to-access-storage-credentials"></a>Linux VM에서 MSI(관리 서비스 ID)를 사용하여 Azure Storage 자격 증명 액세스

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Linux 가상 컴퓨터에 대해 MSI(관리 서비스 ID)를 사용하도록 설정한 다음 해당 ID를 사용하여 저장소 키에 액세스하는 방법을 보여 줍니다. Storage SDK를 사용하는 등의 Storage 작업을 수행할 때 일반적인 방식으로 저장소 키를 사용할 수 있습니다. 이 자습서에서는 Azure CLI를 사용하여 BLOB를 업로드하고 다운로드합니다. 다음 방법을 알게 됩니다.


> [!div class="checklist"]
> * Linux 가상 컴퓨터에서 MSI를 사용하도록 설정 
> * 새 저장소 계정 만들기
> * VM에 저장소 액세스 권한 부여
> * VM ID를 사용하여 저장소 계정용 액세스 토큰을 가져와 저장소 키 액세스 


Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>새 리소스 그룹에 Linux 가상 컴퓨터 만들기

이 자습서에서는 새 Linux VM을 만듭니다. 기존 VM에서 MSI를 사용하도록 설정할 수도 있습니다.

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.
2. **계산**을 선택한 후 **Ubuntu Server 16.04 LTS**를 선택합니다.
3. 가상 컴퓨터 정보를 입력합니다. **인증 유형**으로 **SSH 공용 키** 또는 **암호**를 선택합니다. 생성된 자격 증명을 사용하여 VM에 로그인할 수 있습니다.

    ![대체 이미지 텍스트](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 드롭다운에서 가상 컴퓨터의 **구독**을 선택합니다.
5. 가상 컴퓨터를 만들 새 **리소스 그룹**을 선택하려면 **새로 만들기**를 선택합니다. 완료되면 **확인**을 클릭합니다.
6. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 지원되는 디스크 형식 필터를 변경합니다. 설정 블레이드에서 기본값을 그대로 유지하고 **확인**을 클릭합니다.

## <a name="enable-msi-on-your-vm"></a>VM에서 MSI를 사용하도록 설정

가상 컴퓨터 MSI를 사용하면 코드에 자격 증명을 포함하지 않고도 Azure AD에서 액세스 토큰을 가져올 수 있습니다. MSI를 사용하도록 설정하는 경우 내부적으로는 두 가지 작업이 수행됩니다. 즉, VM에 MSI VM 확장이 설치되고 VM에 대해 관리 서비스 ID가 사용하도록 설정됩니다.  

1. MSI를 사용하도록 설정할 **가상 컴퓨터**를 선택합니다.
2. 왼쪽 탐색 모음에서 **구성**을 클릭합니다.
3. **관리 서비스 ID**가 표시됩니다. MSI를 등록하고 사용하도록 설정하려면 **예**를 선택하고, 사용하지 않도록 설정하려면 아니요를 선택합니다.
4. **저장**을 클릭하여 구성을 저장합니다.

    ![대체 이미지 텍스트](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 이 **Linux VM**에 있는 확장을 확인하려면 **확장**을 클릭합니다. MSI가 사용하도록 설정된 경우 목록에 **ManagedIdentityExtensionforLinux**가 표시됩니다.

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
7. 마지막으로 **선택**의 드롭다운에서 Linux 가상 컴퓨터를 선택하고 **저장**을 클릭합니다. 
    ![대체 이미지 텍스트](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>VM ID를 사용하여 액세스 토큰을 가져온 다음 Azure Resource Manager를 호출하는 데 사용

Bash 터미널을 사용해야 하며 [여기](https://msdn.microsoft.com/commandline/wsl/install_guide)서 Linux 배포를 선택하고 다운로드해야 합니다.


1. Portal에서 Linux 가상 컴퓨터로 이동한 다음 **개요**에서 **연결**을 클릭합니다. Bash를 사용하라는 메시지가 표시됩니다. 경고에 나와 있는 SSH 및 VM IP를 적어 두세요. 
2. Bash를 열어서 Bash에 연결합니다.  
3. 터미널에서 연결하려는 **SSH** 및 **VM**(예 "ssh admin@12.61.219.35 ")을 입력합니다.  
4. 그리고 나면 **Linux VM**을 만들 때 추가했던 **암호**를 입력하라는 메시지가 표시됩니다. 이제 정상적으로 로그인되어야 합니다.  
5. 그런 후에는 CURL을 통해 요청을 하여 로그인한 Linux VM용 인증 토큰을 가져올 수 있습니다. **Azure Resource Manager** 끝점은 https://management.azure.com입니다.  

    액세스 토큰에 대한 CURL 요청은 다음과 같습니다.
    
    ```bash
    curl --data "authority= https://login.microsoftonline.com/<TENANT ID>&&resource=https://management.azure.com/"  -H Metadata:true http://localhost:50432/oauth2/token   
    ```
    
    > [!NOTE]
    > 액세스를 요청하려는 리소스의 URL이 'https:<RESOURCE>/'와 같이 끝에 슬래시가 붙은 적절한 서식인지 확인합니다.
    
    ```powershell
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="the-curl-request-to-get-storage-keys-from-azure-resource-manager"></a>Azure Resource Manager에서 저장소 키를 가져오기 위한 CURL 요청  

> [!NOTE]
> URL의 텍스트는 대/소문자를 구분하므로 리소스 그룹을 적절하게 반영하는 대/소문자를 사용하는지 확인하세요. 또한 이 요청은 GET 요청이 아닌 POST 요청임을 기억해야 하며, -d(NULL일 수 있음)를 사용하여 길이 제한을 캡처하는 값을 전달해야 합니다.  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –request POST -d"" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

CURL 응답에서는 키 목록을 제공합니다.  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


업로드할 파일을 만듭니다. 이 파일은 생성한 컨테이너 내의 저장소 계정에 저장소 키와 함께 업로드할 수 있는 샘플 BLOB 파일입니다. 

Linux VM에서는 다음 명령을 사용하여 이 작업을 수행할 수 있습니다. 

```bash
echo "This is a test file." > test.txt
```
 그리고 나면 Azure CLI를 사용하여 파일을 업로드하고 저장소 키를 사용하여 인증을 할 수 있습니다.
 

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name 
                        [--account-name] 
                        [--account-key] 
```

응답: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

또한 Azure CLI를 사용하여 파일을 다운로드하고 저장소 키를 사용하여 인증을 할 수 있습니다. 

요청: 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         [--account-name]
                         [--account-key]  
```

응답: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-12T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="related-content"></a>관련 콘텐츠

MSI의 개요는 [관리 서비스 ID 개요](../active-directory/msi-overview.md)를 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.


