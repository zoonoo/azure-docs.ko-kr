---
title: Azure VMware 솔루션-Azure Data Box를 사용 하 여 마이그레이션
description: Azure Data Box를 사용 하 여 데이터를 Azure VMware 솔루션으로 대량 마이그레이션하는 방법입니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77019760"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Azure Data Box를 사용 하 여 Azure VMware 솔루션으로 데이터 마이그레이션

Microsoft Azure Data Box 클라우드 솔루션을 사용 하면 빠르고 저렴 하 고 안정적인 방식으로 Azure로 tb (tb)의 데이터를 보낼 수 있습니다. 사용자에게 전용 Data Box 스토리지 디바이스를 배송하여 안전한 데이터 전송을 더욱 빠르게 실현시켜 드립니다. 각 저장 장치는 사용 가능한 최대 저장소 용량 (80 TB)을 가지 며 지역 캐리어에 의해 데이터 센터에 전송 됩니다. 디바이스에는 운송 중에 데이터를 안전하게 보호하기 위한 견고한 케이스가 있습니다.

Data Box를 사용 하 여 VMware 데이터를 사설 클라우드로 대량 마이그레이션할 수 있습니다. 온-프레미스 VMware vSphere 환경의 데이터는 NFS (네트워크 파일 시스템) 프로토콜을 통해 Data Box 복사 됩니다. 대량 데이터 마이그레이션은 Data Box에 가상 컴퓨터, 구성 및 연결 된 데이터의 지정 시간 복사본을 저장 한 다음 Azure에 수동으로 전달 하는 작업을 포함 합니다.

이 문서에서는 다음에 대해 알아봅니다.

* Data Box를 설정 합니다.
* NFS를 통해 온-프레미스 VMware 환경에서 Data Box로 데이터를 복사 합니다.
* Data Box의 반환을 준비 하는 중입니다.
* Azure VMware 솔루션에 복사할 blob 데이터를 준비 하는 중입니다.
* Azure에서 사설 클라우드로 데이터를 복사 하는 중입니다.

## <a name="scenarios"></a>시나리오

대량 데이터 마이그레이션을 위해 다음 시나리오에서 Data Box를 사용 합니다.

* 온-프레미스에서 Azure VMware 솔루션으로 많은 양의 데이터를 마이그레이션하는 경우 이 메서드는 기준선을 설정 하 고 네트워크를 통한 차이를 동기화 합니다.
* 꺼져 있는 많은 수의 가상 컴퓨터를 마이그레이션하려면 (콜드 가상 컴퓨터).
* 개발 및 테스트 환경을 설정 하기 위한 가상 머신 데이터를 마이그레이션합니다.
* 많은 수의 가상 머신 템플릿, ISO 파일 및 가상 머신 디스크를 마이그레이션합니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure Portal를 통해 Data Box 필수 구성 요소 및 [순서](../databox/data-box-deploy-ordered.md) 를 확인 합니다. 주문 프로세스 중에 Blob storage를 사용 하도록 설정 하는 저장소 계정을 선택 해야 합니다. Data Box 장치를 받은 후 온-프레미스 네트워크에 연결 하 고 vSphere 관리 네트워크에서 연결할 수 있는 IP 주소를 사용 하 여 [장치를 설정](../databox/data-box-deploy-set-up.md) 합니다.

* Azure VMware 솔루션이 프로 비전 되는 동일한 지역에서 가상 네트워크 및 저장소 계정을 만듭니다.

* [Express 경로를 사용 하 여 azure 가상 네트워크를 CloudSimple에 연결](virtual-network-connection.md)의 단계를 수행 하 여 사설 클라우드에서 저장소 계정이 만들어진 가상 네트워크에 [azure 가상 네트워크 연결](cloudsimple-azure-network-connection.md) 을 만듭니다.

## <a name="set-up-data-box-for-nfs"></a>NFS에 대 한 Data Box 설정

자습서: 케이블의 "장치에 연결" 섹션에 나오는 단계를 수행 하 여 Data Box 로컬 웹 UI에 연결 [하 고 Azure Data Box에 연결](../databox/data-box-deploy-set-up.md)합니다.  NFS 클라이언트에 대 한 액세스를 허용 하도록 Data Box를 구성 합니다.

