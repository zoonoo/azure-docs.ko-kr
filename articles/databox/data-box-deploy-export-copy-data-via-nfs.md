---
title: NFS를 통해 Azure Data Box에서 데이터를 복사하는 자습서 | Microsoft Docs
description: NFS를 통해 Azure Data Box에서 데이터를 복사하는 방법 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 301c75df6bedf430af64bbeff63f2eb759691355
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86208909"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs-preview"></a>자습서: NFS를 통해 Azure Data Box에서 데이터 복사(미리 보기)

이 자습서에서는 Data Box의 로컬 웹 UI에서 NFS를 통해 온-프레미스 데이터 서버로 연결하고 데이터를 복사하는 방법을 설명합니다. Data Box의 데이터를 Azure Storage 계정에서 내보냅니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 필수 구성 요소
> * Data Box에 연결
> * Data Box에서 데이터 복사

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. Azure Data Box에 대한 주문을 배치했습니다.
    - 가져오기 순서는 [자습서: Azure Data Box 주문](data-box-deploy-ordered.md)을 완료했습니다.
    - 내보내기 순서는 [자습서: Azure Data Box 주문](data-box-deploy-export-ordered.md)을 완료했습니다.
2. Data Box를 받았고 포털의 주문 상태가 **배달됨**입니다.
3. Data Box에서 데이터를 복사할 호스트 컴퓨터가 있습니다. 호스트 컴퓨터는 다음 사항이 필수입니다.
   * [지원되는 운영 체제](data-box-system-requirements.md)를 실행합니다.
   * 고속 네트워크에 연결되어 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용해도 되지만, 이 경우 복사 속도가 떨어집니다.

## <a name="connect-to-data-box"></a>Data Box에 연결

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Linux 호스트 컴퓨터를 사용하는 경우 다음 단계에 따라 NFS 클라이언트에 대한 액세스를 허용하도록 Data Box를 구성합니다.

1. 공유에 액세스할 수 있도록 허용된 클라이언트의 IP 주소를 입력합니다. 로컬 웹 UI에서 **연결 및 복사** 페이지로 이동합니다. **NFS 설정** 아래에서 **NFS 클라이언트 액세스**를 클릭합니다. 

    ![NFS 클라이언트 액세스 구성 1](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

2. NFS 클라이언트의 IP 주소를 입력하고 **추가**를 클릭합니다. 이 단계를 반복하여 여러 NFS 클라이언트에 대한 액세스를 구성할 수 있습니다. **확인**을 클릭합니다.

    ![NFS 클라이언트 액세스 구성 2](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. Linux 호스트 컴퓨터에 [지원되는 버전](data-box-system-requirements.md)의 NFS 클라이언트를 설치합니다. 해당 Linux 배포판에 맞는 버전을 사용하세요. 

3. NFS 클라이언트가 설치되면 다음 명령을 사용하여 Data Box 디바이스에 NFS 공유를 탑재합니다.

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    다음 예제에서는 NFS를 통해 Data Box 공유에 연결하는 방법을 보여줍니다. Data Box 디바이스 IP는 `10.161.23.130`이고, `Mystoracct_Blob` 공유는 ubuntuVM에 탑재되며, 탑재 지점은 `/home/databoxubuntuhost/databox`입니다.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Mac 클라이언트에 대해 다음과 같은 추가 옵션을 추가해야 합니다. 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **복사하려는 파일에 대한 폴더는 항상 공유 아래에 만든 다음 이 폴더에 파일을 복사합니다**. 블록 Blob 및 페이지 Blob 공유 아래에 만들어진 폴더는 데이터가 Blob으로 업로드되는 컨테이너를 나타냅니다. 스토리지 계정의 *root* 폴더에 파일을 직접 복사할 수 없습니다.

## <a name="copy-data-from-data-box"></a>Data Box에서 데이터 복사

Data Box 공유에 연결된 후에는 데이터를 복사합니다.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 이제 데이터 복사를 시작할 수 있습니다. Linux 호스트 컴퓨터를 사용하는 경우 Robocopy와 비슷한 복사 유틸리티를 사용합니다. Linux에서 사용할 수 있는 대안 중 일부는 [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) 또는 [Ultracopier](https://ultracopier.first-world.info/)입니다.  

`cp` 명령은 디렉터리를 복사하는 최고의 옵션 중 하나입니다. 사용 방법에 대한 자세한 내용은 [cp 기본 페이지](http://man7.org/linux/man-pages/man1/cp.1.html)를 참조하세요.

다중 스레드 복사에 rsync 옵션을 사용하는 경우 다음 지침을 따르세요.

* Linux 클라이언트에서 사용하는 파일 시스템에 따라 **CIFS 유틸리티** 또는 **NFS 유틸리티** 패키지를 설치합니다.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* **Rsync** 및 **Parallel**을 설치합니다(Linux 배포판 버전에 따라 다름).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* 탑재 지점을 만듭니다.

    `sudo mkdir /mnt/databox`

* 볼륨을 탑재합니다.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* 폴더 디렉터리 구조를 미러링합니다.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* 파일을 복사합니다.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     여기서 j는 병렬 처리의 수를 지정하고, X는 병렬 복사본의 수

     병렬 복사본 16개로 시작하여 가용 리소스에 따라 스레드 수를 늘리는 것이 좋습니다.

> [!IMPORTANT]
> Linux 파일 형식인 기호 링크, 문자 파일, 블록 파일, 소켓 및 파이프는 지원되지 않습니다. 이러한 파일 형식을 사용하면 **배송 준비** 단계 동안 오류가 발생합니다.

복사가 완료되면 **대시보드**로 이동하여 디바이스에서 사용 중인 공간과 여유 공간을 확인합니다.

이제 Data Box를 Microsoft로 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
>
> * 필수 구성 요소
> * Data Box에 연결
> * Data Box에서 데이터 복사

Data Box를 Microsoft로 다시 배송하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Microsoft로 Azure Data Box 배송](./data-box-deploy-export-picked-up.md)
