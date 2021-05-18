---
title: Azure VMware Solution - Azure Data Box를 사용하는 마이그레이션
description: Azure Data Box를 사용해 데이터를 Azure VMware Solution에 대량으로 마이그레이션하는 방법입니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741447"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Azure Data Box를 사용해 데이터를 Azure VMware Solution에 마이그레이션하기

Microsoft Azure Data Box 클라우드 솔루션을 사용하면 TB(테라바이트) 단위의 데이터를 빠르고 저렴하게 신뢰할 수 있는 방식으로 Azure로 보낼 수 있습니다. 사용자에게 전용 Data Box 스토리지 디바이스를 배송하여 안전한 데이터 전송을 더욱 빠르게 실현시켜 드립니다. 각 스토리지 디바이스는 사용 가능한 최대 스토리지 용량이 80TB이며, 지역 이동 통신 사업자를 통해 사용자의 데이터 센터에 수송됩니다. 디바이스에는 운송 중에 데이터를 안전하게 보호하기 위한 견고한 케이스가 있습니다.

Data Box를 통해 VMware 데이터를 대량으로 프라이빗 클라우드에 마이그레이션할 수 있습니다. 온-프레미스 VMware vSphere 환경의 데이터가 NFS(네트워크 파일 시스템) 프로토콜을 통해 Data Box에 복사됩니다. 대량 데이터 마이그레이션은 지정 시점의 가상 머신, 구성 및 관련 데이터 사본을 Data Box에 저장한 다음 Azure에 수동으로 전달하는 작업을 포함합니다.

이 문서에서는 다음에 대해 알아봅니다.

* Data Box 설정하기.
* NFS를 통해 온-프레미스 VMware 환경에서 Data Box로 데이터 복사하기.
* Data Box 반환 준비하기.
* Azure VMware Solution으로의 복사 관련 BLOB 데이터 준비하기.
* Azure에서 프라이빗 클라우드로 데이터 복사하기.

## <a name="scenarios"></a>시나리오

대량 데이터 마이그레이션에 대한 다음 시나리오에서는 다음 목적을 위해 Data Box를 사용합니다.

* 대량의 데이터를 온-프레미스에서 Azure VMware Solution으로 마이그레이션하기. 해당 메서드는 기준선을 설정하고 네트워크를 통해 차이를 동기화합니다.
* 꺼져 있는 다수의 가상 머신(콜드 가상 머신)을 마이그레이션하기.
* 개발 및 테스트 환경 설정을 위한 가상 머신 데이터 마이그레이션하기.
* 다수의 가상 머신 템플릿, ISO 파일, 가상 머신 디스크를 마이그레이션하기.

## <a name="before-you-begin"></a>시작하기 전에

* Azure Portal에서 사전 요구 사항을 확인하고 [Data Box를 주문](../databox/data-box-deploy-ordered.md)합니다. 주문 프로세스 중에는 Blob Storage를 사용하는 스토리지 계정을 선택해야 합니다. Data Box 디바이스를 받은 뒤에는, 해당 디바이스를 자신의 온-프레미스 네트워크에 연결하고, vSphere 관리 네트워크에서 도달할 수 있는 IP 주소로 [디바이스를 설정](../databox/data-box-deploy-set-up.md)합니다.

* 자신의 Azure VMware Solution이 프로비전되는 동일 지역에서 가상 네트워크와 스토리지 계정을 만듭니다.

* [ExpressRoute를 사용해 Azure 가상 네트워크를 CloudSimple에 연결하기](virtual-network-connection.md)에 나와 있는 단계에 따라 스토리지 계정을 만들어 둔 가상 네트워크와 프라이빗 클라우드를 연결하는 [Azure 가상 네트워크 연결](cloudsimple-azure-network-connection.md)을 만듭니다.

## <a name="set-up-data-box-for-nfs"></a>NFS용 Data Box 설정하기

[자습서: Azure Data Box에 대한 케이블 및 연결](../databox/data-box-deploy-set-up.md)의 “디바이스에 연결” 섹션에 나와 있는 단계에 따라 Data Box 로컬 웹 UI에 연결합니다.  NFS 클라이언트에 액세스하도록 Data Box를 구성합니다.