1. 로컬 웹 UI에서 **연결 및 복사** 페이지로 이동 합니다. **Nfs 설정**아래에서 **nfs 클라이언트 액세스**를 선택 합니다. 

    ![NFS 클라이언트 액세스 구성 1](media/nfs-client-access.png)

2. VMware ESXi 호스트의 IP 주소를 입력 하 고 **추가**를 선택 합니다. 이 단계를 반복 하 여 vSphere 클러스터의 모든 호스트에 대 한 액세스를 구성할 수 있습니다. **확인**을 선택합니다.

    ![NFS 클라이언트 액세스 구성 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **복사하려는 파일에 대한 폴더는 항상 공유 아래에 만든 다음 이 폴더에 파일을 복사합니다**. 블록 Blob 및 페이지 Blob 공유 아래에 만들어진 폴더는 데이터가 Blob으로 업로드되는 컨테이너를 나타냅니다. 저장소 계정의 *루트* 폴더로 직접 파일을 복사할 수 없습니다.

블록 Blob 및 페이지 Blob 공유에서는 첫 번째 수준 엔터티가 컨테이너, 두 번째 수준 엔터티가 Blob입니다. Azure Files에 대 한 공유에서 첫 번째 수준 엔터티는 공유이 고, 두 번째 수준 엔터티는 파일입니다.

다음 표에서는 데이터가 업로드되는 Data Box 및 Azure Storage 경로 URL의 공유에 대한 UNC 경로를 보여줍니다. 최종 Azure Storage 경로 URL은 UNC 공유 경로에서 파생될 수 있습니다.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 블록 Blob | <li>공유 UNC 경로: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 페이지 Blob  | <li>공유 UNC 경로: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 파일       |<li>공유 UNC 경로: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> VMware 데이터를 복사 하는 데 Azure 블록 blob을 사용 합니다.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>온-프레미스 vCenter 클러스터에서 NFS 공유를 데이터 저장소로 탑재 하 고 데이터를 복사 합니다.

Data Box에서 NFS 공유는 NFS 데이터 저장소에 데이터를 복사 하기 위해 온-프레미스 vCenter 클러스터 또는 VMware ESXi 호스트에 데이터 저장소로 탑재 되어야 합니다.

1. 온-프레미스 vCenter server에 로그인 합니다.

2. **데이터 센터**를 마우스 오른쪽 단추로 클릭 하 고 **저장소**를 선택 하 고 **새 데이터 저장소**를 선택한 후 **다음**을 선택 합니다.

   ![새 데이터 저장소 추가](media/databox-migration-add-datastore.png)

3. 데이터 저장소 추가 마법사의 1 단계에서 **유형**아래에 있는 **NFS** 를 선택 합니다.

   ![새 데이터 저장소-형식 추가](media/databox-migration-add-datastore-type.png)

4. 마법사의 2 단계에서 nfs **3** 을 nfs 버전으로 선택한 후 **다음**을 선택 합니다.

   ![새 데이터 저장소-NFS 버전 추가](media/databox-migration-add-datastore-nfs-version.png)

5. 마법사의 3 단계에서 데이터 저장소의 이름, 경로 및 서버를 지정 합니다. 서버에 대 한 Data Box IP 주소를 사용할 수 있습니다. 폴더 경로는 `/<StorageAccountName_BlockBlob>/<ContainerName>/` 형식이 됩니다.

   ![새 데이터 저장소-NFS 구성 추가](media/databox-migration-add-datastore-nfs-configuration.png)

6. 마법사의 4 단계에서 데이터 저장소를 탑재할 ESXi 호스트를 선택 하 고 **다음**을 선택 합니다.  클러스터에서 모든 호스트를 선택 하 여 가상 컴퓨터의 마이그레이션을 확인 합니다.

   ![새 데이터 저장소 추가-호스트 선택](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 마법사의 5 단계에서 요약을 검토 하 고 **마침**을 선택 합니다.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Data Box NFS 데이터 저장소로 데이터 복사

가상 컴퓨터를 마이그레이션하거나 새 데이터 저장소로 복제할 수 있습니다.  마이그레이션하려는 사용 되지 않는 가상 컴퓨터는 **저장소 vMotion** 옵션을 사용 하 여 Data Box NFS 데이터 저장소로 마이그레이션할 수 있습니다. 활성 가상 컴퓨터를 Data Box NFS 데이터 저장소에 복제할 수 있습니다.

* **이동할**수 있는 가상 컴퓨터를 식별 하 고 나열 합니다.
* **복제**해야 하는 가상 컴퓨터를 식별 하 고 나열 합니다.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Data Box 데이터 저장소로 가상 컴퓨터 이동

1. Data Box 데이터 저장소로 이동 하려는 가상 컴퓨터를 마우스 오른쪽 단추로 클릭 한 다음 **마이그레이션**을 선택 합니다.

    ![가상 컴퓨터 마이그레이션](media/databox-migration-vm-migrate.png)

2. 마이그레이션 유형 **으로만 저장소 변경** 을 선택한 후 **다음**을 선택 합니다.

    ![가상 컴퓨터 마이그레이션-저장소 전용](media/databox-migration-vm-migrate-change-storage.png)

3. **Databox-데이터 저장소** 를 대상으로 선택 하 고 **다음**을 선택 합니다.

    ![가상 컴퓨터 마이그레이션-데이터 저장소 선택](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. 정보를 검토 하 고 **마침**을 선택 합니다.

5. 추가 가상 컴퓨터에 대해 1 ~ 4 단계를 반복 합니다.

> [!TIP]
> 동일한 전원 상태 (설정 또는 해제 됨)에 있는 여러 가상 컴퓨터를 선택 하 고 대량으로 마이그레이션할 수 있습니다.

가상 머신은 Data Box에서 NFS 데이터 저장소로 마이그레이션됩니다. 모든 가상 컴퓨터를 마이그레이션한 후에는 Azure VMware 솔루션으로의 데이터 마이그레이션을 준비 하기 위해 활성 가상 컴퓨터를 끌 수 있습니다 (종료).

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Data Box 데이터 저장소에 가상 컴퓨터 또는 가상 컴퓨터 템플릿을 복제 합니다.

1. 복제 하려는 가상 컴퓨터 또는 가상 컴퓨터 템플릿을 마우스 오른쪽 단추로 클릭 합니다. 복제 **Clone**복제  >  **를 가상 컴퓨터에 복제를**선택 합니다.

    ![가상 컴퓨터 복제](media/databox-migration-vm-clone.png)

2. 복제 된 가상 컴퓨터 또는 가상 컴퓨터 템플릿의 이름을 선택 합니다.

3. 복제 된 개체를 넣을 폴더를 선택 하 고 **다음**을 선택 합니다.

4. 복제 된 개체를 넣을 클러스터 또는 리소스 풀을 선택 하 고 **다음**을 선택 합니다.

5. 저장소 위치로 **Databox-데이터** 저장소를 선택 하 고 **다음**을 선택 합니다.

    ![가상 컴퓨터 복제-데이터 저장소 선택](media/databox-migration-vm-clone-select-datastore.png)

6. 복제 된 개체에 대 한 옵션을 사용자 지정 하려면 사용자 지정 옵션을 선택한 후 **다음**을 선택 합니다.

7. 구성을 검토 하 고 **마침**을 선택 합니다.

8. 추가 가상 컴퓨터 또는 가상 컴퓨터 템플릿에 대해 1 ~ 7 단계를 반복 합니다.

가상 컴퓨터는 Data Box에서 NFS 데이터 저장소에 복제 되어 저장 됩니다. 가상 컴퓨터를 복제 한 후에는 Azure VMware 솔루션으로 데이터를 마이그레이션하기 위한 준비를 수행 하 여 가상 컴퓨터를 종료 해야 합니다.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Data Box 데이터 저장소에 ISO 파일 복사

1. 온-프레미스 vCenter 웹 UI에서 **저장소**로 이동 합니다.  **Databox-데이터 저장소** 를 선택한 다음 **파일**을 선택 합니다. ISO 파일을 저장할 새 폴더를 만듭니다.

    ![ISO 복사-새 폴더 만들기](media/databox-migration-create-folder.png)

2. ISO 파일이 저장 될 폴더의 이름을 지정 합니다.

3. 새로 만든 폴더를 두 번 클릭 하 여 엽니다.

4. **파일 업로드** 를 선택 하 고 업로드 하려는 ISO 파일을 선택 합니다.
    
    ![ISO 업로드 파일 복사](media/databox-migration-upload-iso.png)

> [!TIP]
> 온-프레미스 데이터 저장소에 ISO 파일이 이미 있는 경우 파일을 선택 하 고 **로 복사** 하 여 파일을 Data Box NFS 데이터 저장소에 복사할 수 있습니다.


## <a name="prepare-data-box-for-return"></a>반환 Data Box 준비

모든 가상 컴퓨터 데이터, 가상 컴퓨터 템플릿 데이터 및 모든 ISO 파일이 Data Box NFS 데이터 저장소에 복사 된 후 vCenter에서 데이터 저장소의 연결을 끊을 수 있습니다. 데이터 저장소의 연결을 끊으려면 먼저 모든 가상 머신 및 가상 머신 템플릿을 인벤토리에 제거 해야 합니다.

### <a name="remove-objects-from-inventory"></a>인벤토리에서 개체 제거

1. 온-프레미스 vCenter 웹 UI에서 **저장소**로 이동 합니다. **Databox** 을 선택 하 고 **vm**을 선택 합니다.

    ![인벤토리에서 가상 컴퓨터 제거-꺼짐](media/databox-migration-select-databox-vm.png)

2. 모든 가상 컴퓨터가 종료 되었는지 확인 합니다.

3. 모든 가상 컴퓨터를 선택 하 고 마우스 오른쪽 단추를 클릭 한 다음 **인벤토리에서 제거**를 선택 합니다.

    ![인벤토리에서 가상 컴퓨터 제거](media/databox-migration-remove-vm-from-inventory.png)

4. **폴더에서 VM 템플릿** 을 선택 하 고 3 단계를 반복 합니다.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>VCenter에서 Data Box NFS 데이터 저장소를 제거 합니다.

반환을 준비 하기 전에 VMware ESXi 호스트에서 Data Box NFS 데이터 저장소의 연결을 끊어야 합니다.

1. 온-프레미스 vCenter 웹 UI에서 **저장소**로 이동 합니다.

2. **Databox** 를 마우스 오른쪽 단추로 클릭 하 고 **데이터 저장소 분리**를 선택 합니다.

    ![데이터 저장소 Data Box 분리](media/databox-migration-unmount-datastore.png)

3. 데이터 저장소가 탑재 된 모든 ESXi 호스트를 선택 하 고 **확인**을 선택 합니다.

    ![데이터 저장소 Data Box 분리-호스트 선택](media/databox-migration-unmount-datastore-select-hosts.png)

4. 경고를 검토 하 고 수락 하 고 **확인**을 선택 합니다.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>반환 Data Box 준비 하 고 반환 합니다.

Azure Data Box 반환 문서에 설명 된 단계를 수행 하 [고 Azure에 데이터 업로드를 확인](../databox/data-box-deploy-picked-up.md) 하 여 Data Box을 반환 합니다. Azure storage 계정에 대 한 데이터 복사 상태를 확인 합니다. 상태가 완료로 표시 되 면 Azure storage 계정에서 데이터를 확인할 수 있습니다.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Azure storage에서 Azure VMware 솔루션으로 데이터 복사

Data Box 장치에 복사 된 데이터는 Data Box의 주문 상태가 완료로 표시 된 후 Azure storage 계정에서 사용할 수 있습니다. 이제 데이터를 Azure VMware 솔루션에 복사할 수 있습니다. 저장소 계정의 데이터는 NFS 프로토콜을 사용 하 여 사설 클라우드의 vSAN 데이터 저장소에 복사 해야 합니다. 

먼저 **AzCopy**을 사용 하 여 Azure에서 Linux 가상 머신의 관리 디스크로 Blob 저장소 데이터를 복사 합니다. NFS를 통해 관리 디스크를 사용할 수 있도록 설정 하 고, 사설 클라우드의 데이터 저장소로 NFS 공유를 탑재 한 다음 데이터를 복사 합니다. 이 방법을 사용 하면 사설 클라우드로 데이터를 더 빠르게 복사할 수 있습니다.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Linux 가상 머신 및 managed disks를 사용 하 여 사설 클라우드에 데이터를 복사한 다음 NFS 공유로 내보내기

1. 저장소 계정이 만들어지고 사설 클라우드에 대 한 Azure 가상 네트워크 연결이 있는 동일한 지역의 Azure에서 [Linux 가상 머신을](../virtual-machines/linux/quick-create-portal.md) 만듭니다.

2. 저장소 용량이 blob 데이터의 용량 보다 큰 관리 디스크를 만들고 [Linux 가상 머신에 연결](../virtual-machines/linux/attach-disk-portal.md)합니다.  Blob 데이터의 양이 사용 가능한 가장 큰 관리 디스크 용량 보다 큰 경우에는 데이터를 여러 단계로 복사 하거나 여러 개의 관리 디스크를 사용 하 여 복사 해야 합니다.

3. Linux 가상 머신에 연결 하 고 관리 디스크를 탑재 합니다.

4. [Linux 가상 머신에 AzCopy를](../storage/common/storage-use-azcopy-v10.md)설치 합니다.

5. AzCopy를 사용 하 여 Azure Blob 저장소에서 관리 디스크로 데이터를 다운로드 합니다.  명령 구문: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` .  을 `<storage-account-name>` Azure storage 계정 이름으로 바꾸고,을 `<container-name>` Data Box를 통해 복사 된 데이터를 보유 하는 컨테이너로 바꿉니다.

6. Linux 가상 머신에 NFS 서버를 설치 합니다.

    - Ubuntu/Debian 배포: `sudo apt install nfs-kernel-server` .
    - 엔터프라이즈 Linux 배포판 `sudo yum install nfs-utils` 에서:

7. Azure Blob 저장소의 데이터가 복사 된 관리 디스크의 폴더에 대 한 사용 권한을 변경 합니다.  NFS 공유로 내보내려는 모든 폴더에 대 한 사용 권한을 변경 합니다.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. 파일을 편집 하 여 NFS 공유에 액세스할 클라이언트 IP 주소에 대 한 사용 권한을 할당 `/etc/exports` 합니다.

    ```bash
    sudo vi /etc/exports
    ```
    
    사설 클라우드의 모든 ESXi 호스트 IP에 대해 파일에 다음 줄을 입력 합니다.  여러 폴더에 대 한 공유를 만드는 경우 모든 폴더를 추가 합니다.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. 명령을 사용 하 여 NFS 공유를 내보냅니다 `sudo exportfs -a` .

10. 명령을 사용 하 여 NFS 커널 서버를 다시 시작 `sudo systemctl restart nfs-kernel-server` 합니다.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>사설 클라우드 vCenter 클러스터에서 Linux 가상 컴퓨터 NFS 공유를 데이터 저장소로 탑재 한 다음 데이터 복사

Linux 가상 머신에서 NFS 공유를 사설 클라우드 vCenter 클러스터에 데이터 저장소로 탑재 해야 합니다. 탑재 된 후에는 NFS 데이터 저장소에서 사설 클라우드 vSAN 데이터 저장소로 데이터를 복사할 수 있습니다.

1. 사설 클라우드 vCenter 서버에 로그인 합니다.

2. **데이터 센터**를 마우스 오른쪽 단추로 클릭 하 고 **저장소**를 선택 하 고 **새 데이터 저장소**를 선택한 후 **다음**을 선택 합니다.

   ![새 데이터 저장소 추가](media/databox-migration-add-datastore.png)

3. 데이터 저장소 추가 마법사의 1 단계에서 **NFS** 종류를 선택 합니다.

   ![새 데이터 저장소-형식 추가](media/databox-migration-add-datastore-type.png)

4. 마법사의 2 단계에서 nfs **3** 을 nfs 버전으로 선택한 후 **다음**을 선택 합니다.

   ![새 데이터 저장소-NFS 버전 추가](media/databox-migration-add-datastore-nfs-version.png)

5. 마법사의 3 단계에서 데이터 저장소의 이름, 경로 및 서버를 지정 합니다.  서버에 대 한 Linux 가상 머신의 IP 주소를 사용할 수 있습니다.  폴더 경로는 `/<folder>/<subfolder>/` 형식이 됩니다.

   ![새 데이터 저장소-NFS 구성 추가](media/databox-migration-add-datastore-nfs-configuration.png)

6. 마법사의 4 단계에서 데이터 저장소를 탑재할 ESXi 호스트를 선택 하 고 **다음**을 선택 합니다.  클러스터에서 모든 호스트를 선택 하 여 가상 컴퓨터의 마이그레이션을 확인 합니다.

   ![새 데이터 저장소 추가-호스트 선택](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 마법사의 5 단계에서 요약을 검토 한 다음 **마침**을 선택 합니다.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>NFS 데이터 저장소의 가상 컴퓨터 및 가상 컴퓨터 템플릿을 인벤토리에 추가 합니다.

1. 사설 클라우드 vCenter 웹 UI에서 **저장소**로 이동 합니다.  Linux 가상 컴퓨터 NFS 데이터 저장소를 선택 하 고 **파일**을 선택 합니다.

    ![NFS 데이터 저장소에서 파일 선택](media/databox-migration-datastore-select-files.png)

2. 가상 컴퓨터 또는 가상 컴퓨터 템플릿을 포함 하는 폴더를 선택 하십시오.  세부 정보 창에서 가상 머신에 대 한 .vmx 파일을 선택 하거나 가상 머신 템플릿에 대 한 .vmtx 파일을 선택 합니다.

3. **VM 등록** 을 선택 하 여 사설 클라우드 vCenter에 가상 머신을 등록 합니다.

    ![가상 컴퓨터 등록](media/databox-migration-datastore-register-vm.png)

4. 가상 컴퓨터를 등록 하려는 데이터 센터, 폴더 및 클러스터/리소스 풀을 선택 합니다.

4. 모든 가상 머신 및 가상 머신 템플릿에 대해 3 단계와 4 단계를 반복 합니다.

5. ISO 파일이 포함 된 폴더로 이동 합니다.  ISO 파일을 선택 하 고 **복사를** 선택 하 여 vsan 데이터 저장소의 폴더에 파일을 복사 합니다.

이제 가상 컴퓨터 및 가상 컴퓨터 템플릿을 사설 클라우드 vCenter에서 사용할 수 있습니다. 이러한 가상 컴퓨터를 설정 하기 전에 NFS 데이터 저장소에서 vSAN 데이터 저장소로 이동 해야 합니다. **저장소 vMotion** 옵션을 사용 하 고 가상 컴퓨터의 대상으로 vsan 데이터 저장소를 선택할 수 있습니다.

가상 컴퓨터 템플릿을 Linux 가상 컴퓨터 NFS 데이터 저장소에서 vSAN 데이터 저장소로 복제 해야 합니다.

### <a name="clean-up-your-linux-virtual-machine"></a>Linux 가상 머신 정리

모든 데이터가 사설 클라우드에 복사 된 후 사설 클라우드에서 NFS 데이터 저장소를 제거할 수 있습니다.

1. 모든 가상 컴퓨터 및 템플릿이 이동 되 고 vSAN 데이터 저장소로 복제 되었는지 확인 합니다.

2. NFS 데이터 저장소에서 모든 가상 머신 템플릿을 인벤토리에 제거 합니다.

3. 사설 클라우드 vCenter에서 Linux 가상 컴퓨터 데이터 저장소를 탑재 해제 합니다.

4. Azure에서 가상 머신 및 관리 디스크를 삭제 합니다.

5. 저장소 계정에서 Data Box 여 전송 된 데이터를 유지 하지 않으려면 Azure storage 계정을 삭제 합니다.  
    


## <a name="next-steps"></a>다음 단계

* [Data Box](../databox/data-box-overview.md)에 대해 자세히 알아보세요.
* [사설 클라우드로 워크 로드를 마이그레이션하](migrate-workloads.md)는 다양 한 옵션에 대해 자세히 알아보세요.
