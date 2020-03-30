---
title: Azure VMware 솔루션 - Azure 데이터 상자를 사용한 마이그레이션
description: Azure 데이터 상자를 사용하여 데이터를 Azure VMware 솔루션으로 대량 마이그레이션하는 방법
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019760"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Azure 데이터 상자를 사용하여 Azure VMware 솔루션으로 데이터 마이그레이션

Microsoft Azure 데이터 박스 클라우드 솔루션을 사용하면 테라바이트(TB)의 데이터를 빠르고 저렴하며 신뢰할 수 있는 방식으로 Azure로 보낼 수 있습니다. 사용자에게 전용 Data Box 스토리지 디바이스를 배송하여 안전한 데이터 전송을 더욱 빠르게 실현시켜 드립니다. 각 저장 장치는 최대 사용 가능한 저장 용량이 80TB이며 지역 이동통신사가 데이터 센터로 전송합니다. 디바이스에는 운송 중에 데이터를 안전하게 보호하기 위한 견고한 케이스가 있습니다.

데이터 상자를 사용하면 VMware 데이터를 프라이빗 클라우드로 대량 마이그레이션할 수 있습니다. 온-프레미스 VMware vSphere 환경의 데이터는 NFS(네트워크 파일 시스템) 프로토콜을 통해 데이터 박스에 복사됩니다. 대량 데이터 마이그레이션에는 가상 컴퓨터, 구성 및 연결된 데이터의 특정 시점 복사본을 데이터 상자에 저장한 다음 수동으로 Azure로 전송해야 합니다.

이 문서에서는 다음을 알아봅니다.

* 데이터 상자 설정.
* NFS를 통해 온-프레미스 VMware 환경에서 데이터 박스로 데이터를 복사합니다.
* 데이터 상자의 반환 준비.
* Azure VMware 솔루션에 복사할 Blob 데이터를 준비합니다.
* Azure에서 프라이빗 클라우드로 데이터를 복사합니다.

## <a name="scenarios"></a>시나리오

대량 데이터 마이그레이션을 위해 다음 시나리오에서 데이터 상자를 사용합니다.

* 온-프레미스에서 Azure VMware 솔루션으로 많은 양의 데이터를 마이그레이션합니다. 이 메서드는 기준을 설정하고 네트워크를 통해 차이를 동기화합니다.
* 꺼져 있는 많은 수의 가상 시스템(콜드 가상 시스템)을 마이그레이션합니다.
* 개발 및 테스트 환경 설정을 위한 가상 시스템 데이터를 마이그레이션합니다.
* 많은 수의 가상 컴퓨터 템플릿, ISO 파일 및 가상 컴퓨터 디스크를 마이그레이션합니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure 포털을 통해 필수 구성 조건을 선택하고 [데이터 상자를 주문합니다.](../databox/data-box-deploy-ordered.md) 주문 프로세스 중에 Blob 저장소를 사용할 수 있는 저장소 계정을 선택해야 합니다. 데이터 박스 장치를 받은 후 온-프레미스 네트워크에 연결하고 vSphere 관리 네트워크에서 연결할 수 있는 IP 주소로 [장치를 설정합니다.](../databox/data-box-deploy-set-up.md)

* Azure VMware 솔루션이 프로비전된 동일한 리전에서 가상 네트워크 및 저장소 계정을 만듭니다.

* 익스프레스루트를 사용하여 Azure 가상 네트워크 연결에서 [CloudSimple에 연결의](virtual-network-connection.md)단계를 수행하여 저장소 계정이 생성되는 가상 네트워크에 대한 Azure [가상 네트워크 연결을](cloudsimple-azure-network-connection.md) 만듭니다.

## <a name="set-up-data-box-for-nfs"></a>NFS용 데이터 상자 설정

[자습서의](../databox/data-box-deploy-set-up.md)"장치에 연결" 섹션: 케이블 및 Azure 데이터 상자에 연결 하는 단계에 따라 데이터 박스 로컬 웹 UI에 연결 합니다.  NFS 클라이언트에 대한 액세스를 허용하도록 데이터 상자를 구성합니다.

