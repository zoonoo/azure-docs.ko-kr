---
title: "PowerShell을 사용하여 Azure File storage를 관리하는 방법 | Microsoft Docs"
description: "PowerShell을 사용하여 Azure File Storage를 관리하는 방법을 알아봅니다."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 148375b156c4ae1aa4bf203d215f7ed607a71b89
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-use-powershell-to-manage-azure-file-storage"></a>PowerShell을 사용하여 Azure File storage를 관리하는 방법
Azure PowerShell을 사용하여 파일 공유를 만들고 관리할 수 있습니다.

## <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Azure 저장소에 대한 PowerShell cmdlet 설치
PowerShell 사용을 준비하려면 Azure PowerShell cmdlet을 다운로드하여 설치합니다. 설치 지점 및 설치 지침에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs) 을 참조하세요.

> [!NOTE]
> 최신 Azure PowerShell 모듈을 다운로드하여 설치하거나 최신 모듈로 업그레이드하는 것이 좋습니다.
> 
> 

**시작**을 클릭하고 **Windows PowerShell**을 입력하여 Azure PowerShell 창을 엽니다. PowerShell 창에 Azure Powershell 모듈이 로드됩니다.

## <a name="create-a-context-for-your-storage-account-and-key"></a>저장소 계정 및 키에 대한 컨텍스트 만들기
저장소 계정 컨텍스트를 만듭니다. 이 컨텍스트는 저장소 계정 이름 및 계정 키를 캡슐화합니다. [Azure Portal](https://portal.azure.com)에서 계정 키를 복사하는 방법에 대한 지침은 [저장소 액세스 키 보기 및 복사](storage-create-storage-account.md#view-and-copy-storage-access-keys)를 참조하세요.

다음 예제에서 `storage-account-name` 및 `storage-account-key`을(를) 본인의 저장소 계정 이름 및 키로 바꿉니다.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

## <a name="create-a-new-file-share"></a>새 파일 공유 만들기
`logs`라는 새 공유를 만듭니다.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

이제 파일 저장소에 파일 공유가 있습니다. 다음에는 디렉터리와 파일을 추가할 것입니다.

> [!IMPORTANT]
> 파일 공유의 이름은 모두 소문자여야 합니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](https://msdn.microsoft.com/library/azure/dn167011.aspx)를 참조하세요.
> 
> 

## <a name="create-a-directory-in-the-file-share"></a>파일 공유에 디렉터리 만들기
공유에 디렉터리를 만듭니다. 다음 예제에서 디렉터리 이름은 `CustomLogs`입니다.

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

## <a name="upload-a-local-file-to-the-directory"></a>디렉터리에 로컬 파일 업로드
이제 디렉터리에 로컬 파일을 업로드합니다. 다음 예제에서는 `C:\temp\Log1.txt`에서 파일을 업로드합니다. 로컬 컴퓨터의 유효한 파일을 가리키도록 파일 경로를 편집합니다.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

## <a name="list-the-files-in-the-directory"></a>디렉터리의 파일 나열
디렉터리의 파일을 보려면 디렉터리의 파일을 모두 나열할 수 있습니다. 이 명령은 CustomLogs 디렉터리에서 파일 및 하위 디렉터리(있는 경우)를 반환합니다.

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile은 디렉터리 개체가 전달되는 파일 및 디렉터리의 목록을 반환합니다. "Get-AzureStorageFile -Share $s"는 루트 디렉터리에 파일 및 디렉터리의 목록을 반환합니다. 하위 디렉터리에 있는 파일의 목록을 가져오려면 Get-AzureStorageFile에 하위 디렉터리를 전달해야 합니다. 즉, 파이프에 대한 명령의 첫 번째 부분은 CustomLogs 하위 디렉터리의 디렉터리 인스턴스를 반환하는 기능을 갖습니다. 그런 다음 Get-AzureStorageFile에 전달되고 이는 CustomLogs에 파일 및 디렉터리를 반환합니다.

## <a name="copy-files"></a>파일 복사
Azure PowerShell 버전 0.9.7부터 파일을 다른 파일로, 파일을 Blob으로 또는 Blob을 파일로 복사할 수 있습니다. 아래에는 PowerShell Cmdlet을 사용하여 이러한 복사 작업을 수행하는 방법이 나와 있습니다.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```
## <a name="next-steps"></a>다음 단계
Azure 파일 저장소에 대한 자세한 내용은 다음 링크를 참조합니다.

* [FAQ](storage-files-faq.md)
* [문제 해결](storage-troubleshoot-file-connection-problems.md)
