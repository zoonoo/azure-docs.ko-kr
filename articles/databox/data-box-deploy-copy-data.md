---
title: Microsoft Azure Data Box에 데이터 복사 | Microsoft Docs
description: Azure Data Box에 데이터를 복사하는 방법 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/10/2018
ms.author: alkohli
ms.openlocfilehash: b59830677ac8c07c6b7adbab24c82ca25d71f5a0
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093462"
---
# <a name="tutorial-copy-data-to-azure-data-box"></a>자습서: Azure Data Box에 데이터 복사 

이 자습서에서는 로컬 웹 UI를 사용하여 호스트 컴퓨터에서 연결하고 데이터를 복사한 다음, Data Box 배송을 준비하는 방법을 설명합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Data Box에 연결
> * Data Box에 데이터 복사
> * Data Box 배송 준비

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 사항을 확인합니다.

1. [자습서: Azure Data Box 설정](data-box-deploy-set-up.md)을 완료했습니다.
2. Data Box를 받았고 포털의 주문 상태가 **배달됨**입니다.
3. Data Box에 복사할 데이터가 포함된 호스트 컴퓨터가 있습니다. 호스트 컴퓨터는 다음 사항이 필수입니다.
    - [지원되는 운영 체제](data-box-system-requirements.md)를 실행합니다.
    - 고속 네트워크에 연결되어 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용할 수 있지만, 이 경우 복사 속도가 떨어집니다. 

## <a name="connect-to-data-box"></a>Data Box에 연결

선택한 저장소 계정에 따라, Data Box가 다음 항목을 만듭니다.
- GPv1 및 GPv2에서 연결된 각 저장소에 대한 공유 3개.
- 프리미엄 또는 Blob 저장소 계정에 대한 공유 1개. 

블록 Blob 및 페이지 Blob 공유에서는 첫 번째 수준 엔터티가 컨테이너, 두 번째 수준 엔터티가 Blob입니다. Azure Files 공유에서 첫 번째 수준 엔터티는 공유, 두 번째 수준 엔터티는 파일입니다.

아래 예제를 고려해 보세요. 

- 저장소 계정: *Mystoracct*
- 블록 Blob 공유: *Mystoracct_BlockBlob/my-container/blob*
- 페이지 Blob 공유: *Mystoracct_PageBlob/my-container/blob*
- 파일 공유: *Mystoracct_AzFile/my-share*

Data Box가 Windows Server 호스트 컴퓨터에 연결되는지 아니면 Linux 호스트에 연결되는지에 따라 연결하고 복사하는 단계가 달라질 수 있습니다.

### <a name="connect-via-smb"></a>SMB를 통해 연결 

Windows Server 호스트 컴퓨터를 사용하는 경우 다음 단계에 따라 Data Box에 연결합니다.