1. 로컬 웹 UI에서 연결 **및 복사** 페이지로 이동합니다. **NFS 설정에서** **NFS 클라이언트 액세스를**선택합니다. 

    ![NFS 클라이언트 액세스 구성 1](media/nfs-client-access.png)

2. VMware ESXi 호스트의 IP 주소를 입력하고 **추가를**선택합니다. 이 단계를 반복하여 vSphere 클러스터의 모든 호스트에 대한 액세스를 구성할 수 있습니다. **확인**을 선택합니다.

    ![NFS 클라이언트 액세스 구성 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **복사하려는 파일에 대한 폴더는 항상 공유 아래에 만든 다음 이 폴더에 파일을 복사합니다**. 블록 Blob 및 페이지 Blob 공유 아래에 만들어진 폴더는 데이터가 Blob으로 업로드되는 컨테이너를 나타냅니다. 저장소 계정의 *루트* 폴더에 파일을 직접 복사할 수 없습니다.

블록 Blob 및 페이지 Blob 공유에서는 첫 번째 수준 엔터티가 컨테이너, 두 번째 수준 엔터티가 Blob입니다. Azure Files에 대한 공유에서 첫 번째 수준 엔터티는 공유이고 두 번째 수준 엔터티는 파일입니다.

다음 표에서는 데이터가 업로드되는 Data Box 및 Azure Storage 경로 URL의 공유에 대한 UNC 경로를 보여줍니다. 최종 Azure Storage 경로 URL은 UNC 공유 경로에서 파생될 수 있습니다.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 블록 Blob | <li>공유 UNC 경로: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 페이지 Blob  | <li>공유 UNC 경로: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 파일       |<li>공유 UNC 경로: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> VMware 데이터를 복사할 때 Azure 블록 Blob을 사용합니다.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>온-프레미스 vCenter 클러스터에 NFS 공유를 데이터 스토어로 마운트하고 데이터를 복사합니다.

데이터를 NFS 데이터 스토어에 복사하려면 데이터 상자의 NFS 공유를 온-프레미스 vCenter 클러스터 또는 VMware ESXi 호스트에 데이터스토어로 탑재해야 합니다.

1. 온-프레미스 vCenter 서버에 로그인합니다.

2. **데이터 센터를**마우스 오른쪽 단추로 클릭하고 **저장소를**선택하고 **새 데이터 스토어를**선택한 다음 **다음을**선택합니다.

   ![새 데이터 스토어 추가](media/databox-migration-add-datastore.png)

3. 데이터 스토어 추가 마법사의 1단계에서 **유형**에서 **NFS를** 선택합니다.

   ![새 데이터스토어 추가 - 유형](media/databox-migration-add-datastore-type.png)

4. 마법사의 2단계에서 **NFS 3을 NFS** 버전으로 **선택한**다음 다음 을 선택합니다.

   ![새 데이터스토어 추가 - NFS 버전](media/databox-migration-add-datastore-nfs-version.png)

5. 마법사의 3단계에서 데이터스토어, 경로 및 서버의 이름을 지정합니다. 서버에 데이터 상자의 IP 주소를 사용할 수 있습니다. 폴더 경로는 형식이 `/<StorageAccountName_BlockBlob>/<ContainerName>/` 됩니다.

   ![새 데이터스토어 추가 - NFS 구성](media/databox-migration-add-datastore-nfs-configuration.png)

6. 마법사의 4단계에서 데이터스토어를 탑재할 ESXi 호스트를 선택한 다음 **다음을**선택합니다.  클러스터에서 모든 호스트를 선택하여 가상 시스템의 마이그레이션을 보장합니다.

   ![새 데이터스토어 추가 - 호스트 선택](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 마법사의 5단계에서 요약을 검토하고 **완료를**선택합니다.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>데이터 상자 NFS 데이터 스토어에 데이터 복사

가상 컴퓨터를 새 데이터스토어로 마이그레이션하거나 복제할 수 있습니다.  마이그레이션하려는 사용하지 않는 가상 시스템은 **저장소 vMotion** 옵션을 사용하여 데이터 상자 NFS 데이터 스토어로 마이그레이션할 수 있습니다. 활성 가상 시스템은 데이터 상자 NFS 데이터 스토어에 복제할 수 있습니다.

* **이동할**수 있는 가상 컴퓨터를 식별하고 나열합니다.
* 복제해야 하는 가상 컴퓨터를 식별하고 **나열합니다.**

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>가상 컴퓨터를 데이터 상자 데이터 저장소로 이동

1. 데이터 상자 데이터 스토어로 이동할 가상 컴퓨터를 마우스 오른쪽 단추로 클릭한 다음 **마이그레이션을 선택합니다.**

    ![가상 시스템 마이그레이션](media/databox-migration-vm-migrate.png)

2. 마이그레이션 유형에 **대해서만 저장소 변경을** 선택한 다음 **다음**을 선택합니다.

    ![가상 시스템 마이그레이션 - 저장소만](media/databox-migration-vm-migrate-change-storage.png)

3. **데이터박스-데이터스토어를** 대상으로 선택한 다음 **다음**을 선택합니다.

    ![가상 컴퓨터 마이그레이션 - 데이터스토어 선택](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. 정보를 검토하고 **완료를 선택합니다.**

5. 추가 가상 시스템에 대해 1~4단계를 반복합니다.

> [!TIP]
> 동일한 전원 상태(켜기 또는 꺼져 있음)에 있는 여러 가상 컴퓨터를 선택하고 대량으로 마이그레이션할 수 있습니다.

가상 시스템은 데이터 상자에서 NFS 데이터 스토어로 마이그레이션됩니다. 모든 가상 시스템이 마이그레이션된 후 Azure VMware 솔루션으로 데이터를 마이그레이션할 준비를 위해 활성 가상 컴퓨터를 해제(종료)할 수 있습니다.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>데이터 상자 데이터 스토어에 가상 컴퓨터 또는 가상 컴퓨터 템플릿 복제

1. 복제하려는 가상 컴퓨터 또는 가상 컴퓨터 템플릿을 마우스 오른쪽 단추로 클릭합니다. **가상 컴퓨터에** **복제** > 를 선택합니다.

    ![가상 시스템 복제](media/databox-migration-vm-clone.png)

2. 복제된 가상 컴퓨터 또는 가상 컴퓨터 템플릿의 이름을 선택합니다.

3. 복제된 개체를 넣을 폴더를 선택한 다음 **다음을**선택합니다.

4. 복제된 개체를 넣을 클러스터 또는 리소스 풀을 선택한 다음 **다음을**선택합니다.

5. **데이터박스-데이터스토어를** 저장소 위치로 선택한 다음 **다음**을 선택합니다.

    ![가상 시스템 복제 - 데이터스토어 선택](media/databox-migration-vm-clone-select-datastore.png)

6. 복제된 개체에 대한 옵션을 사용자 지정하려면 사용자 지정 옵션을 선택한 다음 **다음을 선택합니다.**

7. 구성을 검토하고 **완료를**선택합니다.

8. 추가 가상 컴퓨터 또는 가상 컴퓨터 템플릿에 대해 1~7단계를 반복합니다.

가상 컴퓨터는 데이터 상자에서 NFS 데이터 스토어에 복제되고 저장됩니다. 가상 시스템이 복제된 후 Azure VMware 솔루션으로 데이터를 마이그레이션할 준비를 위해 가상 시스템이 종료되었는지 확인합니다.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>ISO 파일 데이터 박스 데이터 스토어에 복사

1. 온-프레미스 vCenter 웹 UI에서 **저장소**로 이동합니다.  **데이터박스-데이터스토어를** 선택한 다음 **파일을**선택합니다. ISO 파일을 저장하기 위한 새 폴더를 만듭니다.

    ![ISO 복사 - 새 폴더 만들기](media/databox-migration-create-folder.png)

2. ISO 파일이 저장될 폴더의 이름을 제공합니다.

3. 새로 만든 폴더를 두 번 클릭하여 엽니다.

4. **파일 업로드를** 선택한 다음 업로드할 ISO 파일을 선택합니다.
    
    ![ISO 복사 - 파일 업로드](media/databox-migration-upload-iso.png)

> [!TIP]
> 온-프레미스 데이터스토어에 ISO 파일이 이미 있는 경우 파일을 선택하고 **복사하여** 파일을 데이터 박스 NFS 데이터스토어에 복사할 수 있습니다.


## <a name="prepare-data-box-for-return"></a>반환을 위한 데이터 상자 준비

모든 가상 컴퓨터 데이터, 가상 컴퓨터 템플릿 데이터 및 ISO 파일이 데이터 상자 NFS 데이터 스토어에 복사되면 vCenter에서 데이터 스토어를 분리할 수 있습니다. 데이터스토어연결을 끊기 전에 모든 가상 컴퓨터와 가상 컴퓨터 템플릿을 인벤토리에서 제거해야 합니다.

### <a name="remove-objects-from-inventory"></a>인벤토리에서 개체 제거

1. 온-프레미스 vCenter 웹 UI에서 **저장소**로 이동합니다. **데이터박스-데이터스토어를** 선택한 다음 **VM을**선택합니다.

    ![인벤토리에서 가상 시스템 제거 - 꺼져](media/databox-migration-select-databox-vm.png)

2. 모든 가상 시스템이 종료되었는지 확인합니다.

3. 모든 가상 컴퓨터를 선택하고 마우스 오른쪽 단추로 클릭한 다음 **인벤토리에서 제거를**선택합니다.

    ![인벤토리에서 가상 시스템 제거](media/databox-migration-remove-vm-from-inventory.png)

4. **폴더에서 VM 템플릿을** 선택한 다음 3단계를 반복합니다.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>vCenter에서 데이터 상자 NFS 데이터 스토어 제거

반환을 준비하기 전에 데이터 상자 NFS 데이터 스토어를 VMware ESXi 호스트에서 분리해야 합니다.

1. 온-프레미스 vCenter 웹 UI에서 **저장소**로 이동합니다.

2. **데이터박스-데이터스토어를** 마우스 오른쪽 단추로 클릭하고 **데이터 스토어 마운트 해제를**선택합니다.

    ![데이터 상자 데이터 스토어 마운트 해제](media/databox-migration-unmount-datastore.png)

3. 데이터스토어가 탑재된 모든 ESXi 호스트를 선택하고 **확인을**선택합니다.

    ![데이터 박스 데이터 스토어 마운트 해제 - 호스트 선택](media/databox-migration-unmount-datastore-select-hosts.png)

4. 경고를 검토하고 수락하고 **확인을**선택합니다.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>반환을 위해 데이터 상자를 준비한 다음 반환합니다.

Azure 데이터 상자 반환 문서에서 설명한 단계에 따라 [Azure에 데이터 업로드를 확인하여](../databox/data-box-deploy-picked-up.md) 데이터 상자를 반환합니다. Azure 저장소 계정에 대한 데이터 복사본의 상태를 확인합니다. 상태가 완료된 것으로 표시되면 Azure 저장소 계정의 데이터를 확인할 수 있습니다.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Azure 저장소에서 Azure VMware 솔루션으로 데이터 복사

데이터 상자 장치에 복사된 데이터는 데이터 상자의 주문 상태가 완료된 것으로 표시되면 Azure 저장소 계정에서 사용할 수 있습니다. 이제 데이터를 Azure VMware 솔루션으로 복사할 수 있습니다. 저장소 계정의 데이터는 NFS 프로토콜을 사용하여 사설 클라우드의 vSAN 데이터스토어에 복사해야 합니다. 

먼저 **AzCopy를**사용하여 Azure의 Linux 가상 시스템에서 Blob 저장소 데이터를 관리디스크에 복사합니다. NFS를 통해 관리 디스크를 사용할 수 있도록 하고 NFS 공유를 프라이빗 클라우드에 데이터 스토어로 마운트한 다음 데이터를 복사합니다. 이 방법을 사용하면 데이터를 프라이빗 클라우드로 더 빠르게 복사할 수 있습니다.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Linux 가상 머신 및 관리 디스크를 사용하여 데이터를 사설 클라우드로 복사한 다음 NFS 공유로 내보내기

1. 저장소 계정이 만들어지고 사설 클라우드에 Azure 가상 네트워크 연결이 있는 동일한 지역에서 Azure에서 Linux 가상 [컴퓨터를](../virtual-machines/linux/quick-create-portal.md) 만듭니다.

2. 저장 용량이 Blob 데이터의 양보다 큰 관리 디스크를 만들고 [Linux 가상 컴퓨터에 연결합니다.](../virtual-machines/linux/attach-disk-portal.md)  Blob 데이터의 양이 사용 가능한 가장 큰 관리 디스크의 용량보다 큰 경우 데이터를 여러 단계로 복사하거나 여러 관리 디스크를 사용하여 복사해야 합니다.

3. Linux 가상 컴퓨터에 연결하고 관리 디스크를 탑재합니다.

4. [리눅스 가상 머신에 AzCopy를 설치합니다.](../storage/common/storage-use-azcopy-v10.md)

5. AzCopy를 사용하여 Azure Blob 저장소의 데이터를 관리 디스크에 다운로드합니다.  명령 구문: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Azure `<storage-account-name>` 저장소 계정 이름과 `<container-name>` 데이터 상자를 통해 복사된 데이터를 포함하는 컨테이너로 바꿉니다.

6. 리눅스 가상 머신에 NFS 서버를 설치:

    - 우분투/데비안 배포판: `sudo apt install nfs-kernel-server`.
    - 엔터프라이즈 리눅스 `sudo yum install nfs-utils`배포판에서: .

7. Azure Blob 저장소의 데이터가 복사된 관리 디스크의 폴더 사용 권한을 변경합니다.  NFS 공유로 내보낼 모든 폴더에 대한 사용 권한을 변경합니다.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. 클라이언트 IP 주소에 대한 권한을 할당하여 파일을 편집하여 `/etc/exports` NFS 공유에 액세스할 수 있습니다.

    ```bash
    sudo vi /etc/exports
    ```
    
    프라이빗 클라우드의 모든 ESXi 호스트 IP에 대해 파일에 다음 줄을 입력합니다.  여러 폴더에 대한 공유를 만드는 경우 모든 폴더를 추가합니다.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. 명령을 사용하여 NFS 공유를 내보냅니다. `sudo exportfs -a`

10. 명령을 사용하여 NFS 커널 `sudo systemctl restart nfs-kernel-server` 서버를 다시 시작합니다.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Linux 가상 머신 NFS 공유를 프라이빗 클라우드 vCenter 클러스터에서 데이터스토어로 탑재한 다음 데이터를 복사합니다.

Linux 가상 머신의 NFS 공유는 프라이빗 클라우드 vCenter 클러스터에 데이터스토어로 탑재되어야 합니다. 마운트된 후 NFS 데이터스토어에서 프라이빗 클라우드 vSAN 데이터스토어로 데이터를 복사할 수 있습니다.

1. 프라이빗 클라우드 vCenter 서버에 로그인합니다.

2. **데이터 센터를**마우스 오른쪽 단추로 클릭하고 **저장소를**선택하고 **새 데이터 스토어를**선택한 다음 **다음을**선택합니다.

   ![새 데이터 스토어 추가](media/databox-migration-add-datastore.png)

3. 데이터 스토어 추가 마법사의 1단계에서 **NFS** 유형을 선택합니다.

   ![새 데이터스토어 추가 - 유형](media/databox-migration-add-datastore-type.png)

4. 마법사의 2단계에서 **NFS 3을 NFS** 버전으로 **선택한**다음 다음 을 선택합니다.

   ![새 데이터스토어 추가 - NFS 버전](media/databox-migration-add-datastore-nfs-version.png)

5. 마법사의 3단계에서 데이터스토어, 경로 및 서버의 이름을 지정합니다.  서버에 대 한 리눅스 가상 시스템의 IP 주소를 사용할 수 있습니다.  폴더 경로는 형식이 `/<folder>/<subfolder>/` 됩니다.

   ![새 데이터스토어 추가 - NFS 구성](media/databox-migration-add-datastore-nfs-configuration.png)

6. 마법사의 4단계에서 데이터스토어를 탑재할 ESXi 호스트를 선택한 다음 **다음을**선택합니다.  클러스터에서 모든 호스트를 선택하여 가상 시스템의 마이그레이션을 보장합니다.

   ![새 데이터스토어 추가 - 호스트 선택](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 마법사의 5단계에서 요약을 검토한 다음 **완료를**선택합니다.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>NFS 데이터 스토어의 가상 컴퓨터 및 가상 컴퓨터 템플릿을 인벤토리에 추가

1. 프라이빗 클라우드 vCenter 웹 UI에서 **저장소로**이동합니다.  Linux 가상 머신 NFS 데이터스토어를 선택한 다음 **파일을**선택합니다.

    ![NFS 데이터스토어에서 파일 선택](media/databox-migration-datastore-select-files.png)

2. 가상 컴퓨터 또는 가상 컴퓨터 템플릿이 포함된 폴더를 선택합니다.  세부 정보 창에서 가상 컴퓨터의 .vmx 파일 또는 가상 컴퓨터 템플릿의 .vmtx 파일을 선택합니다.

3. **VM 등록을** 선택하여 프라이빗 클라우드 vCenter에 가상 컴퓨터를 등록합니다.

    ![가상 시스템 등록](media/databox-migration-datastore-register-vm.png)

4. 가상 컴퓨터를 등록할 데이터 센터, 폴더 및 클러스터/리소스 풀을 선택합니다.

4. 모든 가상 컴퓨터 및 가상 컴퓨터 템플릿에 대해 3단계와 4단계를 반복합니다.

5. ISO 파일이 포함된 폴더로 이동합니다.  ISO 파일을 선택한 다음 **복사를 선택하여** vSAN 데이터스토어의 폴더에 파일을 복사합니다.

이제 프라이빗 클라우드 vCenter에서 가상 컴퓨터 및 가상 컴퓨터 템플릿을 사용할 수 있습니다. 이러한 가상 컴퓨터를 설정하려면 NFS 데이터스토어에서 vSAN 데이터스토어로 이동해야 합니다. **저장소 vMotion** 옵션을 사용하고 vSAN 데이터 스토어를 가상 시스템의 대상으로 선택할 수 있습니다.

가상 컴퓨터 템플릿은 Linux 가상 컴퓨터 NFS 데이터스토어에서 vSAN 데이터스토어로 복제되어야 합니다.

### <a name="clean-up-your-linux-virtual-machine"></a>리눅스 가상 머신 정리

모든 데이터가 프라이빗 클라우드로 복사되면 프라이빗 클라우드에서 NFS 데이터스토어를 제거할 수 있습니다.

1. 모든 가상 컴퓨터와 템플릿이 vSAN 데이터 스토어로 이동하고 복제되었는지 확인합니다.

2. NFS 데이터스토어에서 모든 가상 컴퓨터 템플릿을 인벤토리에서 제거합니다.

3. 프라이빗 클라우드 vCenter에서 Linux 가상 머신 데이터스토어마운트 해제.

4. Azure에서 가상 시스템 및 관리 디스크를 삭제합니다.

5. 데이터 박스에서 전송한 데이터를 저장소 계정에 보관하지 않으려면 Azure 저장소 계정을 삭제합니다.  
    


## <a name="next-steps"></a>다음 단계

* [데이터 상자에](../databox/data-box-overview.md)대해 자세히 알아보십시오.
* [워크로드를 프라이빗 클라우드로 마이그레이션하기](migrate-workloads.md)위한 다양한 옵션에 대해 자세히 알아보십시오.
