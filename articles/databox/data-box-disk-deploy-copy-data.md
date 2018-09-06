---
title: Microsoft Azure Data Box Disk에 데이터 복사 | Microsoft Docs
description: 이 자습서를 사용하여 Azure Data Box Disk에 데이터를 복사하는 방법 알아보기
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: f25d0b3522658d5fcd4b34110cb03b624dd9e7b1
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841508"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>자습서: Azure Data Box Disk에 데이터 복사 및 확인

이 자습서에는 호스트 컴퓨터에서 데이터를 복사한 다음, 데이터 무결성을 확인하는 체크섬을 생성하는 방법을 설명합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Data Box Disk에 데이터 복사
> * 데이터 무결성 확인

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 사항을 확인합니다.
- [자습서: Azure Data Box Disk 설치 및 구성](data-box-disk-deploy-set-up.md)을 완료했습니다.
- 디스크가 압축되고 켜져 있습니다.
- 디스크에 데이터를 복사할 호스트 컴퓨터가 있습니다. 호스트 컴퓨터는 다음 사항이 필수입니다.
    - [지원되는 운영 체제](data-box-disk-system-requirements.md)를 실행합니다.
    - [Windows PowerShell 4가 설치되어](https://www.microsoft.com/download/details.aspx?id=40855) 있습니다.
    - [.NET Framework 4.5가 설치되어](https://www.microsoft.com/download/details.aspx?id=30653) 있습니다.


## <a name="copy-data-to-disks"></a>디스크에 데이터 복사

다음 단계를 수행하여 컴퓨터에서 Data Box Disk로 데이터를 연결하고 복사합니다.

1. 잠금 해제된 드라이브의 콘텐츠를 봅니다. 

    ![드라이브 콘텐츠 보기](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. BlockBlob 폴더에서 블록 Blob으로 가져와야 하는 데이터를 복사합니다. 마찬가지로 VHD/VHDX와 같은 데이터를 PageBlob 폴더로 복사합니다. 

    BlockBlob 및 PageBlob 폴더 아래에 각 하위 폴더에 대한 Azure 저장소 계정에 컨테이너가 만들어집니다. BlockBlob 및 PageBlob 폴더 아래의 모든 파일은 Azure Storage 계정 아래의 기본 컨테이너 `$root`로 복사됩니다. `$root` 컨테이너 있는 모든 파일은 항상 블록 Blob으로 업로드됩니다.

    파일 및 폴더가 루트 디렉터리에 있는 경우 데이터 복사를 시작하기 전에 다른 폴더로 이동해야 합니다.

    컨테이너 및 Blob 이름에 대해 Azure 명명 요구 사항을 따릅니다.

    |엔터티   |규칙  |
    |---------|---------|
    |컨테이너 이름 블록 Blob 및 페이지 Blob     |문자 또는 숫자로 시작해야 하며, 소문자, 숫자 및 하이픈(-)만 포함할 수 있습니다. 모든 하이픈(-)은 앞뒤에 문자 또는 숫자가 와야 합니다. 이름에 연속적인 하이픈은 허용되지 않습니다. <br>올바른 DNS 이름은 3~63자여야 합니다.          |
    |블록 Blob 및 페이지 Blob에 대한 Blob 이름    |Blob 이름은 대/소문자를 구분하며 문자 조합을 포함할 수 있습니다. <br>Blob 이름은 길이가 1~1,024자 사이여야 합니다.<br>예약된 URL 문자는 적절히 이스케이프되어야 합니다.<br>Blob 이름을 구성하는 경로 세그먼트 수는 254개를 초과할 수 없습니다. 경로 세그먼트는 가상 디렉터리 이름에 해당하는 연속 구분 기호 문자 사이의 문자열입니다(예: 슬래시 '/').         |

    > [!IMPORTANT] 
    > 모든 컨테이너 및 Blob은 [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions)을 준수해야 합니다. 이러한 규칙을 따르지 않는 경우 Azure로 데이터 업로드에 실패합니다.

3. 파일을 복사하는 경우 파일이 블록 Blob에 대해 4.7TiB 및 페이지 Blob에 대해 8TiB를 초과하지 않도록 합니다. 
4. 파일 탐색기로 끌어서 놓기를 사용하여 데이터를 복사할 수 있습니다. Robocopy와 같은 SMB 호환 파일 복사 도구를 사용하여 데이터를 복사할 수도 있습니다. 여러 복사 작업은 다음 Robocopy 명령을 사용하여 시작될 수 있습니다.

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    명령에 대한 매개 변수 및 옵션은 다음과 같이 표로 정리됩니다.
    
    |매개 변수/옵션  |설명 |
    |--------------------|------------|
    |원본            | 원본 디렉터리에 대한 경로를 지정합니다.        |
    |대상       | 대상 디렉터리에 대한 경로를 지정합니다.        |
    |/E                  | 빈 디렉터리를 포함하는 하위 디렉터리를 복사합니다. |
    |/MT[:N]             | N 스레드를 사용하여 다중 스레드 복사본을 만듭니다. 여기서 N은 1에서 128 사이의 정수입니다. <br>N의 기본값은 8입니다.        |
    |/R: <N>             | 실패한 복사에 대한 재시도 횟수를 지정합니다. N의 기본값은 1,000,000입니다(백만 번의 다시 시도).        |
    |/W: <N>             | 다시 시도 간의 대기 시간을 지정합니다(초). N의 기본값은 30입니다(대기 시간 30초).        |
    |/NFL                | 기록하지 않을 파일 이름을 지정합니다.        |
    |/NDL                | 기록하지 않을 디렉터리 이름을 지정합니다.        |
    |/FFT                | FAT 파일 시간을 가정합니다(2초 자릿수).        |
    |/Log:<Log File>     | 로그 파일에 상태 출력을 작성합니다(기존 로그 파일을 덮어씀).         |

    각 디스크에서 실행 중인 여러 작업을 사용하여 병렬로 여러 디스크를 사용할 수 있습니다. 

6. 작업이 진행되면 복사 상태를 확인합니다. 다음 샘플에서는 Data Box Disk에 파일을 복사할 robocopy 명령의 출력을 보여줍니다.

    ```
    C:\Users>robocopy
        -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:53 PM
           Simple Usage :: ROBOCOPY source destination /MIR
    
                 source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                   /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                   Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
       Files :        17        17         0         0         0         0
       Bytes :     3.9 k     3.9 k         0         0         0         0
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    
7. 대상 폴더를 열어 복사된 파일을 보고 확인합니다. 복사 프로세스 중 오류가 있는 경우 문제 해결을 위해 로그 파일을 다운로드합니다. 로그 파일은 robobopy 명령에 지정된 위치에 있습니다.
 


> [!IMPORTANT]
> - 적절한 데이터 형식에 해당하는 폴더에 데이터를 복사하는지 확인해야 합니다. 예를 들어 블록 Blob에 대한 폴더에 블록 Blob 데이터를 복사합니다. 데이터 형식이 적절한 폴더(저장소 형식)와 일치하지 않는 경우 이후 단계에서 Azure에 대한 데이터 업로드가 실패합니다.
> -  데이터를 복사하는 동안 데이터 크기가 [Azure 저장소 및 Data Box Disk 제한](data-box-disk-limits.md)에 설명된 크기 제한을 준수하는지 확인합니다. 
> - Data Box Disk에 의해 업로드되는 데이터가 Data Box Disk 외부의 다른 응용 프로그램에 의해 동시에 업로드되는 경우 업로드 작업이 실패하고 데이터 손상이 발생할 수 있습니다.

## <a name="verify-data-integrity"></a>데이터 무결성 확인

데이터 무결성을 확인하려면 다음 단계를 수행합니다.

1. 체크섬 유효성 검사를 위해 `AzureExpressDiskService.ps1`을 실행합니다. 파일 탐색기에서 드라이브의 *AzureImportExport* 폴더로 이동합니다. **PowerShell을 사용하여 실행**을 마우스 오른쪽 단추로 클릭하고 선택합니다. 

    ![체크섬 실행](media/data-box-disk-deploy-copy-data/data-box-disk-checksum.png)

2. 데이터 크기에 따라 이 단계는 시간이 걸릴 수 있습니다. 스크립트가 완료되면 프로세스가 완료되는 시간과 함께 데이터 무결성 확인 프로세스의 요약이 표시됩니다. **Enter** 키를 눌러 명령 창을 종료할 수 있습니다.

    ![체크섬 출력](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

3. 여러 디스크를 사용하는 경우 각 디스크에 대해 명령을 실행합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box Disk 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Data Box Disk에 데이터 복사
> * 데이터 무결성 확인

Data Box Disk를 반환하고 Azure에 대한 데이터 업로드를 확인하는 방법을 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [Microsoft로 Azure Data Box 반송](./data-box-disk-deploy-picked-up.md)

