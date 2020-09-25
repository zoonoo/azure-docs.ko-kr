---
title: 사용자가 파일 수준에서 수행할 수 있는 작업 제어-Azure 파일 공유
description: Azure 파일 공유에 대 한 온-프레미스 AD DS 인증에 대 한 Windows Acl 사용 권한을 구성 하는 방법을 알아봅니다. 세부적인 액세스 제어를 활용할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: de0f58b54f0cb5ad450949bb1a7b8744f081227d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320339"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>3 부: SMB를 통한 디렉터리 및 파일 수준 권한 구성 

이 문서를 시작 하기 전에 이전 문서를 완료 하 여 공유 수준 사용 권한이 있는지 확인 하는 [id에 공유 수준 권한 할당](storage-files-identity-ad-ds-assign-permissions.md) 을 완료 했는지 확인 합니다.

RBAC를 사용 하 여 공유 수준 권한을 할당 한 후에는 세분화 된 액세스 제어를 활용 하기 위해 루트, 디렉터리 또는 파일 수준에서 적절 한 Windows Acl을 구성 해야 합니다. RBAC 공유 수준 사용 권한은 사용자가 공유에 액세스할 수 있는지 여부를 결정 하는 상위 수준 게이트 키퍼로 간주 합니다. Windows Acl은 보다 세부적인 수준에서 작동 하 여 사용자가 디렉터리 또는 파일 수준에서 수행할 수 있는 작업을 결정 합니다. 사용자가 파일/디렉터리에 액세스 하려고 할 때 공유 수준 및 파일/디렉터리 수준 권한이 모두 적용 되므로 둘 중 하나에 차이가 있는 경우 가장 제한적인 항목만 적용 됩니다. 예를 들어 사용자가 파일 수준에서 읽기/쓰기 액세스 권한을가지고 있지만 공유 수준 에서만 읽을 수 있는 경우에는 해당 파일만 읽을 수 있습니다. 이는 반대로, 사용자가 공유 수준에서 읽기/쓰기 액세스 권한을가지고 있지만 파일 수준 에서만 읽을 수 있는 경우에도 마찬가지입니다. 단, 파일을 읽을 수만 있습니다.

## <a name="rbac-permissions"></a>RBAC 권한

다음 표에는이 구성과 관련 된 RBAC 권한이 나와 있습니다.


| 기본 제공 역할  | NTFS 권한  | 결과 액세스  |
|---------|---------|---------|
|스토리지 파일 데이터 SMB 공유 Reader | 모든 권한, 수정, 읽기, 쓰기, 실행 | 읽기 & 실행  |
|     |   읽기 |     읽기  |
|스토리지 파일 데이터 SMB 공유 Contributor  |  모든 권한    |  수정, 읽기, 쓰기, 실행 |
|     |  수정         |  수정    |
|     |  읽기 & 실행 |  읽기 & 실행 |
|     |  읽기           |  읽기    |
|     |  쓰기          |  쓰기   |
|스토리지 파일 데이터 SMB 공유 관리자 권한 Contributor | 모든 권한  |  수정, 읽기, 쓰기, 편집, 실행 |
|     |  수정          |  수정 |
|     |  읽기 & 실행  |  읽기 & 실행 |
|     |  읽기            |  읽기   |
|     |  쓰기           |  쓰기  |



## <a name="supported-permissions"></a>지원 되는 권한

Azure Files은 기본 및 고급 Windows Acl의 전체 집합을 지원 합니다. 공유를 탑재 한 다음 windows 파일 탐색기를 사용 하 여 windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 명령을 실행 하거나 [ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl) 을 사용 하 여 Azure 파일 공유의 디렉터리와 파일에서 windows acl을 보고 구성할 수 있습니다. 

수퍼유저 권한으로 Acl을 구성 하려면 도메인에 가입 된 VM의 저장소 계정 키를 사용 하 여 공유를 탑재 해야 합니다. 다음 섹션의 지침에 따라 명령 프롬프트에서 Azure 파일 공유를 탑재 하 고 Windows Acl을 구성 합니다.

파일 공유의 루트 디렉터리에는 다음과 같은 사용 권한이 포함 됩니다.

- BUILTIN\Administrators:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

