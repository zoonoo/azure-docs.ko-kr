---
title: Windows 가상 데스크톱에서 Windows 10 Vm에 언어 팩 설치-Azure
description: Windows 가상 데스크톱에서 Windows 10 다중 세션 Vm 용 언어 팩을 설치 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 542163511a1b4fc0acde9b44d73be6ffc042d5d3
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008766"
---
# <a name="install-language-packs"></a>언어 팩 설치

국제적으로 Windows 가상 데스크톱 배포를 설정 하는 경우 배포에서 여러 언어를 지원 하는지 확인 하는 것이 좋습니다. Windows 10 Enterprise 다중 세션 VM (가상 머신) 이미지에 언어 팩을 설치 하 여 조직에 필요한 만큼의 언어를 지원할 수 있습니다. 이 문서에서는 사용자가 고유한 표시 언어를 선택할 수 있도록 언어 팩을 설치 하 고 이미지를 캡처하는 방법을 설명 합니다.

Azure에서 VM을 배포 하는 방법에 대 한 자세한 내용은 Azure Portal를 사용 하 여 [가용성 영역에서 Windows 가상 머신 만들기](../virtual-machines/windows/create-portal-availability-zone.md)를 확인 하세요.

>[!NOTE]
>이 문서는 Windows 10 Enterprise 다중 세션 Vm에 적용 됩니다.

## <a name="install-a-language-pack"></a>언어 팩 설치

언어 팩을 사용 하 여 VM 이미지를 만들려면 먼저 컴퓨터에 언어 팩을 설치 하 고 이미지를 캡처해야 합니다.

언어 팩을 설치 하려면:

1. 관리자로 로그인 합니다.
2. 최신 Windows 및 Windows 스토어 업데이트를 모두 설치 했는지 확인 합니다.
3. **설정**  >  **시간 & 언어**  >  **지역**으로 이동 합니다.
4. **국가 또는 지역**아래의 드롭다운 메뉴에서 원하는 국가 또는 지역을 선택 합니다.
    이 예제에서는 다음 스크린샷에 표시 된 것 처럼 **프랑스**를 선택 하겠습니다.

    > [!div class="mx-imgBorder"]
    > ![영역 페이지의 스크린샷 현재 선택 된 지역은 프랑스입니다.](media/region-page-france.png)

5. 그런 다음 **언어**를 선택 하 고 **언어 추가**를 선택 합니다. 목록에서 설치 하려는 언어를 선택 하 고 **다음**을 선택 합니다.
6. **설치 언어 기능** 창이 열리면 **언어 팩 설치 및 Windows 표시 언어로 설정**확인란을 선택 합니다.
7. **설치**를 선택합니다.
8. 여러 언어를 한 번에 추가 하려면 **언어 추가**를 선택한 다음 프로세스를 반복 하 여 5 단계와 6 단계에서 언어를 추가 합니다. 설치 하려는 각 언어에 대해이 프로세스를 반복 합니다. 그러나 한 번에 하나의 언어를 표시 언어로만 설정할 수 있습니다.

    빠른 시각적 데모를 살펴보겠습니다. 다음 이미지는 프랑스어 및 네덜란드어 언어 팩을 설치 하 고 프랑스어를 표시 언어로 설정 하는 방법을 보여 줍니다.

    > [!div class="mx-imgBorder"]
    > ![프로세스의 시작 부분에 있는 언어 페이지의 스크린샷 선택한 Windows 표시 언어는 영어입니다.](media/language-page-default.png)

    > [!div class="mx-imgBorder"]
    > ![언어 선택 창의 스크린샷 사용자가 프랑스어 언어 패키지를 찾기 위해 검색 창에 "프랑스어"를 입력 했습니다.](media/select-language-french.png)

    > [!div class="mx-imgBorder"]
    > ![언어 기능 설치 페이지의 스크린샷 프랑스어는 기본 설정 언어로 선택 됩니다. 선택한 옵션은 "내 표시 언어 설정", "언어 팩 설치", "음성 인식" 및 "필기"입니다.](media/install-language-features.png)

    언어 팩을 설치한 후 언어 팩의 이름이 언어 목록에 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![새 언어 팩이 설치 된 언어 페이지의 스크린샷 프랑스어 및 네덜란드 언어 팩이 "기본 설정 언어" 아래에 나열 됩니다.](media/language-page-complete.png)

9. 세션에서 로그 아웃 하 라는 창이 표시 되 면이 고, 로그 아웃 한 다음 다시 로그인 합니다. 이제 표시 언어가 선택한 언어로 표시 됩니다.

10.  **제어판**  >  **시계 및 지역**  >  **영역**으로 이동 합니다.

11.  **지역** 창이 열리면 **관리** 탭을 선택한 다음, **설정 복사**를 선택 합니다.

12.  **시작 화면 및 시스템 계정** 및 **새 사용자 계정**이라는 확인란을 선택 합니다.

13.  **확인**을 선택합니다.

14.  창이 열리고 세션을 다시 시작 하는 메시지가 표시 됩니다. **지금 다시 시작**을 선택 합니다.

15.  다시 로그인 한 후 **제어판**  >  **시계 및 지역**  >  **영역**으로 돌아갑니다.

16.  **관리** 탭을 선택 합니다.

