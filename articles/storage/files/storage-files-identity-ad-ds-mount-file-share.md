---
title: AD DS에 조인된 VM에 Azure 파일 공유 탑재
description: 온-프레미스 Active Directory Domain Services에 조인된 머신에 파일 공유를 탑재하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: d71526fea23e3a440428266addfc497b1a89c63c
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117142"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>4부: 도메인 조인 VM에서 파일 공유 탑재

이 문서를 시작하기 전에 이전 문서인 [SMB에 대한 디렉터리 및 파일 수준 권한 구성](storage-files-identity-ad-ds-configure-permissions.md)을 완료하세요.

이 문서에 설명된 프로세스는 내 파일 공유 및 액세스 권한이 올바르게 설정되어 있는지 확인하고 도메인 조인 VM에서 Azure File 공유에 액세스할 수 있는지 확인합니다. 공유 수준 Azure 역할 할당은 적용하는 데 다소 시간이 걸릴 수 있습니다. 

다음 이미지와 같이 권한을 부여한 자격 증명을 사용하여 클라이언트에 로그인합니다.

![사용자 인증용 Azure AD 로그인 화면이 표시된 스크린샷](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="mounting-prerequisites"></a>필수 구성 요소 탑재

파일 공유를 탑재하려면 먼저 다음 필수 구성 요소를 확인해야 합니다.

- 이전에 내 스토리지 계정 키를 사용하여 파일 공유를 탑재한 클라이언트에서 파일 공유를 탑재하는 경우, 공유를 끊고 스토리지 계정 키의 영구 자격 증명을 제거했는지 확인하고, 현재 인증에 AD DS 자격 증명을 사용하고 있는지 확인합니다. 스토리지 계정 키를 사용하여 탑재된 공유를 삭제하는 지침은 [FAQ 페이지](./storage-files-faq.md#ad-ds--azure-ad-ds-authentication)를 참조하세요.
- 클라이언트가 AD DS에 연결되어 있어야 합니다. 내 머신 또는 VM이 AD DS에서 관리하는 네트워크를 벗어난 경우, 인증을 위해 AD DS에 도달하려면 VPN을 사용하도록 설정해야 합니다.

자리 표시자 값을 고유한 값으로 바꾼 후 다음 명령을 사용하여 Azure 파일 공유를 탑재합니다. 항상 아래 표시된 경로를 사용하여 탑재해야 합니다. 파일 탑재에 CNAME을 사용하는 것은 ID 기반 인증(AD DS 또는 Azure AD DS)에서 지원되지 않습니다.

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

AD DS 자격 증명으로 탑재하는 데 문제가 발생하는 경우 [AD 자격 증명을 사용하여 Azure Files를 탑재할 수 없음](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)을 참조하세요.

파일 공유를 탑재하는 데 성공하면 Azure 파일 공유를 위한 온-프레미스 AD DS 인증을 사용하고 구성하는 데 성공한 것입니다.

## <a name="next-steps"></a>다음 단계

스토리지 계정을 나타내기 위해 AD DS에서 생성한 ID가 암호 회전을 강제하는 도메인 또는 OU에 있는 경우 다음 문서로 이동하여 암호 업데이트에 대한 지침을 확인하세요.

[AD DS에서 스토리지 계정 ID의 암호를 업데이트](storage-files-identity-ad-ds-update-password.md)