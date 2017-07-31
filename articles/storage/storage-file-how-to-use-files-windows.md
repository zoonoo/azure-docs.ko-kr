---
title: "Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스 | Microsoft Docs"
description: "Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스합니다."
services: storage
documentationcenter: na
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
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: e911e787cd1e29b2bbeaa648869c50245f2dd9ba
ms.contentlocale: ko-kr
ms.lasthandoff: 07/22/2017

---

# <a name="mount-an-azure-file-share-and-access-the-share-in-windows"></a>Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스
[Azure File Storage](storage-dotnet-how-to-use-files.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 Windows 및 Windows Server에 탑재할 수 있습니다. 이 문서에서는 세 가지 방법, 즉 파일 탐색기 UI, PowerShell 및 명령 프롬프트를 사용하여 Windows에 Azure File 공유를 탑재합니다. 

온-프레미스 또는 다른 Azure 지역에서 호스팅되는 Azure 지역의 외부에 Azure 파일 공유를 탑재하려면 OS에서 SMB 3.0을 지원해야 합니다. 

Azure 파일 공유는 OS 버전에 따라 온-프레미스 또는 Azure VM의 Windows 컴퓨터에 탑재할 수 있습니다. 다음 표에서 자세히 보여 줍니다. 

| Windows 버전        | SMB 버전 |Azure VM에 탑재 가능|온-프레미스에 탑재 가능|
|------------------------|-------------|---------------------|---------------------|
| 윈도우 7              | SMB 2.1     | 예                 | 아니요                  |
| Windows Server 2008 R2 | SMB 2.1     | 예                 | 아니요                  |
| Windows 8              | SMB 3.0     | 예                 | 예                 |
| Windows Server 2012    | SMB 3.0     | 예                 | 예                 |
| Windows Server 2012 R2 | SMB 3.0     | 예                 | 예                 |
| Windows 10             | SMB 3.0     | 예                 | 예                 |

> [!Note]  
> 사용자의 Windows 버전에 대해 가장 최근의 KB를 선택하는 것이 좋습니다.

## <a name="aprerequisites-for-mounting-azure-file-share-with-windows"></a></a>Windows에 Azure 파일 공유를 탑재하기 위한 필수 구성 요소 
* **저장소 계정 이름**: Azure 파일 공유를 탑재하려면 저장소 계정의 이름이 필요합니다.

* **저장소 계정 키**: Azure File 공유를 탑재하려면 기본(또는 보조) 저장소 키가 필요합니다. SAS 키는 현재 탑재를 지원하지 않습니다.

* **445 포트가 열려 있는지 확인**: Azure File Storage는 SMB 프로토콜을 사용합니다. SMB는 445 TCP 포트를 통해 통신합니다. 클라이언트 컴퓨터에서 방화벽이 445 TCP 포트를 차단하고 있지 않은지 확인합니다.

## <a name="mount-the-azure-file-share-with-file-explorer"></a>파일 탐색기를 통해 Azure 파일 공유 탑재
> [!Note]  
> 다음 지침은 Windows 10에서 보여 주는 것이며, 이전 릴리스와 다를 수 있습니다. 

1. **파일 탐색기를 엽니다**. 이 작업은 [시작] 메뉴에서 열거나 Win+E 바로 가기 키를 눌러 수행할 수 있습니다.

2. **창 왼쪽에 있는 "이 PC" 항목으로 이동합니다. 이렇게 하면 리본에서 사용할 수 있는 메뉴가 변경됩니다. [컴퓨터] 메뉴 아래에서 "네트워크 드라이브 연결"을 선택합니다**.
    
    !["네트워크 드라이브 연결" 드롭다운 메뉴의 스크린샷](media/storage-file-how-to-use-files-windows/1_MountOnWindows10.png)

3. **Azure Portal의 "연결" 창에서 UNC 경로 복사합니다**. 이 정보를 찾는 방법에 대한 자세한 설명은 [여기서](storage-file-how-to-use-files-portal.md#connect-to-file-share) 찾을 수 있습니다.

    ![Azure File Storage 연결 창의 UNC 경로](media/storage-file-how-to-use-files-windows/portal_netuse_connect.png)

4. **드라이브 문자를 선택하고 UNC 경로를 입력합니다.** 
    
    !["네트워크 드라이브 연결" 대화 상자의 스크린샷](media/storage-file-how-to-use-files-windows/2_MountOnWindows10.png)

5. **사용자 이름으로 `Azure\`가 앞에 붙은 저장소 계정 이름을 사용하고, 암호로 저장소 계정 키를 사용합니다.**
    
    ![네트워크 자격 증명 대화 상자의 스크린샷](media/storage-file-how-to-use-files-windows/3_MountOnWindows10.png)

6. **Azure 파일 공유를 원하는 대로 사용합니다**.
    
    ![현재 탑재된 Azure 파일 공유](media/storage-file-how-to-use-files-windows/4_MountOnWindows10.png)

7. **Azure 파일 공유를 분리(또는 연결 해제)할 준비가 되면 파일 탐색기의 "네트워크 위치" 아래에서 공유 항목을 마우스 오른쪽 단추로 클릭하고 "연결 해제"를 선택하여 Azure 파일 공유를 탑재 해제할 수 있습니다**.

## <a name="mount-the-azure-file-share-with-powershell"></a>PowerShell을 통해 Azure 파일 공유 탑재
1. **다음 명령을 사용하여 Azure 파일 공유를 탑재합니다**. `<storage-account-name>`, `<share-name>`, `<storage-account-key>`, `<desired-drive-letter>`를 적절한 정보로 바꿉니다.

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **Azure 파일 공유를 원하는 대로 사용합니다**.

3. **완료되면 다음 명령을 사용하여 Azure 파일 공유를 연결 해제합니다**.

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> Azure 파일 공유를 탑재하는 동안 나머지 OS에서 볼 수 있도록 `New-PSDrive`에서 `-Persist` 매개 변수를 사용할 수 있습니다.

## <a name="mount-the-azure-file-share-with-command-prompt"></a>명령 프롬프트를 통해 Azure 파일 공유 탑재
1. **다음 명령을 사용하여 Azure 파일 공유를 탑재합니다**. `<storage-account-name>`, `<share-name>`, `<storage-account-key>`, `<desired-drive-letter>`를 적절한 정보로 바꿉니다.

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **Azure 파일 공유를 원하는 대로 사용합니다**.

3. **완료되면 다음 명령을 사용하여 Azure 파일 공유를 연결 해제합니다**.

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> Windows에서 자격 증명을 유지하여 다시 부팅할 때 Azure 파일 공유가 자동으로 다시 연결되도록 구성할 수 있습니다. 자격 증명을 유지하기 위한 명령은 다음과 같습니다.
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## <a name="next-steps"></a>다음 단계
Azure 파일 저장소에 대한 자세한 내용은 다음 링크를 참조합니다.

* [FAQ](storage-files-faq.md)
* [문제 해결](storage-troubleshoot-file-connection-problems.md)

### <a name="conceptual-articles-and-videos"></a>개념 문서 및 비디오
* [Azure File Storage: 원활한 Windows 및 Linux용 클라우드 SMB 파일 시스템(영문)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Linux에서 Azure File Storage 사용 방법](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-azure-file-storage"></a>Azure File Storage용 도구 지원
* [Azure 저장소와 함께 Azure PowerShell 사용](storage-powershell-guide-full.md)
* [Microsoft Azure 저장소와 함께 AzCopy를 사용하는 방법](storage-use-azcopy.md)
* [Azure 저장소에서 Azure CLI 사용](storage-azure-cli.md#create-and-manage-file-shares)
* [Azure File Storage 문제 해결](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="blog-posts"></a>블로그 게시물
* [Azure 파일 저장소 일반적으로 사용 가능(영문)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure File Storage 내부 구조(영문)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure 파일 서비스 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Azure 파일로 데이터 마이그레이션(영문)](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>참조
* [Storage Client Library for .NET 참조](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [파일 서비스 REST API 참조](http://msdn.microsoft.com/library/azure/dn167006.aspx)

