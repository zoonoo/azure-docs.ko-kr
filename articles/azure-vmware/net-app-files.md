---
title: Azure VMware 솔루션에 대 한 NetApp 파일
description: Azure VMware Vm과 Azure NetApp Files를 사용 하 여 온-프레미스 서버, Azure VMware 솔루션 Vm 및 클라우드 인프라에서 데이터를 마이그레이션하고 동기화 합니다.
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 993a67f82d5af971a4c4a0010bd9d19e2a3113b2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573925"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>Azure VMware 솔루션에 대 한 NetApp 파일

이 문서에서는 Azure VMware 솔루션 기반 워크 로드와 Azure NetApp Files를 통합 하는 단계를 안내 합니다. [Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) 은 코드를 변경 하지 않고 클라우드에서 엔터프라이즈 파일 작업을 마이그레이션하고 실행 하기 위한 Azure 서비스입니다. 이를 통해 온-프레미스 및 클라우드 인프라에서 데이터를 쉽게 마이그레이션할 수 있습니다. 신속한 보안 강화 데이터 동기화는 즉각적인 스냅숏, 복원 및 Active Directory 통합과 같은 기능을 통해 마이그레이션 및 DevOps 시나리오를 간소화 합니다.

## <a name="topology"></a>토폴로지

이 시나리오에서 Azure NetApp Files 풀 공유를 테스트 하 고 확인 하려면 용량 풀, 볼륨 풀 및 서브넷을 사용 하 여 Azure에서 Azure NetApp Files 구성 합니다. NFS 프로토콜을 사용 했습니다. Azure NetApp Files와 Azure VMware 솔루션은 모두 동일한 Azure 지역 미국 동부에 생성 됩니다. Azure VMware 솔루션에 대 한 연결은 Azure Express 경로를 통해입니다.

