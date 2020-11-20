---
title: Linux 용 Azure에서 SAP BusinessObjects BI 플랫폼 배포 | Microsoft Docs
description: Linux 용 Azure에서 SAP BusinessObjects BI 플랫폼 배포 및 구성
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 17b978d3f4faebd3870868bceeea4572288ecb07
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965360"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Azure의 linux용 SAP BusinessObjects BI 플랫폼 배포 가이드

이 문서에서는 Linux 용 Azure에서 SAP BOBI 플랫폼을 배포 하는 전략을 설명 합니다. 이 예제에서는 프리미엄 SSD Managed Disks 설치 디렉터리로 구성 된 두 개의 가상 컴퓨터가 구성 됩니다. Azure Database for MySQL는 CMS 데이터베이스에 사용 되 고, 파일 리포지토리 서버에 대 한 Azure NetApp Files는 두 서버에서 공유 됩니다. 기본 Tomcat Java 웹 응용 프로그램 및 BI 플랫폼 응용 프로그램은 두 가상 컴퓨터에 함께 설치 됩니다. 사용자 요청의 부하를 분산 하기 위해 기본 TLS/SSL 오프 로딩 기능을 포함 하는 Application Gateway 사용 됩니다.

이러한 유형의 아키텍처는 소규모 배포 또는 비프로덕션 환경에 적용 됩니다. 프로덕션 또는 대규모 배포의 경우 웹 응용 프로그램에 대해 별도의 호스트를 사용할 수 있으며 서버가 추가 정보를 처리 하도록 허용 하는 여러 BOBI 응용 프로그램을 호스팅할 수 있습니다.

![Linux 용 Azure에서 SAP BOBI 배포](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

이 예제에서는 아래 제품 버전 및 파일 시스템 레이아웃을 사용 합니다.

- SAP BusinessObjects Platform 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (버전: 8.0.15)
- MySQL C API 커넥터-Libmysqlclient (버전: 6.1.11)

| 파일 시스템        | Description                                                                                                               | 크기(GB)             | 소유자  | 그룹  | 스토리지                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | SAP BOBI 인스턴스, 기본 Tomcat 웹 응용 프로그램 및 데이터베이스 드라이버 (필요한 경우)를 설치 하기 위한 파일 시스템 | SAP 크기 조정 지침 | bl1adm | sapsys | 관리 되는 프리미엄 디스크-SSD |
| /usr/sap/frsinput  | 탑재 디렉터리는 입력 파일 리포지토리 디렉터리로 사용 되는 모든 BOBI 호스트에서 공유 파일에 대 한 것입니다.  | 비즈니스 요구 사항         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | 탑재 디렉터리는 출력 파일 리포지토리 디렉터리로 사용할 모든 BOBI 호스트에서 공유 파일에 대 한 것입니다. | 비즈니스 요구 사항         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Azure Portal를 통해 linux 가상 컴퓨터 배포

이 섹션에서는 SAP BOBI 플랫폼용 Linux 운영 체제 (OS) 이미지를 사용 하 여 두 개의 Vm (가상 머신)을 만듭니다. Virtual Machines를 만드는 개략적인 단계는 다음과 같습니다.

1. [리소스 그룹](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) 만들기

2. [Virtual Network](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)를 만듭니다.

   - SAP BI 플랫폼 배포의 모든 Azure 서비스에 대해 단일 서브넷을 사용 하지 마세요. SAP BI 플랫폼 아키텍처에 따라 여러 서브넷을 만들어야 합니다. 이 배포에서는 응용 프로그램 서브넷, 파일 리포지토리 저장소 서브넷 및 Application Gateway 서브넷의 3 개 서브넷을 만듭니다.
   - Azure에서 Application Gateway와 Azure NetApp Files는 항상 별도의 서브넷에 있어야 합니다. 자세한 내용은 [Azure NetApp Files 네트워크 계획 문서에 대 한](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) [Azure 애플리케이션 게이트웨이](../../../application-gateway/configuration-overview.md) 및 지침을 확인 하세요.

3. 가용성 집합을 만듭니다.

   - 다중 인스턴스 배포의 각 계층에 대해 중복성을 얻으려면 가용성 집합의 각 계층에 대 한 가상 컴퓨터를 배치 합니다. 아키텍처에 따라 각 계층에 대 한 가용성 집합을 분리 해야 합니다.

4. 가상 컴퓨터 1 **(azusbosl1)을 만듭니다.**

   - 사용자 지정 이미지를 사용 하거나 Azure Marketplace에서 이미지를 선택할 수 있습니다. [Sap 용 Azure Marketplace에서 Vm 배포](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) 또는 필요에 따라 [sap 용 사용자 지정 이미지를 사용 하 여 vm 배포](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) 를 참조 하세요.

5. 가상 컴퓨터 2 **(azusbosl2)를 만듭니다.**
6. 프리미엄 SSD 디스크 하나를 추가 합니다. SAP BOBI 설치 디렉터리로 사용 됩니다.

## <a name="provision-azure-netapp-files"></a>프로 비전 Azure NetApp Files

Azure NetApp Files에 대 한 설치를 계속 하기 전에 Azure [Netapp 파일 설명서](../../../azure-netapp-files/azure-netapp-files-introduction.md)를 숙지 하세요.

Azure NetApp Files는 여러 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)에서 사용할 수 있습니다. 선택한 Azure 지역에서 Azure NetApp Files를 제공 하는지 확인 합니다.

[Azure 지역별 Azure NetApp Files 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) 페이지를 사용 하 여 지역별 Azure NetApp Files의 가용성을 확인할 수 있습니다.

Azure NetApp Files를 배포 하기 전에 [Azure NetApp Files 지침 등록](../../../azure-netapp-files/azure-netapp-files-register.md) 으로 이동 하 여 Azure NetApp Files 등록을 요청 합니다.

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files 리소스 배포

다음 지침에서는 이미 [Azure virtual network](../../../virtual-network/virtual-networks-overview.md)를 배포 했다고 가정 합니다. Azure NetApp Files 리소스가 탑재 되는 Azure NetApp Files 리소스 및 Vm은 동일한 Azure virtual network 또는 피어 링 Azure virtual networks에 배포 되어야 합니다.

1. 리소스를 아직 배포 하지 않은 경우 [Azure NetApp Files에 대 한 온 보 딩](../../../azure-netapp-files/azure-netapp-files-register.md)을 요청 합니다.

2. [Netapp 계정 만들기](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)의 지침에 따라 선택한 Azure 지역에서 netapp 계정을 만듭니다.

