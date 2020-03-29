---
title: Android Studio로 모바일 응용 프로그램 개발을 가르치는 랩 설정
titleSuffix: Azure Lab Services
description: Android Studio를 사용하는 데이터 모바일 응용 프로그램 개발 클래스를 가르치기 위해 랩을 설정하는 방법에 대해 알아봅니다.  또한 Azure의 가상 컴퓨터에서 Android Studio를 사용할 때 조정하는 방법에 대해서도 설명합니다.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849787"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Android Studio로 데이터 모바일 응용 프로그램 개발을 가르치는 랩 설정

이 문서에서는 소개 모바일 응용 프로그램 개발 클래스를 설정하는 방법을 설명합니다.  이 클래스는 [Google Play 스토어](https://play.google.com/store/apps)에 게시할 수 있는 Android 모바일 애플리케이션을 중심으로 설명합니다.  학생들은 [Android Studio](https://developer.android.com/studio)를 사용하여 애플리케이션을 빌드하는 방법을 알아봅니다.  [Android용 Visual Studio 에뮬레이터](https://visualstudio.microsoft.com/vs/msft-android-emulator/)는 애플리케이션을 로컬로 테스트하는 데 사용됩니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독 및 랩 계정을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받으면 Azure Lab 서비스에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정을 만드는 자세한 내용은 [랩 계정을 설정하는 자습서를](tutorial-setup-lab-account.md)참조하십시오.  기존 랩 계정을 사용할 수도 있습니다.

[클래스룸 랩 설정 자습서를](tutorial-setup-classroom-lab.md) 따라 새 랩을 만든 다음 다음 설정을 적용합니다.

| 가상 머신 크기 | 이미지 |
| -------------------- | ----- |
| 중간(중첩 가상화) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>템플릿 기계 구성

템플릿 컴퓨터 생성이 완료되면 [컴퓨터를 시작하고 다음에 연결합니다.](how-to-create-manage-template.md#update-a-template-vm)

1. 하이퍼 V 역할 추가
2. 자바를 다운로드하고 설치합니다.  
3. 다운로드 및 안드로이드에 대한 비주얼 스튜디오 에뮬레이터를 설치합니다.
4. 다운로드 및 안드로이드 스튜디오를 설치합니다.
5. 안드로이드 스튜디오에 대한 비주얼 스튜디오 에뮬레이터를 구성합니다.

## <a name="add-hyper-v-role"></a>하이퍼 V 역할 추가

안드로이드에 대한 비주얼 스튜디오 에뮬레이터의 성공적인 설치를 위해 하이퍼 V를 활성화해야합니다.  템플릿 가상 시스템 아티클에서 [중첩 가상화를 활성화하는 방법의](how-to-enable-nested-virtualization-template-vm.md) 지침을 따릅니다.

## <a name="install-java"></a>Java 설치

안드로이드 스튜디오는 자바가 필요합니다.  자바의 최신 버전을 다운로드하려면 아래 단계에 따라.

1. [Java 다운로드 페이지로](https://www.java.com/download/)이동합니다. 자바 **다운로드** 버튼을 클릭합니다.
2. Java용 64비트 Windows 웹 페이지에서 동의 및 **무료 다운로드 시작이라고**표시된 버튼을 클릭합니다.
3. **Java 설치** 설치 프로그램이 나타나면 **설치를**클릭합니다.
4. 설치 프로그램 제목이 **Java 설치로**변경될 때까지 기다립니다.  **닫기 단추를** 클릭합니다.

## <a name="install-visual-studio-emulator-for-android"></a>안드로이드에 대한 비주얼 스튜디오 에뮬레이터를 설치

로컬로 Android 응용 프로그램을 테스트하려면 가상버전의 Android 장치를 사용해야 합니다.  개발자가 자신의 컴퓨터에서 응용 프로그램을 테스트 할 수 있도록 사용할 수있는 몇 가지 Android 에뮬레이터가 있습니다.  중첩 된 가상화를 지원하는 에뮬레이터이기 때문에 안드로이드용 Visual Studio 에뮬레이터를 사용하고 있습니다.  랩 서비스 VM은 이미 가상 시스템이기 때문에 중첩가상화를 지원하는 에뮬레이터가 필요합니다.  안드로이드 스튜디오에 대한 내장 에뮬레이터는 중첩 가상화를 지원하지 않습니다.  중첩 가상화를 지원하는 에뮬레이터를 보려면 [에뮬레이터 성능에 대한 하드웨어 가속(Hyper-V & HAXM)을](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)참조하십시오.

다음 지침을 사용하여 Android용 Visual Studio 에뮬레이터를 다운로드하여 설치합니다.

1. 안드로이드 홈 페이지에 [대한 비주얼 스튜디오 에뮬레이터로](https://visualstudio.microsoft.com/vs/msft-android-emulator/) 이동합니다.
2. **에뮬레이터 다운로드 버튼을 클릭합니다.**
3. vs_emulatorsetup.exe를 다운로드하면 실행 을 실행합니다.
4. Visual Studio 설치 대화 상자가 나타나면 **설치** 버튼을 클릭합니다.
5. 설치 프로그램이 완료될 때까지 기다립니다.  지금 **시작** 버튼을 클릭하여 컴퓨터를 다시 시작하고 설치를 완료합니다.

Android Studio를 사용하여 응용 프로그램을 배포하기 전에 먼저 에뮬레이터를 시작합니다.  안드로이드용 비주얼 스튜디오 에뮬레이터에 대한 자세한 내용은 [안드로이드 설명서용 비주얼 스튜디오 에뮬레이터를](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)참조하십시오.

## <a name="install-android-studio"></a>안드로이드 스튜디오 설치

다운로드 안드로이드 [스튜디오를](https://developer.android.com/studio)설치하려면 아래의 지침을 따르십시오.

1. 안드로이드 [스튜디오 다운로드 페이지로](https://developer.android.com/studio#downloads)이동합니다.  
    > [!NOTE]
    > 인터넷 익스플로러는 이 사이트에서 지원되지 않습니다.
2. 실행 가능한 Android Studio 패키지를 클릭합니다.
3. 팝업에 기록된 법적 용어를 읽어보십시오.  계속할 준비가 되면 **위의 이용 약관 확인란을 읽고 동의하고** **Windows용 Android Studio 다운로드** 버튼을 클릭합니다.
4. Android Studio 설치 실행 이 후에 실행 프로그램을 다운로드하면 실행 을 실행합니다.
5. **안드로이드** 스튜디오 설치 설치 프로그램의 안드로이드 스튜디오 설치 에 **오신 것을 환영합니다에,** **다음을**클릭합니다 .
6. 구성 **설정** 페이지에서 다음 을 **클릭합니다.**
7. 시작 **메뉴 폴더 선택** 페이지에서 **설치를**클릭합니다.
8. 설정이 완료될 때까지 기다립니다.
9. 설치 완료 페이지에서 **다음**을 **클릭합니다.**
10. 안드로이드 **스튜디오 설치 완료** 페이지에서.  **마침**을 클릭합니다.
11. 설정이 완료되면 Android Studio가 자동으로 시작됩니다.
12. Android **설정 가져오기 에서...** 대화 상자에서 **설정을 가져오지 않음을**선택합니다. **확인**을 클릭합니다.
13. Android 스튜디오 설치 **마법사의** **시작** 페이지에서 **다음을**클릭합니다.
14. 설치 **유형** 페이지에서 **표준**을 선택합니다. **다음**을 클릭합니다.
15. UI **테마 선택** 페이지에서 원하는 테마를 선택합니다. **다음**을 클릭합니다.
16. 설정 **확인** 페이지에서 다음 을 **클릭합니다.**
17. 구성 **요소 다운로드** 페이지에서 모든 구성 요소가 다운로드될 때까지 기다립니다.  **마침**을 클릭합니다.

    > [!IMPORTANT]
    > HAXM 설치에 실패할 것으로 예상됩니다.  HAXM은 중첩 가상화를 지원하지 않으므로 이 문서의 초초에 Android용 Visual Studio 에뮬레이터를 설치했습니다.

18. 설정 **마법사가** 완료되면 Android Studio 에 대한 환영 대화 상자가 나타납니다.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>안드로이드 스튜디오 와 안드로이드 에뮬레이터를 구성

안드로이드 스튜디오는 거의 사용할 준비가되어 있습니다.  우리는 여전히 안드로이드 SDK가 설치된 안드로이드에 대한 비주얼 스튜디오 에뮬레이터를 말할 필요가있다.  이렇게 하면 Android 용 Visual Studio에서 실행되는 에뮬레이터가 Android Studio 디버깅을 위한 배포 대상으로 표시됩니다.

우리는 안드로이드 SDK가있는 안드로이드에 대한 비주얼 스튜디오 에뮬레이터를 알려 특정 레지스트리 키를 설정해야합니다.  필요한 레지스트리 키를 설정하려면 아래 스크립트를 실행합니다.  아래 의 PowerShell 스크립트는 Android Sdk의 기본 설치 위치를 가정합니다.  다른 위치에 Android Sdk를 `$androidSdkPath` 설치한 경우 스크립트를 실행하기 전에 값을 수정합니다.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> 새로운 설정이 사용되도록 안드로이드와 안드로이드 스튜디오에 대한 비주얼 스튜디오 에뮬레이터를 다시 시작합니다.

비주얼 스튜디오 에뮬레이터에서 필요한 버전을 시작합니다.  그것은 안드로이드 스튜디오에서 안드로이드 응용 프로그램에 대한 배포 대상으로 표시됩니다.  안드로이드 스튜디오 프로젝트에 대한 최소 버전은 안드로이드에 대한 비주얼 스튜디오 에뮬레이터에서 실행되는 버전을 지원해야합니다.  이제 안드로이드 스튜디오와 안드로이드에 대한 비주얼 스튜디오 에뮬레이터를 사용하여 프로젝트를 만들고 디버깅 할 준비가되었습니다.  문제가 있는 경우 Android 에뮬레이터 문제 해결을 참조하십시오.

## <a name="cost"></a>비용

이 랩의 비용을 추정하려면 아래 예제를 따를 수 있습니다.
25명의 학생이 20시간의 수업 시간 및 숙제 또는 과제에 대한 할당량 10시간의 수업의 경우, 랩의 가격은  

25명의 \* 학생(20명 + 10할당량) 시간 * 55랩 유닛 * 시간당 0.01 USD = 412.5 USD

가격에 대한 자세한 내용은 [Azure 랩 서비스 가격 을](https://azure.microsoft.com/pricing/details/lab-services/)참조하십시오.

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정하는 데 일반적입니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에게 이메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
