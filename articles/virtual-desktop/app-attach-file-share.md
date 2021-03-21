---
title: Windows 가상 데스크톱 설정 파일 공유 MSIX 앱 연결 미리 보기-Azure
description: Windows 가상 데스크톱에 대 한 MSIX 앱 연결에 대 한 파일 공유를 설정 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 49a350b77958901aae5e54e82d856e4f3772702e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930789"
---
# <a name="set-up-a-file-share-for-msix-app-attach-preview"></a>MSIX 앱 연결 (미리 보기)에 대 한 파일 공유 설정

> [!IMPORTANT]
> MSIX 앱 연결은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

모든 MSIX 이미지는 읽기 전용 권한이 있는 호스트 풀의 사용자가 액세스할 수 있는 네트워크 공유에 저장 되어야 합니다.

MSIX 앱 연결 (미리 보기)에는 파일 공유에서 사용 하는 저장소 패브릭 유형에 대 한 종속성이 없습니다. MSIX 앱 연결 공유에 대 한 고려 사항은 FSLogix 공유와 동일 합니다. 저장소 요구 사항에 대 한 자세한 내용은 [Windows 가상 데스크톱의 FSLogix 프로필 컨테이너에 대 한 저장소 옵션](store-fslogix-profile.md)을 참조 하세요.

## <a name="performance-requirements"></a>성능 요구 사항

시스템에 대 한 MSIX 앱 연결 이미지 크기 제한은 VHD 또는 VHDx 파일을 저장 하는 데 사용 하는 저장소 유형과 VHD, VHSD 또는 CIM 파일 및 파일 시스템의 크기 제한에 따라 달라 집니다.

다음 표에서는 각 VM에 대해 하나의 MSIX 개 앱이 필요한 단일 1gb MSIX 이미지의 리소스 수에 대 한 예를 제공 합니다.

| 리소스             | 요구 사항 |
|----------------------|--------------|
| 안정적인 상태 IOPs    | IOPs 1 개       |
| 컴퓨터 부팅 로그인 | 10 IOPs      |
| 대기 시간              | 400 밀리초       |

요구 사항은 MSIX 이미지에 저장 되는 MSIX 패키지 응용 프로그램의 수에 따라 크게 달라질 수 있습니다. 더 큰 MSIX 이미지의 경우 더 많은 대역폭을 할당 해야 합니다.

### <a name="storage-recommendations"></a>스토리지 권장 사항

Azure는 오 x 앱 연결에 사용할 수 있는 여러 저장소 옵션을 제공 합니다. Azure Files 또는 Azure NetApp Files를 사용 하는 것이 좋습니다. 이러한 옵션은 비용과 관리 오버 헤드 사이에서 최상의 가치를 제공 합니다. [Windows 가상 데스크톱의 FSLogix 프로필 컨테이너에 대 한 저장소 옵션](store-fslogix-profile.md) 문서는 Azure에서 제공 하는 다른 관리 되는 저장소 솔루션을 Windows 가상 데스크톱의 컨텍스트에서 비교 합니다.

### <a name="optimize-msix-app-attach-performance"></a>MSIX 앱 연결 성능 최적화

MSIX 앱 연결 성능을 최적화 하기 위해 수행 하는 몇 가지 다른 작업은 다음과 같습니다.

- MSIX 앱을 연결 하는 데 사용 하는 저장소 솔루션은 세션에서 호스트 하는 것과 동일한 데이터 센터 위치에 있어야 합니다.
- 성능 병목 현상을 방지 하려면 다음 VHD, VHDX 및 CIM 파일을 바이러스 백신 검사에서 제외 합니다.
   
    - MSIXAppAttachFileShare \> \* 를 <합니다. .VHD
    - MSIXAppAttachFileShare \> \* 를 <합니다. 높이려면
    - \\\\storageaccount.file.core.windows.net \\ share \* \* . .VHD
    - \\\\storageaccount.file.core.windows.net \\ share \* \* . 높이려면
    - <MSIXAppAttachFileShare>. CIM
    - \\\\storageaccount.file.core.windows.net \\ share \* \* . CIM

- FSLogix 프로필 컨테이너에서 MSIX 앱 연결에 대 한 저장소 패브릭을 분리 합니다.
- 모든 VM 시스템 계정 및 사용자 계정에는 파일 공유에 액세스할 수 있는 읽기 전용 권한이 있어야 합니다.
- Windows 가상 데스크톱에 대 한 모든 재해 복구 계획은 보조 장애 조치 (failover) 위치에 MSIX 앱 첨부 파일 공유 복제를 포함 해야 합니다. 재해 복구에 대해 자세히 알아보려면 [비즈니스 연속성 및 재해 복구 계획 설정](disaster-recovery.md)을 참조 하세요.

## <a name="how-to-set-up-the-file-share"></a>파일 공유를 설정 하는 방법

