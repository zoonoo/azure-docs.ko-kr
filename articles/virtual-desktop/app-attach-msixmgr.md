---
title: MSIXMGR 도구 사용-Azure
description: Windows 가상 데스크톱에 대 한 MSIXMGR 도구를 사용 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4b34fb0d3bb2d49255007b9722a0a636c1441b8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745259"
---
# <a name="using-the-msixmgr-tool"></a>MSIXMGR 도구 사용

MSIXMGR 도구는 msix 패키지 응용 프로그램을 MSIX 이미지로 확장 하는 데 사용할 수 있습니다. 이 도구는 MSIX 패키지 응용 프로그램 ()을 사용 합니다. MSIX)로 확장 하 고 VHD, VHDx 또는 CIM 파일로 확장 합니다. 결과 MSIX 이미지는 Windows 가상 데스크톱 배포에서 사용 하는 Azure Storage 계정에 저장 됩니다. 이 문서에서는 MSIXMGR 도구를 사용 하는 방법을 보여 줍니다.

>[!NOTE]
>호환성을 보장 하려면 MSIX 이미지를 저장 하는 CIMs가 Windows 가상 데스크톱 호스트 풀에서 실행 중인 OS 버전에 생성 되었는지 확인 합니다. MSIXMGR은 CIM 파일을 만들 수 있지만 Windows 10 20H2를 실행 하는 호스트 풀 에서만 해당 파일을 사용할 수 있습니다.

## <a name="requirements"></a>요구 사항

이 문서의 지침을 따르려면 먼저 다음 작업을 수행 해야 합니다.

- [MSIXMGR 도구 다운로드](https://aka.ms/msixmgr)
- MSIX 패키지 응용 프로그램을 가져옵니다 (. MSIX 파일)
- MSIX 이미지를 만들 컴퓨터에 대 한 관리 권한 가져오기

## <a name="create-an-msix-image"></a>MSIX 이미지 만들기

확장은 MSIX 패키지 응용 프로그램 ()을 수행 하는 프로세스입니다. MSIX)로 압축을 푼 후 MSIX 이미지 (. VHD (x) 또는 CIM 파일).

MSIX 파일을 확장 하려면:

1. 아직 없는 경우 [MSIXMGR 도구를 다운로드](https://aka.ms/msixmgr) 합니다.

2. 로컬 폴더에 MSIXMGR.zip 압축을 풉니다.

3. 관리자 모드에서 명령 프롬프트를 엽니다.

4. 2 단계에서 로컬 폴더를 찾습니다.

5. 명령 프롬프트에서 다음 명령을 실행 하 여 MSIX 이미지를 만듭니다.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    자리 표시자 값을 관련 값으로 대체 해야 합니다. 예를 들면 다음과 같습니다.

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. 이제 이미지를 만들었으므로 대상 폴더로 이동 하 여 MSIX 이미지 ()를 성공적으로 만들었는지 확인 합니다. VHDX).

## <a name="create-an-msix-image-in-a-cim-file"></a>CIM 파일에 MSIX 이미지 만들기

또한 [5 단계의](#create-an-msix-image) 명령을 사용 하 여 파일 유형과 대상 경로를 바꿔서 CIM 및 VHDX 파일을 만들 수 있습니다.

예를 들어이 명령을 사용 하 여 CIM 파일을 만드는 방법은 다음과 같습니다.

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

이 명령을 사용 하 여 VHDX를 만드는 방법은 다음과 같습니다.

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>다음 단계

Msix 앱 연결에 대 한 자세한 내용은 [msix 앱](what-is-app-attach.md) 연결에 대해 자세히 알아보세요.

앱 연결을 설정 하는 방법을 알아보려면 다음 문서를 확인 하세요.

- [Azure Portal을 사용하여 MSIX 앱 연결 설정](app-attach-azure-portal.md)
- [PowerShell을 사용 하 여 MSIX 앱 연결 설정](app-attach-powershell.md)
- [MSIX 앱 연결을 위한 PowerShell 스크립트 만들기](app-attach.md)
- [Windows 가상 데스크톱에 대 한 MSIX 이미지 준비](app-attach-image-prep.md)
- [MSIX 앱 연결에 대 한 파일 공유 설정](app-attach-file-share.md)

MSIX 앱 연결에 대 한 질문이 있으면 [앱 연결 FAQ](app-attach-faq.md) 및 [앱 연결 용어집](app-attach-glossary.md)을 참조 하세요.