1. 첫 번째 단계는 세션을 인증하고 시작하는 것입니다. **연결 및 복사**로 이동합니다. **자격 증명 가져오기**를 클릭하여 저장소 계정과 연결된 공유의 액세스 자격 증명을 가져옵니다. 

    ![공유 자격 증명 가져오기 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. [액세스 공유 및 데이터 복사] 대화 상자에서 공유에 해당하는 **사용자 이름** 및 **암호**를 복사합니다. **확인**을 클릭합니다.
    
    ![공유 자격 증명 가져오기 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. 저장소 계정(다음 예제의 Mystoracct)과 연결된 공유에 액세스합니다. `\\<IP of the device>\ShareName` 경로를 사용하여 공유에 액세스합니다. 데이터 형식에 따라 다음 주소의 공유에 연결합니다(동일한 이름 사용). 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    호스트 컴퓨터에서 공유에 연결하려면 명령 창을 엽니다. 명령 프롬프트에 다음을 입력합니다.

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    메시지가 표시되면 공유 암호를 입력합니다. 다음 샘플은 이전 명령을 통해 공유에 연결하는 방법을 보여줍니다.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Windows + R을 누르고 **실행** 창에서 `\\<device IP address>`를 지정합니다. **확인**을 클릭합니다. 그러면 [파일 탐색기]가 열립니다. 이제 공유가 폴더로 표시될 것입니다.
    
    ![파일 탐색기를 통해 공유에 연결 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5.  **복사하려는 파일에 대한 폴더는 항상 공유 아래에 만든 다음 이 폴더에 파일을 복사합니다**. 때로는 폴더에 회색 + 기호가 표시될 수 있습니다. 이 기호는 오류 조건을 나타내는 것이 아닙니다. 응용 프로그램이 상태를 추적하기 위해 폴더에 플래그를 지정합니다.
    
    ![파일 탐색기를 통해 공유에 연결 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) ![파일 탐색기를 통해 공유에 연결 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

### <a name="connect-via-nfs"></a>NFS를 통해 연결 

Linux 호스트 컴퓨터를 사용하는 경우 다음 단계에 따라 NFS 클라이언트에 대한 액세스를 허용하도록 Data Box를 구성합니다.

1. 공유에 액세스할 수 있도록 허용된 클라이언트의 IP 주소를 입력합니다. 로컬 웹 UI에서 **연결 및 복사** 페이지로 이동합니다. **NFS 설정** 아래에서 **NFS 클라이언트 액세스**를 클릭합니다. 

    ![NFS 클라이언트 액세스 구성 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. NFS 클라이언트의 IP 주소를 입력하고 **추가**를 클릭합니다. 이 단계를 반복하여 여러 NFS 클라이언트에 대한 액세스를 구성할 수 있습니다. **확인**을 클릭합니다.

    ![NFS 클라이언트 액세스 구성 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Linux 호스트 컴퓨터에 [지원되는 버전](data-box-system-requirements.md)의 NFS 클라이언트를 설치합니다. 해당 Linux 배포판에 맞는 버전을 사용하세요. 

3. NFS 클라이언트가 설치되면 다음 명령을 사용하여 Data Box 장치에 NFS 공유를 탑재합니다.

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    다음 예제에서는 NFS를 통해 Data Box 공유에 연결하는 방법을 보여줍니다. Data Box 장치 IP는 `10.161.23.130`이고, `Mystoracct_Blob` 공유는 ubuntuVM에 탑재되며, 탑재 지점은 `/home/databoxubuntuhost/databox`입니다.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Data Box에 데이터 복사

Data Box 공유에 연결된 후에는 데이터를 복사합니다. 데이터를 복사하기 전에, 다음 사항을 검토합니다.

- 적절한 데이터 형식에 해당하는 공유에 데이터를 복사해야 합니다. 예를 들어 블록 Blob에 대한 공유에 블록 Blob 데이터를 복사합니다. 데이터 형식이 적절한 공유 형식과 일치하지 않는 경우 이후 단계에서 Azure에 데이터를 업로드하는 작업이 실패합니다.
-  데이터를 복사하는 동안 데이터 크기가 [Azure 저장소 및 Data Box 제한](data-box-limits.md)에 설명된 크기 제한을 준수해야 합니다. 
- Data Box에 의해 업로드되는 데이터가 Data Box 외부의 다른 응용 프로그램에 의해 동시에 업로드되는 경우 업로드 작업이 실패하고 데이터 손상이 발생할 수 있습니다.
- SMB 및 NFS를 동시에 사용하거나 Azure의 동일한 최종 대상에 동일한 데이터를 복사하지 않는 것이 좋습니다. 이 경우 최종 결과를 확인할 수 없습니다.

### <a name="copy-data-via-smb"></a>SMB를 통해 데이터 복사

SMB 공유에 연결한 후에는 데이터 복사를 시작합니다. 

Robocopy처럼 SMB 호환 파일 복사 도구를 사용하여 데이터를 복사할 수 있습니다. Robocopy 명령을 사용하여 여러 복사 작업을 시작할 수 있습니다. 다음 명령을 사용합니다.
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 다음 표에는 특성이 설명되어 있습니다.
    
|특성  |설명  |
|---------|---------|
|/e     |빈 디렉터리를 포함하는 하위 디렉터리를 복사합니다.         |
|/r:     |실패한 복사에 대한 재시도 횟수를 지정합니다.         |
|/w:     |다시 시도 간의 대기 시간을 지정합니다(초).         |
|/is     |동일한 파일을 포함합니다.         |
|/nfl     |기록하지 않을 파일 이름을 지정합니다.         |
|/ndl    |기록하지 않을 디렉터리 이름을 지정합니다.        |
|/np     |복사 작업의 진행률(지금까지 복사된 파일 또는 디렉터리의 수)이 표시되지 않도록 지정합니다. 진행률을 표시하면 성능이 대폭 저하됩니다.         |
|/MT     | 다중 스레딩을 사용합니다. 32 또는 64 스레드를 권장합니다. 이 옵션은 암호화된 파일에 사용되지 않습니다. 암호화된 파일과 암호화되지 않은 파일을 구분해야 합니다. 그러나 단일 스레드 복사를 사용하면 성능이 대폭 저하됩니다.           |
|/fft     | 파일 시스템의 타임스탬프 세분성을 줄이기 위해 사용합니다.        |
|/b     | 백업 모드로 파일을 복사합니다.        |
|/z    | 다시 시작 모드로 파일을 복사합니다. 환경이 불안정한 경우에 사용하세요. 이 옵션은 추가 로깅으로 인해 처리량이 감소합니다.      |
| /zb     | 다시 시작 모드를 사용합니다. 액세스가 거부되는 경우 이 옵션은 백업 모드를 사용합니다. 이 옵션은 검사점 설정 때문에 처리량이 감소합니다.         |
|/efsraw     | EFS 원시 모드에서 암호화된 파일을 모두 복사합니다. 암호화된 파일에만 사용하세요.         |
|log+:<LogFile>| 기존 로그 파일에 출력을 추가합니다.|    
 
다음 샘플에서는 Data Box에 파일을 복사하는 robocopy 명령의 출력을 보여줍니다.
    
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
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
    C:\Users>
       

성능을 최적화하기 위해, 데이터를 복사할 때 다음 robocopy 매개 변수를 사용하세요.

|    플랫폼    |    대부분 512KB 미만의 작은 파일                           |    대부분 512KB-1MB의 중간 파일                      |    대부분 1MB를 초과하는 큰 파일                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    Robocopy 세션 2개 <br> 세션당 16스레드    |    Robocopy 세션 3개 <br> 세션당 16스레드    |    Robocopy 세션 2개 <br> 세션당 24스레드    |  |


Robocopy 명령에 대한 자세한 내용은 [Robocopy 및 몇 가지 예제](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)를 참조하세요.

대상 폴더를 열어 복사된 파일을 보고 확인합니다. 복사 프로세스 중 오류가 있는 경우 문제 해결을 위해 오류 파일을 다운로드하세요.

데이터 무결성을 보장하기 위해, 데이터가 복사될 때 체크섬이 인라인으로 계산됩니다. 복사가 완료되면 장치에서 사용 중인 공간과 여유 공간을 확인합니다.
    
   ![대시보드에서 여유 공간 및 사용 중인 공간 확인](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

### <a name="copy-data-via-nfs"></a>NFS를 통해 데이터 복사

Linux 호스트 컴퓨터를 사용하는 경우 Robocopy와 비슷한 복사 유틸리티를 사용합니다. Linux에서 사용할 수 있는 대안 중 일부는 [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) 또는 [Ultracopier](https://ultracopier.first-world.info/)입니다.  

`cp` 명령은 디렉터리를 복사하는 최고의 옵션 중 하나입니다. 사용 방법에 대한 자세한 내용은 [cp 기본 페이지](http://man7.org/linux/man-pages/man1/cp.1.html)를 참조하세요.

다중 스레드 복사에 rsync 옵션을 사용하는 경우 다음 지침을 따르세요.

 - Linux 클라이언트에서 사용하는 파일 시스템에 따라 **CIFS 유틸리티** 또는 **NFS 유틸리티** 패키지를 설치합니다.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  **Rsync** 및 **Parallel**을 설치합니다(Linux 배포판 버전에 따라 다름).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - 탑재 지점을 만듭니다.

    `sudo mkdir /mnt/databox`

 - 볼륨을 탑재합니다.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - 폴더 디렉터리 구조를 미러링합니다.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - 파일을 복사합니다. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     여기서 j는 병렬 처리의 수를 지정하고, X는 병렬 복사본의 수

     병렬 복사본 16개로 시작하여 가용 리소스에 따라 스레드 수를 늘리는 것이 좋습니다.

## <a name="prepare-to-ship"></a>배송 준비

마지막 단계는 배송할 장치를 준비하는 것입니다. 이 단계에서는 모든 장치 공유가 오프라인 상태가 됩니다. 장치 배송 준비를 시작한 후에는 공유에 액세스할 수 없습니다.
1. **배송 준비**로 이동하여 **준비 시작**을 클릭합니다. 
   
    ![배송 준비 1](media/data-box-deploy-copy-data/prepare-to-ship1.png)

2. 기본적으로 체크섬은 배송을 준비하는 동안 계산된 인라인입니다. 체크섬 계산은 데이터의 크기에 따라 다소 시간이 걸릴 수 있습니다. **준비 시작**을 클릭합니다.
    1. 배송을 준비하면 장치 공유가 오프라인으로 전환되고 장치가 잠깁니다.
        
        ![배송 준비 1](media/data-box-deploy-copy-data/prepare-to-ship2.png) 
   
    2. 장치 준비가 완료되면 장치 상태가 *배송 준비 완료*로 업데이트됩니다. 
        
        ![배송 준비 1](media/data-box-deploy-copy-data/prepare-to-ship3.png)

    3. 이 프로세스에서 복사된 파일(매니페스트) 목록을 다운로드합니다. 나중에 이 목록을 사용하여 Azure에 업로드된 파일을 확인할 수 있습니다.
        
        ![배송 준비 1](media/data-box-deploy-copy-data/prepare-to-ship4.png)

3. 장치를 종료합니다. **종료하거나 다시 시작** 페이지로 이동하여 **종료**를 클릭합니다. 확인을 요청하는 메시지가 표시되면 **확인**을 클릭하여 계속 진행합니다.
4. 케이블을 분리합니다. 다음 단계는 장치를 Microsoft로 보내는 것입니다.

 
<!--## Appendix - robocopy parameters

This section describes the robocopy parameters used when copying the data to optimize the performance.

|    Platform    |    Mostly small files < 512 KB                           |    Mostly medium  files 512 KB-1 MB                      |    Mostly large files > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy sessions <br> 16 threads per sessions    |    3 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 24 threads per sessions    |  |
|    Data Box Heavy     |    6 Robocopy sessions <br> 24 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |   
|    Data Box Disk         |    4 Robocopy sessions <br> 16 threads per sessions             |    2 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 16 threads per sessions    |   
-->

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Data Box에 연결
> * Data Box에 데이터 복사
> * Data Box 배송 준비

다음 자습서로 넘어가서 Data Box를 설정하고 데이터를 복사하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Microsoft로 Azure Data Box 배송](./data-box-deploy-picked-up.md)