3. [Azure NetApp Files 용량 풀 설정](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)의 지침에 따라 Azure NetApp Files 용량 풀을 설정 합니다.

   - 이 문서에 제공 된 SAP BI 플랫폼 아키텍처는 *프리미엄* 서비스 수준에서 단일 Azure NetApp Files 용량 풀을 사용 합니다. Azure의 SAP BI 파일 리포지토리 서버에 대해 Azure NetApp Files *프리미엄* 또는 *Ultra* [service 수준을](../../../azure-netapp-files/azure-netapp-files-service-levels.md)사용 하는 것이 좋습니다.

4. [Azure NetApp Files에 서브넷 위임](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)의 지침에 설명 된 대로 Azure NetApp Files에 서브넷을 위임 합니다.

5. [Azure NetApp Files에 대 한 NFS 볼륨 만들기](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)의 지침에 따라 Azure NetApp Files 볼륨을 배포 합니다.

   SAP BOBI 플랫폼에 대해 두 프로토콜이 지원 되므로 ANF 볼륨을 NFSv3 및 NFSv 4.1으로 배포할 수 있습니다. 해당 Azure NetApp Files 서브넷에 볼륨을 배포 합니다. Azure NetApp 볼륨의 IP 주소는 자동으로 할당 됩니다.

Azure NetApp Files 리소스와 Azure Vm은 동일한 Azure virtual network 또는 피어 링 Azure virtual network에 있어야 합니다. 예를 들어 azusbobi-frsinput, azusbobi는 볼륨 이름과 nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput는 Azure NetApp Files 볼륨의 파일 경로입니다.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>중요 고려 사항

SAP BOBI Platform 파일 리포지토리 서버에 대 한 Azure NetApp Files를 만들 때 다음 사항을 고려해 야 합니다.

1. 최소 용량 풀은 4 tebibytes (TiB)입니다.
2. 최소 볼륨 크기는 100 GiB (바이트)입니다.
3. Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재 될 모든 가상 머신은 동일한 지역의 동일한 Azure 가상 네트워크 또는 [피어 링 가상 네트워크](../../../virtual-network/virtual-network-peering-overview.md) 에 있어야 합니다. 이제 동일한 지역에서 VNET 피어링을 통한 Azure NetApp Files 액세스가 지원됩니다. 전역 피어 링을 통한 Azure NetApp 액세스는 아직 지원 되지 않습니다.
4. 선택한 가상 네트워크에 Azure NetApp Files로 위임 된 서브넷이 있어야 합니다.
5. Azure NetApp Files [내보내기 정책을](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)사용 하면 허용 되는 클라이언트, 액세스 유형 (읽기-쓰기, 읽기 전용 등)을 제어할 수 있습니다.
6. Azure NetApp Files 기능은 아직 영역을 인식 하지 않습니다. 현재이 기능은 Azure 지역의 모든 가용성 영역에 배포 되지 않습니다. 일부 Azure 지역에서 잠재적 대기 시간 영향을 염두에 두어야 합니다.
7. Azure NetApp Files 볼륨은 NFSv3 또는 NFSv4.1 볼륨으로 배포할 수 있습니다. 두 프로토콜 모두 SAP BI 플랫폼 응용 프로그램에 대해 지원 됩니다.

## <a name="configure-file-systems-on-linux-servers"></a>Linux 서버에서 파일 시스템 구성

이 섹션의 다음 단계에서는 다음과 같은 접두사를 사용합니다.

**[A]**: 단계가 모든 호스트에 적용 됩니다.

### <a name="format-and-mount-sap-file-system"></a>SAP 파일 시스템 포맷 및 탑재

1. **[A]** 연결 된 모든 디스크 나열

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** /usr/sap에 대 한 블록 장치 서식 지정

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** 탑재 디렉터리 만들기

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** 블록 장치의 UUID 가져오기

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** /etc/fstab에서 파일 시스템 탑재 항목 유지 관리

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** 파일 시스템 탑재

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Azure NetApp Files 볼륨 탑재

1. **[A]** 탑재 디렉터리 만들기

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** Nfsv 4.1 탑재를 지원 하도록 클라이언트 OS 구성 **(nfsv 4.1을 사용 하는 경우에만 해당)**

   NFSv 4.1 프로토콜을 사용 하 여 Azure NetApp Files 볼륨을 사용 하는 경우 Azure NetApp Files NFSv 4.1 볼륨을 탑재 해야 하는 모든 Vm에서 다음 구성을 실행 합니다.

   **NFS 도메인 설정 확인**

   도메인이 기본 Azure NetApp Files 도메인 (  **defaultv4iddomain.com** )으로 구성 되어 있고 매핑이 **없음으로 설정** 되어 있는지 확인 합니다.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Azure NetApp Files: **defaultv4iddomain.com** 의 기본 도메인 구성과 일치 하도록 VM에서/ETC/IDMAPD.CONF의 NFS 도메인을 설정 해야 합니다. NFS 클라이언트의 도메인 구성 (예: VM)과 NFS 서버 (예: Azure NetApp 구성)가 일치 하지 않는 경우 Vm에 탑재 된 Azure NetApp 볼륨의 파일에 대 한 권한이 없음으로 표시 됩니다.

   `nfs4_disable_idmapping`를 확인합니다. **Y** 로 설정되어야 합니다. `nfs4_disable_idmapping`이 있는 디렉터리 구조를 만들려면 mount 명령을 실행합니다. 커널/드라이버용으로 액세스가 예약되어 있기 때문에 /sys/modules 아래에 디렉터리를 수동으로 만들 수 없습니다.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** 탑재 항목 추가

   NFSv3를 사용 하는 경우

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   NFSv 4.1을 사용 하는 경우

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** NFS 볼륨 탑재

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>CMS 데이터베이스 구성-Azure database for MySQL

이 섹션에서는 Azure Portal를 사용 하 여 Azure Database for MySQL를 프로 비전 하는 방법에 대해 자세히 설명 합니다. 또한 SAP BOBI 플랫폼용 CMS 및 감사 데이터베이스를 만들고 데이터베이스에 액세스 하기 위한 사용자 계정을 만드는 방법에 대 한 지침을 제공 합니다.

이 지침은 MySQL 용 Azure DB를 사용 하는 경우에만 적용 됩니다. 다른 데이터베이스의 경우 SAP 또는 데이터베이스 관련 설명서에서 지침을 참조 하세요.

### <a name="create-an-azure-database-for-mysql"></a>Azure database for MySQL 만들기

Azure Portal에 로그인 하 고이 [빠른 시작 가이드 Azure Database for MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)에 설명 된 단계를 따릅니다. 프로 비전 하는 동안 주의 사항 Azure Database for MySQL-

1. SAP BI 플랫폼 응용 프로그램 서버가 실행 되는 Azure Database for MySQL에 대해 동일한 지역을 선택 합니다.

