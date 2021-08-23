---
title: MSIXMGR 도구 사용 - Azure
description: Azure Virtual Desktop용 MSIXMGR 도구를 사용하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 7b997f817e8e430d6f2985f73e4005da86dcda10
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745532"
---
# <a name="using-the-msixmgr-tool"></a>MSIXMGR 도구 사용

MSIXMGR 도구는 MSIX 패키지 애플리케이션을 MSIX 이미지로 확장하는 데 사용됩니다. 이 도구는 MSIX 패키지 애플리케이션(.MSIX)을 사용하여 VHD, VHDx 또는 CIM 파일로 확장합니다. 결과 MSIX 이미지는 Azure Virtual Desktop 배포에서 사용하는 Azure Storage 계정에 저장됩니다. 이 문서에서는 MSIXMGR 도구를 사용하는 방법을 보여줍니다.

>[!NOTE]
>호환성을 보장하려면 MSIX 이미지를 저장하는 CIM이 Azure Virtual Desktop 호스트 풀에서 실행 중인 OS 버전에 생성되었는지 확인합니다. MSIXMGR은 CIM 파일을 만들 수 있지만 Windows 10 20H2를 실행하는 호스트 풀에서만 해당 파일을 사용할 수 있습니다.

## <a name="requirements"></a>요구 사항

이 문서의 지침을 따르려면 먼저 다음 작업을 수행해야 합니다.

- [MSIXMGR 도구 다운로드](https://aka.ms/msixmgr)
- MSIX 패키지 애플리케이션(.MSIX 파일) 가져오기
- MSIX 이미지를 만들 머신에 대한 관리 권한 가져오기

## <a name="create-an-msix-image"></a>MSIX 이미지 만들기

확장은 MSIX 패키지 애플리케이션(.MSIX)을 가져와 MSIX 이미지(.VHD(x) 또는 .CIM 파일)로 압축을 푸는 프로세스입니다.

MSIX 파일을 확장하려면 다음을 수행합니다.

1. 아직 다운로드하지 않았다면 [MSIXMGR 도구를 다운로드](https://aka.ms/msixmgr)합니다.

2. 로컬 폴더에 MSIXMGR.zip 압축을 풉니다.

3. 관리자 모드에서 명령 프롬프트를 엽니다.

4. 2단계에서 로컬 폴더를 찾습니다.

5. 명령 프롬프트에서 다음 명령을 실행하여 MSIX 이미지를 만듭니다.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    자리 표시자 값을 관련 값으로 바꿔야 합니다. 예를 들어:

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. 이제 이미지를 만들었으므로 대상 폴더로 이동하여 MSIX 이미지(.VHDX)를 성공적으로 만들었는지 확인합니다.

## <a name="create-an-msix-image-in-a-cim-file"></a>CIM 파일에 MSIX 이미지 만들기

[5단계](#create-an-msix-image)의 명령을 사용하여 파일 유형과 대상 경로를 바꿔서 CIM 및 VHDX 파일을 만들 수도 있습니다.

예를 들어 이 명령을 사용하여 CIM 파일을 만드는 방법은 다음과 같습니다.

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

이 명령을 사용하여 VHDX를 만드는 방법은 다음과 같습니다.

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>다음 단계

[MSIX 앱 연결이란?](what-is-app-attach.md)에서 MSIX 앱 연결에 대해 자세히 알아보세요.

앱 연결을 설정하는 방법을 알아보려면 다음 문서를 확인하세요.

- [Azure Portal을 사용하여 MSIX 앱 연결 설정](app-attach-azure-portal.md)
- [PowerShell을 사용하여 MSIX 앱 연결 설정](app-attach-powershell.md)
- [MSIX 앱 연결용 PowerShell 스크립트 만들기](app-attach.md)
- [Azure Virtual Desktop용 MSIX 이미지 준비](app-attach-image-prep.md)
- [MSIX 앱 연결에 대한 파일 공유 설정](app-attach-file-share.md)

MSIX 앱 연결에 대한 질문이 있으면 [앱 연결 FAQ](app-attach-faq.md) 및 [앱 연결 용어집](app-attach-glossary.md)을 참조하세요.
