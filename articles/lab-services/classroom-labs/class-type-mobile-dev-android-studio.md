---
title: Android Studio를 사용 하 여 모바일 응용 프로그램 개발에 대해 학습 하기 위한 랩 설정
titleSuffix: Azure Lab Services
description: Android Studio를 사용 하는 데이터 모바일 응용 프로그램 개발 클래스를 학습 하도록 랩을 설정 하는 방법에 대해 알아봅니다.  또한 Azure의 가상 머신에서 Android Studio를 사용 하는 경우의 조정에 대해서도 설명 합니다.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76849787"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Android Studio를 사용 하 여 데이터 모바일 응용 프로그램 개발을 학습 하기 위한 랩 설정

이 문서에서는 소개 모바일 응용 프로그램 개발 클래스를 설정 하는 방법을 보여 줍니다.  이 클래스는 [Google Play 스토어](https://play.google.com/store/apps)에 게시할 수 있는 Android 모바일 애플리케이션을 중심으로 설명합니다.  학생들은 [Android Studio](https://developer.android.com/studio)를 사용하여 애플리케이션을 빌드하는 방법을 알아봅니다.  [Android용 Visual Studio 에뮬레이터](https://visualstudio.microsoft.com/vs/msft-android-emulator/)는 애플리케이션을 로컬로 테스트하는 데 사용됩니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정 하려면 시작 하려면 Azure 구독 및 랩 계정이 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받은 후 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정을 만드는 방법에 대 한 자세한 내용은 자습서를 참조 [하 여 랩 계정 설정](tutorial-setup-lab-account.md)을 참조 하세요.  기존 랩 계정을 사용할 수도 있습니다.

[교실 랩 설정 자습서](tutorial-setup-classroom-lab.md) 에 따라 새 랩을 만들고 다음 설정을 적용 합니다.

| 가상 머신 크기 | 이미지 |
| -------------------- | ----- |
| 중간 (중첩 된 가상화) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>템플릿 컴퓨터 구성

템플릿 컴퓨터 생성이 완료 되 면 [컴퓨터를 시작 하 고 연결](how-to-create-manage-template.md#update-a-template-vm) 하 여 다음 작업을 완료 합니다.

1. Hyper-v 역할 추가
2. Java를 다운로드 하 여 설치 합니다.  
3. Android 용 Visual Studio 에뮬레이터를 다운로드 하 여 설치 합니다.
4. Android Studio를 다운로드 하 고 설치 합니다.
5. Android Studio에 대해 Visual Studio 에뮬레이터를 구성 합니다.

## <a name="add-hyper-v-role"></a>Hyper-v 역할 추가

Android 용 Visual Studio 에뮬레이터를 성공적으로 설치 하려면 hyper-v를 사용 하도록 설정 해야 합니다.  [템플릿 가상 머신에서 중첩 된 가상화를 사용 하도록 설정 하는 방법](how-to-enable-nested-virtualization-template-vm.md) 문서에 있는 지침을 따르세요.

## <a name="install-java"></a>Java 설치

Android Studio에는 Java가 필요 합니다.  최신 버전의 Java를 다운로드 하려면 다음 단계를 수행 합니다.

1. [Java 다운로드 페이지로](https://www.java.com/download/)이동 합니다. **Java 다운로드** 단추를 클릭 합니다.
2. Java 용 64 비트 Windows 웹 페이지에서 동의 함 이라는 단추를 클릭 하 **고 무료 다운로드를 시작**합니다.
3. **Java 설치** 관리자가 표시 되 면 **설치**를 클릭 합니다.
4. 설치 관리자 제목이 **Java 설정-전체**로 변경 될 때까지 기다립니다.  **닫기** 단추를 클릭 합니다.

## <a name="install-visual-studio-emulator-for-android"></a>Android 용 Visual Studio 에뮬레이터 설치

Android 응용 프로그램을 로컬로 테스트 하려면 가상화 된 Android 장치 버전을 사용 해야 합니다.  개발자가 자신의 컴퓨터에서 응용 프로그램을 테스트할 수 있는 몇 가지 Android 에뮬레이터가 제공 됩니다.  Visual Studio Emulator for Android는 중첩 된 가상화를 지 원하는 에뮬레이터 이기 때문에 Android 용으로 사용 하 고 있습니다.  랩 서비스 VM은 이미 가상 컴퓨터 이므로 중첩 된 가상화를 지 원하는 에뮬레이터가 필요 합니다.  Android Studio에 대 한 기본 제공 에뮬레이터는 중첩 된 가상화를 지원 하지 않습니다.  중첩 된 가상화를 지 원하는 에뮬레이터를 확인 하려면 [에뮬레이터 성능에 대 한 하드웨어 가속 (hyper-v & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)을 참조 하세요.

Android 용 Visual Studio 에뮬레이터를 다운로드 하 고 설치 하려면 다음 지침을 따르십시오.

1. [Android 용 Visual Studio 에뮬레이터](https://visualstudio.microsoft.com/vs/msft-android-emulator/) 홈 페이지로 이동 합니다.
2. **에뮬레이터 다운로드** 단추를 클릭 합니다.
3. Vs_emulatorsetup 다운로드 되 면 실행 파일을 실행 합니다.
4. Visual Studio 설치 대화 상자가 나타나면 **설치** 단추를 클릭 합니다.
5. 설치 관리자가 완료 될 때까지 기다립니다.  **지금 다시 시작** 단추를 클릭 하 여 컴퓨터를 다시 시작 하 고 설치를 완료 합니다.

Android Studio를 사용 하 여 응용 프로그램을 배포 하기 전에 에뮬레이터를 먼저 시작 합니다.  Android 용 Visual Studio 에뮬레이터에 대 한 자세한 내용은 Android 용 [Visual Studio 에뮬레이터 설명서](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)를 참조 하세요.

## <a name="install-android-studio"></a>Android Studio 설치

[Android Studio](https://developer.android.com/studio)를 다운로드 하 고 설치 하려면 아래 지침을 따르세요.

1. [Android Studio 다운로드 페이지로](https://developer.android.com/studio#downloads)이동 합니다.  
    > [!NOTE]
    > Internet Explorer는이 사이트에서 지원 되지 않습니다.
2. Windows (64 비트) 실행 파일 Android Studio 패키지를 클릭 합니다.
3. 팝업에서 작성 된 약관을 읽습니다.  계속할 준비가 되 면 **위의 사용 약관에 동의 함** 확인란을 선택 하 고 **Windows에 대 한 Android Studio 다운로드** 단추를 클릭 합니다.
4. Android Studio 설치 실행 파일이 다운로드 되 면 실행 파일을 실행 합니다.
5. **Android Studio 설치** 관리자의 **Android Studio 설정 시작** 페이지에서 **다음**을 클릭 합니다.
6. **구성 설정** 페이지에서 **다음**을 클릭 합니다.
7. **시작 메뉴 폴더 선택** 페이지에서 **설치**를 클릭 합니다.
8. 설치가 완료 될 때까지 기다립니다.
9. **설치 완료** 페이지에서 **다음**을 클릭 합니다.
10. **Android Studio 설치 완료** 페이지에서  **마침**을 클릭합니다.
11. 설치가 완료 된 후 Android Studio가 자동으로 시작 됩니다.
12. **Android 설정 가져오기** ... 대화 상자에서 **설정 가져오기 안 함**을 선택 합니다. **확인**을 클릭합니다.
13. **Android Studio 설치 마법사**의 **시작** 페이지에서 **다음**을 클릭 합니다.
14. **설치 유형** 페이지에서 **표준**을 선택 합니다. **다음**을 클릭합니다.
15. **UI 테마 선택** 페이지에서 원하는 테마를 선택 합니다. **다음**을 클릭합니다.
16. **설정 확인** 페이지에서 **다음**을 클릭 합니다.
17. **구성 요소 다운로드 중** 페이지에서 모든 구성 요소가 다운로드 될 때까지 기다립니다.  **마침**을 클릭합니다.

    > [!IMPORTANT]
    > HAXM 설치에 실패 하는 것으로 예상 됩니다.  HAXM는 중첩 된 가상화를 지원 하지 않습니다 .이는이 문서 앞부분에서 Android 용 Visual Studio 에뮬레이터를 설치한 이유입니다.

18. 설치 마법사가 완료 되 면 **Android Studio 시작** 대화 상자가 표시 됩니다.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Android 용 Android Studio 및 Visual Studio 에뮬레이터 구성

Android Studio은 거의 사용할 준비가 된 것입니다.  Android SDK 설치 된 경우 Visual Studio Emulator for Android에도 알려야 합니다.  이렇게 하면 Android 용 Visual Studio에서 실행 되는 모든 에뮬레이터가 Android Studio 디버깅을 위한 배포 대상으로 표시 됩니다.

Android Sdk가 있는 경우 Android 용 Visual Studio 에뮬레이터를 알리기 위해 특정 레지스트리 키를 설정 해야 합니다.  필요한 레지스트리 키를 설정 하려면 아래 스크립트를 실행 합니다.  아래 PowerShell 스크립트는 Android Sdk의 기본 설치 위치를 가정 합니다.  다른 위치에 Android Sdk를 설치한 경우 스크립트를 실행 하기 전에에 `$androidSdkPath` 대 한 값을 수정 합니다.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Android 용 Visual Studio 에뮬레이터를 다시 시작 하 고 새 설정을 사용 하도록 Android Studio 합니다.

Visual Studio 에뮬레이터에서 필요한 버전을 시작 합니다.  Android studio에서 Android 앱에 대 한 배포 대상으로 표시 됩니다.  Android Studio 프로젝트의 최소 버전은 Android 용 Visual Studio 에뮬레이터에서 실행 되는 버전을 지원 해야 합니다.  이제 Android Studio 및 Android 용 Visual Studio 에뮬레이터를 사용 하 여 프로젝트를 만들고 디버그할 준비가 되었습니다.  문제가 있는 경우 Android 에뮬레이터 문제 해결을 참조 하세요.

## <a name="cost"></a>비용

이 랩의 비용을 예상 하려면 아래 예제를 따르는 것이 좋습니다.
20 시간의 예약 된 클래스 시간 및 숙제 또는 할당에 대 한 10 시간의 할당량을 포함 하는 25 학생 클래스의 경우 랩 가격은  

25 개 \* 학생 (20 개의 예약 된 + 10 할당량) 시간 * 55 랩 단위 * 0.01 usd/시간 = 412.5 USD

가격 책정에 대 한 추가 세부 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정 하는 데 일반적입니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에 대 한 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
