---
title: Windows 가상 데스크톱 MSIX 앱 연결 포털 미리 보기-Azure
description: Azure Portal를 사용 하 여 Windows 가상 데스크톱에 대 한 MSIX 앱 연결을 설정 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 34bcef24d5e7fbda53984f14a2307859c9210262
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185957"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Azure Portal을 사용하여 MSIX 앱 연결 설정

> [!IMPORTANT]
> MSIX 앱 연결은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Windows 가상 데스크톱 환경에서 MSIX 앱 연결 (미리 보기)을 설정 하는 방법을 안내 합니다.

## <a name="requirements"></a>요구 사항

>[!IMPORTANT]
>시작 하기 전에 구독에서 MSIX 앱 연결을 사용할 수 있도록 [이 양식을](https://aka.ms/enablemsixappattach) 작성 하 고 제출 해야 합니다. 승인 된 요청이 없으면 MSIX 앱 연결이 작동 하지 않습니다. 요청 승인은 업무 시간 동안 최대 24 시간이 걸릴 수 있습니다. 요청이 수락 되 고 완료 되 면 전자 메일을 받게 됩니다.

MSIX 앱 연결을 구성 하는 데 필요한 사항은 다음과 같습니다.

- 작동하는 Windows Virtual Desktop 배포 Windows 가상 데스크톱 (클래식)을 배포 하는 방법을 알아보려면 [Windows 가상 데스크톱에서 테 넌 트 만들기](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)를 참조 하세요. Azure Resource Manager 통합을 사용 하 여 Windows 가상 데스크톱을 배포 하는 방법을 알아보려면 [Azure Portal를 사용 하 여 호스트 풀 만들기](./create-host-pools-azure-marketplace.md)를 참조 하세요.
- 하나 이상의 활성 세션 호스트를 포함 하는 Windows 가상 데스크톱 호스트 풀
- 이 호스트 풀은 유효성 검사 환경에 있어야 합니다. 
- MSIX 패키징 도구입니다.
- MSIX 패키지 응용 프로그램은 파일 공유에 업로드 된 MSIX 이미지로 확장 됩니다.
- MSIX 패키지가 저장 되는 Windows 가상 데스크톱 배포의 파일 공유입니다.
- MSIX 이미지를 업로드 한 파일 공유에도 호스트 풀의 모든 Vm (가상 머신)에 액세스할 수 있어야 합니다. 사용자는 이미지에 액세스 하기 위한 읽기 전용 권한이 필요 합니다.

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>MSIX 앱 연결 응용 프로그램에 대 한 자동 업데이트 끄기

시작 하기 전에 MSIX 앱 연결 응용 프로그램에 대 한 자동 업데이트를 사용 하지 않도록 설정 해야 합니다. 자동 업데이트를 사용하지 않도록 설정하려면 관리자 권한 명령 프롬프트에서 다음 명령을 실행해야 합니다.

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

>[!NOTE]
>Hyper-v를 사용 하도록 설정한 후 가상 컴퓨터를 다시 시작 하는 것이 좋습니다.

## <a name="configure-the-msix-app-attach-management-interface"></a>MSIX 앱 연결 관리 인터페이스 구성

그런 다음 Azure Portal에 대 한 MSIX 앱 연결 관리 인터페이스를 다운로드 하 고 구성 해야 합니다.

관리 인터페이스를 설정 하려면:

1. [Preview 포털을 엽니다](https://preview.portal.azure.com/?feature.msixapplications=true#home).
2. 확장을 신뢰할 수 있는지 여부를 묻는 메시지가 표시 되 면 **허용** 을 선택 합니다.

      > [!div class="mx-imgBorder"]
      > ![신뢰할 수 없는 확장 창의 스크린샷 "허용"은 빨간색으로 강조 표시 됩니다.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>호스트 풀에 MSIX 이미지 추가

다음에는 MSIX 이미지를 호스트 풀에 추가 해야 합니다.

MSIX 이미지를 추가 하려면:

1. Azure Portal을 엽니다.

2. 검색 창에 **Windows 가상 데스크톱** 을 입력 하 고 서비스 이름을 선택 합니다.

      > [!div class="mx-imgBorder"]
      > ![Azure Portal의 검색 표시줄 드롭다운 메뉴에서 "Windows 가상 데스크톱"을 선택 하는 사용자의 스크린샷 "Windows 가상 데스크톱"은 빨간색으로 강조 표시 됩니다.](media/find-and-select.png)

3. MSIX 앱을 배치할 호스트 풀을 선택 합니다.

4. 현재 호스트 풀에 추가 된 모든 **msix 패키지** 를 사용 하 여 데이터 표를 열려면 **msix 패키지** 를 선택 합니다.

5. **+ 추가** 를 선택 하 여 **Msix 패키지 추가** 탭을 엽니다.

6. **MSIX 패키지 추가** 탭에서 다음 값을 입력 합니다.

      - **Msix 이미지 경로** 에 파일 공유의 msix 이미지를 가리키는 유효한 UNC 경로를 입력 합니다. (예: `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` ) 완료 되 면 **추가** 를 선택 하 여 msix 컨테이너를 확인 하 고 경로가 유효한 지 확인 합니다.

      - **Msix 패키지** 의 경우 드롭다운 메뉴에서 관련 msix 패키지 이름을 선택 합니다. 이 메뉴는 **Msix 이미지 경로** 에 유효한 이미지 경로를 입력 한 경우에만 채워집니다.

      - **패키지 응용 프로그램** 의 경우 msix 패키지의 사용자가 사용할 수 있도록 하려는 모든 msix 응용 프로그램을 목록에 포함 해야 합니다.

      - 필요에 따라 사용자 배포에 사용자에 게 친숙 한 패키지를 추가 하려면 **표시 이름을** 입력 합니다.

      - **버전** 번호가 올바른지 확인 합니다.

      - 사용할 **등록 유형을** 선택 합니다. 사용자의 요구 사항에 따라 사용 하는 항목은 다음과 같습니다.

    - **주문형 등록** 은 사용자가 응용 프로그램을 시작할 때까지 msix 응용 프로그램의 전체 등록을 연기 합니다. 이 등록 유형은 사용 하는 것이 좋습니다.

    - 사용자가 로그인 하는 동안에만 등록 **차단 로그온** 사용자에 대 한 로그인 시간이 길어질 수 있으므로이 형식을 권장 하지 않습니다.

7.  **상태** 에서 기본 설정 상태를 선택 합니다.
    -  **활성** 상태는 사용자가 패키지와 상호 작용할 수 있도록 합니다.
    -  **비활성** 상태를 설정 하면 Windows 가상 데스크톱에서 패키지를 무시 하 고 사용자에 게 전달 하지 않습니다.

8. 완료되면 **추가** 를 선택합니다.

## <a name="publish-msix-apps-to-an-app-group"></a>앱 그룹에 MSIX 앱 게시

다음으로 패키지에 앱을 게시 해야 합니다. 데스크톱 및 원격 앱 응용 프로그램 그룹 모두에 대해이 작업을 수행 해야 합니다.

MSIX 이미지가 이미 있는 경우 [앱 그룹에 MSIX 앱 게시](#publish-msix-apps-to-an-app-group)로 건너뜁니다. 레거시 애플리케이션을 테스트하려면 [VM의 데스크톱 설치 관리자에서 MSIX 패키지 만들기](/windows/msix/packaging-tool/create-app-package-msi-vm/)의 지침에 따라 레거시 애플리케이션을 MSIX 패키지로 변환합니다.

앱을 게시 하려면:

1. Windows 가상 데스크톱 리소스 공급자에서 **응용 프로그램 그룹** 탭을 선택 합니다.

2. 앱을 게시할 응용 프로그램 그룹을 선택 합니다.

   >[!NOTE]
   >Msix 응용 프로그램을 원격 앱 및 데스크톱 앱 그룹 모두에 대 한 MSIX 앱 연결로 배달할 수 있습니다.

3. 앱 그룹에 있는 경우 **응용 프로그램** 탭을 선택 합니다. **응용 프로그램** 표에 앱 그룹 내에 있는 모든 기존 앱이 표시 됩니다.

4. **+ 추가** 를 선택 하 여 **응용 프로그램 추가** 탭을 엽니다.

      > [!div class="mx-imgBorder"]
      > ![+ 추가를 선택 하 여 응용 프로그램 추가 탭을 여는 사용자의 스크린샷](media/select-add.png)

5. **응용 프로그램 소스** 에서 응용 프로그램의 소스를 선택 합니다.
    - 데스크톱 앱 그룹을 사용 하는 경우 **Msix 패키지** 를 선택 합니다.
      
      > [!div class="mx-imgBorder"]
      > ![응용 프로그램 원본 드롭다운 메뉴에서 MSIX 패키지를 선택 하는 고객의 스크린샷 MSIX 패키지는 빨간색으로 강조 표시 됩니다.](media/select-source.png)
    
    - 원격 앱 그룹을 사용 하는 경우 다음 옵션 중 하나를 선택 합니다.
        
        - 시작 메뉴
        - 앱 경로
        - MSIX 패키지

    - **응용 프로그램 이름** 에 응용 프로그램에 대 한 설명이 포함 된 이름을 입력 합니다.

    다음과 같은 선택적 기능을 구성할 수도 있습니다.
   
    - **표시 이름** 에 사용자에 게 표시 되는 패키지의 새 이름을 입력 합니다.

    - **설명** 에 앱 패키지에 대 한 간단한 설명을 입력 합니다.

    - 원격 앱 그룹을 사용 하는 경우 다음 옵션을 구성할 수도 있습니다.

        - **아이콘 경로**
        - **아이콘 인덱스**
        - **웹 피드에 표시**

6. 완료되면 **저장** 을 선택합니다.

>[!NOTE]
>사용자가 동일한 호스트 풀에서 원격 앱 그룹 및 데스크톱 앱 그룹에 할당 되 면 데스크톱 앱 그룹이 피드에 표시 됩니다.

## <a name="assign-a-user-to-an-app-group"></a>앱 그룹에 사용자 할당

앱 그룹에 MSIX 앱을 할당 한 후에는 사용자에 게 액세스 권한을 부여 해야 합니다. 게시 된 MSIX 응용 프로그램으로 앱 그룹에 사용자 또는 사용자 그룹을 추가 하 여 액세스 권한을 할당할 수 있습니다. Azure Portal를 사용 하 여 [앱 그룹 관리](manage-app-groups.md) 의 지침에 따라 사용자를 앱 그룹에 할당 합니다.

## <a name="change-msix-package-state"></a>MSIX 패키지 상태 변경

그런 다음 패키지를 사용 하 여 수행 하려는 작업에 따라 MSIX 패키지 상태를 **활성** 또는 **비활성** 으로 변경 해야 합니다. 활성 패키지는 게시 될 때 사용자가 상호 작용할 수 있는 패키지입니다. 비활성 패키지는 Windows 가상 데스크톱에서 무시 되므로 사용자는 내부 앱과 상호 작용할 수 없습니다.

### <a name="change-state-with-the-applications-list"></a>응용 프로그램 목록으로 상태 변경

응용 프로그램 목록에서 패키지 상태를 변경 하려면 다음을 수행 합니다.

1. 호스트 풀로 이동 하 여 **Msix 패키지** 를 선택 합니다. 호스트 풀 내의 모든 기존 MSIX 패키지 목록이 표시 됩니다.

2. 상태를 변경 해야 하는 MSIX 패키지를 선택 하 고 **상태 변경** 을 선택 합니다.

### <a name="change-state-with-update-package"></a>업데이트 패키지를 사용 하 여 상태 변경

업데이트 패키지를 사용 하 여 패키지 상태를 변경 하려면:

1. 호스트 풀로 이동 하 여 **Msix 패키지** 를 선택 합니다. 호스트 풀 내의 모든 기존 MSIX 패키지 목록이 표시 됩니다.

2. MSIX 패키지 목록에서 해당 상태를 변경 하려는 패키지의 이름을 선택 합니다. 그러면 **패키지 업데이트** 탭이 열립니다.

3. **상태** 스위치를 **비활성** 또는 **활성** 으로 전환 하 고 저장을 선택 **합니다.**

## <a name="change-msix-package-registration-type"></a>MSIX 패키지 등록 유형 변경

패키지의 등록 유형을 변경 하려면 다음을 수행 합니다.

1. **Msix 패키지** 를 선택 합니다. 호스트 풀 내의 모든 기존 MSIX 패키지 목록이 표시 됩니다.

2. **Msix 패키지 그리드** **에서 패키지 이름** 을 선택 하 여 패키지를 업데이트 하는 블레이드를 엽니다.

3. **필요에 따라 요청 시/로그온 차단** 단추를 통해 **등록 유형을** 전환 하 고 저장을 선택 **합니다.**

## <a name="remove-an-msix-package"></a>MSIX 패키지 제거

호스트 풀에서 MSIX 패키지를 제거 하려면 다음을 수행 합니다.

1. **Msix 패키지** 를 선택 합니다.  호스트 풀 내의 모든 기존 MSIX 패키지 목록이 표시 됩니다.

2. 삭제할 패키지의 이름 오른쪽에 있는 줄임표 (...)를 선택 하 고 **제거** 를 선택 합니다.

## <a name="remove-msix-apps"></a>MSIX 앱 제거

패키지에서 개별 MSIX 앱을 제거 하려면 다음을 수행 합니다.

1. 호스트 풀로 이동 하 고 **응용 프로그램 그룹** 을 선택 합니다.

2. MSIX 앱을 제거 하려는 응용 프로그램 그룹을 선택 합니다.

3. **응용 프로그램** 탭을 엽니다.

4. 제거 하려는 앱을 선택 하 고 **제거** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

[Windows 가상 데스크톱 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서이 기능에 대 한 커뮤니티 질문을 요청 합니다.

[Windows Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Windows Virtual Desktop에 대한 피드백을 남길 수도 있습니다.

다음은 유용 하 게 사용할 수 있는 몇 가지 문서입니다.

- [MSIX 앱 연결 용어집](app-attach-glossary.md)
- [MSIX 앱 연결 FAQ](app-attach-faq.md)