17.  **시스템 로캘 변경**을 선택 합니다.

18. 드롭다운 메뉴의 **현재 시스템 로캘**에서 사용 하려는 로캘 언어를 선택 합니다. 그런 다음 **확인**을 선택 합니다.

19. 세션을 다시 시작 하려면 **지금 다시 시작** 을 선택 합니다.

축 하 합니다. 언어 팩을 설치 했습니다.

계속 하기 전에 시스템에 최신 버전의 Windows 및 Windows 스토어가 설치 되어 있는지 확인 합니다.

## <a name="sysprep"></a>Sysprep

그런 다음 이미지 캡처 프로세스를 준비 하기 위해 컴퓨터를 sysprep 해야 합니다.

컴퓨터를 sysprep 하려면:

1. 관리자 권한으로 PowerShell을 엽니다.
2. 다음 cmdlet을 실행 하 여 올바른 디렉터리로 이동 합니다.

    ```powershell
    cd Windows\System32\Sysprep
    ```

3. 그런 다음, 다음 cmdlet을 실행 합니다.

    ```powershell
    .\sysprep.exe
    ```

4. 시스템 준비 도구 창이 열리면 **일반화**됨 이라는 확인란을 선택 하 고 **종료 옵션** 으로 이동한 다음 드롭다운 메뉴에서 **종료** 를 선택 합니다.

>[!NOTE]
>Syprep 프로세스를 완료 하는 데 몇 분 정도 걸립니다. VM이 종료 되 면 원격 세션의 연결이 끊어집니다.

### <a name="resolve-sysprep-errors"></a>Sysprep 오류 해결

Sysprep 프로세스 중에 오류 메시지가 표시 되는 경우 다음을 수행 해야 합니다.

1. **C 드라이브** 를 열고 **Windows**  >  **System32 Sysprep**  >  **Panther**로 이동한 다음 **setuperr.log** 파일을 엽니다.

   오류 파일의 텍스트는 다음 그림에 표시 된 것 처럼 특정 언어 패키지를 제거 해야 한다는 것을 알려줍니다. 다음 단계에 대 한 언어 패키지 이름을 복사 합니다.

   > [!div class="mx-imgBorder"]
   > ![Setuperr.log 파일의 스크린샷 패키지 이름을 포함 하는 텍스트는 진한 파란색으로 강조 표시 됩니다.](media/setuperr-package-name.png)

2. 새 PowerShell 창을 열고 2 단계에서 복사한 패키지 이름으로 다음 cmdlet을 실행 하 여 언어 패키지를 제거 합니다.

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Cmdlet을 다시 실행 하 여 패키지를 제거 했는지 확인 `Remove-AppxPackage` 합니다. 패키지를 성공적으로 제거 하면 제거 하려고 하는 패키지는 없다는 메시지가 표시 됩니다.

4. Cmdlet을 `sysprep.exe` 다시 실행 합니다.

## <a name="capture-the-image"></a>이미지 캡처

이제 시스템이 준비 되 면 다른 사용자가 구성 프로세스를 반복할 필요 없이 시스템을 기반으로 하는 Vm을 사용 하 여 시작할 수 있도록 이미지를 캡처할 수 있습니다.

이미지를 캡처하려면 다음을 수행 합니다.

1. Azure Portal로 이동 하 고 [언어 팩 설치](#install-a-language-pack) 및 [sysprep](#sysprep)에서 구성한 컴퓨터의 이름을 선택 합니다.

2. **캡처**를 선택 합니다.

3. 다음 그림에 표시 된 것 처럼 **이름** 필드에 이미지의 이름을 입력 하 고 **리소스 그룹** 드롭다운 메뉴를 사용 하 여 리소스 그룹에 할당 합니다.

   > [!div class="mx-imgBorder"]
   > ![이미지 만들기 창의 스크린샷 사용자가이 테스트 이미지에 지정한 이름은 "vmwvd-testwvdimagerg" 이며 "" 리소스 그룹에 할당 되었습니다.](media/create-image.png)

4. **만들기**를 선택합니다.

5. 캡처 프로세스가 완료 될 때까지 몇 분 정도 기다립니다. 이미지가 준비 되 면 이미지 캡처를 알려 주는 메시지가 알림 센터에 표시 됩니다.

이제 새 이미지를 사용 하 여 VM을 배포할 수 있습니다. VM을 배포 하는 경우 [Azure Portal 사용 하 여 가용성 영역에서 Windows 가상 머신 만들기](../virtual-machines/windows/create-portal-availability-zone.md)의 지침을 따르세요.

### <a name="how-to-change-display-language-for-standard-users"></a>표준 사용자에 대 한 표시 언어를 변경 하는 방법

표준 사용자는 Vm에서 표시 언어를 변경할 수 있습니다.

표시 언어를 변경 하려면:

1. **언어 설정**으로 이동 합니다. 어디에 있는지 잘 모르는 경우 시작 메뉴의 검색 창에 **언어** 를 입력할 수 있습니다.

2. Windows 표시 언어 드롭다운 메뉴에서 표시 언어로 사용 하려는 언어를 선택 합니다.

3. 세션에서 로그 아웃 한 다음 다시 로그인 합니다. 이제 표시 언어가 2 단계에서 선택한 것과 같아야 합니다.
