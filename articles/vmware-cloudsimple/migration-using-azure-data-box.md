---
title: Azure Data Box를 사용 하 여 CloudSimple 마이그레이션 별 Azure VMware 솔루션
description: Azure Data Box를 사용 하 여 CloudSimple로 Azure VMware 솔루션으로 대량 데이터 마이그레이션
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5f19b98fbbbad2f43227bfa2f73eab47bf7b1699
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817463"
---
# <a name="migrating-data-to-azure-vmware-solution-by-cloudsimple-using-azure-data-box"></a>Azure Data Box를 사용 하 여 CloudSimple에서 Azure VMware 솔루션으로 데이터 마이그레이션

Microsoft Azure Data Box 클라우드 솔루션을 사용하면 테라바이트 단위의 데이터를 빠르고 저렴하게 신뢰할 수 있는 방식으로 Azure로 보낼 수 있습니다. 사용자에게 전용 Data Box 스토리지 디바이스를 배송하여 안전한 데이터 전송을 더욱 빠르게 실현시켜 드립니다. 각 스토리지 디바이스는 사용 가능한 최대 스토리지 용량이 80TB이며, 지역 이동 통신 사업자를 통해 사용자의 데이터 센터에 수송됩니다. 디바이스는 운송 중에 데이터를 안전하게 보호하기 위해 견고하게 포장됩니다.

Azure Data Box를 사용 하 여 VMware 데이터를 대량으로 사설 클라우드로 마이그레이션할 수 있습니다.  온-프레미스 vSphere 환경에서 데이터는 NFS 프로토콜을 사용 하 여 Data Box에 복사 됩니다.  대량 데이터 마이그레이션에는 가상 컴퓨터, 구성 및 연결 된 데이터의 지정 시간 복사본을 Data Box 복사 하 고 Azure에 전달 하는 작업이 포함 됩니다.

이 문서에서는 다음에 대해 알아봅니다.

* Azure Data Box를 설정 합니다.
* NFS를 사용 하 여 온-프레미스 VMware 환경에서 Data Box로 데이터를 복사 합니다.
* Azure Data Box의 반환을 준비 하는 중입니다.
* CloudSimple로 Azure VMware 솔루션에 복사 하기 위한 blob 데이터를 준비 합니다.
* Azure에서 사설 클라우드로 데이터를 복사 하는 중입니다.

## <a name="scenarios"></a>시나리오

대량 데이터 마이그레이션을 위해 다음 시나리오에서 Azure Data Box를 사용 해야 합니다.

* 클라우드를 사용 하 여 온-프레미스에서 Azure VMware 솔루션으로 많은 양의 데이터를 마이그레이션하고 네트워크를 통한 동기화 차이를 기준으로 사용 합니다.
* 많은 양의 전원이 꺼진 가상 머신 (콜드 가상 머신)를 마이그레이션합니다.
* 개발 및 테스트 환경을 설정 하기 위한 가상 머신 데이터를 마이그레이션합니다.
* 많은 수의 가상 머신 템플릿, Iso, 가상 머신 디스크를 마이그레이션합니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

* Azure Portal에서 필수 조건 및 [순서 Data Box](../databox/data-box-deploy-ordered.md) 확인 합니다.  주문 프로세스 중에 BLOB storage를 허용 하는 저장소 계정을 선택 해야 합니다.  Data Box 수신 되 면 온-프레미스 네트워크에 연결 하 고 vSphere 관리 네트워크에서 연결할 수 있는 IP 주소를 사용 하 여 [Data Box를 설정](../databox/data-box-deploy-set-up.md) 합니다.

* CloudSimple의 Azure VMware 솔루션이 프로 비전 되는 Azure의 동일한 지역에 가상 네트워크 및 저장소 계정을 만듭니다.

* Express 경로를 [사용 하 여 azure 가상 네트워크를 CloudSimple에 연결](virtual-network-connection.md) 문서에 설명 된 단계를 사용 하 여 저장소 계정이 만들어진 가상 네트워크에 대 한 [azure 가상 네트워크 연결](cloudsimple-azure-network-connection.md) 을 만듭니다.

## <a name="set-up-azure-data-box-for-nfs"></a>NFS에 대 한 Azure Data Box 설정

