---
title: 프리미엄 파일 공유를 사용 하 여 FCI SQL Server-Azure Virtual Machines
description: 이 문서에서는 Azure virtual machines에서 프리미엄 파일 공유를 사용 하 여 SQL Server 장애 조치 (failover) 클러스터 인스턴스를 만드는 방법을 설명 합니다.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 2453b29c5efd768930f534df89d4c62320ed4770
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965353"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Azure virtual machines에서 프리미엄 파일 공유를 사용 하 여 SQL Server 장애 조치 (failover) 클러스터 인스턴스 구성

이 문서에서는 [프리미엄 파일 공유](../../../storage/files/storage-how-to-create-premium-fileshare.md)를 사용 하 여 Azure virtual machines에서 SQL Server 장애 조치 (failover) 클러스터 인스턴스 (fci)를 만드는 방법을 설명 합니다.

프리미엄 파일 공유는 Windows Server 2012 이상에서 SQL Server 2012 이상에 대 한 장애 조치 (Failover) 클러스터 인스턴스와 함께 사용할 수 있도록 완전히 지원 되는 SSD 지원 및 대기 시간이 짧은 파일 공유입니다. 프리미엄 파일 공유는 더 많은 유연성을 제공 하 여 가동 중지 시간 없이 파일 공유 크기를 조정 하 고 크기를 조정할 수 있도록 합니다.


## <a name="before-you-begin"></a>시작하기 전에

시작 하기 전에 알아 두어야 할 몇 가지 사항이 있습니다.

이러한 기술에 대 한 작업을 이해 해야 합니다.

- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 장애 조치 (Failover) 클러스터 인스턴스](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Azure IaaS VM 장애 조치 (failover) 클러스터에서는 서버당 단일 NIC (클러스터 노드)와 단일 서브넷을 권장 합니다. Azure 네트워킹에는 Azure IaaS VM 게스트 클러스터에서 추가 Nic 및 서브넷을 불필요 하 게 만드는 물리적 중복성이 있습니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크 에서만 연결할 수 있다는 경고를 표시 합니다. Azure IaaS VM 장애 조치 (failover) 클러스터에서이 경고를 무시할 수 있습니다.

또한 다음과 같은 기술에 대해 전반적으로 이해 해야 합니다.

- [Azure premium 파일 공유](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure 리소스 그룹](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> SQL Server 현재 Azure 가상 컴퓨터의 장애 조치 (failover) 클러스터 인스턴스는 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)의 [경량 관리 모드](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) 에서만 지원 됩니다. 전체 확장 모드에서 경량 모드로 변경 하려면 해당 Vm에 대 한 **Sql 가상 컴퓨터** 리소스를 삭제 한 다음 경량 모드로 sql VM 리소스 공급자에 등록 합니다. Azure Portal를 사용 하 여 **SQL 가상 컴퓨터** 리소스를 삭제 하는 경우 **올바른 가상 컴퓨터 옆의 확인란을 선택 취소**합니다. 전체 확장은 자동화 된 백업, 패치, 고급 포털 관리 등의 기능을 지원 합니다. 이러한 기능은 에이전트가 경량 관리 모드로 다시 설치 된 후 SQL Vm에 대해 작동 하지 않습니다.

프리미엄 파일 공유는 많은 워크 로드의 요구 사항을 충족 하는 IOPS 및 전체 용량을 제공 합니다. IO를 많이 사용 하는 워크 로드의 경우 관리 되는 프리미엄 디스크 또는 ultra disks에 따라 [스토리지 공간 다이렉트를 사용 하 여 장애 조치 클러스터 인스턴스를 SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md)합니다.  

사용자 환경의 IOPS 작업을 확인 하 고 프리미엄 파일 공유에서 배포 또는 마이그레이션을 시작 하기 전에 필요한 IOPS를 제공 하는지 확인 합니다. Windows 성능 모니터 디스크 카운터를 사용 하 여 데이터, 로그 및 임시 DB 파일을 SQL Server 하는 데 필요한 총 IOPS (디스크 전송 수/초) 및 처리량 (디스크 바이트/초)을 모니터링할 수 있습니다.

많은 작업에는 버스트 IO가 있으므로 사용량이 많은 기간 중에 확인 하 고 최대 IOPS 및 평균 IOPS를 모두 기록해 두는 것이 좋습니다. 프리미엄 파일 공유는 공유 크기에 따라 IOPS를 제공 합니다. 프리미엄 파일 공유는 또한 최대 1 시간 동안 기준 금액을 3 배까지 버스트 하는 데 사용할 수 있는 무료 버스트를 제공 합니다.

프리미엄 파일 공유 성능에 대 한 자세한 내용은 [파일 공유 성능 계층](https://docs.microsoft.com/azure/storage/files/storage-files-planning#file-share-performance-tiers)을 참조 하세요.

### <a name="licensing-and-pricing"></a>라이선스 및 가격 책정

Azure virtual machines에서 종 량 제 (PAYG) 또는 BYOL (사용자 라이선스) VM 이미지를 사용 하 여 SQL Server 라이선스를 부여할 수 있습니다. 선택한 이미지 유형은 요금이 부과 되는 방식에 영향을 줍니다.

종 량 제 라이선스를 사용 하는 경우 Azure virtual machines에서 SQL Server의 FCI (장애 조치 (failover) 클러스터 인스턴스)는 수동 노드를 포함 하 여 FCI의 모든 노드에 대 한 요금을 발생 시킵니다. 자세한 내용은 [SQL Server Enterprise Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)을 참조하세요.

소프트웨어 보증의 기업계약 있는 경우 각 활성 노드에 대해 하나의 무료 수동 FCI 노드를 사용할 수 있습니다. Azure에서 이러한 혜택을 활용 하려면 BYOL VM 이미지를 사용 하 고 FCI의 능동 노드와 수동 노드 모두에 동일한 라이선스를 사용 합니다. 자세한 내용은 [기업계약](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)을 참조하세요.

Azure 가상 컴퓨터에서 SQL Server에 대 한 종 량 제 및 BYOL 라이선스를 비교 하려면 [SQL vm 시작](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)을 참조 하세요.

SQL Server 라이선싱에 대한 자세한 내용은 [가격 책정](https://www.microsoft.com/sql-server/sql-server-2017-pricing)을 참조하세요.

### <a name="filestream"></a>Filestream

Filestream은 프리미엄 파일 공유를 사용 하는 장애 조치 (failover) 클러스터에 대해 지원 되지 않습니다. Filestream을 사용 하려면 [스토리지 공간 다이렉트](virtual-machines-windows-portal-sql-create-failover-cluster.md)를 사용 하 여 클러스터를 배포 합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료 하기 전에 다음이 이미 있어야 합니다.

- Microsoft Azure 구독
- Azure 가상 머신의 Windows 도메인
- Azure 가상 컴퓨터와 Active Directory 모두에서 개체를 만들 수 있는 권한이 있는 계정입니다.
- 이러한 구성 요소에 대 한 충분 한 IP 주소 공간이 있는 Azure 가상 네트워크 및 서브넷:
   - 가상 컴퓨터 2 대
   - 장애 조치 클러스터 IP 주소
   - 각 FCI에 대한 IP 주소
- 도메인 컨트롤러를 가리키는 Azure 네트워크에 구성 된 DNS입니다.
- 데이터 파일에 대 한 데이터베이스의 저장소 할당량을 기반으로 하는 [프리미엄 파일 공유](../../../storage/files/storage-how-to-create-premium-fileshare.md) 입니다.

이러한 필수 구성 요소가 준비 되 면 장애 조치 (failover) 클러스터 빌드를 시작할 수 있습니다. 첫 번째 단계는 가상 머신을 만드는 것입니다.

## <a name="step-1-create-the-virtual-machines"></a>1 단계: 가상 컴퓨터 만들기

1. 구독을 사용 하 여 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

1. [Azure 가용성 집합을 만듭니다](../tutorial-availability-sets.md).

   가용성 집합은 장애 도메인 및 업데이트 도메인에 대해 가상 머신을 그룹화합니다. 이를 통해 응용 프로그램은 네트워크 스위치 또는 서버 랙의 전원 장치와 같은 단일 실패 지점의 영향을 받지 않습니다.

   가상 컴퓨터에 대 한 리소스 그룹을 만들지 않은 경우 Azure 가용성 집합을 만들 때이를 수행 합니다. Azure Portal를 사용 하 여 가용성 집합을 만드는 경우 다음 단계를 수행 합니다.

   1. Azure Portal에서 **리소스 만들기** 를 선택 하 여 Azure Marketplace를 엽니다. **가용성 집합**을 검색합니다.
   1. **가용성 집합**을 선택 합니다.
   1. **만들기**를 선택합니다.
   1. **가용성 집합 만들기**에서 다음 값을 제공 합니다.
      - **이름**: 가용성 집합에 대한 이름입니다.
      - **구독:** 사용자의 Azure 구독입니다.
      - **리소스 그룹**: 기존 그룹을 사용 하려면 **기존 그룹 선택** 을 클릭 한 다음 목록에서 그룹을 선택 합니다. 그렇지 않은 경우 **새로 만들기** 를 선택 하 고 그룹의 이름을 입력 합니다.
      - **위치**: 가상 머신을 만들 위치를 설정합니다.
      - **장애 도메인**: 기본값 (**3**)을 사용 합니다.
      - **도메인 업데이트**: 기본값 (**5**)을 사용 합니다.
   1. **만들기** 를 선택 하 여 가용성 집합을 만듭니다.

1. 가용성 집합에 가상 머신을 만듭니다.

   Azure 가용성 집합에서 두 개의 SQL Server 가상 머신을 프로비전합니다. 지침은 [Azure Portal에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.

   두 가상 머신을 다음에 배치합니다.

   - 가용성 집합과 동일한 Azure 리소스 그룹에 있습니다.
   - 도메인 컨트롤러와 동일한 네트워크에.
   - 결과적으로 클러스터에서 사용할 수 있는 가상 머신과 모든 FCIs에 대해 충분 한 IP 주소 공간이 있는 서브넷
   - Azure 가용성 집합에.

      >[!IMPORTANT]
      >가상 컴퓨터를 만든 후에는 가용성 집합을 설정 하거나 변경할 수 없습니다.

   Azure Marketplace에서 이미지를 선택 합니다. Windows Server 및 SQL Server를 포함 하는 Azure Marketplace 이미지를 사용 하거나 Windows Server만 포함 하는 이미지를 사용할 수 있습니다. 자세한 내용은 [Azure virtual machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조 하세요.

   Azure 갤러리의 공식 SQL Server 이미지에는 설치 된 SQL Server 인스턴스, SQL Server 설치 소프트웨어 및 필요한 키가 포함 되어 있습니다.

   >[!IMPORTANT]
   > 가상 머신을 만든 후에 사전 설치된 독립 실행형 SQL Server 인스턴스를 제거합니다. 장애 조치 (failover) 클러스터 및 프리미엄 파일 공유를 저장소로 설정한 후 미리 설치 된 SQL Server 미디어를 사용 하 여 SQL Server FCI를 만듭니다.

   또는 운영 체제만 포함 하는 Azure Marketplace 이미지를 사용할 수 있습니다. 장애 조치 (failover) 클러스터 및 프리미엄 파일 공유를 저장소로 설정한 후 **Windows Server 2016 Datacenter** 이미지를 선택 하 고 SQL Server fci를 설치 합니다. 이 이미지는 SQL Server 설치 미디어를 포함 하지 않습니다. SQL Server 설치 미디어를 각 서버에 대해 실행할 수 있는 위치에 배치 합니다.

1. Azure가 가상 컴퓨터를 만든 후 RDP를 사용 하 여 각 가상 컴퓨터에 연결 합니다.

   RDP를 사용 하 여 가상 컴퓨터에 처음 연결 하는 경우 네트워크에서 PC를 검색할 수 있도록 허용할지 묻는 메시지가 표시 됩니다. **예**를 선택합니다.

1. SQL Server 기반 가상 머신 이미지 중 하나를 사용 하는 경우 SQL Server 인스턴스를 제거 합니다.

   1. **프로그램 및 기능**에서 **Microsoft SQL Server 201_ (64 비트)** 을 마우스 오른쪽 단추로 클릭 하 고 **제거/변경**을 선택 합니다.
   1. **제거**를 선택합니다.
   1. 기본 인스턴스를 선택합니다.
   1. **데이터베이스 엔진 서비스**에서 모든 기능을 제거합니다. **공유 기능**을 제거 하지 마십시오. 다음 스크린샷과 같은 내용이 표시 됩니다.

        ![기능 선택](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. **다음**을 선택 하 고 **제거**를 선택 합니다.

1. <a name="ports"></a>방화벽 포트를 엽니다.

   각 가상 컴퓨터의 Windows 방화벽에서 이러한 포트를 엽니다.

   | 용도 | TCP 포트 | 메모
   | ------ | ------ | ------
   | SQL Server | 1433 | SQL Server의 기본 인스턴스에 대한 표준 포트입니다. 갤러리에서 이미지를 사용한 경우 이 포트는 자동으로 열립니다.
   | 상태 프로브 | 59999 | 모든 공개 TCP 포트입니다. 이후 단계에서 이 포트를 사용하려면 부하 분산 장치 [상태 프로브](#probe) 및 클러스터를 구성합니다.
   | 파일 공유 | 445 | 파일 공유 서비스에 사용 되는 포트입니다.

1. [기존 도메인에 가상 머신을 추가합니다](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

가상 머신을 만들고 구성한 후에는 프리미엄 파일 공유를 구성할 수 있습니다.

## <a name="step-2-mount-the-premium-file-share"></a>2 단계: 프리미엄 파일 공유 탑재

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 여 저장소 계정으로 이동 합니다.
1. **파일 서비스** 에서 **파일 공유** 로 이동 하 고 SQL 저장소에 사용할 프리미엄 파일 공유를 선택 합니다.
1. **연결** 을 선택 하 여 파일 공유에 대 한 연결 문자열을 가져옵니다.
1. 드롭다운 목록에서 사용 하려는 드라이브 문자를 선택 하 고 두 코드 블록을 메모장에 복사 합니다.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="파일 공유 연결 포털에서 두 PowerShell 명령 모두 복사":::

1. RDP를 사용 하 여 SQL Server FCI가 서비스 계정에 사용할 계정을 사용 하 여 SQL Server VM에 연결 합니다.
1. 관리 PowerShell 명령 콘솔을 엽니다.
1. 포털에서 작업 하는 동안 이전에 저장 한 명령을 실행 합니다.
1. 파일 탐색기 또는 **실행** 대화 상자 (Windows 로고 키 + r)를 사용 하 여 공유로 이동 합니다. 네트워크 경로 `\\storageaccountname.file.core.windows.net\filesharename`를 사용 합니다. 예를 들어 `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. 새로 연결 된 파일 공유에 SQL 데이터 파일을 저장할 폴더를 하나 이상 만듭니다.
1. 클러스터에 참여 하는 각 SQL Server VM에 대해 이러한 단계를 반복 합니다.

  > [!IMPORTANT]
  > 백업 파일에 대 한 별도의 파일 공유를 사용 하 여 데이터 및 로그 파일에 대 한이 공유의 IOPS 및 공간 용량을 저장 하는 것이 좋습니다. 백업 파일에 프리미엄 또는 표준 파일 공유 중 하나를 사용할 수 있습니다.

## <a name="step-3-configure-the-failover-cluster-with-the-file-share"></a>3 단계: 파일 공유를 사용 하 여 장애 조치 (failover) 클러스터 구성

다음 단계는 장애 조치 (failover) 클러스터를 구성 하는 것입니다. 이 단계에서는 다음 하위 단계를 완료 합니다.

1. Windows Server 장애 조치 (Failover) 클러스터링 기능을 추가 합니다.
1. 클러스터의 유효성을 검사 합니다.
1. 장애 조치 (failover) 클러스터를 만듭니다.
1. 클라우드 감시를 만듭니다.


### <a name="add-windows-server-failover-clustering"></a>Windows Server 장애 조치 (Failover) 클러스터링 추가

1. 로컬 관리자의 구성원이 고 Active Directory에서 개체를 만들 수 있는 권한이 있는 도메인 계정을 사용 하 여 RDP를 사용 하 여 첫 번째 가상 컴퓨터에 연결 합니다. 구성의 나머지 부분에서는 이 계정을 사용합니다.

1. [장애 조치 (Failover) 클러스터링을 각 가상 컴퓨터에 추가](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)합니다.

   UI에서 장애 조치 (Failover) 클러스터링을 설치 하려면 두 가상 컴퓨터에서 다음 단계를 수행 합니다.
   1. **서버 관리자**에서 **관리**를 선택 하 고 **역할 및 기능 추가**를 선택 합니다.
   1. **역할 및 기능 추가 마법사**에서 **다음** 을 선택 하 여 **기능을 선택**합니다.
   1. **기능 선택**에서 **장애 조치 (Failover) 클러스터링**을 선택 합니다. 필요한 모든 기능 및 관리 도구를 포함합니다. **기능 추가**를 선택 합니다.
   1. **다음**을 선택 하 고 **마침** 을 선택 하 여 기능을 설치 합니다.

   PowerShell을 사용 하 여 장애 조치 (Failover) 클러스터링을 설치 하려면 가상 머신 중 하나의 관리자 PowerShell 세션에서 다음 스크립트를 실행 합니다.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>클러스터 유효성 검사

UI에서 또는 PowerShell을 사용 하 여 클러스터의 유효성을 검사 합니다.

UI를 사용 하 여 클러스터의 유효성을 검사 하려면 가상 머신 중 하나에서 다음 단계를 수행 합니다.

1. **서버 관리자**에서 **도구**를 선택한 다음 **장애 조치(Failover) 클러스터 관리자**를 선택 합니다.
1. **장애 조치(Failover) 클러스터 관리자**에서 **작업**을 선택한 다음 **구성 유효성 검사**를 선택 합니다.
1. **다음**을 선택합니다.
1. **서버 또는 클러스터 선택**에서 두 가상 컴퓨터의 이름을 입력 합니다.
1. **테스트 옵션**에서 **선택한 테스트만 실행**을 선택 합니다. **다음**을 선택합니다.
1. 아래와 같이 **테스트 선택**에서 **저장소** 및 **스토리지 공간 다이렉트**를 제외한 모든 테스트를 선택 합니다.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="클러스터 유효성 검사 테스트 선택":::

1. **다음**을 선택합니다.
1. **확인**에서 **다음**을 선택 합니다.

**구성 유효성 검사 마법사**가 유효성 검사 테스트를 실행합니다.

PowerShell을 사용 하 여 클러스터의 유효성을 검사 하려면 가상 머신 중 하나의 관리자 PowerShell 세션에서 다음 스크립트를 실행 합니다.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

클러스터의 유효성을 검사한 후에 장애 조치 클러스터를 만듭니다.

### <a name="create-the-failover-cluster"></a>장애 조치 클러스터 만들기

장애 조치 클러스터를 만들려면 다음이 필요합니다.
- 클러스터 노드가 될 가상 컴퓨터의 이름입니다.
- 장애 조치 클러스터의 이름
- 장애 조치 클러스터의 IP 주소 클러스터 노드와 동일한 Azure 가상 네트워크 및 서브넷에서 사용 되지 않는 IP 주소를 사용할 수 있습니다.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows server 2012-Windows Server 2016

다음 PowerShell 스크립트는 windows server 2016를 통해 Windows Server 2012에 대 한 장애 조치 (failover) 클러스터를 만듭니다. Azure 가상 네트워크에서 노드 이름 (가상 머신 이름) 및 사용 가능한 IP 주소를 사용 하 여 스크립트를 업데이트 합니다.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

다음 PowerShell 스크립트는 Windows Server 2019에 대 한 장애 조치 (failover) 클러스터를 만듭니다. 자세한 내용은 [장애 조치 (Failover) 클러스터: 클러스터 네트워크 개체](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)를 참조 하세요. Azure 가상 네트워크에서 노드 이름 (가상 머신 이름) 및 사용 가능한 IP 주소를 사용 하 여 스크립트를 업데이트 합니다.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>클라우드 감시 만들기

클라우드 감시는 Azure storage blob에 저장 되는 새로운 유형의 클러스터 쿼럼 감시입니다. 이렇게 하면 미러링 모니터 공유를 호스트 하는 별도의 VM이 필요 하지 않습니다.

1. [장애 조치 클러스터에 대한 클라우드 감시를 만듭니다](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Blob 컨테이너를 만듭니다.

1. 액세스 키 및 컨테이너 URL을 저장합니다.

1. 장애 조치(Failover) 클러스터 쿼럼 감시를 구성합니다. [사용자 인터페이스에서 쿼럼 감시 구성](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)을 참조 하세요.


## <a name="step-4-test-cluster-failover"></a>4 단계: 클러스터 장애 조치 테스트

클러스터의 장애 조치 (failover)를 테스트 합니다. **장애 조치(Failover) 클러스터 관리자**에서 클러스터를 마우스 오른쪽 단추로 클릭 하 고 **추가 작업** > **코어 클러스터 리소스 이동** > **노드를 선택**하 고 클러스터의 다른 노드를 선택 합니다. 코어 클러스터 리소스를 클러스터의 모든 노드로 이동한 다음 주 노드로 다시 이동 합니다. 클러스터를 각 노드로 성공적으로 이동할 수 있는 경우 SQL Server를 설치할 준비가 된 것입니다.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="핵심 리소스를 다른 노드로 이동 하 여 클러스터 장애 조치 (failover) 테스트":::

## <a name="step-5-create-the-sql-server-fci"></a>5 단계: SQL Server FCI 만들기

장애 조치 (failover) 클러스터를 구성한 후 SQL Server FCI를 만들 수 있습니다.

1. RDP를 사용 하 여 첫 번째 가상 컴퓨터에 연결 합니다.

1. **장애 조치(Failover) 클러스터 관리자**에서 모든 코어 클러스터 리소스가 첫 번째 가상 컴퓨터에 있는지 확인 합니다. 필요한 경우이 가상 머신으로 모든 리소스를 이동 합니다.

1. 설치 미디어를 찾습니다. 가상 머신이 Azure Marketplace 이미지 중 하나를 사용하는 경우 미디어는 `C:\SQLServer_<version number>_Full`에 있습니다. **Setup**을 선택합니다.

1. **SQL Server 설치 센터**에서 **설치**를 선택 합니다.

1. **새로 만들기 SQL Server 장애 조치 (failover) 클러스터 설치**를 선택 합니다. 마법사의 지침에 따라 SQL Server FCI를 설치합니다.

   FCI 데이터 디렉터리는 프리미엄 파일 공유에 있어야 합니다. 공유의 전체 경로를이 형식으로 입력 합니다 (예: `\\storageaccountname.file.core.windows.net\filesharename\foldername`). 파일 서버를 데이터 디렉터리로 지정 했음을 알려주는 경고가 표시 됩니다. 이 경고는 정상적인 것입니다. 파일 공유를 유지 하는 데 사용 된 계정이 SQL Server 서비스에서 가능한 오류를 방지 하는 데 사용 하는 계정과 동일 해야 합니다.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="SQL 데이터 디렉터리로 파일 공유 사용":::

1. 마법사의 단계를 완료 한 후 설치 프로그램은 첫 번째 노드에 SQL Server FCI를 설치 합니다.

1. 설치 프로그램에서 첫 번째 노드에 FCI를 설치 하 고 나 서 RDP를 사용 하 여 두 번째 노드에 연결 합니다.

1. **SQL Server 설치 센터**를 엽니다. **설치**를 선택 합니다.

1. **SQL Server 장애 조치 (failover) 클러스터에 노드 추가를**선택 합니다. 마법사의 지침에 따라 SQL Server를 설치 하 고 FCI에 서버를 추가 합니다.

   >[!NOTE]
   >SQL Server와 함께 Azure Marketplace 갤러리 이미지를 사용한 경우 SQL Server 도구는 이미지에 포함되었습니다. 이러한 이미지 중 하나를 사용 하지 않은 경우 SQL Server 도구를 별도로 설치 합니다. [SSMS(SQL Server Management Studio) 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)를 참조하세요.

## <a name="step-6-create-the-azure-load-balancer"></a>6 단계: Azure 부하 분산 장치 만들기

Azure 가상 머신에서 클러스터는 한 번에 하나의 클러스터 노드에 있어야 하는 IP 주소를 저장하는 부하 분산 장치를 사용합니다. 이 솔루션에서 부하 분산 장치는 SQL Server FCI에 대한 IP 주소를 저장합니다.

자세한 내용은 [Azure 부하 분산 장치 만들기 및 구성](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)을 참조 하세요.

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Azure Portal에서 부하 분산 장치 만들기

부하 분산 장치를 만들려면:

1. Azure Portal에서 가상 컴퓨터를 포함 하는 리소스 그룹으로 이동 합니다.

1. **추가**를 선택합니다. **Load Balancer**에 대 한 Azure Marketplace를 검색 합니다. **Load Balancer**를 선택 합니다.

1. **만들기**를 선택합니다.

1. 다음 값을 사용 하 여 부하 분산 장치를 설정 합니다.

   - **구독:** 사용자의 Azure 구독입니다.
   - **리소스 그룹**: 가상 컴퓨터를 포함 하는 리소스 그룹입니다.
   - **이름**: 부하 분산 장치를 식별하는 이름입니다.
   - **지역**: 가상 머신을 포함 하는 Azure 위치입니다.
   - **Type**: public 또는 private입니다. 전용 부하 분산 장치는 가상 네트워크 내에서 액세스할 수 있습니다. 대부분의 Azure 애플리케이션은 프라이빗 부하 분산 장치를 사용할 수 있습니다. 응용 프로그램에서 인터넷을 통해 직접 SQL Server에 액세스 해야 하는 경우 공용 부하 분산 장치를 사용 합니다.
   - **SKU**: 표준.
   - **가상 네트워크**: 가상 머신과 동일한 네트워크입니다.
   - **IP 주소 할당**: 정적. 
   - **개인 IP 주소**: SQL Server fci 클러스터 네트워크 리소스에 할당 한 IP 주소입니다.

   다음 그림에서는 **부하 분산 장치 UI 만들기** 를 보여 줍니다.

   ![부하 분산 장치 설정](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>부하 분산 장치 백 엔드 풀 구성

1. 가상 머신이 포함 된 Azure 리소스 그룹으로 돌아가서 새 부하 분산 장치를 찾습니다. 리소스 그룹에 대 한 보기를 새로 고쳐야 할 수도 있습니다. 부하 분산 장치를 선택합니다.

1. **백 엔드 풀**을 선택 하 고 **추가**를 선택 합니다.

1. VM이 포함된 가용성 집합과 백 엔드 풀을 연결합니다.

1. **대상 네트워크 IP 구성**에서 **가상 컴퓨터** 를 선택 하 고 클러스터 노드로 참여할 가상 컴퓨터를 선택 합니다. FCI를 호스팅하는 모든 가상 머신을 포함해야 합니다.

1. **확인** 을 선택 하 여 백 엔드 풀을 만듭니다.

### <a name="configure-a-load-balancer-health-probe"></a>부하 분산 장치 상태 프로브 구성

1. 부하 분산 장치 블레이드에서 **상태 프로브**를 선택 합니다.

1. **추가**를 선택합니다.

1. **상태 프로브 추가** 블레이드에서 다음 상태 프로브 <a name="probe"> </a>매개 변수를 설정 합니다.

   - **이름**: 상태 프로브의 이름입니다.
   - **프로토콜**: TCP입니다.
   - **포트**: [이 단계](#ports)에서는 방화벽에서 상태 프로브에 대해 만든 포트입니다. 이 문서에서는 `59999`TCP 포트를 사용 합니다.
   - **간격**: 5초입니다.
   - **비정상 임계값**: 두 번 연속 실패입니다.

1. **확인**을 선택합니다.

### <a name="set-load-balancing-rules"></a>부하 분산 규칙 설정

1. 부하 분산 장치 블레이드에서 **부하 분산 규칙**을 선택 합니다.

1. **추가**를 선택합니다.

1. 부하 분산 규칙 매개 변수를 설정 합니다.

   - **이름**: 부하 분산 규칙의 이름입니다.
   - **프런트 엔드 ip 주소**: SQL Server fci 클러스터 네트워크 리소스에 대 한 ip 주소입니다.
   - **포트**: SQL Server FCI TCP 포트입니다. 기본 인스턴스 포트는 1433입니다.
   - **백 엔드 포트**: **부동 IP (direct server return)** 를 사용 하는 경우 **포트** 값과 동일한 포트를 사용 합니다.
   - **백 엔드 풀**: 이전에 구성한 백 엔드 풀 이름입니다.
   - **상태 프로브**: 이전에 구성한 상태 프로브입니다.
   - **세션 지속성**: 없음.
   - **유휴 제한 시간(분)** : 4.
   - **부동 IP (direct server return)** : 사용

1. **확인**을 선택합니다.

## <a name="step-7-configure-the-cluster-for-the-probe"></a>7 단계: 프로브에 대 한 클러스터 구성

PowerShell에서 클러스터 프로브 포트 매개 변수를 설정합니다.

클러스터 프로브 포트 매개 변수를 설정 하려면 다음 스크립트의 변수를 사용자 환경의 값으로 업데이트 합니다. 스크립트에서 꺾쇠 괄호 (`<` 및 `>`)를 제거 합니다.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

다음 목록에서는 업데이트 해야 하는 값에 대해 설명 합니다.

   - `<Cluster Network Name>`: 네트워크에 대 한 Windows Server 장애 조치 (Failover) 클러스터 이름입니다. **장애 조치(Failover) 클러스터 관리자** > 네트워크에서 네트워크를 마우스 오른쪽 단추로 클릭 하 고 **속성** **을 선택**합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다.

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server FCI IP 주소 리소스 이름입니다. **장애 조치(Failover) 클러스터 관리자** > **역할**의 SQL Server Fci 역할 아래에서 **서버 이름**아래에 있는 IP 주소 리소스를 마우스 오른쪽 단추로 클릭 하 고 **속성**을 선택 합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다.

   - `<ILBIP>`: ILB IP 주소입니다. 이 주소는 Azure Portal에서 ILB 프런트 엔드 주소로 구성됩니다. 또한 SQL Server FCI IP 주소입니다. 이 주소는 `<SQL Server FCI IP Address Resource Name>`이 있는 동일한 속성 페이지의 **장애 조치(Failover) 클러스터 관리자**에서 확인할 수 있습니다.  

   - `<nnnnn>`: 부하 분산 장치 상태 프로브에서 구성한 프로브 포트입니다. 사용하지 않는 모든 TCP 포트는 유효합니다.

>[!IMPORTANT]
>클러스터 매개 변수에 대한 서브넷 마스크는 TCP IP 브로드캐스트 주소여야 합니다(`255.255.255.255`).

클러스터 프로브를 설정한 후 PowerShell에서 모든 클러스터 매개 변수를 볼 수 있습니다. 다음 스크립트를 실행합니다.

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>8 단계: FCI 장애 조치 (failover) 테스트

FCI의 장애 조치(failover)를 테스트하여 클러스터 기능의 유효성을 검사합니다. 다음과 같은 단계를 수행합니다.

1. RDP를 사용 하 여 SQL Server FCI 클러스터 노드 중 하나에 연결 합니다.

1. **장애 조치(Failover) 클러스터 관리자**를 엽니다. **역할**을 선택합니다. SQL Server FCI 역할을 소유하는 노드를 살펴봅니다.

1. SQL Server FCI 역할을 마우스 오른쪽 단추로 클릭합니다.

1. **이동**을 선택 하 고 **가장 적합 한 노드**를 선택 합니다.

**장애 조치(Failover) 클러스터 관리자** 역할이 표시 되 고 해당 리소스가 오프 라인으로 전환 됩니다. 그러면 리소스가 이동 하 여 다른 노드에서 온라인 상태로 돌아갑니다.

### <a name="test-connectivity"></a>연결 테스트

연결을 테스트 하려면 동일한 가상 네트워크의 다른 가상 컴퓨터에 로그인 합니다. **SQL Server Management Studio**를 열고 SQL Server FCI 이름에 연결합니다.

>[!NOTE]
>필요한 경우 [SQL Server Management Studio를 다운로드할](https://msdn.microsoft.com/library/mt238290.aspx)수 있습니다.

## <a name="limitations"></a>제한 사항

Azure virtual machines는 CSV (클러스터 공유 볼륨)의 저장소와 [표준 부하 분산 장치](../../../load-balancer/load-balancer-standard-overview.md)를 사용 하 여 Windows Server 2019에서 MSDTC (Microsoft DTC(Distributed Transaction Coordinator))를 지원 합니다.

Azure virtual machines에서 MSDTC는 Windows Server 2016 이전 버전에서는 지원 되지 않습니다.

- 클러스터형 MSDTC 리소스는 공유 저장소를 사용 하도록 구성할 수 없습니다. Windows Server 2016에서는 MSDTC 리소스를 만들 경우 저장소를 사용할 수 있는 경우에도 사용할 수 있는 공유 저장소가 표시 되지 않습니다. 이 문제는 Windows Server 2019에서 수정되었습니다.
- 기본 부하 분산 장치는 RPC 포트를 처리 하지 않습니다.

## <a name="see-also"></a>참고 항목

- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 장애 조치 (Failover) 클러스터 인스턴스](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
