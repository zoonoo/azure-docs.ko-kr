---
title: Windows Virtual Desktop에 Microsoft Office FSLogix 애플리케이션 컨테이너 설치 - Azure
description: 앱 규칙 편집기를 사용하여 Windows Virtual Desktop에서 Office로 FSLogix 애플리케이션 컨테이너를 만드는 방법을 알아봅니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a144adcfbf6c7cefc6b946f95bdb734868de801f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446811"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>FSLogix 애플리케이션 컨테이너를 사용하여 Microsoft Office 설치

FSLogix 애플리케이션 컨테이너를 호스트 풀의 다른 VM(가상 머신)에 대한 템플릿으로 사용하여 빠르고 효율적으로 Microsoft Office를 설치할 수 있습니다.

FSLogix 앱 컨테이너를 사용하면 더 빠르게 설치할 수 있는 이유는 다음과 같습니다.

- Office 앱을 앱 컨테이너에 오프로드하면 C 드라이브 크기에 대한 요구 사항이 줄어듭니다.
- VM의 스냅샷 또는 백업에서 리소스를 더 적게 사용합니다.
- 단일 이미지 업데이트를 통해 자동화된 파이프라인을 사용하면 VM을 더 쉽게 업데이트할 수 있습니다.
- Windows Virtual Desktop 배포 시 모든 VM에 Office(및 기타 앱)를 설치하려면 하나의 이미지만 필요합니다.

이 문서에서는 Office로 FSLogix 애플리케이션 컨테이너를 설정하는 방법을 보여줍니다.

## <a name="requirements"></a>요구 사항

규칙 편집기를 설정 하려면 다음 항목이 필요합니다.

- Office가 설치되지 않은 Windows를 실행하는 VM
- Office의 복사본
- 배포 시 설치된 FSLogix의 복사본
- 호스트 풀의 모든 VM에 읽기 전용 액세스 권한이 있는 네트워크 공유

## <a name="install-office"></a>Office 설치

VHD 또는 VHDX에 Office를 설치하려면 VM에서 원격 데스크톱 프로토콜을 사용하도록 설정한 다음, [VHD 마스터 이미지에 Office 설치](install-office-on-wvd-master-image.md)의 지침을 따르세요. 설치할 때 [올바른 라이선스](overview.md#requirements)를 사용하고 있어야 합니다.

>[!NOTE]
>Windows Virtual Desktop을 사용하려면 SCA(공유 컴퓨터 인증)가 필요합니다.

## <a name="install-fslogix"></a>FSLogix 설치

FSLogix 및 규칙 편집기를 설치하려면 [FSLogix 다운로드 및 설치](/fslogix/install-ht)의 지침을 따르세요.

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Office를 저장할 VHD 만들기 및 준비

다음으로, 규칙 편집기를 사용할 VHD 이미지를 만들고 준비해야 합니다.

1. 관리자 권한으로 명령 프롬프트를 엽니다. 다음 명령을 실행합니다.

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > 이 명령에 표시되는 빈 공간을 유지해야 합니다.

2. 다음 명령을 실행합니다.

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   서비스를 찾으면 3단계를 계속하기 전에 VM을 다시 시작합니다.

    ```cmd
    net stop ClickToRunSvc
    ```

3. **프로그램 파일** > **FSLogix** > **앱** 으로 이동하고 다음 명령을 실행하여 대상 VHD를 만듭니다.

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    이 명령을 사용하여 만든 VHD는 C:\\Program Files\\Microsoft Office 폴더를 포함해야 합니다.

    >[!NOTE]
    >오류가 표시되면 Office를 제거하고 1단계부터 다시 시작합니다.

## <a name="configure-the-rule-editor"></a>규칙 편집기 구성

이제 이미지를 준비했으므로 규칙 편집기를 구성하고 규칙을 저장할 파일을 만들어야 합니다.

1. **프로그램 파일** > **FSLogix** > **앱** 으로 이동하고 **RuleEditor.exe** 를 실행합니다.

2. **파일** > **새로 만들기** > **만들기** 를 선택하여 새 규칙 집합을 만든 다음, 해당 규칙 집합을 로컬 폴더에 저장합니다.

3. **빈 규칙 집합** 을 선택한 다음, **확인** 을 선택합니다.

4. **+** 단추를 선택합니다. 그러면 **규칙 추가** 창이 열리고 **규칙 추가** 대화 상자의 옵션이 변경됩니다.

5. 드롭다운 메뉴에서 **앱 컨테이너(VHD) 규칙** 을 선택합니다.

6. **폴더** 필드에 **C:\\Program Files\\Microsoft Office** 를 입력합니다.

7. **디스크 파일** 필드의 경우 **대상 VHD 만들기** 섹션에서 **\<path\>\\office.vhd** 를 선택합니다.

8. **확인** 을 선택합니다.

9. **C:\\Users\\\<username\>\\Documents\\FSLogix Rule Sets** 에서 작업 폴더로 이동하고 .frx 및 .fxa 파일을 찾습니다. 규칙이 작동되도록 하려면 이러한 파일을 **C:\\Program Files\\FSLogix\\Apps\\Rules** 에 있는 Rules 폴더로 이동해야 합니다.

10. 규칙이 적용되도록 **시스템에 규칙 적용** 을 선택합니다.

     >[!NOTE]
     > 모든 세션 호스트에 필요한 앱 규칙 파일을 적용해야 합니다.

## <a name="next-steps"></a>다음 단계

FSLogix에 대해 자세히 알아보려면 [FSLogix 설명서](/fslogix/)를 확인하세요.