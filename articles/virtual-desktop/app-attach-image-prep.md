---
title: Windows 가상 데스크톱 준비 MSIX 앱 연결 이미지 미리 보기-Azure
description: Windows 가상 데스크톱 호스트 풀에 대 한 MSIX 앱 연결 이미지를 만드는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 204cc9a05d62caf62179100fa3496be422a3ec0c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425883"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대 한 MSIX 이미지 준비

> [!IMPORTANT]
> MSIX 앱 연결은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

MSIX 앱 연결 (미리 보기)은 MSIX 패키지의 앱을 사용자 세션에 동적으로 연결 하는 데 사용할 수 있는 응용 프로그램 계층화 솔루션입니다. MSIX 패키지 시스템은 운영 체제에서 앱을 분리 하므로 가상 컴퓨터에 대 한 이미지를 더 쉽게 빌드할 수 있습니다. MSIX 패키지를 사용 하면 사용자가 자신의 가상 컴퓨터에서 액세스할 수 있는 앱을 보다 강력 하 게 제어할 수 있습니다. 마스터 이미지에서 앱을 분리 하 고 나중에 사용자에 게 제공할 수도 있습니다.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>MSIX 용 VHD 또는 VHDX 패키지 만들기

MSIX 패키지가 제대로 작동 하려면 VHD 또는 VHDX 형식이 있어야 합니다. 즉, 시작 하려면 VHD 또는 VHDX 패키지를 만들어야 합니다.

>[!NOTE]
>아직 수행 하지 않은 경우 [Windows 10에서 Hyper-v 설치](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)의 지침에 따라 hyper-v를 사용 하도록 설정 해야 합니다.

MSIX 용 VHD 또는 VHDX 패키지를 만들려면 다음을 수행 합니다.

1. 먼저 PowerShell을 엽니다.
2. 그런 다음, 다음 cmdlet을 실행 하 여 VHD를 만듭니다.

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > VHD가 확장 된 MSIX 패키지를 저장할 수 있을 만큼 충분히 큰지 확인 합니다.

3. 다음 cmdlet을 실행 하 여 방금 만든 VHD를 탑재 합니다.

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. 그런 다음이 cmdlet을 실행 하 여 탑재 된 VHD를 초기화 합니다.

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. 그런 다음이 cmdlet을 실행 하 여 초기화 된 VHD에 대 한 새 파티션을 만듭니다.

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. 이 cmdlet을 실행하여 파티션을 포맷합니다.

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. 마지막으로 탑재 된 VHD에 부모 폴더를 만듭니다. MSIX 패키지에 제대로 작동 하려면 부모 폴더가 있어야 하기 때문에이 단계가 필요 합니다. 부모 폴더가 있는 한 부모 폴더의 이름을 결정 하는 것은 중요 하지 않습니다.

## <a name="expand-msix"></a>MSIX 확장

그런 다음 해당 파일을 VHD로 "압축 풀기" 하 여 MSIX 이미지를 확장 해야 합니다.

MSIX 이미지를 확장 하려면:

1. [msixmgr 도구를 다운로드](https://aka.ms/msixmgr)하고 .zip 폴더를 세션 호스트 VM 내 폴더에 저장합니다.

2. msixmgr 도구 .zip 폴더의 압축을 풉니다.

3. msixmgr 도구의 압축을 푼 폴더에 소스 MSIX 패키지를 넣습니다.

4. 관리자 권한으로 명령 프롬프트를 열고 msixmgr 도구를 다운로드하고 압축을 푼 폴더로 이동합니다.

5. 다음 cmdlet을 실행 하 여 MSIX을 이전 섹션에서 만든 VHD로 압축을 풉니다.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    압축 풀기를 완료 한 후에는 다음 메시지가 표시 됩니다.

    > 패키지의 압축을 풀고 적용 한 Acl: <package name> . msix

    >[!NOTE]
    > 네트워크 또는 인터넷에 연결 되지 않은 장치에서 비즈니스 또는 교육용 Microsoft Store의 패키지를 사용 하는 경우 앱을 실행 하려면 Microsoft Store에서 패키지 라이선스를 다운로드 하 여 설치 해야 합니다. 라이선스를 가져오려면 [오프 라인으로 패키지 사용](app-attach.md#use-packages-offline)을 참조 하세요.

6. 탑재 된 VHD로 이동 하 고 앱 폴더를 열어 패키지 내용이 있는지 확인 합니다.

7. VHD를 분리합니다.

## <a name="upload-msix-image-to-share"></a>공유할 MSIX 이미지 업로드

MSIX 패키지를 만든 후에는 사용자의 가상 컴퓨터에서 액세스할 수 있는 공유에 결과 VHD, VHDX 또는 CIM 파일을 업로드 해야 합니다.

## <a name="next-steps"></a>다음 단계

[Windows 가상 데스크톱 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서이 기능에 대 한 커뮤니티 질문을 요청 합니다.

[Windows Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Windows Virtual Desktop에 대한 피드백을 남길 수도 있습니다.

다음은 유용 하 게 사용할 수 있는 몇 가지 문서입니다.

- [MSIX 앱 연결 용어집](app-attach-glossary.md)
- [MSIX 앱 연결 FAQ](app-attach-faq.md)