@No__t-1Tutorial 문서에서 **장치에 연결** 섹션에 설명 된 단계를 사용 하 여 Azure Data Box 로컬 웹 UI에 연결 합니다. 케이블을 연결 하 고 Azure Data Box @ no__t-0에 연결 합니다.  NFS 클라이언트에 대 한 액세스를 허용 하도록 Data Box를 구성 합니다.

1. 로컬 웹 UI에서 **연결 및 복사** 페이지로 이동합니다. **NFS 설정** 아래에서 **NFS 클라이언트 액세스**를 클릭합니다. 

    ![NFS 클라이언트 액세스 구성 1](media/nfs-client-access.png)

2. VMware ESXi 호스트의 IP 주소를 입력 하 고 **추가**를 클릭 합니다. 이 단계를 반복 하 여 vSphere 클러스터의 모든 호스트에 대 한 액세스를 구성할 수 있습니다. **확인**을 클릭합니다.

    ![NFS 클라이언트 액세스 구성 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **복사하려는 파일에 대한 폴더는 항상 공유 아래에 만든 다음 이 폴더에 파일을 복사합니다**. 블록 Blob 및 페이지 Blob 공유 아래에 만들어진 폴더는 데이터가 Blob으로 업로드되는 컨테이너를 나타냅니다. 스토리지 계정의 *root* 폴더에 파일을 직접 복사할 수 없습니다.

블록 Blob 및 페이지 Blob 공유에서는 첫 번째 수준 엔터티가 컨테이너, 두 번째 수준 엔터티가 Blob입니다. Azure Files 공유에서 첫 번째 수준 엔터티는 공유, 두 번째 수준 엔터티는 파일입니다.

다음 표에서는 데이터가 업로드되는 Data Box 및 Azure Storage 경로 URL의 공유에 대한 UNC 경로를 보여줍니다. 최종 Azure Storage 경로 URL은 UNC 공유 경로에서 파생될 수 있습니다.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 블록 Blob | <li>공유 UNC 경로: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 페이지 Blob  | <li>공유 UNC 경로: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>공유 UNC 경로: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> VMware 데이터를 복사 하는 데 Azure 블록 Blob을 사용 합니다.

## <a name="mount-nfs-share-as-a-datastore-on-on-premises-vcenter-cluster-and-copy-data"></a>온-프레미스 vCenter 클러스터에서 NFS 공유를 데이터 저장소로 탑재 및 데이터 복사

Azure Data Box에서 NFS 공유는 데이터를 복사 하기 위해 온-프레미스 vCenter 클러스터 또는 VMware ESXi 호스트에 데이터 저장소로 탑재 되어야 합니다.  탑재 된 데이터는 NFS 데이터 저장소에 복사할 수 있습니다.

1. 온-프레미스 vCenter server에 로그인 합니다.

2. **데이터 센터**를 마우스 오른쪽 단추로 클릭 하 고 **저장소** 추가를 선택 하 고 **새 데이터 저장소** 를 클릭 한 후 **다음** 을 클릭

   ![새 데이터 저장소 추가](media/databox-migration-add-datastore.png)

3. 데이터 저장소 추가 마법사의 1 단계에서 **NFS**유형을 선택 합니다.

   ![새 데이터 저장소-형식 추가](media/databox-migration-add-datastore-type.png)

4. 2 단계에서 nfs **3** 을 nfs 버전으로 선택 하 고 **다음**을 클릭 합니다.

   ![새 데이터 저장소-NFS 버전 추가](media/databox-migration-add-datastore-nfs-version.png)

5. 3 단계에서 데이터 저장소의 이름, 경로 및 서버를 지정 합니다.  서버의 Data Box IP 주소를 사용할 수 있습니다.  폴더 경로는 `/<StorageAccountName_BlockBlob>/<ContainerName>/` 형식입니다.

   ![새 데이터 저장소-NFS 구성 추가](media/databox-migration-add-datastore-nfs-configuration.png)

6. 4 단계에서 데이터 저장소를 탑재할 ESXi 호스트를 선택 하 고 **다음**을 클릭 합니다.  클러스터에서 가상 컴퓨터의 마이그레이션을 보장 하려면 모든 호스트를 선택 합니다.

   ![새 데이터 저장소 추가-호스트 선택](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 5 단계에서 요약을 검토 하 고 **마침** 을 클릭 합니다.

## <a name="copy-data-to-data-box-nfs-datastore"></a>Data Box NFS 데이터 저장소로 데이터 복사

가상 컴퓨터를 마이그레이션하거나 새 데이터 저장소로 복제할 수 있습니다.  마이그레이션해야 하는 사용 하지 않는 가상 컴퓨터는 **저장소 vMotion**를 사용 하 여 Data Box NFS 데이터 저장소로 마이그레이션할 수 있습니다.  활성 가상 컴퓨터를 Data Box NFS 데이터 저장소에 **복제할** 수 있습니다.

* **이동할**수 있는 가상 컴퓨터의 목록을 식별 합니다.
* **복제**해야 하는 가상 컴퓨터의 목록을 식별 합니다.

### <a name="move-virtual-machine-to-data-box-datastore"></a>Data Box 데이터 저장소로 가상 컴퓨터 이동

1. 가상 컴퓨터를 마우스 오른쪽 단추로 클릭 하 Data Box 데이터 저장소로 이동 하 고 **마이그레이션**을 선택 합니다.

    ![가상 컴퓨터 마이그레이션](media/databox-migration-vm-migrate.png)

2. 마이그레이션 유형 **으로만 저장소 변경** 을 선택 하 고 **다음**을 클릭 합니다.

    ![가상 컴퓨터 마이그레이션-저장소 전용](media/databox-migration-vm-migrate-change-storage.png)

3. 대상으로 데이터 저장소 Data Box 선택 하 고 **다음**을 클릭 합니다.

    ![가상 컴퓨터 마이그레이션-데이터 저장소 선택](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. 정보를 검토 하 고 **마침**을 클릭 합니다.

5. 다른 가상 컴퓨터에 대해 1 ~ 4 단계를 반복 합니다.

> [!TIP]
> 동일한 전원 상태 (전원 켜기 또는 전원 꺼짐)에서 여러 가상 머신을 선택 하 고 대량으로 마이그레이션할 수 있습니다.

가상 컴퓨터의 저장소는 Azure Data Box에서 NFS 데이터 저장소로 마이그레이션됩니다.  모든 가상 컴퓨터를 마이그레이션한 후에는 Azure VMware 솔루션으로의 데이터 마이그레이션을 준비 하는 동안 가상 컴퓨터의 전원을 종료할 수 있습니다.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-data-box-datastore"></a>가상 컴퓨터 또는 가상 컴퓨터 템플릿을 Data Box 데이터 저장소에 복제

1. 복제 하려는 가상 컴퓨터 또는 가상 컴퓨터 템플릿을 마우스 오른쪽 단추로 클릭 합니다.  **복제**, **가상 머신으로 복제** 를 선택 합니다.

    ![가상 컴퓨터 복제](media/databox-migration-vm-clone.png)

2. 복제 된 가상 컴퓨터 또는 가상 컴퓨터 템플릿의 이름을 선택 합니다.

3. 복제 된 개체를 저장할 폴더를 선택 하 고 **다음**을 클릭 합니다.

4. 복제 된 개체를 원하는 클러스터 나 리소스 풀을 선택 하 고 **다음**을 클릭 합니다.

5. Azure Data Box NFS 데이터 저장소를 저장소 위치로 선택 하 고 **다음**을 클릭 합니다.

    ![가상 컴퓨터 복제-데이터 저장소 선택](media/databox-migration-vm-clone-select-datastore.png)

6. 복제 된 개체에 대 한 옵션을 사용자 지정 하려면 사용자 지정 옵션을 선택 하 고 **다음**을 클릭 합니다.

7. 구성을 검토 하 고 **마침**을 클릭 합니다.

8. 추가 가상 컴퓨터 또는 가상 컴퓨터 템플릿에 대해 1 ~ 7 단계를 반복 합니다.

가상 컴퓨터는 Azure Data Box에서 NFS 데이터 저장소에 복제 되어 저장 됩니다.  가상 머신을 복제 한 후에는 복제 된 가상 머신이 Azure VMware 솔루션으로 데이터를 마이그레이션하기 위한 준비를 할 때 전원이 꺼져 있는지 확인 합니다.

### <a name="copy-iso-files-to-data-box-datastore"></a>Data Box 데이터 저장소에 ISO 파일 복사

1. 온-프레미스 vCenter 웹 UI에서 **저장소**로 이동 합니다.  Data Box NFS 데이터 저장소를 선택 하 고 **파일**을 클릭 합니다.  ISO 파일을 저장할 **새 폴더** 를 만듭니다.

    ![ISO 복사-새 폴더 만들기](media/databox-migration-create-folder.png)

2. ISO 파일이 저장 될 폴더의 이름을 지정 합니다.

3. 새로 만든 폴더를 두 번 클릭 하면 내용에 액세스할 수 있습니다.

4. **파일 업로드** 를 클릭 하 고 업로드 하려는 iso를 선택 합니다.
    
    ![ISO 업로드 파일 복사](media/databox-migration-upload-iso.png)

> [!TIP]
> 온-프레미스 데이터 저장소에 ISO 파일이 이미 있는 경우 파일을 선택 하 고 Data Box NFS 데이터 저장소 **에 복사할** 수 있습니다.


## <a name="prepare-azure-data-box-for-return"></a>반환 Azure Data Box 준비

모든 가상 컴퓨터 데이터, 가상 컴퓨터 템플릿 데이터 및 ISO 파일이 Data Box NFS 데이터 저장소에 복사 되 면 데이터 저장소의 vCenter에서 데이터 저장소의 연결을 끊을 수 있습니다.  데이터 저장소의 연결을 해제 하려면 먼저 인벤토리에 있는 모든 가상 머신 및 가상 머신 템플릿을 제거 해야 합니다.

### <a name="remove-objects-from-inventory"></a>인벤토리에서 개체 제거

1. 온-프레미스 vCenter 웹 UI에서 **저장소**로 이동 합니다.  Data Box NFS 데이터 저장소를 선택 하 고 **vm**을 클릭 합니다.

2. 모든 가상 머신이 꺼져 있는지 확인 합니다.

    ![인벤토리에서 가상 컴퓨터 제거-전원 꺼짐](media/databox-migration-select-databox-vm.png)

3. 모든 가상 컴퓨터를 선택 하 고 마우스 오른쪽 단추를 클릭 한 다음 **인벤토리에서 제거**를 선택 합니다.

    ![인벤토리에서 가상 컴퓨터 제거](media/databox-migration-remove-vm-from-inventory.png)

4. 위쪽 단추에서 **폴더의 VM 템플릿** 을 선택 하 고 3 단계를 반복 합니다. 

### <a name="remove-azure-data-box-nfs-datastore-from-vcenter"></a>VCenter에서 NFS 데이터 저장소 Azure Data Box 제거

반환을 준비 하기 전에 VMware ESXi 호스트에서 NFS 데이터 저장소의 연결을 끊어야 합니다. Data Box

1. 온-프레미스 vCenter 웹 UI에서 **저장소**로 이동 합니다.

2. Data Box NFS 데이터 저장소를 마우스 오른쪽 단추로 클릭 하 고 **데이터 저장소 분리**를 선택 합니다.

    ![데이터 저장소 Data Box 분리](media/databox-migration-unmount-datastore.png)

3. 데이터 저장소가 탑재 된 모든 ESXi 호스트를 선택 하 고 **확인**을 클릭 합니다.

    ![데이터 저장소 Data Box 분리-호스트 선택](media/databox-migration-unmount-datastore-select-hosts.png)

4. 경고를 검토 하 고 수락 하 고 **확인**을 클릭 합니다.

### <a name="prepare-data-box-for-return-and-return-the-data-box"></a>반환 Data Box 준비 하 고 Data Box 반환

Azure Data Box 반환 문서에 설명 된 단계를 수행 하 [고 Azure에 데이터 업로드를 확인](../databox/data-box-deploy-picked-up.md) 하 여 Data Box을 반환 합니다.  Azure storage 계정에 대 한 데이터 복사 상태를 확인 하 고 상태가 완료 됨으로 표시 되 면 Azure storage 계정에서 데이터를 확인할 수 있습니다.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution-by-cloudsimple"></a>Azure storage에서 CloudSimple로 Azure VMware 솔루션으로 데이터 복사

Azure Data Box에 복사 된 데이터는 Data Box의 주문 상태가 완료로 표시 되 면 Azure storage 계정에서 사용할 수 있습니다.  이제 데이터를 CloudSimple을 통해 Azure VMware 솔루션에 복사할 수 있습니다.  저장소 계정의 데이터는 NFS 프로토콜을 사용 하 여 사설 클라우드의 vSAN 데이터 저장소에 복사 해야 합니다.  먼저 **AzCopy**를 사용 하 여 Azure의 Linux 가상 머신에서 blob 저장소 데이터를 관리 되는 디스크에 복사 합니다.  NFS 프로토콜을 통해 관리 디스크를 사용할 수 있도록 설정 하 고 NFS 공유를 사설 클라우드에 데이터 저장소로 탑재 하 고 데이터를 복사 합니다.  이 방법을 사용 하면 사설 클라우드로 데이터를 더 빠르게 복사할 수 있습니다. 

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-export-as-nfs-share"></a>Linux 가상 머신 및 managed disks를 사용 하 여 사설 클라우드에 데이터를 복사 하 고 NFS 공유로 내보내기

1. 저장소 계정이 만들어지고 사설 클라우드에 대 한 Azure 가상 네트워크 연결이 있는 동일한 지역의 Azure에서 [Linux 가상 머신을](../virtual-machines/linux/quick-create-portal.md) 만듭니다.

2. Blob 데이터 용량 보다 큰 관리 디스크를 만들고 [Linux 가상 머신에 연결](../virtual-machines/linux/attach-disk-portal.md)합니다.  Blob 데이터의 양이 사용 가능한 가장 큰 관리 디스크 보다 큰 경우에는 데이터를 여러 단계로 복사 하거나 여러 개의 관리 디스크를 사용 하 여 복사 해야 합니다.

3. Linux 가상 머신에 연결 하 고 관리 디스크를 탑재 합니다.

4. [Linux 가상 머신에 AzCopy를](../storage/common/storage-use-azcopy-v10.md)설치 합니다.

5. AzCopy를 사용 하 여 Azure blob storage의 데이터를 관리 되는 디스크에 다운로드 합니다.  명령 구문: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  @No__t-0을 Azure storage 계정 이름으로 바꾸고 `<container-name>`을 Azure Data Box를 사용 하 여 복사한 데이터를 포함 하는 컨테이너로 바꿉니다.

6. Linux 가상 머신에 NFS 서버를 설치 합니다.

    1. Ubuntu/Debian 배포: `sudo apt install nfs-kernel-server`입니다.
    2. 엔터프라이즈 Linux 배포에서: `sudo yum install nfs-utils`입니다.

7. Azure blob 저장소의 데이터가 복사 된 관리 디스크의 폴더에 대 한 사용 권한을 변경 합니다.  NFS 공유로 내보내려는 모든 폴더에 대 한 사용 권한을 변경 합니다.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. @No__t-0 파일을 편집 하 여 NFS 공유에 액세스할 클라이언트 IP 주소에 대 한 사용 권한을 할당 합니다.

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

9. @No__t-0 명령을 사용 하 여 NFS 공유를 내보냅니다.

10. @No__t-0 명령을 사용 하 여 NFS 커널 서버를 다시 시작 합니다.


### <a name="mount-linux-virtual-machine-nfs-share-as-a-datastore-on-private-cloud-vcenter-cluster-and-copy-data"></a>Linux 가상 머신 NFS 공유를 사설 클라우드 vCenter 클러스터의 데이터 저장소로 탑재 및 데이터 복사

Linux 가상 머신에서 NFS 공유는 사설 클라우드 vCenter 클러스터에서 데이터를 복사 하기 위한 데이터 저장소로 탑재 되어야 합니다.  탑재 되 면 NFS 데이터 저장소에서 사설 클라우드 vSAN 데이터 저장소로 데이터를 복사할 수 있습니다.

1. 사설 클라우드 vCenter 서버에 로그인 합니다.

2. **데이터 센터**를 마우스 오른쪽 단추로 클릭 하 고 **저장소** 추가를 선택 하 고 **새 데이터 저장소** 를 클릭 한 후 **다음** 을 클릭

   ![새 데이터 저장소 추가](media/databox-migration-add-datastore.png)

3. 데이터 저장소 추가 마법사의 1 단계에서 **NFS**유형을 선택 합니다.

   ![새 데이터 저장소-형식 추가](media/databox-migration-add-datastore-type.png)

4. 2 단계에서 nfs **3** 을 nfs 버전으로 선택 하 고 **다음**을 클릭 합니다.

   ![새 데이터 저장소-NFS 버전 추가](media/databox-migration-add-datastore-nfs-version.png)

5. 3 단계에서 데이터 저장소의 이름, 경로 및 서버를 지정 합니다.  서버에 대 한 Linux 가상 머신의 IP 주소를 사용할 수 있습니다.  폴더 경로는 `/<folder>/<subfolder>/` 형식입니다.

   ![새 데이터 저장소-NFS 구성 추가](media/databox-migration-add-datastore-nfs-configuration.png)

6. 4 단계에서 데이터 저장소를 탑재할 ESXi 호스트를 선택 하 고 **다음**을 클릭 합니다.  클러스터에서 가상 컴퓨터의 마이그레이션을 보장 하려면 모든 호스트를 선택 합니다.

   ![새 데이터 저장소 추가-호스트 선택](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 5 단계에서 요약을 검토 하 고 **마침** 을 클릭 합니다.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-nfs-datastore-to-the-inventory"></a>NFS 데이터 저장소의 가상 컴퓨터 및 가상 컴퓨터 템플릿을 인벤토리에 추가 합니다.

1. 사설 클라우드 vCenter 웹 UI에서 **저장소**로 이동 합니다.  Linux 가상 컴퓨터 NFS 데이터 저장소를 선택 하 고 **파일**을 클릭 합니다.

    ![NFS 데이터 저장소에서 파일 선택](media/databox-migration-datastore-select-files.png)

2. 가상 컴퓨터 또는 가상 컴퓨터 템플릿을 포함 하는 폴더를 선택 합니다.  세부 정보 창에서 가상 머신에 대 한 `.vmx` 파일 또는 가상 머신 템플릿에 대 한 `.vmtx` 파일을 선택 합니다.

3. **VM 등록** 을 클릭 하 여 사설 클라우드 vCenter에 가상 머신을 등록 합니다.

    ![가상 컴퓨터 등록](media/databox-migration-datastore-register-vm.png)

4. 가상 컴퓨터를 등록 하려는 데이터 센터, 폴더 및 클러스터/리소스 풀을 선택 합니다.

4. 모든 가상 머신 및 가상 머신 템플릿에 대해 3 단계와 4 단계를 반복 합니다.

5. ISO 파일이 포함 된 폴더로 이동 합니다.  ISO 파일을 선택 하 고 vSAN 데이터 저장소의 폴더 **에 복사** 합니다.

이제 가상 컴퓨터 및 가상 컴퓨터 템플릿을 사설 클라우드 vCenter에서 사용할 수 있습니다.  이러한 가상 컴퓨터는 전원을 켜기 전에 NFS 데이터 저장소에서 vSAN 데이터 저장소로 이동 해야 합니다.  가상 컴퓨터의 저장소 vMotion을 수행 하 고 가상 컴퓨터의 대상으로 vSAN 데이터 저장소를 선택할 수 있습니다.

가상 컴퓨터 템플릿을 Linux 가상 컴퓨터 NFS 데이터 저장소에서 vSAN 데이터 저장소로 복제 해야 합니다.

### <a name="clean-up-of-your-linux-virtual-machine"></a>Linux 가상 머신 정리

모든 데이터가 사설 클라우드에 복사 되 면 사설 클라우드에서 NFS 데이터 저장소를 제거할 수 있습니다.

1. 모든 가상 컴퓨터 및 템플릿이 이동 되 고 vSAN 데이터 저장소로 복제 되었는지 확인 합니다.

2. 인벤토리에서 NFS 데이터 저장소의 모든 가상 머신 템플릿을 제거 합니다.

3. 사설 클라우드 vCenter에서 Linux 가상 컴퓨터 데이터 저장소를 분리 합니다.

4. Azure에서 가상 머신 및 관리 디스크를 삭제 합니다.

5. 저장소 계정에서 Azure Data Box를 사용 하 여 전송 된 데이터를 유지 하지 않으려면 Azure storage 계정을 삭제 합니다.  
    


## <a name="next-steps"></a>다음 단계

* [Azure Data Box](../databox/data-box-overview.md) 에 대 한 자세한 정보
* [사설 클라우드로 워크 로드를 마이그레이션하](migrate-workloads.md) 는 다양 한 옵션에 대 한 자세한 정보