1. 로컬 웹 UI에서 **연결 및 복사** 페이지로 이동합니다. **NFS 설정** 에서 **NFS 클라이언트 액세스** 를 선택합니다. 

    ![NFS 클라이언트 액세스 구성 1](media/nfs-client-access.png)

2. VMware ESXi 호스트 IP 주소로 들어가서 **추가** 를 선택합니다. 해당 단계를 반복하면 vSphere 클러스터에서 호스트 전체에 대한 액세스를 구성할 수 있습니다. **확인** 을 선택합니다.

    ![NFS 클라이언트 액세스 구성 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **복사하려는 파일에 대한 폴더는 항상 공유 아래에 만든 다음 이 폴더에 파일을 복사합니다**. 블록 Blob 및 페이지 Blob 공유 아래에 만들어진 폴더는 데이터가 Blob으로 업로드되는 컨테이너를 나타냅니다. 스토리지 계정 내 *루트* 폴더에는 파일을 직접 복사할 수 없습니다.

블록 Blob 및 페이지 Blob 공유에서는 첫 번째 수준 엔터티가 컨테이너, 두 번째 수준 엔터티가 Blob입니다. Azure Files 공유에서는 첫 번째 수준 엔터티가 공유, 두 번째 수준 엔터티가 파일입니다.

다음 표에서는 데이터가 업로드되는 Data Box 및 Azure Storage 경로 URL의 공유에 대한 UNC 경로를 보여줍니다. 최종 Azure Storage 경로 URL은 UNC 공유 경로에서 파생될 수 있습니다.
 
| Blob 및 파일 | 경로 및 URL |
|---------------- | ------------ |
| Azure 블록 Blob | <li>공유 UNC 경로: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 페이지 Blob  | <li>공유 UNC 경로: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 파일       |<li>공유 UNC 경로: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Azure 블록 Blob을 사용해 VMware 데이터를 복사합니다.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>NFS 공유를 데이터 저장소로 온-프레미스 vCenter 클러스터에 탑재하고 데이터를 복사합니다.

데이터를 NFS 데이터 저장소에 복사하기 위해 Data Box의 NFS 공유는 온-프레미스 vCenter 클러스터나 VMware ESXi 호스트에 데이터 저장소로 탑재해야 합니다.

1. 온-프레미스 vCenter Server에 로그인합니다.

2. **데이터 센터** 를 마우스 오른쪽 단추로 클릭하여 **스토리지** 를 선택하고 **새 데이터 저장소** 를 선택한 뒤, **다음** 을 선택합니다.

   ![새 데이터 저장소 추가하기](media/databox-migration-add-datastore.png)

3. 데이터 저장소 추가 마법사의 첫 번째 단계에서 **형식** 에 **NFS** 를 선택합니다.

   ![새 데이터 저장소 추가하기 - 형식](media/databox-migration-add-datastore-type.png)

4. 동일 마법사 두 번째 단계에서 NFS 버전을 **NFS 3** 로 선택한 뒤 **다음** 을 선택합니다.

   ![새 데이터 저장소 추가하기 - NFS 버전](media/databox-migration-add-datastore-nfs-version.png)

5. 동일 마법사 세 번째 단계에서, 데이터 저장소 이름, 경로, 서버를 정합니다. 서버용 Data Box의 IP 주소를 사용할 수 있습니다. 폴더 경로는 `/<StorageAccountName_BlockBlob>/<ContainerName>/` 형식이 됩니다.

   ![새 데이터 저장소 추가하기 - NFS 구성](media/databox-migration-add-datastore-nfs-configuration.png)

6. 마법사의 네 번째 단계에서 데이터 저장소를 탑재할 ESXi 호스트를 선택한 뒤 **다음** 을 선택합니다.  클러스터에서 모든 호스트를 선택하여 가상 머신의 마이그레이션을 확인합니다.

   ![새 데이터 저장소 추가하기 - 호스트 선택하기](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 마법사의 다섯 번째 단계에서 요약을 검토하고 **마침** 을 선택합니다.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Data Box NFS 데이터 저장소에 데이터 복사하기

가상 머신을 새 데이터 저장소에 마이그레이션하거나 복제할 수 있습니다.  사용하지 않는 가상 머신 가운데 마이그레이션하려는 것이 있으면 **스토리지 vMotion** 옵션을 이용해 Data Box NFS 데이터 저장소로 마이그레이션할 수 있습니다. 활성 가상 머신을 Data Box NFS 데이터 저장소에 복제할 수 있습니다.

* **이동** 할 수 있는 가상 머신을 식별하여 목록으로 나열합니다.
* **이동** 해야만 하는 가상 머신을 식별하여 목록으로 나열합니다.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Data Box 데이터 저장소로 가상 머신 이동하기

1. Data Box 데이터 저장소로 이동하려는 가상 머신을 마우스 오른쪽 단추로 클릭한 뒤 **마이그레이션** 을 선택합니다.

    ![가상 머신 마이그레이션하기](media/databox-migration-vm-migrate.png)

2. 마이그레이션 형식에 **스토리지만 변경** 을 선택한 뒤 **다음** 을 선택합니다.

    ![가상 머신 마이그레이션하기 - 스토리지만](media/databox-migration-vm-migrate-change-storage.png)

3. 대상으로 **Databox-데이터 저장소** 를 선택한 뒤 **다음** 을 선택합니다.

    ![가상 머신 마이그레이션하기 - 데이터 저장소 선택하기](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. 정보를 검토한 뒤 **마침** 을 선택합니다.

5. 가상 머신이 더 있다면 1~4단계를 반복합니다.

> [!TIP]
> 전원 상태(켜짐 또는 꺼짐)가 같은 여러 대의 가상 머신을 선택해 대량으로 마이그레이션할 수 있습니다.

가상 머신은 Data Box에서 NFS 데이터 저장소로 마이그레이션됩니다. 가상 머신을 모두 마이그레이션한 뒤에는 데이터를 Azure VMware Solution으로 마이그레이션할 준비를 하면서 활성 가상 머신을 끌(종료할) 수 있습니다.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Data Box 데이터 저장소에 가상 머신이나 가상 머신 템플릿 복제하기

1. 복제할 가상 머신이나 가상 머신 템플릿을 마우스 오른쪽 단추로 클릭합니다. **복제** > **가상 머신에 복제** 를 선택합니다.

    ![가상 머신 복제본](media/databox-migration-vm-clone.png)

2. 가상 머신 복제본 또는 가상 머신 템플릿 복제본의 이름을 선택합니다.

3. 복제한 개체가 위치할 폴더를 선택한 뒤 **다음** 을 선택합니다.

4. 복제한 개체가 위치할 클러스터나 리소스 풀을 선택한 뒤 **다음** 을 선택합니다.

5. 스토리지 장소에 **Databox-데이터 저장소** 를 선택한 뒤 **다음** 을 선택합니다.

    ![가상 머신 복제본 - 데이터 저장소 선택하기](media/databox-migration-vm-clone-select-datastore.png)

6. 복제한 개체의 옵션을 사용자 지정하려면 사용자 지정 옵션을 선택한 뒤 **다음** 을 선택합니다.

7. 구성을 검토하고 **마침** 을 선택합니다.

8. 가상 머신이나 가상 머신 템플릿이 더 있는 경우 1~7단계를 반복합니다.

가상 머신을 Data Box에서 복제하여 NFS 데이터 저장소에 보관합니다. 가상 머신을 복제한 뒤에는 데이터를 Azure VMware Solution으로 마이그레이션할 준비를 하면서 해당 가상 머신들이 종료되었는지 확인합니다.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Data Box 데이터 저장소에 ISO 파일 복사하기

1. 온-프레미스 vCenter 웹 UI에서 **스토리지** 로 이동합니다.  **Databox-데이터 저장소** 를 선택한 다음 **파일** 을 선택합니다. ISO 파일을 저장할 새 폴더를 만듭니다.

    ![ISO 복사하기 - 새 폴더 만들기](media/databox-migration-create-folder.png)

2. ISO 파일을 저장할 폴더 이름을 지정합니다.

3. 새로 만든 폴더를 두 번 클릭하여 엽니다.

4. **파일 업로드** 를 선택한 뒤 업로드할 ISO 파일을 선택합니다.
    
    ![ISO 복사하기 - 파일 업로드하기](media/databox-migration-upload-iso.png)

> [!TIP]
> 온-프레미스 데이터 저장소에 이미 ISO 파일이 있다면, 해당 파일을 선택하고 **...로 복사** 를 선택하여 파일을 Data Box NFS 데이터 저장소에 복사할 수 있습니다.


## <a name="prepare-data-box-for-return"></a>Data Box 반환 준비하기

가상 머신 데이터, 가상 머신 템플릿 데이터, ISO 파일을 모두 Data Box NFS 데이터 저장소로 복사한 뒤에는 vCenter에서 데이터 저장소의 연결을 해제할 수 있습니다. 데이터 저장소의 연결을 해제하기 전에 인벤토리에서 가상 머신과 가상 머신 템플릿을 모두 삭제해야 합니다.

### <a name="remove-objects-from-inventory"></a>인벤토리의 개체 제거하기

1. 온-프레미스 vCenter 웹 UI에서 **스토리지** 로 이동합니다. **Databox-데이터 저장소** 를 선택한 뒤 **VM** 을 선택합니다.

    ![인벤토리의 가상 머신 제거하기 - 꺼짐](media/databox-migration-select-databox-vm.png)

2. 가상 머신이 모두 종료되었는지 확인합니다.

3. 가상 머신을 모두 선택하여 마우스 오른쪽 단추를 클릭한 다음, **인벤토리에서 제거** 를 선택합니다.

    ![인벤토리의 가상 머신 제거하기](media/databox-migration-remove-vm-from-inventory.png)

4. **폴더 내의 VM 템플릿** 을 선택한 뒤 세 번째 단계를 반복합니다.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>vCenter에서 Data Box NFS 데이터 저장소 제거하기

반환을 준비하기 전에 VMware ESXi 호스트에서 Data Box NFS 데이터 저장소의 연결을 해제해야 합니다.

1. 온-프레미스 vCenter 웹 UI에서 **스토리지** 로 이동합니다.

2. **Databox-데이터 저장소** 를 마우스 오른쪽 단추로 클릭하고 **데이터 저장소 분리** 를 선택합니다.

    ![Data Box 데이터 저장소 분리하기](media/databox-migration-unmount-datastore.png)

3. 데이터 저장소를 탑재한 ESXi 호스트를 모두 선택하고 **확인** 을 선택합니다.

    ![Data Box 데이터 저장소 분리하기 - 호스트 선택하기](media/databox-migration-unmount-datastore-select-hosts.png)

4. 경고를 검토 후 모두 수락하고 **확인** 을 선택합니다.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Data Box 반환 준비하여 반환하기

[Azure Data Box 반환 및 Azure로의 데이터 업로드 확인](../databox/data-box-deploy-picked-up.md) 문서의 단계를 따라 Data Box를 반환합니다. Azure Storage 계정으로의 데이터 복사 상태를 확인합니다. 해당 상태가 완료로 표시된 후에는 Azure Storage 계정에서 데이터를 확인할 수 있습니다.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Azure Storage에서 Azure VMware Solution으로 데이터 복사하기

Data Box 디바이스로 복사한 데이터는 Data Box의 주문 상태가 완료로 표시된 다음부터 Azure Storage 계정에서 사용할 수 있습니다. 이제 데이터를 Azure VMware Solution으로 복사할 수 있습니다. 스토리지 계정 내의 데이터는 NFS 프로토콜을 사용해 프라이빗 클라우드의 vSAN 데이터 저장소에 복사해야 합니다. 

먼저 **AzCopy** 를 통해 Blob Storage 데이터를 Azure의 Linux 가상 머신에 있는 관리 디스크에 복사합니다. NFS를 통해 관리 디스크를 사용할 수 있도록 설정하고 NFS 공유를 데이터 저장소로 탑재한 뒤 데이터를 복사합니다. 이렇게 함으로써 데이터를 프라이빗 클라우드에 보다 빠르게 복사할 수 있습니다.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Linux 가상 머신과 관리 디스크를 사용하여 데이터를 프라이빗 클라우드에 복사한 다음 NFS 공유로 내보내기

1. 스토리지 계정을 만들고 프라이빗 클라우드에 대한 Azure 가상 네트워크 연결을 갖춘 동일 지역의 Azure에 [Linux 가상 머신](../virtual-machines/linux/quick-create-portal.md)을 만듭니다.

2. 스토리지 용량이 Blob 데이터의 용량보다 큰 관리 디스크를 만들어 [Linux 가상 머신에 연결합니다](../virtual-machines/linux/attach-disk-portal.md).  Blob 데이터 용량이 관리 디스크에서 사용할 수 있는 최대 용량을 초과하는 경우에는 여러 단계로 해당 데이터를 복사하거나 관리 디스크를 여러 개 사용해 해당 데이터를 복사해야 합니다.

3. Linux 가상 머신에 연결해 관리 디스크를 탑재합니다.

4. [AzCopy를 Linux 가상 머신에](../storage/common/storage-use-azcopy-v10.md) 설치합니다.

5. AzCopy를 이용해 데이터를 Azure Blob Storage에서 관리 디스크로 다운로드합니다.  명령 구문: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  `<storage-account-name>`를 자신의 Azure Storage 계정 이름으로 바꾸고 `<container-name>`는 Data Box를 통해 복사한 데이터가 있는 컨테이너로 바꿉니다.

6. Linux 가상 머신에 NFS 서버를 설치합니다.

    - Ubuntu/Debian 배포: `sudo apt install nfs-kernel-server`.
    - Enterprise Linux 배포: `sudo yum install nfs-utils`.

7. Azure Blob Storage의 데이터를 복사한 관리 디스크의 폴더 권한을 변경합니다.  NFS 공유로 내보내려는 모든 폴더의 권한을 변경합니다.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. `/etc/exports` 파일을 편집해 클라이언트 IP 주소가 NFS 공유에 액세스하도록 권한을 할당합니다.

    ```bash
    sudo vi /etc/exports
    ```
    
    프라이빗 클라우드의 ESXi 호스트 파일 모두에 다음 줄을 입력합니다.  여러 폴더에 대한 공유를 만드는 중이라면 모든 폴더를 추가합니다.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. `sudo exportfs -a` 명령을 사용해 NFS 공유를 내보냅니다.

10. `sudo systemctl restart nfs-kernel-server` 명령을 사용해 NFS 커널 서버를 다시 시작합니다.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Linux 가상 머신 NFS 공유를 데이터 저장소로 프라이빗 클라우드 vCenter 클러스터에 탑재한 다음 데이터 복사하기

Linux 가상 머신의 NFS 공유는 반드시 데이터 저장소로 프라이빗 클라우드 vCenter 클러스터에 탑재해야 합니다. 탑재한 후에는 데이터를 NFS 데이터 저장소에서 프라이빗 클라우드 vSAN 데이터 저장소로 복사할 수 있습니다.

1. 프라이빗 클라우드 vCenter Server에 로그인합니다.

2. **데이터 센터** 를 마우스 오른쪽 단추로 클릭하여 **스토리지** 를 선택하고 **새 데이터 저장소** 를 선택한 뒤, **다음** 을 선택합니다.

   ![새 데이터 저장소 추가하기](media/databox-migration-add-datastore.png)

3. 데이터 저장소 추가 마법사의 첫 번째 단계에서 **NFS** 형식을 선택합니다.

   ![새 데이터 저장소 추가하기 - 형식](media/databox-migration-add-datastore-type.png)

4. 동일 마법사 두 번째 단계에서 NFS 버전을 **NFS 3** 로 선택한 뒤 **다음** 을 선택합니다.

   ![새 데이터 저장소 추가하기 - NFS 버전](media/databox-migration-add-datastore-nfs-version.png)

5. 동일 마법사 세 번째 단계에서, 데이터 저장소 이름, 경로, 서버를 정합니다.  서버용 Linux 가상 머신 IP 주소를 사용할 수 있습니다.  폴더 경로는 `/<folder>/<subfolder>/` 형식이 됩니다.

   ![새 데이터 저장소 추가하기 - NFS 구성](media/databox-migration-add-datastore-nfs-configuration.png)

6. 마법사의 네 번째 단계에서 데이터 저장소를 탑재할 ESXi 호스트를 선택한 뒤 **다음** 을 선택합니다.  클러스터에서 모든 호스트를 선택하여 가상 머신의 마이그레이션을 확인합니다.

   ![새 데이터 저장소 추가하기 - 호스트 선택하기](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 마법사의 다섯 번째 단계에서 요약을 검토한 다음 **마침** 을 선택합니다.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>NFS 데이터 저장소에서 인벤토리로 가상 머신과 가상 머신 템플릿을 추가합니다.

1. 프라이빗 클라우드 vCenter 웹 UI에서 **스토리지** 로 이동합니다.  Linux 가상 머신 NFS 데이터 저장소를 선택한 뒤 **파일** 을 선택합니다.

    ![NFS 데이터 저장소에서 파일 선택하기](media/databox-migration-datastore-select-files.png)

2. 가상 머신이나 가상 머신 템플릿이 들어 있는 폴더를 선택합니다.  가상 머신인 경우에는 .vmx 파일을, 가상 머신 템플릿인 경우에는 .vmtx 파일을 세부 정보 창에서 선택합니다.

3. **VM 등록** 을 선택하여 해당 가상 머신을 자신의 프라이빗 클라우드 vCenter에 등록합니다.

    ![가상 머신 등록하기](media/databox-migration-datastore-register-vm.png)

4. 가상 머신을 등록하려는 데이터 센터, 폴더, 클러스터/리소스 풀을 선택합니다.

4. 가상 머신과 가상 머신 템플릿 모두에 대해 3단계와 4단계를 반복합니다.

5. ISO 파일이 들어 있는 폴더로 이동합니다.  ISO 파일을 선택한 다음 **...로 복사** 를 선택하여 파일을 vSAN 데이터 저장소의 폴더에 복사합니다.

이제 프라이빗 클라우드 vCenter에서 가상 머신과 가상 머신 템플릿을 사용할 수 있습니다. 해당 가상 머신은 켜기 전에 반드시 NFS 데이터 저장소에서 vSAN 데이터 저장소로 이동해야 합니다. **스토리지 vMotion** 옵션을 사용하고 vSAN 데이터 저장소를 가상 머신의 대상으로 선택할 수 있습니다.

반드시 해당 가상 머신 템플릿을 Linux 가상 머신 NFS 데이터 저장소에서 vSAN 데이터 저장소로 복제해야 합니다.

### <a name="clean-up-your-linux-virtual-machine"></a>Linux 가상 머신 정리하기

프라이빗 클라우드로 데이터를 모두 복사한 뒤에는 프라이빗 클라우드에서 NFS 데이터 저장소를 제거할 수 있습니다.

1. 가상 머신과 템플릿을 모두 자신의 vSAN 데이터 저장소로 이동 및 복제했는지 확인합니다.

2. NFS 데이터 저장소의 가상 머신 템플릿을 모두 인벤토리에서 제거합니다.

3. Linux 가상 머신 데이터 저장소를 프라이빗 클라우드 vCenter에서 분리합니다.

4. Azure에서 가상 머신과 관리 디스크를 삭제합니다.

5. 스토리지 계정의 Data Box를 통해 전송한 데이터를 유지하지 않으려면 Azure Storage 계정을 삭제합니다.  
    


## <a name="next-steps"></a>다음 단계

* [Data Box](../databox/data-box-overview.md)에서 자세한 내용을 참조하세요.
* [프라이빗 클라우드로 워크로드 마이그레이션](migrate-workloads.md)을 위한 다양한 옵션에 대해 자세한 내용을 확인하세요.