MSIX 앱 첨부 파일 공유에 대 한 설치 프로세스는 대부분 [FSLogix 프로필 파일 공유에 대 한 설치](create-host-pools-user-profile.md)프로세스와 동일 합니다. 그러나 사용자에 게 다른 권한을 할당 해야 합니다. MSIX 앱 연결에는 파일 공유에 액세스 하기 위한 읽기 전용 권한이 필요 합니다.

Azure Files에서 MSIX 응용 프로그램을 저장 하는 경우 세션 호스트의 경우 모든 세션 호스트 Vm을 할당 해야 합니다. 저장소 계정 역할 기반 액세스 제어 (RBAC)와 공유에 대 한 파일 공유 새 기술 파일 시스템 (NTFS) 권한을 모두 할당 해야 합니다.

| Azure 개체                      | 필수 역할                                     | Role 함수                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| 세션 호스트 (VM 컴퓨터 개체)| 스토리지 파일 데이터 SMB 공유 Contributor          | 읽기 및 실행, 읽기, 폴더 내용 보기  |
| 파일 공유의 관리자              | 스토리지 파일 데이터 SMB 공유 관리자 권한 Contributor | 모든 권한                                  |
| 파일 공유의 사용자               | 스토리지 파일 데이터 SMB 공유 Contributor          | 읽기 및 실행, 읽기, 폴더 내용 보기  |

저장소 계정 및 파일 공유에 대 한 세션 호스트 Vm 권한을 할당 하려면:

1. Active Directory Domain Services (AD DS) 보안 그룹을 만듭니다.

2. 모든 세션 호스트 Vm에 대 한 컴퓨터 계정을 그룹의 멤버로 추가 합니다.

3. AD DS 그룹을 Azure Active Directory (Azure AD)에 동기화 합니다.

4. 스토리지 계정을 만듭니다.

5. [Azure 파일 공유 만들기](../storage/files/storage-how-to-create-file-share.md#create-file-share)의 지침에 따라 저장소 계정에서 파일 공유를 만듭니다.

6. [1 부: Azure 파일 공유에 대 한 AD DS 인증 사용](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)의 지침에 따라 저장소 계정을 AD DS에 조인 합니다.

7. Azure AD에 동기화 된 AD DS 그룹을 할당 하 고 저장소 계정에 저장소 파일 데이터 SMB 공유 참가자 역할을 할당 합니다.

8. [2 부: id에 공유 수준 권한 할당](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)의 지침에 따라 세션 호스트에 파일 공유를 탑재 합니다.

9. 파일 공유에 대 한 NTFS 권한을 AD DS 그룹에 부여 합니다.

10. 사용자 계정에 대 한 NTFS 사용 권한을 설정 합니다. VM의 계정이 속한 AD DS에서 제공 하는 OU (운영 단위)가 필요 합니다.

저장소에 id를 할당 한 후 [다음 단계](#next-steps) 문서에 설명 된 지침에 따라 vm에 할당 한 id에 다른 필요한 권한을 부여 합니다.

또한 세션 호스트 Vm에 NTFS (새 기술 파일 시스템) 권한이 있는지 확인 해야 합니다. Active Directory Domain Services (AD DS)에서 원본으로 작동 하는 작업 단위 컨테이너가 있어야 하 고 사용자가 이러한 권한을 사용 하려면 해당 운영 단위의 구성원 이어야 합니다.

## <a name="next-steps"></a>다음 단계

파일 공유를 설정한 후에 수행 해야 하는 다른 작업은 다음과 같습니다.

- [Azure Files 및 AD DS를 사용 하 여 프로필 컨테이너 만들기](create-file-share.md)에서 Azure Active Directory Domain Services (AD DS)를 설정 하는 방법에 대해 알아봅니다.
- [Azure Files 및 azure AD DS를 사용 하 여 프로필 컨테이너 만들기](create-profile-container-adds.md)에서 Azure Files 및 azure AD DS를 설정 하는 방법에 대해 알아봅니다.
- [Azure NetApp Files 및 AD DS를 사용 하 여 프로필 컨테이너 만들기](create-fslogix-profile-container.md)에서 msix 앱 연결에 대 한 Azure NetApp Files를 설정 하는 방법에 대해 알아봅니다.
- [파일 공유를 사용 하 여 호스트 풀의 프로필 컨테이너 만들기](create-host-pools-user-profile.md)에서 가상 컴퓨터 기반 파일 공유를 사용 하는 방법에 대해 알아봅니다.

완료 되 면 다음과 같은 몇 가지 다른 리소스를 확인할 수 있습니다.

- [Windows 가상 데스크톱 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서이 기능에 대 한 커뮤니티 질문을 요청 합니다.
- [Windows Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Windows Virtual Desktop에 대한 피드백을 남길 수도 있습니다.
- [MSIX 앱 연결 용어집](app-attach-glossary.md)
- [MSIX 앱 연결 FAQ](app-attach-faq.md)
