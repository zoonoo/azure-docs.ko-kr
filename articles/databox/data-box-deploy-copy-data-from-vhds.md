---
title: Azure Data Box를 통해 VHD의 데이터를 관리 디스크에 복사하는 자습서 | Microsoft Docs
description: 온-프레미스 VM 워크로드의 VHD에서 Azure Data Box로 데이터를 복사하는 방법 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alkohli
ms.openlocfilehash: 3284821e0ec65a76b29d5195315136639304e411
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925469"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>자습서: Data Box를 사용하여 Azure의 관리 디스크로 데이터 가져오기

이 자습서에서는 Azure Data Box를 사용하여 Azure의 관리 디스크로 온-프레미스 VHD를 마이그레이션하는 방법을 설명합니다. 온-프레미스 VM의 VHD는 Data Box에 페이지 Blob으로 복사되며 관리 디스크로 Azure에 업로드됩니다. 그런 다음, 이러한 관리 디스크를 Azure VM에 연결할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 필수 구성 요소 검토
> * Data Box에 연결
> * Data Box에 데이터 복사


## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 사항을 확인합니다.

1. [자습서: Azure Data Box 설정](data-box-deploy-set-up.md)을 완료했습니다.
2. Data Box를 받았고 포털의 주문 상태가 **배달됨**입니다.
3. 고속 네트워크에 연결되어 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용해도 되지만, 이 경우 복사 속도가 떨어집니다.
4. 다음을 검토했습니다.

    - [Azure 개체 크기 제한에서 지원되는 관리 디스크 크기](data-box-limits.md#azure-object-size-limits).
    - [Azure 관리 디스크 소개](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box"></a>Data Box에 연결

지정된 리소스 그룹에 따라 Data Box는 각 연결된 리소스 그룹에 대해 하나의 공유를 만듭니다. 예를 들어 주문 시 `mydbmdrg1` 및 `mydbmdrg2`가 생성되면 다음 공유가 만들어집니다.

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

각 공유 내에서 스토리지 계정의 컨테이너에 해당하는 다음 세 개의 폴더가 생성됩니다.

- 프리미엄 SSD
- 표준 HDD
- 표준 SSD

다음 표에는 Data Box에서 공유에 대한 UNC 경로가 나와 있습니다.
 
|        연결 프로토콜           |             공유에 대한 UNC 경로                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

연결 단계는 Data Box 공유에 연결하는 데 SMB를 사용하는지 아니면 NFS를 사용하는지 여부에 따라 다릅니다.

> [!NOTE]
> 이 기능에는 REST를 통한 연결이 지원되지 않습니다.

### <a name="connect-to-data-box-via-smb"></a>SMB를 통한 Data Box에 연결

Windows Server 호스트 컴퓨터를 사용하는 경우 다음 단계에 따라 Data Box에 연결합니다.

1. 첫 번째 단계는 세션을 인증하고 시작하는 것입니다. **연결 및 복사**로 이동합니다. **자격 증명 가져오기**를 클릭하여 리소스 그룹과 연결된 공유의 액세스 자격 증명을 가져옵니다. Azure Portal의 **디바이스 세부 정보**에서 액세스 자격 증명을 가져올 수도 있습니다.

    > [!NOTE]
    > 관리 디스크에 대한 모든 공유의 자격 증명은 동일합니다.

    ![공유 자격 증명 가져오기 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. 액세스 공유 및 데이터 복사 대화 상자에서 공유에 대한 **사용자 이름** 및 **암호**를 복사합니다. **확인**을 클릭합니다.
    
    ![공유 자격 증명 가져오기 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. 호스트 컴퓨터에서 리소스와 연결된 공유(다음 예제의 *mydbmdrg1*)에 액세스하려면 명령 창을 엽니다. 명령 프롬프트에 다음을 입력합니다.

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    이 예제의 UNC 공유 경로는 다음과 같습니다.

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. 메시지가 표시되면 공유 암호를 입력합니다. 다음 샘플은 이전 명령을 통해 공유에 연결하는 방법을 보여줍니다.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Windows + R을 누르고 **실행** 창에서 `\\<device IP address>\<ShareName>`를 지정합니다. **확인**을 클릭하여 파일 탐색기를 엽니다.
    
    ![파일 탐색기를 통해 공유에 연결 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    이제 각 공유 내에 사전 생성된 다음과 같은 폴더가 표시됩니다.
    
    ![파일 탐색기를 통해 공유에 연결 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>NFS를 통한 Data Box에 연결

Linux 호스트 컴퓨터를 사용하는 경우 다음 단계에 따라 NFS 클라이언트에 대한 액세스를 허용하도록 Data Box를 구성합니다.

1. 공유에 액세스할 수 있도록 허용된 클라이언트의 IP 주소를 입력합니다. 로컬 웹 UI에서 **연결 및 복사** 페이지로 이동합니다. **NFS 설정** 아래에서 **NFS 클라이언트 액세스**를 클릭합니다.

    ![NFS 클라이언트 액세스 구성 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. NFS 클라이언트의 IP 주소를 입력하고 **추가**를 클릭합니다. 이 단계를 반복하여 여러 NFS 클라이언트에 대한 액세스를 구성할 수 있습니다. **확인**을 클릭합니다.

    ![NFS 클라이언트 액세스 구성 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Linux 호스트 컴퓨터에 [지원되는 버전](data-box-system-requirements.md)의 NFS 클라이언트를 설치합니다. 해당 Linux 배포판에 맞는 버전을 사용하세요.

3. NFS 클라이언트가 설치되면 다음 명령을 사용하여 Data Box 디바이스에 NFS 공유를 탑재합니다.

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    다음 예제에서는 NFS를 통해 Data Box 공유에 연결하는 방법을 보여줍니다. Data Box 디바이스 IP는 `169.254.250.200`이고, `mydbmdrg1_MDisk` 공유는 ubuntuVM에 탑재되며, 탑재 지점은 `/home/databoxubuntuhost/databox`입니다.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Data Box에 데이터 복사

데이터 서버에 연결된 후 다음 단계는 데이터를 복사하는 것입니다. VHD 파일은 준비 스토리지 계정에 페이지 Blob으로 복사됩니다. 그런 다음, 페이지 Blob은 관리 디스크로 변환되어 리소스 그룹으로 이동합니다.

데이터 복사를 시작하기 전에 다음 고려 사항을 검토합니다.

- 항상 사전 생성된 폴더 중 하나에 VHD를 복사합니다. 이러한 폴더 외부 또는 사용자가 만든 폴더 안에 VHD를 복사하는 경우 VHD는 Azure Storage 계정에 관리 디스크가 아닌 페이지 Blob으로 업로드됩니다.
- 고정된 VHD만 업로드하여 관리 디스크를 만들 수 있습니다. VHDX 파일 또는 동적 및 차이점 보관용 VHD는 지원되지 않습니다.
- 사전 생성된 폴더 전체에서 리소스 그룹에 지정된 이름의 관리 디스크 하나만 가질 수 있습니다. 즉, 사전 생성된 폴더에 업로드된 VHD는 이름이 고유해야 함을 의미합니다. 지정된 이름이 리소스 그룹의 기존 관리 디스크와 일치하지 않도록 해야 합니다.
- [Azure 개체 크기 제한](data-box-limits.md#azure-object-size-limits)의 관리 디스크 제한을 검토합니다.

SMB 또는 NFS를 통해 연결하는지 여부에 따라 다음을 사용할 수 있습니다.

- [SMB를 통한 데이터 복사](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [NFS를 통한 데이터 복사](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

복사 작업이 완료될 때까지 기다립니다. 다음 단계를 진행하기 전에 오류 없이 복사 작업이 완료되었는지 확인합니다.

![**연결 및 복사** 페이지에서 오류 없음](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

복사하는 동안 오류가 있는 경우 **연결 및 복사** 페이지에서 로그를 다운로드합니다.

- 512 바이트로 정렬되지 않은 파일을 복사한 경우 파일이 준비 스토리지 계정에 페이지 Blob으로 업로드되지 않습니다. 로그에 오류가 표시됩니다. 파일을 제거하고 512 바이트로 정렬된 파일을 복사합니다.

- 이름이 긴 VHDX를 복사한 경우(이러한 파일은 지원되지 않음) 로그에 오류가 표시됩니다.

    ![**연결 및 복사** 페이지의 로그에 표시된 오류](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    다음 단계를 계속 진행하기 전에 오류를 해결합니다.

데이터 무결성을 보장하기 위해, 데이터가 복사될 때 체크섬이 인라인으로 계산됩니다. 복사가 완료되면 디바이스에서 사용 중인 공간과 여유 공간을 확인합니다.
    
![대시보드에서 여유 공간 및 사용 중인 공간 확인](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

복사 작업이 완료되면 **배송 준비**로 이동할 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * 필수 구성 요소 검토
> * Data Box에 연결
> * Data Box에 데이터 복사


Data Box를 Microsoft로 다시 배송하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Microsoft로 Azure Data Box 배송](./data-box-deploy-picked-up.md)

