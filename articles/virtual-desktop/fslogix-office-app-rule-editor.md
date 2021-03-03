---
title: Windows 가상 데스크톱에 Microsoft Office FSLogix 응용 프로그램 컨테이너 설치-Azure
description: 앱 규칙 편집기를 사용 하 여 Windows 가상 데스크톱에서 Office를 사용 하 여 FSLogix 응용 프로그램 컨테이너를 만드는 방법입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7d66d370f321323ec1aeb45ad0fe628904b14fe6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746037"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>FSLogix 응용 프로그램 컨테이너를 사용 하 여 Microsoft Office 설치

FSLogix 응용 프로그램 컨테이너를 호스트 풀의 다른 Vm (가상 컴퓨터)에 대 한 템플릿으로 사용 하 여 빠르고 효율적으로 Microsoft Office를 설치할 수 있습니다.

다음은 FSLogix 앱 컨테이너를 사용 하 여 더 빠르게 설치 하는 데 도움이 되는 이유입니다.

- Office 앱을 앱 컨테이너로 오프 로드 하면 C 드라이브 크기에 대 한 요구 사항이 줄어듭니다.
- VM의 스냅숏 또는 백업에서 리소스를 더 많이 사용 합니다.
- 단일 이미지를 업데이트 하는 자동 파이프라인을 사용 하면 Vm을 더 쉽게 업데이트할 수 있습니다.
- Windows 가상 데스크톱 배포의 모든 Vm에 Office (및 기타 앱)를 설치 하는 이미지는 한 개만 필요 합니다.

이 문서에서는 Office를 사용 하 여 FSLogix 응용 프로그램 컨테이너를 설정 하는 방법을 보여 줍니다.

## <a name="requirements"></a>요구 사항

규칙 편집기를 설정 하려면 다음 항목이 필요 합니다.

- Office가 설치 되지 않은 Windows를 실행 하는 VM
- Office의 복사본입니다.
- 배포에 설치 된 FSLogix의 복사본
- 호스트 풀의 모든 Vm에 대 한 읽기 전용 액세스 권한이 있는 네트워크 공유

## <a name="install-office"></a>Office 설치

VHD 또는 VHDX에 Office를 설치 하려면 VM에서 원격 데스크톱 프로토콜를 사용 하도록 설정 하 고 [vhd 마스터 이미지에 Office 설치](install-office-on-wvd-master-image.md)의 지침을 따르세요. 를 설치할 때 [올바른 라이선스](overview.md#requirements)를 사용 하 고 있는지 확인 합니다.

>[!NOTE]
>Windows 가상 데스크톱을 사용 하려면 SCA (공유 컴퓨터 정품 인증)가 필요 합니다.

## <a name="install-fslogix"></a>FSLogix 설치

FSLogix 및 규칙 편집기를 설치 하려면 [Fslogix 다운로드 및 설치](/fslogix/install-ht)의 지침을 따르세요.

## <a name="create-and-prepare-a-vhd-to-store-office"></a>사무실을 저장할 VHD 만들기 및 준비

다음에는에서 규칙 편집기를 사용할 VHD 이미지를 만들고 준비 해야 합니다.

1. 관리자 권한으로 명령 프롬프트를 엽니다. 다음 명령을 실행 합니다.

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > 이 명령에 표시 되는 빈 공간을 유지 해야 합니다.

2. 다음 명령을 실행 합니다.

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   서비스를 찾으면 3 단계를 계속 하기 전에 VM을 다시 시작 합니다.

    ```cmd
    net stop ClickToRunSvc
    ```

3. 그런 다음 **Program Files**  >  **fslogix**  >  **앱** 으로 이동 하 고 다음 명령을 실행 하 여 대상 VHD를 만듭니다.

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    이 명령을 사용 하 여 만든 VHD는 C: \\ Program Files Microsoft Office 폴더를 포함 해야 합니다 \\ .

    >[!NOTE]
    >오류가 표시 되 면 Office를 제거 하 고 1 단계부터 다시 시작 합니다.

## <a name="configure-the-rule-editor"></a>규칙 편집기 구성

이제 이미지를 준비 했으므로 규칙 편집기를 구성 하 고 규칙을 저장할 파일을 만들어야 합니다.

1. **프로그램 파일**  >  **fslogix**  >  **앱** 으로 이동 하 고 **RuleEditor.exe** 를 실행 합니다.

2. **파일**  >  **새로**  >  **만들기** 를 선택 하 여 새 규칙 집합을 만든 다음 해당 규칙 집합을 로컬 폴더에 저장 합니다.

3. **빈 규칙 집합** 을 선택 하 고 **확인** 을 선택 합니다.

4. **+** 단추를 선택합니다. 그러면 **규칙 추가** 창이 열립니다. 그러면 **규칙 추가** 대화 상자의 옵션이 변경 됩니다.

5. 드롭다운 메뉴에서 **앱 컨테이너 (VHD) 규칙** 을 선택 합니다.

6. **폴더** 필드에 **C: \\ Program Files \\ Microsoft Office** 을 입력 합니다.

7. **디스크 파일** 필드의 경우 **대상 vhd 만들기** 섹션에서 **\<path\> \\ office .vhd** 를 선택 합니다.

8. **확인** 을 선택합니다.

9. **C: \\ Users \\ \<username\> \\ Documents \\ fslogix 규칙 집합** 에서 작업 폴더로 이동 하 고 frx 및. fxa 파일을 찾습니다. 규칙의 작동이 시작 되도록 하려면 이러한 파일을 **C: \\ Program files \\ fslogix \\ \\ 규칙** 에 있는 rules 폴더로 이동 해야 합니다.

10. 규칙 **적용을 위해 시스템에 규칙 적용** 을 선택 합니다.

     >[!NOTE]
     > 앱 규칙 파일은 모든 세션 호스트에 적용 해야 합니다.

## <a name="next-steps"></a>다음 단계

FSLogix에 대해 자세히 알아보려면 [fslogix 설명서](/fslogix/)를 확인 하세요.