2. SAP BOBI 버전과 관련 된 [SAP BI 용 PAM (제품 가용성 매트릭스)](https://support.sap.com/pam) 을 기반으로 하는 지원 되는 DB 버전을 선택 합니다. SAP PAM에서 MySQL AB에 대해 해결 된 것과 동일한 호환성 지침 준수

3. "Compute + storage"에서 **서버 구성** 을 선택 하 고 크기 조정 출력에 따라 적절 한 가격 책정 계층을 선택 합니다.

4. **저장소 자동 증가** 는 기본적으로 사용 하도록 설정 되어 있습니다. [저장소](../../../mysql/concepts-pricing-tiers.md#storage) 는 다운 되지 않고 확장만 가능 합니다.

5. 기본적으로 **백업 보존 기간은** 7 일 이지만 [선택적으로](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) 최대 35 일을 구성할 수 있습니다.

6. Azure Database for MySQL 백업은 기본적으로 중복 되므로 지역 중복 저장소에서 서버 백업을 수행 하려면 **백업 중복 옵션** 에서 **지역적 중복** 을 선택 합니다.

> [!NOTE]
> 서버 생성 후 [백업 중복성 옵션](../../../mysql/concepts-backup.md#backup-redundancy-options) 변경은 지원 되지 않습니다.

### <a name="configure-connection-security"></a>연결 보안 구성

기본적으로 만들어진 서버는 방화벽으로 보호 되 고 공개적으로 액세스할 수 없습니다. SAP BI 플랫폼 응용 프로그램 서버가 실행 되는 가상 네트워크에 대 한 액세스를 제공 하려면 다음 단계를 수행 합니다.  

1. Azure Portal에서 서버 리소스로 이동 하 고 서버 리소스에 대해 왼쪽 메뉴에서 **연결 보안** 을 선택 합니다.
2. **예** 를 선택 하 여 **Azure 서비스에 대 한 액세스를 허용** 합니다.
3. VNET 규칙에서 **기존 가상 네트워크 추가** 를 선택 합니다. SAP BI 플랫폼 응용 프로그램 서버의 가상 네트워크 및 서브넷을 선택 합니다. 또한 [MySQL 워크 벤치](../../../mysql/connect-workbench.md) 를 Azure Database for MySQL에 연결할 수 있는 점프 상자 또는 다른 서버에 대 한 액세스를 제공 해야 합니다. MySQL 워크 벤치는 CMS 및 감사 데이터베이스를 만드는 데 사용 됩니다.
4. 가상 네트워크가 추가 되 면 **저장** 을 선택 합니다.

### <a name="create-cms-and-audit-database"></a>CMS 및 감사 데이터베이스 만들기

1. Mysql [웹 사이트](https://dev.mysql.com/downloads/workbench/)에서 mysql 워크 벤치를 다운로드 하 고 설치 합니다. Azure Database for MySQL 액세스할 수 있는 서버에 MySQL 워크 벤치를 설치 했는지 확인 합니다.

2. MySQL 워크 벤치를 사용 하 여 서버에 연결 합니다. 이 [문서](../../../mysql/connect-workbench.md#get-connection-information)에 언급 된 지침을 따르세요. 연결 테스트에 성공 하면 다음 메시지가 표시 됩니다.

   ![SQL 워크 벤치 연결](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. SQL 쿼리 탭에서 아래 쿼리를 실행 하 여 CMS 및 감사 데이터베이스에 대 한 스키마를 만듭니다.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;

4. Create user account to connect to schema

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. MySQL 사용자 계정의 권한 및 역할을 확인 하려면

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Linux 서버에 MySQL C API 커넥터 (linux 용 Csqlclient) 설치

SAP BOBI 응용 프로그램 서버에서 데이터베이스에 액세스 하려면 데이터베이스 클라이언트/드라이버가 필요 합니다. Linux 용 MySQL C API 커넥터는 CMS 및 감사 데이터베이스에 액세스 하는 데 사용 해야 합니다. CMS 데이터베이스에 대 한 ODBC 연결이 지원 되지 않습니다. 이 섹션에서는 Linux에서 MySQL C API 커넥터를 설정 하는 방법에 대 한 지침을 제공 합니다.

1. Azure Database for MySQL와 호환 되는 드라이버를 설명 하는 [Azure Database for MySQL 문서와 호환 되는 MySQL 드라이버 및 관리 도구](../../../mysql/concepts-compatibility.md) 를 참조 하세요. 문서에서 **MySQL 커넥터/C (** 드라이버 확인) 드라이버를 확인 합니다.

2. 드라이버를 다운로드 하려면이 [링크](https://downloads.mysql.com/archives/c-c/) 를 참조 하세요.

3. 운영 체제를 선택 하 고 MySQL 커넥터용 공유 구성 요소 rpm 패키지를 다운로드 합니다. 이 예제에서는 mysql-6.1.11 커넥터 버전이 사용 됩니다.

4. 모든 SAP BOBI 응용 프로그램 인스턴스에 커넥터를 설치 합니다.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Libmysqlclient.so의 경로를 확인 합니다.

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. `/usr/lib64`설치에 사용할 사용자 계정에 대 한 디렉터리를 가리키도록 LD_LIBRARY_PATH 설정 합니다.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>서버 준비

이 섹션의 다음 단계에서는 다음과 같은 접두사를 사용합니다.

**[A]**: 단계가 모든 호스트에 적용 됩니다.

1. **[A]** Linux의 버전 (SLES 또는 RHEL)에 따라 커널 매개 변수를 설정 하 고 필요한 라이브러리를 설치 해야 합니다. [Unix 용 비즈니스 인텔리전스 플랫폼 설치 가이드](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US)의 **시스템 요구 사항** 섹션을 참조 하세요.

2. **[A]** 컴퓨터의 표준 시간대가 올바르게 설정 되었는지 확인 합니다. 설치 가이드의 [추가 Unix 및 Linux 요구 사항 섹션](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) 을 참조 하세요.

3. **[A]** 소프트웨어의 백그라운드 프로세스를 실행할 수 있는 사용자 계정 (**bl1** adm) 및 그룹 (sapsys)을 만듭니다. 이 계정을 사용 하 여 설치를 실행 하 고 소프트웨어를 실행 합니다. 계정에 루트 권한이 필요 하지 않습니다.

4. **[A]** 지원 되는 utf-8 로캘을 사용 하도록 사용자 계정 (**bl1** adm) 환경을 설정 하 고 콘솔 소프트웨어가 utf-8 문자 집합을 지원 하는지 확인 합니다. 운영 체제에서 올바른 로캘을 사용 하는지 확인 하려면 LC_ALL 및 LANG 환경 변수를 (**bl1** adm) 사용자 환경에서 기본 설정 로캘로 설정 합니다.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** 사용자 계정 구성 (**bl1** adm).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Sap Service Marketplace에서 SAP BusinessObjects BI 플랫폼용 미디어를 다운로드 하 고 압축을 풉니다.

## <a name="installation"></a>설치

서버에서 **bl1** adm 사용자 계정에 대 한 로캘 확인

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

**Bl1** adm 사용자를 사용 하 여 SAP BusinessObjects BI 플랫폼의 미디어로 이동 하 고 아래 명령을 실행 합니다.

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

해당 버전에 따라 Unix 용 [SAP BOBI 플랫폼](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) 설치 가이드를 따르세요. SAP BOBI 플랫폼을 설치 하는 동안 기억해 두어야 할 몇 가지 사항이 있습니다.

- **제품 등록 구성** 화면에서 Sap BusinessObjects 솔루션에 대 한 임시 라이선스 키를 sap Note [1288121](https://launchpad.support.sap.com/#/notes/1288121) 에서 사용 하거나 sap Service Marketplace에서 라이선스 키를 생성할 수 있습니다.

- **설치 유형 선택** 화면에서 첫 번째 서버에서 **전체** 설치 (azusbosl1)를 선택 하 고 다른 서버 (azusbosl2)에 대해 기존 Bobi 설정을 확장할 **사용자 지정/확장** 을 선택 합니다.

- **기본 또는 기존 데이터베이스 선택** 화면에서 **기존 데이터베이스 구성** 을 선택 합니다. 그러면 CMS 및 감사 데이터베이스를 선택 하 라는 메시지가 표시 됩니다. CMS 데이터베이스 유형 및 감사 데이터베이스 유형에 대해 **MySQL** 을 선택 합니다.

  설치 중에 감사를 구성 하지 않으려면 감사 데이터베이스 없음을 선택할 수도 있습니다.

- SAP BOBI 아키텍처에 따라 **Java 웹 응용 프로그램 서버 선택 화면** 에서 적절 한 옵션을 선택 합니다. 이 예제에서는 동일한 SAP BOBI 플랫폼에 tomcat 서버를 설치 하는 옵션 1을 선택 했습니다.

- **Cms 리포지토리 데이터베이스 구성** 에서 cms 데이터베이스 정보를 입력 합니다. Linux 설치를 위한 CMS 데이터베이스 정보에 대 한 예제 입력입니다. Azure Database for MySQL는 기본 포트 3306에서 사용 됩니다.
  
  ![Linux에서 SAP BOBI 배포-CMS 데이터베이스](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- 필드 감사 데이터베이스 정보를 **구성 감사 리포지토리 데이터베이스-MySQL** 을 입력 합니다. Linux 설치를 위한 감사 데이터베이스 정보에 대 한 예제 입력입니다.

  ![Linux에서 SAP BOBI 배포-감사 데이터베이스](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- 지침에 따라 설치를 완료 하는 데 필요한 입력을 입력 합니다.

다중 인스턴스 배포의 경우 두 번째 호스트 (azusbosl2)에서 설치 설치 프로그램을 실행 합니다. **설치 유형 선택** 화면에서 기존 bobi 설정을 확장 하는 **사용자 지정/확장** 을 선택 합니다.

Azure database for MySQL 제공에서 게이트웨이는 서버 인스턴스에 대 한 연결을 리디렉션하는 데 사용 됩니다. 연결이 설정되면 MySQL 클라이언트는 MySQL Server 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MySQL 버전을 표시합니다. MySQL Server 인스턴스의 버전을 확인하려면 MySQL 프롬프트에서 `SELECT VERSION();` 명령을 사용합니다. 따라서 중앙 관리 콘솔 (CMC)에서 기본적으로 게이트웨이에서 설정 된 버전에 해당 하는 다른 데이터베이스 버전을 찾을 수 있습니다. 자세한 내용은 [지원 되는 Azure Database for MySQL 서버 버전](../../../mysql/concepts-supported-versions.md) 을 확인 하세요.

![Linux에서 SAP BOBI 배포-CMC 설정](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>설치 후

### <a name="tomcat-clustering---session-replication"></a>Tomcat 클러스터링-세션 복제

Tomcat는 세션 복제 및 장애 조치 (failover)를 위해 둘 이상의 응용 프로그램 서버 클러스터링을 지원 합니다. SAP BOBI 플랫폼 세션이 serialize 되 면 응용 프로그램 서버가 실패할 경우에도 사용자 세션이 tomcat의 다른 인스턴스로 원활 하 게 장애 조치 (failover) 될 수 있습니다.

예를 들어 사용자가 SAP BI 응용 프로그램에서 폴더 계층을 탐색 하는 동안 실패 하는 웹 서버에 연결 되어 있는 경우입니다. 올바르게 구성 된 클러스터가 있으면 사용자가 로그인 페이지로 리디렉션되지 않고 폴더 계층 구조를 계속 탐색할 수 있습니다.

SAP Note [2808640](https://launchpad.support.sap.com/#/notes/2808640)에서 tomcat 클러스터링을 구성 하는 단계는 멀티 캐스트를 사용 하 여 제공 됩니다. 하지만 Azure에서는 멀티 캐스트가 지원 되지 않습니다. 따라서 Tomcat 클러스터가 Azure에서 작동 하도록 하려면 [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907))를 사용 해야 합니다. SAP 블로그의 [Tomcat 클러스터링을 사용 하 여 Sap BUSINESSOBJECTS BI Platform을 사용 하 여](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) Azure에서 Tomcat 클러스터를 설정 합니다.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI 플랫폼의 부하 분산 웹 계층

SAP BOBI 다중 인스턴스 배포에서는 Java 웹 응용 프로그램 서버 (웹 계층)가 두 개 이상의 호스트에서 실행 되 고 있습니다. 사용자 부하를 웹 서버 간에 균등 하 게 배포 하기 위해 최종 사용자와 웹 서버 간에 부하 분산 장치를 사용할 수 있습니다. Azure에서 Azure Load Balancer 또는 Azure 애플리케이션 게이트웨이를 사용 하 여 웹 응용 프로그램 서버에 대 한 트래픽을 관리할 수 있습니다. 각 제품에 대 한 세부 정보는 다음 섹션에 설명 되어 있습니다.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure 부하 분산 장치 (네트워크 기반 부하 분산 장치)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) 는 정상적인 Virtual Machines 간에 트래픽을 분산 하는 고성능, 낮은 대기 시간 계층 4 (TCP, UDP) 부하 분산 장치입니다. 부하 분산 장치 상태 프로브는 각 VM에서 지정 된 포트를 모니터링 하 고 트래픽을 운영 가상 머신으로 배포 합니다. 인터넷에서 SAP BI 플랫폼에 액세스할 수 있는지 여부에 따라 공용 부하 분산 장치 또는 내부 부하 분산 장치를 선택할 수 있습니다. 영역 중복을 사용 하 여 가용성 영역에서 고가용성을 보장 합니다.

8080 포트에서 웹 응용 프로그램 서버를 실행 하는 아래 그림에서 내부 Load Balancer 섹션을 참조 하세요. 기본 Tomcat HTTP 포트는 상태 프로브에서 모니터링 됩니다. 따라서 최종 사용자가 제공 하는 들어오는 모든 요청은 백 엔드 풀의 웹 응용 프로그램 서버 (azusbosl1 또는 azusbosl2)로 리디렉션됩니다. 부하 분산 장치는 TLS/SSL 종료 (TLS/SSL 오프 로드 라고도 함)를 지원 하지 않습니다. Azure 부하 분산 장치를 사용 하 여 웹 서버 간에 트래픽을 분산 하는 경우 표준 Load Balancer를 사용 하는 것이 좋습니다.

> [!NOTE]
> 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치되는 경우 퍼블릭 엔드포인트로 라우팅을 허용하기 위해 추가 구성을 수행하지 않는 한 아웃바운드 인터넷 연결이 없습니다. 아웃바운드 연결을 설정하는 방법에 대한 자세한 내용은 [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용하는 Virtual Machines에 대한 퍼블릭 엔드포인트 연결](high-availability-guide-standard-load-balancer-outbound-connections.md)을 참조하세요.

![웹 서버 간에 트래픽을 분산 Azure Load Balancer](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure application gateway (웹 응용 프로그램 부하 분산 장치)

[AGW (Azure 애플리케이션 Gateway)](../../../application-gateway/overview.md) 는 ADC (응용 프로그램 배달 컨트롤러)를 서비스로 제공 하 여 응용 프로그램에서 사용자 트래픽을 하나 이상의 웹 응용 프로그램 서버로 전달 하는 데 사용 됩니다. TLS/SSL 오프 로딩, WAF (웹 응용 프로그램 방화벽), 쿠키 기반 세션 선호도 및 기타 응용 프로그램에 대 한 다양 한 계층 7 부하 분산 기능을 제공 합니다.

SAP BI 플랫폼에서 application gateway는 응용 프로그램 웹 트래픽을 백 엔드 풀 azusbosl1 또는 azusbos2의 지정 된 리소스로 보냅니다. 수신기를 포트에 할당 하 고, 규칙을 만들고, 백 엔드 풀에 리소스를 추가 합니다. 아래 그림에서 개인 프런트 엔드 IP 주소 (10.31.3.20)를 사용 하는 응용 프로그램 게이트웨이는 사용자의 진입점으로 작동 하 고, 들어오는 TLS/SSL (HTTPS-TCP/443) 연결을 처리 하 고, TLS/SSL의 암호를 해독 하 고 암호화 되지 않은 요청 (HTTP-TCP/8080)을 백 엔드 풀의 서버로 전달 합니다. 기본 제공 되는 TLS/SSL 종료 기능을 사용 하면 응용 프로그램 게이트웨이에서 하나의 TLS/SSL 인증서를 유지 관리 하기만 하면 작업이 간단해 집니다.

![웹 서버 간에 트래픽을 분산 Application Gateway](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Sap BOBI 웹 서버에 대 한 Application Gateway를 구성 하려면 sap 블로그의 [Azure 애플리케이션 Gateway를 사용 하 여 SAP BOBI 웹 서버 부하 분산](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) 을 참조할 수 있습니다.

> [!NOTE]
> Azure 애플리케이션 Gateway를 사용 하 여 SSL 오프 로딩과 같은 기능을 제공 하 고, Round-Robin 서버에서 암호화 및 암호 해독 오버 헤드를 줄이고, 트래픽 분산, WAF (웹 응용 프로그램 방화벽) 기능, 고가용성 등의 기능을 제공 하기 위해 ssl 관리를 중앙 집중화 하는 것과 같은 기능을 제공 하는 웹 서버에 대 한 트래픽 부하를 분산

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI 플랫폼-백업 및 복원

백업 및 복원은 데이터 및 응용 프로그램의 정기적 복사본을 별도의 위치에 만드는 프로세스입니다. 따라서 원래 데이터 나 응용 프로그램이 손실 되거나 손상 된 경우 이전 상태로 복원 또는 복구 될 수 있습니다. 또한 모든 비즈니스 재해 복구 전략의 필수 구성 요소입니다.

SAP BOBI 플랫폼에 대 한 포괄적인 백업 및 복원 전략을 개발 하려면 시스템 가동 중지 시간 또는 응용 프로그램 중단을 야기 하는 구성 요소를 확인 합니다. SAP BOBI 플랫폼에서 다음 구성 요소의 백업은 응용 프로그램을 보호 하는 데 중요 합니다.

- SAP BOBI 설치 디렉터리 (관리 되는 프리미엄 디스크)
- 파일 리포지토리 서버 (Azure NetApp Files 또는 Azure Premium 파일)
- CMS 데이터베이스 (Azure VM의 Azure Database for MySQL 또는 데이터베이스)

다음 섹션에서는 SAP BOBI 플랫폼의 각 구성 요소에 대 한 백업 및 복원 전략을 구현 하는 방법을 설명 합니다.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>SAP BOBI 설치 디렉터리에 대 한 백업 & 복원

Azure에서 응용 프로그램 서버 및 연결 된 모든 디스크를 백업 하는 가장 간단한 방법은 [Azure Backup](../../../backup/backup-overview.md) 서비스를 사용 하는 것입니다. Vm에서 의도 하지 않은 데이터 소멸을 방지 하기 위해 독립적인 격리 된 백업을 제공 합니다. 백업은 복구 지점에 대한 기본 제공 관리를 사용하여 Recovery Services 자격 증명 모음에 저장됩니다. 구성 및 크기 조정은 간단 하 고 백업이 최적화 되며 필요할 때 쉽게 복원할 수 있습니다.

백업 프로세스의 일부로 스냅숏이 생성 되 고 데이터는 프로덕션 워크 로드에 영향을 주지 않고 Recovery Service 자격 증명 모음으로 전송 됩니다. 스냅숏은 [스냅숏 일관성](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) 문서에 설명 된 대로 서로 다른 수준의 일관성을 제공 합니다. 선택적 디스크 백업 및 복원 기능을 사용 하 여 VM에서 데이터 디스크의 하위 집합을 백업 하도록 선택할 수도 있습니다. 자세한 내용은 [AZURE Vm backup](../../../backup/backup-azure-vms-introduction.md) 문서 및 [Faq-azure vm 백업](../../../backup/backup-azure-vm-backup-faq.md)을 참조 하세요.

#### <a name="backup--restore-for-file-repository-server"></a>파일 저장소 서버에 대 한 백업 & 복원

**Azure NetApp Files** 의 경우 스냅숏 정책을 사용 하 여 주문형 스냅숏을 만들고 자동 스냅숏을 예약할 수 있습니다. 스냅숏 복사는 ANF 볼륨의 특정 시점 복사본을 제공 합니다. 자세한 내용은 [Azure NetApp Files를 사용 하 여 스냅숏 관리](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)를 참조 하세요.

**Azure Files** 백업은 vm 백업과 함께 백업 및 복원 기능을 중앙 집중화 하 고 작업 작업을 간소화 하는 기본 [Azure Backup](../../../backup/backup-overview.md) 서비스와 통합 됩니다. 자세한 내용은 [Azure 파일 공유 백업](../../../backup/azure-file-share-backup-overview.md) 및 [faq-백업 Azure Files](../../../backup/backup-azure-files-faq.md)를 참조 하세요.

#### <a name="backup--restore-for-cms-database"></a>CMS 데이터베이스에 대 한 백업 & 복원

Azure의 azure 데이터베이스 MySQL은 Azure에서 제공 하는 DBaaS 제품으로, 자동으로 서버 백업을 만들고 사용자가 구성한 로컬 중복 또는 지역 중복 저장소에 저장 합니다. Azure Database for MySQL은 데이터 파일과 트랜잭션 로그의 백업을 수행 합니다. 지원 되는 최대 저장소 크기에 따라 전체 및 차등 백업 (4-TB의 최대 저장소 서버) 또는 스냅숏 백업 (최대 16TB의 최대 저장소 서버)을 사용 합니다. 이러한 백업을 사용 하 여 구성 된 백업 보존 기간 내의 특정 시점에 서버를 복원할 수 있습니다. 기본 백업 보존 기간은 7 일 이며 선택적으로 최대 3 일을 [구성할](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) 수 있습니다. 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다.

이러한 백업 파일은 사용자에 게 노출 되지 않으며 내보낼 수 없습니다. 이러한 백업은 Azure Database for MySQL 복원 작업에만 사용할 수 있습니다. [Mysqldump](../../../mysql/concepts-migrate-dump-restore.md) 를 사용 하 여 데이터베이스를 복사할 수 있습니다. 자세한 내용은 [Azure Database for MySQL에서 백업 및 복원](../../../mysql/concepts-backup.md)을 참조 하세요.

Virtual Machines에 설치 된 데이터베이스의 경우 HANA 데이터베이스용 표준 백업 도구 또는 [Azure Backup](../../../backup/sap-hana-db-about.md) 를 사용할 수 있습니다. 또한 Azure 서비스와 도구가 요구 사항을 충족 하지 않는 경우 다른 백업 도구나 스크립트를 사용 하 여 디스크 백업을 만들 수 있습니다.

## <a name="sap-businessobjects-bi-platform-reliability"></a>SAP BusinessObjects BI 플랫폼 안정성

SAP BusinessObjects BI 플랫폼에는 특정 작업 및 작업에 최적화 된 다양 한 계층이 포함 되어 있습니다. 한 계층의 구성 요소를 사용할 수 없게 되 면 SAP BOBI 응용 프로그램에 액세스할 수 없게 되거나 응용 프로그램의 특정 기능이 작동 하지 않습니다. 따라서 각 계층이 비즈니스 중단 없이 응용 프로그램 작동을 유지 하기 위해 신뢰할 수 있도록 설계 되었는지 확인 해야 합니다.

이 섹션에서는 SAP BOBI 플랫폼에 대 한 다음 옵션을 중점적으로 다룹니다.

- **고가용성:** 서버 중 하나를 사용할 수 없게 되 면 응용 프로그램을 계속 작동 하는 데 사용할 수 있는 고가용성 플랫폼에는 Azure 지역 내에 두 개 이상의 항목이 있습니다.
- **재해 복구:** 일부 자연 재해 때문에 전체 Azure 지역에서 사용할 수 없게 되는 경우와 같은 치명적인 손실이 발생 하는 경우 응용 프로그램 기능을 복원 하는 프로세스입니다.

이 솔루션의 구현은 Azure의 시스템 설정 특성에 따라 다릅니다. 따라서 고객은 비즈니스 요구 사항에 따라 고가용성 및 재해 복구 솔루션을 조정 해야 합니다.

### <a name="high-availability"></a>고가용성

고가용성은 동일한 데이터 센터 내의 중복성, 내결함성 또는 장애 조치 (failover) 보호 구성 요소를 통해 응용 프로그램/서비스의 비즈니스 연속성을 제공 함으로써 IT 중단을 최소화할 수 있는 기술 집합을 말합니다. 이 경우 데이터 센터는 하나의 Azure 지역 내에 있습니다. [Sap 용 고가용성 아키텍처 및 시나리오](sap-high-availability-architecture-scenarios.md) 문서에서는 Sap 응용 프로그램용 Azure에서 제공 되는 다양 한 고가용성 기술 및 권장 사항에 대 한 초기 정보를 제공 하며,이 섹션의 지침을 보완 합니다.

SAP BOBI 플랫폼의 크기 조정 결과에 따라, 가로를 디자인 하 고 Azure Virtual Machines 및 서브넷에 걸쳐 BI 구성 요소의 배포를 결정 해야 합니다. 분산 아키텍처의 중복성 수준은 비즈니스에 필요한 RTO (복구 시간 목표) 및 RPO (복구 지점 목표)에 따라 달라 집니다. SAP BOBI 플랫폼에는 여러 계층이 포함 되며 각 계층의 구성 요소는 중복성을 얻기 위해 설계 되어야 합니다. 따라서 한 구성 요소에 오류가 발생 하는 경우 SAP BOBI 응용 프로그램에 대 한 중단이 거의 없습니다. 예를 들면 다음과 같습니다.

- BI 응용 프로그램 서버 및 웹 서버와 같은 중복 응용 프로그램 서버
- CMS 데이터베이스, 파일 리포지토리 서버, Load Balancer 등의 고유한 구성 요소

다음 섹션에서는 SAP BOBI 플랫폼의 각 구성 요소에 대 한 고가용성을 구현 하는 방법을 설명 합니다.

#### <a name="high-availability-for-application-servers"></a>응용 프로그램 서버의 고가용성

BI 및 웹 응용 프로그램 서버의 경우 별도로 설치 하거나 함께 설치할지에 관계 없이는 특정 고가용성 솔루션이 필요 하지 않습니다. 중복성으로 고가용성을 달성할 수 있습니다. 즉, 다양 한 Azure Virtual Machines에서 BI 및 웹 서버의 여러 인스턴스를 구성 합니다.

하나 이상의 이벤트로 인 한 가동 중지 시간의 영향을 줄이려면 여러 가상 머신에서 실행 되는 응용 프로그램 서버에 대해 아래 고가용성 방법을 따르는 것이 좋습니다.

- 가용성 영역를 사용 하 여 데이터 센터 오류를 보호 합니다.
- 중복성을 위해 가용성 집합에서 여러 Virtual Machines를 구성 합니다.
- 가용성 집합의 Vm에 Managed Disks를 사용 합니다.
- 각 응용 프로그램 계층을 별도의 가용성 집합으로 구성 합니다.

자세한 내용은 [Linux 가상 머신의 가용성 관리](../../manage-availability.md) 를 참조 하세요.

#### <a name="high-availability-for-cms-database"></a>CMS 데이터베이스의 고가용성

CMS 데이터베이스에 대 한 Azure DBaaS (Database as a Service) 서비스를 사용 하는 경우 기본적으로 고가용성 프레임 워크가 제공 됩니다. 추가 구성 요소를 구성할 필요 없이 지역을 선택 하 고 고유한 고가용성, 중복성 및 복원 력 기능을 제공 하기만 하면 됩니다. Azure에서 지원 되는 DBaaS의 SLA에 대 한 자세한 내용은 [Azure Database for MySQL 고가용성](../../../mysql/concepts-high-availability.md) 및 고가용성을 참조 하세요 [Azure SQL Database](../../../azure-sql/database/high-availability-sla.md)

CMS 데이터베이스에 대 한 다른 DBMS 배포의 경우 [SAP 워크 로드에 대 한 dbms 배포 가이드](dbms_guide_general.md)를 참조 하 여 다양 한 dbms 배포에 대 한 정보 및 고가용성을 제공 하는 방법에 대해 설명 합니다.

#### <a name="high-availability-for-file-repository-server"></a>파일 저장소 서버에 대 한 고가용성

FRS (파일 리포지토리 서버)는 보고서, universe 및 연결과 같은 내용이 저장 된 디스크 디렉터리를 나타냅니다. 해당 시스템의 모든 응용 프로그램 서버에서 공유 됩니다. 따라서 항상 사용 가능한 지 확인 해야 합니다.

Azure에서 항상 사용 가능 하 고 내구성이 매우 뛰어난 파일 공유에 대해 [Azure Premium Files](../../../storage/files/storage-files-introduction.md) 또는 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) 를 선택할 수 있습니다. 자세한 내용은 Azure Files에 대 한 [중복](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy) 섹션을 참조 하세요.

> [!NOTE]
> Azure Files에 대 한 SMB 프로토콜은 일반적으로 사용할 수 있지만 Azure Files에 대 한 NFS 프로토콜 지원은 현재 미리 보기 상태입니다. 자세한 내용은 [현재 preview로 제공 되는 Azure Files에 대 한 NFS 4.1 지원](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/) 을 참조 하세요.

이 파일 공유 서비스를 일부 지역에서 사용할 수 없으므로 [지역 사이트에서 사용할 수 있는 제품](https://azure.microsoft.com/en-us/global-infrastructure/services/) 을 참조 하 여 최신 정보를 확인 하세요. 해당 지역에서 서비스를 사용할 수 없는 경우 파일 시스템을 SAP BOBI 응용 프로그램에 공유할 수 있는 NFS 서버를 만들 수 있습니다. 하지만 고가용성도 고려해 야 합니다.

#### <a name="high-availability-for-load-balancer"></a>부하 분산 장치에 대 한 고가용성

웹 서버를 통해 트래픽을 분산 하기 위해 Azure Load Balancer 또는 Azure 애플리케이션 게이트웨이를 사용할 수 있습니다. 부하 분산 장치에 대 한 중복성은 배포를 위해 선택한 SKU에 따라 달성할 수 있습니다.

- Azure Load Balancer의 경우 영역 중복으로 표준 Load Balancer 프런트 엔드를 구성 하 여 중복성을 얻을 수 있습니다. 자세한 내용은 [표준 Load Balancer 및 가용성 영역](../../../load-balancer/load-balancer-standard-availability-zones.md) 를 참조 하세요.
- Application Gateway의 경우 배포 중에 선택한 계층 유형에 따라 고가용성을 달성할 수 있습니다.
  - v1 SKU는 둘 이상의 인스턴스를 배포한 경우 고가용성 시나리오를 지원 합니다. 모든 인스턴스가 동시에 실패하는 일이 없도록, Azure는 이러한 인스턴스를 업데이트 및 장애 도메인에 배포합니다. 따라서이 SKU를 사용 하면 영역 내에서 중복성을 얻을 수 있습니다.
  - v2 SKU는 새 인스턴스가 장애 도메인 및 업데이트 도메인에 걸쳐 분산 되어 있는지 자동으로 확인 합니다. 영역 중복을 선택하면 영역 장애 시의 복원력을 제공하기 위해 최신 인스턴스도 여러 가용성 영역에 분산됩니다. 자세한 내용은 자동 크기 조정 [및 영역 중복 Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md) 를 참조 하세요.

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 플랫폼용 고가용성 아키텍처 참조

아래 참조 아키텍처는 영역 내에서 Vm 중복성 및 가용성을 제공 하는 가용성 집합을 사용 하 여 SAP BOBI 플랫폼을 설정 하는 방법을 설명 합니다. 이 아키텍처는 다양 한 고가용성 솔루션을 관리 하는 복잡성을 줄여 주는 기본 제공 중복성을 제공 하는 SAP BOBI 플랫폼용 Azure 애플리케이션 게이트웨이, Azure NetApp Files 및 Azure Database for MySQL와 같은 다양 한 Azure 서비스의 사용을 보여 줍니다.

아래 그림에서 들어오는 트래픽 (HTTPS-TCP/443)은 둘 이상의 인스턴스에 배포 될 때 항상 사용할 수 있는 Azure 애플리케이션 Gateway v1 SKU를 사용 하 여 부하 분산 됩니다. 웹 서버, 관리 서버 및 처리 서버의 여러 인스턴스는 중복성을 얻기 위해 별도의 Virtual Machines에 배포 되며 각 계층은 별도의 가용성 집합에 배포 됩니다. Azure NetApp Files은 데이터 센터 내에 기본 중복성을 제공 하므로 파일 리포지토리 서버용 ANF 볼륨은 항상 사용 가능 합니다. CMS 데이터베이스는 본질적으로 고가용성이 있는 DBaaS (Azure Database for MySQL)에서 프로 비전 됩니다. 자세한 내용은 [Azure Database for MySQL 가이드에서 고가용성](../../../mysql/concepts-high-availability.md) 을 참조 하세요.

![가용성 집합을 사용 하 여 SAP BusinessObjects BI 플랫폼 중복성](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

위의 아키텍처는 Azure에서 SAP BOBI 배포를 수행할 수 있는 방법에 대 한 통찰력을 제공 합니다. 하지만 Azure에서 SAP BOBI 플랫폼에 대 한 모든 가능한 구성 옵션을 다루지 않습니다. 고객은 Load Balancer, 파일 리포지토리 서버 및 DBMS와 같은 여러 구성 요소에 대해 다른 제품/서비스를 선택 하 여 비즈니스 요구 사항에 따라 배포를 조정할 수 있습니다.

여러 Azure 지역에서 가용성 영역 제공 되며,이는 전원 원본, 냉각 및 네트워크의 독립적인 공급을 의미 합니다. 이를 통해 고객은 두 개 또는 세 개의 가용성 영역에서 응용 프로그램을 배포할 수 있습니다. AZs에서 고가용성을 실현 하려는 고객은 가용성 영역에 SAP BOBI 플랫폼을 배포 하 고 응용 프로그램의 각 구성 요소가 영역 중복 인지 확인 합니다.

### <a name="disaster-recovery"></a>재해 복구

이 섹션의 지침에서는 SAP BOBI 플랫폼에 대 한 재해 복구 보호를 제공 하는 전략에 대해 설명 합니다. 전체 SAP 재해 복구 접근 방식에 대 한 기본 리소스를 나타내는 [sap 문서에 대 한 재해 복구](../../../site-recovery/site-recovery-sap.md) 를 보완 합니다.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 플랫폼용 재해 복구 아키텍처 참조

이 참조 아키텍처는 중복 응용 프로그램 서버가 있는 SAP BOBI 플랫폼의 다중 인스턴스 배포를 실행 합니다. 재해 복구를 위해 모든 계층을 보조 지역으로 장애 조치 (failover) 해야 합니다. 각 계층은 다른 전략을 사용 하 여 재해 복구 보호를 제공 합니다.

![SAP BusinessObjects BI 플랫폼 재해 복구](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>부하 분산 장치

Load Balancer는 SAP BOBI 플랫폼의 웹 응용 프로그램 서버 간에 트래픽을 분산 하는 데 사용 됩니다. DR for Azure 애플리케이션 게이트웨이를 구현 하려면 보조 지역에서 Application Gateway의 병렬 설정을 구현 합니다.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>웹 및 BI 응용 프로그램 서버를 실행 하는 가상 머신

Azure Site Recovery 서비스를 사용 하 여 보조 지역의 웹 및 BI 응용 프로그램 서버를 실행 하는 Virtual Machines를 복제할 수 있습니다. 재해 발생 시 장애 조치 (failover)가 발생 하는 경우 복제 된 환경으로 쉽게 장애 조치 (failover) 하 고 작업을 계속할 수 있도록 보조 지역의 서버를 복제 합니다.

#### <a name="file-repository-servers"></a>파일 리포지토리 서버

- **Azure NetApp Files** 는 NFS 및 SMB 볼륨을 제공 하므로 모든 파일 기반 복사 도구를 사용 하 여 Azure 지역 간에 데이터를 복제할 수 있습니다. 다른 지역의 ANF 볼륨을 복사 하는 방법에 대 한 자세한 내용은 [faq](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region) 를 참조 하세요 Azure NetApp Files

  Azure NetApp Files 지역 간 복제를 사용할 수 있습니다 .이 복제는 현재 NetApp SnapMirror® 기술을 사용 하는 [미리 보기](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) 상태입니다. 따라서 변경 된 블록만 압축 된 효율적인 형식으로 네트워크를 통해 전송 됩니다. 이러한 소유 기술은 데이터 전송 비용을 절감 하는 지역에서 복제 하는 데 필요한 데이터의 양을 최소화 합니다. 또한 더 작은 RPO (복원 지점 목표)를 달성할 수 있도록 복제 시간도 단축 됩니다. 자세한 내용은 [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) 을 참조 하세요.

- **Azure premium 파일** 은 LRS (로컬 중복) 및 ZRS (영역 중복 저장소)만 지원 합니다. Azure Premium Files DR 전략의 경우 [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) 또는 [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/) 를 사용 하 여 다른 지역에 있는 다른 저장소 계정에 파일을 복사할 수 있습니다. 자세한 내용은 [재해 복구 및 저장소 계정 장애 조치 (failover)](../../../storage/common/storage-disaster-recovery-guidance.md) 를 참조 하세요.

#### <a name="cms-database"></a>CMS 데이터베이스

재해 발생 시 데이터베이스를 복구 하기 위한 여러 옵션을 제공 Azure Database for MySQL입니다. 비즈니스에 적합 한 옵션을 선택 합니다.

- 지역 간 읽기 복제본을 사용 하 여 비즈니스 연속성 및 재해 복구 계획을 향상 시킵니다. 원본 서버에서 최대 5 개의 복제본으로 복제할 수 있습니다. 읽기 복제본은 MySQL의 이진 로그 복제 기술을 사용 하 여 비동기적으로 업데이트 됩니다. 복제본은 일반 Azure Database for MySQL 서버와 비슷한 방식으로 관리하는 새로운 서버입니다. 복제본 읽기, 사용 가능한 지역, 제한 사항 및 장애 조치 (failover) 방법에 대 한 자세한 내용은 [복제본 읽기 개념 문서](../../../mysql/concepts-read-replicas.md)를 참조 하세요.

- 지역 중복 백업을 사용 하 여 서버를 복원 하는 Azure Database for MySQL의 지역 복원 기능을 사용 합니다. 서버를 호스팅하는 지역이 오프 라인인 경우에도 이러한 백업에 액세스할 수 있습니다. 이러한 백업에서 다른 지역으로 복원하여 서버를 다시 온라인 상태로 만들 수 있습니다.

  > [!NOTE]
  > 지역 복원은 지역 중복 백업 스토리지로 서버를 프로비전한 경우에만 가능합니다. 서버 생성 후 **백업 중복성 옵션** 변경은 지원 되지 않습니다. 자세한 내용은 [백업 중복성](../../../mysql/concepts-backup.md#backup-redundancy-options) 문서를 참조 하세요.

다음은이 예에 사용 된 각 계층의 재해 복구에 대 한 권장 사항입니다.

| SAP BOBI 플랫폼 계층   | 권장                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | 보조 지역에서 Application Gateway의 병렬 설정                                                |
| 웹 응용 프로그램 서버   | Site Recovery를 사용 하 여 복제                                                                         |
| BI 응용 프로그램 서버    | Site Recovery를 사용 하 여 복제                                                                         |
| Azure NetApp Files        | 보조 지역 **또는** Anf 교차 지역 복제 (미리 보기)에 데이터를 복제 하는 파일 기반 복사 도구 |
| Azure Database for MySQL  | 지역 **간 복제를 읽거나 지역** 중복 백업에서 백업을 복원 합니다.                             |

## <a name="next-steps"></a>다음 단계

- [다중 계층 SAP 앱 배포에 대 한 재해 복구 설정](../../../site-recovery/site-recovery-sap.md)
- [SAP용 Azure Virtual Machines 계획 및 구현](planning-guide.md)
- [SAP용 Azure Virtual Machines 배포](deployment-guide.md)
- [SAP용 Azure Virtual Machines DBMS 배포](dbms-guide.md)
