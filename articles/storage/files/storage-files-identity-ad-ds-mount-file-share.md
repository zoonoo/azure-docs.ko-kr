---
title: Azure 파일 공유를 AD DS에 가입 된 VM에 탑재
description: 온-프레미스 Active Directory Domain Services 가입 컴퓨터에 파일 공유를 탑재 하는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 9807563c768b82c823ff754aaa679ddc917bf62d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87535062"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>4 부: 도메인에 가입 된 VM에서 파일 공유 탑재

이 문서를 시작 하기 전에 이전 문서인 [SMB를 통해 디렉터리 및 파일 수준 권한 구성](storage-files-identity-ad-ds-configure-permissions.md)문서를 완료 해야 합니다.

이 문서에서 설명 하는 프로세스는 파일 공유 및 액세스 권한이 올바르게 설정 되었는지 확인 하 고 도메인에 가입 된 VM에서 Azure 파일 공유에 액세스할 수 있는지 확인 합니다. 공유 수준 Azure 역할 할당은 적용 되는 데 다소 시간이 걸릴 수 있습니다. 

다음 그림에 표시 된 것 처럼 권한이 부여 된 자격 증명을 사용 하 여 클라이언트에 로그인 합니다.

![사용자 인증용 Azure AD 로그인 화면이 표시된 스크린샷](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>탑재 필수 구성 요소

파일 공유를 탑재 하려면 먼저 다음 필수 구성 요소를 확인 해야 합니다.

- 이전에 저장소 계정 키를 사용 하 여 파일 공유를 탑재 한 클라이언트에서 파일 공유를 탑재 하는 경우 공유의 연결을 끊고, 저장소 계정 키의 영구 자격 증명을 제거 하 고, 현재 인증에 AD DS 자격 증명을 사용 하 고 있는지 확인 합니다.
- 클라이언트는 AD DS에 대 한 시야를가지고 있어야 합니다. 사용자의 컴퓨터 또는 VM이 AD DS에서 관리 하는 네트워크를 벗어난 경우 인증을 위해 VPN AD DS에 도달 하려면 VPN을 사용 하도록 설정 해야 합니다.

자리 표시자 값을 고유한 값으로 바꾼 후 다음 명령을 사용 하 여 Azure 파일 공유를 탑재 합니다.

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

AD DS 자격 증명으로 탑재 하는 데 문제가 발생 하는 경우 지침은 [AD 자격 증명을 사용 하 여 Azure Files를 탑재할 수 없음](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) 을 참조 하세요.

파일 공유를 탑재 하는 데 성공 하면 Azure 파일 공유에 대 한 온-프레미스 AD DS 인증을 사용 하도록 설정 하 고 구성 했습니다.

## <a name="next-steps"></a>다음 단계

저장소 계정을 나타내는 AD DS에서 만든 id가 암호 순환을 적용 하는 도메인 또는 OU에 있는 경우 암호 업데이트에 대 한 지침은 다음 문서를 계속 진행 합니다.

[AD DS에서 저장소 계정 id의 암호를 업데이트 합니다.](storage-files-identity-ad-ds-update-password.md)
