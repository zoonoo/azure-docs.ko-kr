---
title: NFS를 통해 Microsoft Azure Data Box에 데이터 복사 | Microsoft Docs
description: Azure Data Box에 데이터를 복사하는 방법 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/20/2018
ms.author: alkohli
ms.openlocfilehash: 7ba6bc2cf3cf5286719bc6da519aabb364302af3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550362"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>자습서: NFS를 통해 Azure Data Box에 데이터 복사 

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

Linux 호스트 컴퓨터를 사용하는 경우 다음 단계에 따라 NFS 클라이언트에 대한 액세스를 허용하도록 Data Box를 구성합니다.

1. 공유에 액세스할 수 있도록 허용된 클라이언트의 IP 주소를 입력합니다. 로컬 웹 UI에서 **연결 및 복사** 페이지로 이동합니다. **NFS 설정** 아래에서 **NFS 클라이언트 액세스**를 클릭합니다. 

    ![NFS 클라이언트 액세스 구성 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. NFS 클라이언트의 IP 주소를 입력하고 **추가**를 클릭합니다. 이 단계를 반복하여 여러 NFS 클라이언트에 대한 액세스를 구성할 수 있습니다. **확인**을 클릭합니다.

    ![NFS 클라이언트 액세스 구성 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Linux 호스트 컴퓨터에 [지원되는 버전](data-box-system-requirements.md)의 NFS 클라이언트를 설치합니다. 해당 Linux 배포판에 맞는 버전을 사용하세요. 

3. NFS 클라이언트가 설치되면 다음 명령을 사용하여 Data Box 디바이스에 NFS 공유를 탑재합니다.

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    다음 예제에서는 NFS를 통해 Data Box 공유에 연결하는 방법을 보여줍니다. Data Box 디바이스 IP는 `10.161.23.130`이고, `Mystoracct_Blob` 공유는 ubuntuVM에 탑재되며, 탑재 지점은 `/home/databoxubuntuhost/databox`입니다.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

## <a name="copy-data-to-data-box"></a>Data Box에 데이터 복사

Data Box 공유에 연결된 후에는 데이터를 복사합니다. 데이터를 복사하기 전에, 다음 사항을 검토합니다.

- 적절한 데이터 형식에 해당하는 공유에 데이터를 복사해야 합니다. 예를 들어 블록 Blob에 대한 공유에 블록 Blob 데이터를 복사합니다. 데이터 형식이 적절한 공유 형식과 일치하지 않는 경우 이후 단계에서 Azure에 데이터를 업로드하는 작업이 실패합니다.
-  데이터를 복사하는 동안 데이터 크기가 [Azure 저장소 및 Data Box 제한](data-box-limits.md)에 설명된 크기 제한을 준수해야 합니다. 
- Data Box에 의해 업로드되는 데이터가 Data Box 외부의 다른 애플리케이션에 의해 동시에 업로드되는 경우 업로드 작업이 실패하고 데이터 손상이 발생할 수 있습니다.
- SMB 및 NFS를 동시에 사용하거나 Azure의 동일한 최종 대상에 동일한 데이터를 복사하지 않는 것이 좋습니다. 이 경우 최종 결과를 확인할 수 없습니다.

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

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Data Box에 연결
> * Data Box에 데이터 복사
> * Data Box 배송 준비

Data Box를 Microsoft로 다시 배송하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Microsoft로 Azure Data Box 배송](./data-box-deploy-picked-up.md)