![Azure VMware 솔루션 토폴로지의 NetApp 파일.](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>사전 요구 사항 

> [!div class="checklist"]
> * Azure NetApp Files를 사용 하는 Azure 구독
> * Azure NetApp 파일의 서브넷
> * Azure VMware 솔루션의 Linux VM
> * Azure VMware 솔루션의 Windows VM

## <a name="verify-configuration-of-azure-netapp-files"></a>Azure NetApp Files 구성 확인 

먼저 premium 디스크의 Azure에서 만든 Azure NetApp Files의 구성을 확인 합니다.

1. Azure Portal의 **저장소**에서 **Azure NetApp Files**를 선택 합니다. 구성 된 Azure NetApp Files 목록에 표시 됩니다.

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Azure NetApp Files 목록입니다."::: 

2. NetApp 계정을 선택 하는 경우 다양 한 설정을 볼 수 있습니다. 예를 들어 **Contoso-anf2**을 선택 하면 해당 설정이 표시 됩니다. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Azure NetApp Files 목록입니다."::: 

3. 구성 된 풀을 확인 하려면 **용량 풀** 을 선택 합니다. 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="Azure NetApp Files 목록입니다.":::

    저장소 풀이 프리미엄 디스크를 사용 하 여 4 TiB 구성 된 것을 볼 수 있습니다.

4. **볼륨** (2 단계의 스크린샷 참조)을 선택 하 여 용량 풀에서 생성 된 볼륨을 봅니다.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Azure NetApp Files 목록입니다.":::

5. 볼륨을 선택 하 여 해당 구성을 확인 합니다.  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Azure NetApp Files 목록입니다.":::

    200 GiB가 있는 anfpool 볼륨 풀이 NetApp Files 공유로 생성 된 것을 볼 수 있습니다. Azure VMware 솔루션 Vm에 탑재할 NFS 경로를 제공 하는 Azure NetApp Files에 대 한 개인 IP 가상 네트워크가 생성 되었습니다. 

## <a name="protocols-supported-by-azure-netapp-files"></a>Azure NetApp Files에서 지 원하는 프로토콜  

Azure NetApp Files은 SMB (서버 메시지 블록) 및 NFS (네트워크 파일 시스템) 공유 매핑을 지원 합니다. 

- **Smb 공유**: smb 볼륨을 배포 하려면 먼저 Active Directory 연결을 만들어야 합니다. 연결에 성공 하려면 Azure NetApp Files의 위임 된 서브넷에서 지정 된 도메인 컨트롤러에 액세스할 수 있어야 합니다. Active Directory Azure NetApp Files 계정 내에서 구성 되 면 SMB 볼륨을 만들 때 선택 가능한 항목으로 표시 됩니다. 

- **Nfs 공유**: Azure NetApp Files Nfs (NFSv3 및 nfsv 4.1), SMBv3 또는 이중 프로토콜 (NFSV3 및 SMB)을 사용 하 여 볼륨을 만드는 데 기여 합니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다. NFS는 명령줄을 사용 하 여 Linux 서버에 탑재할 수 있습니다.

## <a name="create-azure-netapp-files"></a>Azure NetApp Files 만들기 

1. [Azure Portal](https://rc.portal.azure.com/#home)에 로그인합니다. Azure 서비스에서 **Azure NetApp Files**를 선택 합니다. 

2. **+ 추가** 를 선택 하 여 새 Azure NetApp Files 볼륨을 만듭니다. 

3. 필수 필드 (이름, 구독, 리소스 그룹 및 위치)를 입력 하 고 **만들기**를 선택 합니다. 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>Azure NetApp Files에 용량 풀 추가 

1. Azure Portal에서 **Azure NetApp Files**를 선택 하 고 **용량 풀** 및 **+ 풀 추가**를 선택 합니다. 

2. 볼륨 풀 이름, 서비스 수준 및 디스크 크기 (FQDN) 또는 IP 및 가중치에 필요한 세부 정보를 입력 합니다. **추가**를 선택합니다.

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="Azure NetApp Files 목록입니다.":::

3. 용량 풀에서 볼륨을 만들려면 검색 창에서 **볼륨** 을 선택 하 고 **+ 볼륨 추가**를 선택 합니다. 
 
4. 다음 스크린샷에 표시 된 것 처럼 필수 필드를 입력 합니다.

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="Azure NetApp Files 목록입니다.":::

5. 다음 페이지에서 프로토콜 유형 공유를 선택 합니다. NFS 공유 인 경우 버전을 선택 하 고, SMB 공유 인 경우 Active Directory 도메인을 선택 합니다.  

6. NFS 공유 인 경우 프로토콜 형식 공유를 액세스할 원본 IP 주소를 추가 합니다. **검토 + 만들기**를 선택합니다. 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="Azure NetApp Files 목록입니다.":::
 
    Azure Portal의 Azure NetApp Files에서 NFS 공유를 사용할 준비가 되었습니다.

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="Azure NetApp Files 목록입니다.":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>Azure VMware 솔루션 Vm에 NFS 파일 공유 탑재

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>Azure VMware 솔루션에 NFS 파일 공유 탑재 Windows VM

- 명령 프롬프트를 열고 명령을 실행 하 여 NFS 파일 공유를 매핑합니다. 다음 스크린샷에는 Azure VMware 솔루션의 Windows VM에서 매핑된 공유 드라이브가 나와 있습니다.  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="Azure NetApp Files 목록입니다.":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="Azure NetApp Files 목록입니다.":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>Azure VMware 솔루션 Linux VM에서 NFS 파일 공유 탑재

#### <a name="setting-up-your-azure-instance"></a>Azure 인스턴스 설정

1. Azure Portal에서 Azure 인스턴스를 볼륨과 동일한 가상 네트워크에 정의 된 서브넷에 연결 합니다.

    > [!NOTE]
    > 서브넷에는 NFS 포트 (2049, 111), UDP 및 TCP에서 트래픽을 허용 하는 네트워크 보안 그룹 규칙이 필요 합니다.

2. SSH 클라이언트를 열고 Azure 인스턴스에 연결 합니다. 자세한 내용은 [Azure에서 Windows를 사용 하 여 SSH 키를 사용 하는 방법을](../virtual-machines/linux/ssh-from-windows.md)참조 하세요.

3. Azure 인스턴스에 NFS 클라이언트를 설치 합니다.
   - Red Hat Enterprise Linux 또는 SUSE Linux 인스턴스: `sudo yum install -y nfs-utils`
   - Ubuntu 또는 Debian 인스턴스에서: `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>파일 시스템 탑재

1. Azure 인스턴스에 새 디렉터리를 만듭니다. `sudo mkdir ANFPOOL`

2. 탑재 대상 IP 주소를 선택 합니다.

3. 파일 시스템 탑재: `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="Azure NetApp Files 목록입니다.":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>SMB 공유에 대 한 Active Directory 연결 만들기

1. Azure Portal에서 NetApp 계정을 선택 합니다.

2. Azure NetApp Files에서 **Active Directory 연결**을 선택 합니다.

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Azure NetApp Files 목록입니다."::: 

3. Active Directory에 SMB 공유를 조인 하려면 **조인** 을 선택 합니다. 다음 스크린샷에서는 SMB 공유의 도메인 세부 정보를 보여 줍니다.

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="Azure NetApp Files 목록입니다."::: 

    Azure SMB 파일 공유를 사용할 준비가 되었습니다.  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="Azure NetApp Files 목록입니다."::: 

4. Windows Azure VMware 솔루션 VM에 SMB 공유를 매핑합니다. 이전 스크린샷에 표시 된 것 처럼 SMB 탑재 경로를 사용 합니다. 자세한 내용은 [Windows 10에서 네트워크 드라이브 매핑](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계
- [Azure NetApp Files의 저장소 계층](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)에 대해 자세히 알아보세요.
- [Azure VMware 솔루션 vm의 수명 주기 관리를](lifecycle-management-of-azure-vmware-solution-vms.md) 참조 하세요.
- [허브 및 스포크 아키텍처에서 Azure VMware 솔루션 통합](concepts-hub-and-spoke.md) 을 참조 하세요.