|사용자|정의|
|---|---|
|BUILTIN\Administrators|온-프레미스 AD DS 환경의 도메인 관리자 인 모든 사용자입니다.
|BUILTIN\Users|AD의 기본 제공 보안 그룹입니다. 여기에는 기본적으로 NT Authority\authenticated users Users가 포함 됩니다. 기존 파일 서버의 경우 서버당 멤버 자격 정의를 구성할 수 있습니다. Azure Files에는 호스팅 서버가 없으므로 NT Authority\authenticated users Users와 동일한 사용자 집합을 BUILTIN\Users에 포함 합니다.|
|NT AUTHORITY\SYSTEM|파일 서버의 운영 체제에 대 한 서비스 계정입니다. 이러한 서비스 계정은 Azure Files 컨텍스트에서 적용 되지 않습니다. 하이브리드 시나리오에 대 한 Windows 파일 서버 환경과 일치 하도록 루트 디렉터리에 포함 되어 있습니다.|
|NT Authority\authenticated users 사용자|유효한 Kerberos 토큰을 가져올 수 있는 AD의 모든 사용자입니다.|
|CREATOR OWNER|디렉터리 또는 파일의 각 개체에는 해당 개체의 소유자가 있습니다. 해당 개체의 "CREATOR OWNER"에 할당 된 Acl이 있는 경우이 개체의 소유자 인 사용자에 게 ACL에서 정의한 개체에 대 한 권한이 있습니다.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>명령 프롬프트에서 파일 공유 탑재

Windows 명령을 사용 `net use` 하 여 Azure 파일 공유를 탑재 합니다. 다음 예제의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다. 파일 공유 탑재에 대 한 자세한 내용은 [Windows에서 Azure 파일 공유 사용](storage-how-to-use-files-windows.md)을 참조 하세요. 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Azure Files에 연결 하는 데 문제가 발생 하는 경우 [Windows에서 Azure Files 탑재 오류에 대해 게시 된 문제 해결 도구](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)를 참조 하세요. 또한 포트 445이 차단 될 때 시나리오를 해결 하기 위한 [지침](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) 을 제공 합니다. 

## <a name="configure-windows-acls"></a>Windows Acl 구성

저장소 계정 키를 사용 하 여 파일 공유를 탑재 한 후에는 Windows Acl (NTFS 사용 권한)을 구성 해야 합니다. Windows 파일 탐색기 또는 icacls 중 하나를 사용 하 여 Windows Acl을 구성할 수 있습니다.

AD DS id에 대해 구성 된 Windows Dacl이 있는 온-프레미스 파일 서버에 디렉터리 또는 파일이 있는 경우 Robocopy 또는 [Azure AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases)와 같은 기존의 파일 복사 도구를 사용 하 여 acl을 유지 하는 Azure Files으로 복사할 수 있습니다. 디렉터리와 파일이 Azure File Sync를 통해 Azure Files 하도록 계층화 된 경우 Acl은 해당 네이티브 형식으로 전달 되 고 유지 됩니다.

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Windows 파일 탐색기를 사용 하 여 Windows Acl 구성

Windows 파일 탐색기를 사용 하 여 루트 디렉터리를 포함 하 여 파일 공유 아래의 모든 디렉터리 및 파일에 대 한 모든 권한을 부여 합니다.

1. Windows 파일 탐색기를 열고 파일/디렉터리를 마우스 오른쪽 단추로 클릭 한 다음 **속성**을 선택 합니다.
1. **보안** 탭을 선택합니다.
1. **편집 ...을** 선택 합니다. 사용 권한을 변경 합니다.
1. 기존 사용자의 사용 권한을 변경 하거나 **추가 ...** 를 선택 하 여 새 사용자에 게 사용 권한을 부여할 수 있습니다.
1. 새 사용자 추가에 대 한 프롬프트 창에서 **선택할 개체 이름을 입력 하십시오** . 상자에 권한을 부여 하려는 대상 사용자 이름을 입력 하 고 **이름 확인** 을 선택 하 여 대상 사용자의 전체 UPN 이름을 찾습니다.
1.    **확인**을 선택합니다.
1.    **보안** 탭에서 새 사용자에 게 부여할 사용 권한을 모두 선택 합니다.
1.    **적용**을 선택합니다.

### <a name="configure-windows-acls-with-icacls"></a>Icacls로 Windows Acl 구성

루트 디렉터리를 비롯하여 파일 공유에 있는 모든 디렉터리와 파일에 대한 모든 권한을 부여하려면 다음 Windows 명령을 사용합니다. 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Icacls를 사용 하 여 Windows Acl을 설정 하는 방법 및 지원 되는 다양 한 권한 유형에 대 한 자세한 내용은 [icacls 명령줄 참조](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

기능을 사용 하도록 설정 하 고 구성 했으므로 이제 도메인에 가입 된 VM에서 Azure 파일 공유를 탑재 하는 다음 문서를 계속 진행 합니다.

[4 부: 도메인에 가입 된 VM에서 파일 공유 탑재](storage-files-identity-ad-ds-mount-file-share.md)
