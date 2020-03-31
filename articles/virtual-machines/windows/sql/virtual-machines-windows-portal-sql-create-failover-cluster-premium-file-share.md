---
title: 프리미엄 파일 공유를 갖춘 SQL 서버 FCI - Azure 가상 시스템
description: 이 문서에서는 Azure 가상 컴퓨터에서 프리미엄 파일 공유를 사용하여 SQL Server 장애 조치 클러스터 인스턴스를 만드는 방법을 설명합니다.
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
ms.openlocfilehash: 9595ee87801fa4ce187a50197fc58d6c448eac24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303225"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Azure 가상 컴퓨터에서 프리미엄 파일 공유를 통해 SQL Server 장애 조치 클러스터 인스턴스 구성

이 문서에서는 [프리미엄 파일 공유를](../../../storage/files/storage-how-to-create-premium-fileshare.md)사용하여 Azure 가상 컴퓨터에서 SQL Server 장애 조치 클러스터 인스턴스(FCI)를 만드는 방법을 설명합니다.

프리미엄 파일 공유는 SSD 지원, 지속적으로 낮은 대기 시간 파일 공유 SQL Server 2012 또는 Windows Server 2012 이상에 대 한 장애 조치 클러스터 인스턴스와 함께 사용 하기 위해 완전히 지원 됩니다. 프리미엄 파일 공유를 통해 유연성이 향상되어 가동 중지 시간 없이 파일 공유의 크기를 조정하고 확장할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

시작하기 전에 알아야 할 몇 가지 사항이 있습니다.

다음 기술에 대한 운영상의 이해가 있어야 합니다.

- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL 서버 장애 조치 클러스터 인스턴스](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

한 가지 주의해야 할 점은 Azure IaaS VM 장애 조치 클러스터에서 서버당 단일 NIC(클러스터 노드)와 단일 서브넷을 권장한다는 것입니다. Azure 네트워킹에는 물리적 중복성이 있어 Azure IaaS VM 게스트 클러스터에서 추가 NIC 및 서브넷이 필요하지 않습니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결할 수 있음을 경고합니다. Azure IaaS VM 장애 조치 클러스터에서 이 경고를 무시할 수 있습니다.

또한 이러한 기술에 대한 일반적인 이해가 있어야 합니다.

- [Azure 프리미엄 파일 공유](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure 리소스 그룹](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 현재 Azure 가상 시스템의 SQL Server 장애 조치 클러스터 인스턴스는 [SQL Server IaaS 에이전트 확장의](virtual-machines-windows-sql-server-agent-extension.md) [간단한 관리 모드에서만](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) 지원됩니다. 전체 확장 모드에서 경량으로 변경하려면 해당 VM에 대한 **SQL 가상 시스템** 리소스를 삭제한 다음 경량 모드에서 SQL VM 리소스 공급자에 등록합니다. Azure 포털을 사용하여 **SQL 가상 시스템** 리소스를 삭제할 때 올바른 가상 컴퓨터 옆에 있는 **확인란을 선택 취소합니다.** 전체 확장은 자동화된 백업, 패치 및 고급 포털 관리와 같은 기능을 지원합니다. 에이전트를 경량 관리 모드로 다시 설치한 후에는 SQL VM에서 이러한 기능이 작동하지 않습니다.

프리미엄 파일 공유는 IOPS를 제공하고 많은 워크로드의 요구를 충족시킬 수 있는 모든 용량을 제공합니다. IO 집약적인 워크로드의 경우 관리되는 프리미엄 디스크 또는 울트라 디스크를 기반으로 [스토리지 스페이스 Direct가 있는 SQL Server 장애 조치 클러스터 인스턴스를](virtual-machines-windows-portal-sql-create-failover-cluster.md)고려하십시오.  

환경의 IOPS 활동을 확인하고 배포 또는 마이그레이션을 시작하기 전에 프리미엄 파일 공유가 필요한 IOPS를 제공하는지 확인합니다. Windows 성능 모니터 디스크 카운터를 사용하여 SQL Server 데이터, 로그 및 임시 DB 파일에 필요한 총 IOPS(디스크 전송/초) 및 처리량(디스크 바이트/초)을 모니터링합니다.

많은 워크로드에 IO가 파열되어 있으므로 사용량이 많은 기간 동안 확인하고 최대 IOPS와 평균 IOPS를 모두 확인하는 것이 좋습니다. 프리미엄 파일 공유는 공유 크기에 따라 IOPS를 제공합니다. 프리미엄 파일 공유는 또한 최대 1시간 동안 기준 금액을 세 배로 늘릴 수 있는 무료 버스팅을 제공합니다.

프리미엄 파일 공유 성능에 대한 자세한 내용은 [파일 공유 성능 계층을](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers)참조하십시오.

### <a name="licensing-and-pricing"></a>라이선스 및 가격 책정

Azure 가상 컴퓨터에서 종량제(PAYG) 또는 BYOL(사용자 라이선스 가져오기) VM 이미지를 사용하여 SQL Server에 라이선스를 부여할 수 있습니다. 선택한 이미지 유형은 청구 방식에 영향을 줍니다.

종량제 라이선스를 사용하면 Azure 가상 머신의 SQL Server의 장애 조치 클러스터 인스턴스(FCI)는 수동 노드를 포함한 FCI의 모든 노드에 대한 요금이 부과됩니다. 자세한 내용은 [SQL Server Enterprise Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)을 참조하세요.

소프트웨어 보증과 엔터프라이즈 계약을 체결한 경우 각 활성 노드에 대해 하나의 무료 수동 FCI 노드를 사용할 수 있습니다. Azure에서 이러한 이점을 활용하려면 BYOL VM 이미지를 사용하고 FCI의 활성 노드와 수동 노드 모두에서 동일한 라이선스를 사용합니다. 자세한 내용은 [기업계약](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)을 참조하세요.

Azure 가상 컴퓨터에서 SQL Server에 대한 종량제 및 BYOL 라이선스를 비교하려면 [SQL VM으로 시작하기 를](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)참조하십시오.

SQL Server 라이선싱에 대한 자세한 내용은 [가격 책정](https://www.microsoft.com/sql-server/sql-server-2017-pricing)을 참조하세요.

### <a name="filestream"></a>Filestream

프리미엄 파일 공유가 있는 장애 조치 클러스터에는 파일 스트림이 지원되지 않습니다. 파일 스트림을 사용하려면 저장소 [공간 직접](virtual-machines-windows-portal-sql-create-failover-cluster.md)을 사용하여 클러스터를 배포합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하기 전에 이미 다음이 있어야 합니다.

- Microsoft Azure 구독
- Azure 가상 머신의 Windows 도메인
- Azure 가상 컴퓨터와 Active Directory에서 개체를 만들 수 있는 권한이 있는 도메인 사용자 계정입니다.
- SQL Server 서비스를 실행하고 파일 공유를 탑재할 때 가상 시스템에 로그인할 수 있는 도메인 사용자 계정입니다.  
- 이러한 구성 요소에 대한 충분한 IP 주소 공간이 있는 Azure 가상 네트워크 및 서브넷:
   - 두 개의 가상 컴퓨터.
   - 장애 조치 클러스터 IP 주소
   - 각 FCI에 대한 IP 주소
- Azure 네트워크에서 구성된 DNS로 도메인 컨트롤러를 가리킵니다.
- 데이터 파일에 대한 데이터베이스의 저장소 할당량에 따라 클러스터된 드라이브로 사용할 [프리미엄 파일 공유입니다.](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- Windows Server 2012 R2 이상인 경우 Windows 2016 및 최신 클라우드 미러링 모니터 서버가 지원되므로 파일 공유 증인으로 사용할 다른 파일 공유가 필요합니다. 다른 Azure 파일 공유를 사용하거나 별도의 가상 컴퓨터에서 파일 공유를 사용할 수 있습니다. 다른 Azure 파일 공유를 사용하려는 경우 클러스터된 드라이브에 사용되는 프리미엄 파일 공유와 동일한 프로세스로 마운트할 수 있습니다. 

이러한 필수 구성 을 사용하면 장애 조치 클러스터를 빌드할 수 있습니다. 첫 번째 단계는 가상 머신을 만드는 것입니다.

## <a name="step-1-create-the-virtual-machines"></a>1단계: 가상 컴퓨터 만들기

1. 구독을 사용하여 [Azure 포털에](https://portal.azure.com) 로그인합니다.

1. [Azure 가용성 집합을 만듭니다](../tutorial-availability-sets.md).

   가용성 집합은 장애 도메인 및 업데이트 도메인에 대해 가상 머신을 그룹화합니다. 이를 통해 네트워크 스위치 또는 서버 랙의 전원 장치와 같은 단일 장애 지점의 영향을 받지 않습니다.

   가상 컴퓨터에 대한 리소스 그룹을 만들지 않은 경우 Azure 가용성 집합을 만들 때 해당 작업을 수행합니다. Azure 포털을 사용하여 가용성 집합을 만드는 경우 다음 단계를 수행합니다.

   1. Azure 포털에서 **Azure 마켓플레이스를 열려면 리소스 만들기를** 선택합니다. **가용성 집합**을 검색합니다.
   1. **가용성 집합을**선택합니다.
   1. **만들기**를 선택합니다.
   1. **가용성 만들기 집합에서**다음 값을 제공합니다.
      - **이름**: 가용성 집합에 대한 이름입니다.
      - **구독**: Azure 구독입니다.
      - **리소스 그룹:** 기존 그룹을 사용하려면 **기존 선택을** 클릭한 다음 목록에서 그룹을 선택합니다. 그렇지 않으면 **새 만들기를** 선택하고 그룹의 이름을 입력합니다.
      - **위치**: 가상 머신을 만들 위치를 설정합니다.
      - **장애 도메인**: 기본값 **(3)을**사용합니다.
      - **도메인 업데이트**: 기본값 **(5)을**사용합니다.
   1. 가용성 집합을 만들려면 **만들기를** 선택합니다.

1. 가용성 집합에 가상 머신을 만듭니다.

   Azure 가용성 집합에서 두 개의 SQL Server 가상 머신을 프로비전합니다. 지침은 [Azure Portal에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.

   두 가상 머신을 다음에 배치합니다.

   - 가용성 집합과 동일한 Azure 리소스 그룹에서 설정합니다.
   - 도메인 컨트롤러와 동일한 네트워크에.
   - 가상 컴퓨터와 클러스터에서 사용할 수 있는 모든 FCI에 충분한 IP 주소 공간이 있는 서브넷에서
   - Azure 가용성 집합에.

      >[!IMPORTANT]
      >가상 컴퓨터를 만든 후에는 가용성 집합을 설정하거나 변경할 수 없습니다.

   Azure 마켓플레이스에서 이미지를 선택합니다. Windows 서버 및 SQL Server가 포함된 Azure 마켓플레이스 이미지를 사용하거나 Windows 서버만 포함하는 이미지를 사용할 수 있습니다. 자세한 내용은 [Azure 가상 시스템의 SQL Server 개요를](virtual-machines-windows-sql-server-iaas-overview.md)참조하십시오.

   Azure 갤러리의 공식 SQL Server 이미지에는 설치된 SQL Server 인스턴스, SQL Server 설치 소프트웨어 및 필요한 키가 포함됩니다.

   >[!IMPORTANT]
   > 가상 머신을 만든 후에 사전 설치된 독립 실행형 SQL Server 인스턴스를 제거합니다. 사전 설치된 SQL Server 미디어를 사용하여 장애 조치 클러스터 및 프리미엄 파일 공유를 저장소로 설정한 후 SQL Server FCI를 만듭니다.

   또는 운영 체제만 포함하는 Azure Marketplace 이미지를 사용할 수 있습니다. 장애 조치 클러스터 및 프리미엄 파일 공유를 저장소로 설정한 후 **Windows Server 2016 데이터 센터** 이미지를 선택하고 SQL Server FCI를 설치합니다. 이 이미지에는 SQL Server 설치 미디어가 포함되어 있지 않습니다. SQL Server 설치 미디어를 각 서버에 대해 실행할 수 있는 위치에 배치합니다.

1. Azure에서 가상 컴퓨터를 만들면 RDP를 사용하여 각 시스템에 연결합니다.

   RDP를 사용하여 가상 시스템에 처음 연결할 때 네트워크에서 PC를 검색할 수 있도록 허용할지 묻는 메시지가 표시됩니다. **예**를 선택합니다.

1. SQL Server 기반 가상 컴퓨터 이미지 중 하나를 사용하는 경우 SQL Server 인스턴스를 제거합니다.

   1. **프로그램 및 기능에서** **Microsoft SQL Server 201_(64비트)를** 마우스 오른쪽 단추로 클릭하고 **제거/변경**을 선택합니다.
   1. **제거**를 선택합니다.
   1. 기본 인스턴스를 선택합니다.
   1. **데이터베이스 엔진 서비스**에서 모든 기능을 제거합니다. **공유 기능을**제거하지 마십시오. 다음 스크린샷과 같은 것이 표시됩니다.

        ![기능 선택](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. **다음**을 선택한 다음 **에서 제거를**선택합니다.

1. <span id="ports"> </span> 방화벽 포트를 엽니다.  

   각 가상 컴퓨터에서 Windows 방화벽에서 다음 포트를 엽니다.

   | 목적 | TCP 포트 | 메모
   | ------ | ------ | ------
   | SQL Server | 1433 | SQL Server의 기본 인스턴스에 대한 표준 포트입니다. 갤러리에서 이미지를 사용한 경우 이 포트는 자동으로 열립니다.
   | 상태 프로브 | 59999 | 모든 공개 TCP 포트입니다. 이후 단계에서 이 포트를 사용하려면 부하 분산 장치 [상태 프로브](#probe) 및 클러스터를 구성합니다.
   | 파일 공유 | 445 | 파일 공유 서비스에서 사용하는 포트입니다.

1. [기존 도메인에 가상 머신을 추가합니다](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

가상 컴퓨터를 만들고 구성한 후 프리미엄 파일 공유를 구성할 수 있습니다.

## <a name="step-2-mount-the-premium-file-share"></a>2단계: 프리미엄 파일 공유 마운트

1. [Azure 포털에](https://portal.azure.com) 로그인하고 저장소 계정으로 이동합니다.
1. **파일 서비스에서** **파일 공유로** 이동하여 SQL 저장소에 사용할 프리미엄 파일 공유를 선택합니다.
1. 파일 공유에 대한 연결 문자열을 가져오려면 **연결을** 선택합니다.
1. 드롭다운 목록에서 사용할 드라이브 문자를 선택한 다음 두 코드 블록을 메모장에 복사합니다.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="파일 공유 연결 포털에서 두 PowerShell 명령 복사":::

1. RDP를 사용하여 SQL Server FCI가 서비스 계정에 사용할 계정으로 SQL Server VM에 연결합니다.
1. 관리 PowerShell 명령 콘솔을 엽니다.
1. 포털에서 작업할 때 이전에 저장한 명령을 실행합니다.
1. 파일 탐색기 또는 **실행** 대화 상자(Windows 로고 키 + r)를 사용하여 공유로 이동합니다. 네트워크 경로를 `\\storageaccountname.file.core.windows.net\filesharename`사용합니다. 예를 들어 `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. 새로 연결된 파일 공유에 하나 이상의 폴더를 만들어 SQL Data 파일을 배치합니다.
1. 클러스터에 참여할 각 SQL Server VM에서 이러한 단계를 반복합니다.

  > [!IMPORTANT]
  > - 백업 파일에 별도의 파일 공유를 사용하여 데이터 및 로그 파일에 대해 이 공유의 IOPS 및 공간 용량을 저장하는 것이 좋습니다. 백업 파일에 프리미엄 또는 표준 파일 공유를 사용할 수 있습니다.
  > - Windows 2012 R2 이상 인 경우 다음 단계에 따라 파일 공유 미러링 모니터 서버로 사용할 파일 공유를 탑재합니다. 

## <a name="step-3-configure-the-failover-cluster"></a>3단계: 장애 조치 클러스터 구성

다음 단계는 장애 조치 클러스터를 구성하는 것입니다. 이 단계에서는 다음 하위 단계를 완료합니다.

1. Windows 서버 장애 조치 클러스터링 기능을 추가합니다.
1. 클러스터의 유효성을 검사합니다.
1. 장애 조치 클러스터를 만듭니다.
1. 클라우드 미러링 모니터 서버(Windows Server 2016 및 최신 기능)나 파일 공유 미러링 모니터 서버(Windows Server 2012 R2 이상 용)를 만듭니다.


### <a name="add-windows-server-failover-clustering"></a>Windows 서버 장애 조치 클러스터링 추가

1. 로컬 관리자의 구성원이며 Active Directory에서 개체를 만들 수 있는 권한이 있는 도메인 계정을 사용하여 RDP를 사용하여 첫 번째 가상 시스템에 연결합니다. 구성의 나머지 부분에서는 이 계정을 사용합니다.

1. [각 가상 시스템에 장애 조치 클러스터링을 추가합니다.](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)

   UI에서 장애 조치 클러스터링을 설치하려면 두 가상 컴퓨터에서 다음 단계를 수행합니다.
   1. **서버 관리자에서** **에서 을 선택한**다음 역할 및 기능 **추가를**선택합니다.
   1. 역할 **및 피처 추가 마법사에서** **피처 선택에**도착할 때까지 **다음을** 선택합니다.
   1. **피처 선택에서** **장애 조치 클러스터링을 선택합니다.** 필요한 모든 기능 및 관리 도구를 포함합니다. **피처 추가를**선택합니다.
   1. **다음을**선택한 다음 **완료를** 선택하여 피처를 설치합니다.

   PowerShell을 사용하여 장애 조치 클러스터링을 설치하려면 가상 컴퓨터 중 하나에서 관리자 PowerShell 세션에서 다음 스크립트를 실행합니다.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>클러스터 유효성 검사

UI에서 또는 PowerShell을 사용하여 클러스터의 유효성을 검사합니다.

UI를 사용하여 클러스터의 유효성을 검사하려면 가상 컴퓨터 중 하나에서 다음 단계를 수행합니다.

1. **서버 관리자에서** **도구를**선택한 다음 **장애 조치 클러스터 관리자를**선택합니다.
1. **장애 조치 클러스터 관리자에서** **작업을**선택한 다음 구성 **유효성 검사를**선택합니다.
1. **다음**을 선택합니다.
1. 서버 선택 또는 클러스터 에서 두 가상 컴퓨터의 이름을 **입력합니다.**
1. **테스트 옵션에서**선택한 **테스트만 실행합니다.** **다음**을 선택합니다.
1. **테스트 선택에서**다음과 같이 **저장소** 및 **저장소 공간 Direct를**제외한 모든 테스트를 선택합니다.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="클러스터 유효성 검사 테스트 선택":::

1. **다음**을 선택합니다.
1. **확인**아래에서 **다음을 선택합니다.**

**구성 유효성 검사 마법사**가 유효성 검사 테스트를 실행합니다.

PowerShell을 사용하여 클러스터의 유효성을 검사하려면 가상 컴퓨터 중 하나에서 관리자 PowerShell 세션에서 다음 스크립트를 실행합니다.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

클러스터의 유효성을 검사한 후에 장애 조치 클러스터를 만듭니다.

### <a name="create-the-failover-cluster"></a>장애 조치 클러스터 만들기

장애 조치 클러스터를 만들려면 다음이 필요합니다.
- 클러스터 노드가 될 가상 시스템의 이름입니다.
- 장애 조치 클러스터의 이름
- 장애 조치 클러스터의 IP 주소 클러스터 노드와 동일한 Azure 가상 네트워크 및 서브넷에서 사용되지 않는 IP 주소를 사용할 수 있습니다.

#### <a name="windows-server-2012-through-windows-server-2016"></a>윈도우 서버 2012 윈도우 서버를 통해 2016

다음 PowerShell 스크립트는 Windows Server 2016을 통해 Windows 서버 2012에 대한 장애 조치 클러스터를 만듭니다. 노드 이름(가상 시스템 이름)과 Azure 가상 네트워크에서 사용 가능한 IP 주소로 스크립트를 업데이트합니다.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

다음 PowerShell 스크립트는 Windows Server 2019에 대한 장애 조치 클러스터를 만듭니다. 자세한 내용은 [장애 조치 클러스터: 클러스터 네트워크 개체](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)를 참조하십시오. 노드 이름(가상 시스템 이름)과 Azure 가상 네트워크에서 사용 가능한 IP 주소로 스크립트를 업데이트합니다.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>클라우드 미러링 모니터 서버 만들기(2016년 승리+)

Windows Server 2016 이상에 있는 경우 클라우드 감시를 만들어야 합니다. 클라우드 감시는 Azure 저장소 Blob에 저장된 새로운 유형의 클러스터 쿼럼 감시입니다. 이렇게 하면 미러링 모니터 서버 공유를 호스팅하는 별도의 VM이 필요하거나 별도의 파일 공유를 사용할 필요가 없습니다.

1. [장애 조치 클러스터에 대한 클라우드 감시를 만듭니다](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Blob 컨테이너를 만듭니다.

1. 액세스 키 및 컨테이너 URL을 저장합니다.

### <a name="configure-quorum"></a>쿼럼 구성 

Windows Server 2016 이상에 대해 방금 만든 클라우드 미러링 모니터 서버를 사용하도록 클러스터를 구성합니다. 모든 단계를 따르십시오 [사용자 인터페이스에서 쿼럼 감시를 구성합니다.](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)

Windows Server 2012 R2 이상에 대해 [사용자 인터페이스에서 쿼럼 감시를 구성하지만 쿼럼](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) **감시자 선택** 페이지에서 동일한 단계를 수행하여 **파일 공유 감시** 자 구성 옵션을 선택합니다. 별도의 가상 컴퓨터에서 구성했거나 Azure에서 탑재한 파일 공유 를 파일 공유 감시로 지정합니다. 


## <a name="step-4-test-cluster-failover"></a>4단계: 테스트 클러스터 장애 조치

클러스터의 장애 조치(failover)를 테스트합니다. **장애 조치 클러스터 관리자에서**클러스터를 마우스 오른쪽 단추로 클릭하고 더 많은 **작업** > **이동 코어 클러스터 리소스** > **선택 노드를**선택한 다음 클러스터의 다른 노드를 선택합니다. 코어 클러스터 리소스를 클러스터의 모든 노드로 이동한 다음 기본 노드로 다시 이동합니다. 클러스터를 각 노드로 성공적으로 이동할 수 있으면 SQL Server를 설치할 준비가 된 것입니다.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="핵심 리소스를 다른 노드로 이동하여 클러스터 장애 조치 테스트":::

## <a name="step-5-create-the-sql-server-fci"></a>5단계: SQL 서버 FCI 만들기

장애 조치 클러스터를 구성한 후 SQL Server FCI를 만들 수 있습니다.

1. RDP를 사용하여 첫 번째 가상 시스템에 연결합니다.

1. **장애 조치 클러스터 관리자에서**모든 핵심 클러스터 리소스가 첫 번째 가상 시스템에 있는지 확인합니다. 필요한 경우 모든 리소스를 이 가상 컴퓨터로 이동합니다.

1. 설치 미디어를 찾습니다. 가상 머신이 Azure Marketplace 이미지 중 하나를 사용하는 경우 미디어는 `C:\SQLServer_<version number>_Full`에 있습니다. **Setup**을 선택합니다.

1. SQL **Server 설치 센터에서** **설치**를 선택합니다.

1. **새 SQL Server 장애 조치 클러스터 설치를**선택합니다. 마법사의 지침에 따라 SQL Server FCI를 설치합니다.

   FCI 데이터 디렉토리는 프리미엄 파일 공유에 있어야 합니다. 공유의 전체 경로를 다음 형식으로 `\\storageaccountname.file.core.windows.net\filesharename\foldername`입력합니다. 파일 서버를 데이터 디렉토리로 지정했다는 경고가 표시됩니다. 이 경고는 정상적인 것입니다. 파일 공유를 지속할 때 RDP가 VM에 RDP를 입력한 사용자 계정이 SQL Server 서비스가 가능한 오류를 방지하기 위해 사용하는 것과 동일한 계정인지 확인합니다.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="파일 공유를 SQL 데이터 디렉터리로 사용":::

1. 마법사의 단계를 완료하면 설치 프로그램이 첫 번째 노드에 SQL Server FCI를 설치합니다.

1. 설치 가 첫 번째 노드에 FCI를 설치한 후 RDP를 사용하여 두 번째 노드에 연결합니다.

1. **SQL Server 설치 센터**를 엽니다. **설치**를 선택합니다.

1. **SQL Server 장애 조치 클러스터에 노드 추가를**선택합니다. 마법사의 지침에 따라 SQL Server를 설치하고 FCI에 서버를 추가합니다.

   >[!NOTE]
   >SQL Server와 함께 Azure Marketplace 갤러리 이미지를 사용한 경우 SQL Server 도구는 이미지에 포함되었습니다. 이러한 이미지 중 하나를 사용하지 않은 경우 SQL Server 도구를 별도로 설치합니다. [SSMS(SQL Server Management Studio) 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)를 참조하세요.

## <a name="step-6-create-the-azure-load-balancer"></a>6단계: Azure 로드 밸러터 만들기

Azure 가상 머신에서 클러스터는 한 번에 하나의 클러스터 노드에 있어야 하는 IP 주소를 저장하는 부하 분산 장치를 사용합니다. 이 솔루션에서 부하 분산 장치는 SQL Server FCI에 대한 IP 주소를 저장합니다.

자세한 내용은 [Azure 로드 밸런서 만들기 및 구성을](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)참조하십시오.

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Azure Portal에서 부하 분산 장치 만들기

부하 분산 장치를 만들려면:

1. Azure 포털에서 가상 컴퓨터를 포함하는 리소스 그룹으로 이동합니다.

1. **추가**를 선택합니다. Azure 마켓플레이스에서 **로드 밸러저를 검색합니다.** **로드 밸러울러 를 선택합니다.**

1. **만들기**를 선택합니다.

1. 다음 값을 사용하여 로드 밸러워를 설정합니다.

   - **구독**: Azure 구독입니다.
   - **리소스 그룹**: 가상 컴퓨터를 포함하는 리소스 그룹입니다.
   - **이름**: 부하 분산 장치를 식별하는 이름입니다.
   - **지역**: 가상 컴퓨터를 포함하는 Azure 위치입니다.
   - **유형**: 공용 또는 비공개. 가상 네트워크 내에서 개인 로드 밸러버에 액세스할 수 있습니다. 대부분의 Azure 애플리케이션은 프라이빗 부하 분산 장치를 사용할 수 있습니다. 응용 프로그램이 인터넷을 통해 직접 SQL Server에 액세스해야 하는 경우 공용 로드 밸런서를 사용합니다.
   - **SKU**: 표준.
   - **가상 네트워크**: 가상 컴퓨터와 동일한 네트워크입니다.
   - **IP 주소 할당**: 정적. 
   - **개인 IP 주소**: SQL Server FCI 클러스터 네트워크 리소스에 할당한 IP 주소입니다.

   다음 이미지는 **로드 밸러서 만들기** UI를 보여 주며,

   ![로드 밸워서 설정](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>부하 분산 장치 백 엔드 풀 구성

1. 가상 컴퓨터가 포함된 Azure 리소스 그룹으로 돌아가서 새 로드 밸러버를 찾습니다. 리소스 그룹에서 보기를 새로 고쳐야 할 수 있습니다. 부하 분산 장치를 선택합니다.

1. **백 엔드 풀을**선택한 다음 에 **추가를**선택합니다.

1. VM이 포함된 가용성 집합과 백 엔드 풀을 연결합니다.

1. **대상 네트워크 IP 구성에서**가상 **컴퓨터를** 선택하고 클러스터 노드로 참여할 가상 컴퓨터를 선택합니다. FCI를 호스팅하는 모든 가상 머신을 포함해야 합니다.

1. **확인을** 선택하여 백 엔드 풀을 만듭니다.

### <a name="configure-a-load-balancer-health-probe"></a>부하 분산 장치 상태 프로브 구성

1. 부하 분산 블레이드에서 **상태 프로브를 선택합니다.**

1. **추가**를 선택합니다.

1. 상태 프로브 블레이드 **추가에서** 다음 상태 프로브 매개 변수를 <span id="probe"> </span> 설정합니다.

   - **이름**: 상태 프로브의 이름입니다.
   - **프로토콜**: TCP.
   - **포트**: [이 단계에서](#ports)상태 프로브에 대한 방화벽에서 만든 포트입니다. 이 문서에서는 TCP 포트를 `59999`사용합니다.
   - **간격**: 5초입니다.
   - **비정상 임계값**: 두 번 연속 실패입니다.

1. **확인**을 선택합니다.

### <a name="set-load-balancing-rules"></a>부하 분산 규칙 설정

1. 로드 밸런서 블레이드에서 **로드 밸런싱 규칙을**선택합니다.

1. **추가**를 선택합니다.

1. 부하 분산 규칙 매개 변수 설정:

   - **이름**: 부하 분산 규칙의 이름입니다.
   - **프런트 엔드 IP 주소**: SQL Server FCI 클러스터 네트워크 리소스의 IP 주소입니다.
   - **포트**: SQL 서버 FCI TCP 포트입니다. 기본 인스턴스 포트는 1433입니다.
   - **백 엔드 포트**: **부동 IP (직접 서버 반환)를**활성화 할 때 **포트** 값과 동일한 포트를 사용합니다.
   - **백 엔드 풀**: 이전에 구성한 백 엔드 풀 이름입니다.
   - **상태 프로브**: 이전에 구성한 상태 프로브입니다.
   - **세션 지속성**: 없음.
   - **유휴 제한 시간(분)**: 4.
   - **부동 IP (직접 서버 반환)**: 사용.

1. **확인**을 선택합니다.

## <a name="step-7-configure-the-cluster-for-the-probe"></a>7단계: 프로브에 대한 클러스터 구성

PowerShell에서 클러스터 프로브 포트 매개 변수를 설정합니다.

클러스터 프로브 포트 매개 변수를 설정하려면 다음 스크립트의 변수를 환경의 값으로 업데이트합니다. 스크립트에서 각 괄호 `>`(및)를`<` 제거합니다.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

다음 목록에서는 업데이트해야 하는 값을 설명합니다.

   - `<Cluster Network Name>`: 네트워크의 Windows 서버 장애 조치 클러스터 이름입니다. **장애 조치 클러스터 관리자** > **네트워크에서**네트워크를 마우스 오른쪽 단추로 클릭하고 **속성을**선택합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다.

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server FCI IP 주소 리소스 이름입니다. **장애 조치 클러스터 관리자** > **역할에서**SQL Server FCI 역할, **서버 이름**아래 IP 주소 리소스를 마우스 오른쪽 단추로 클릭하고 **속성을**선택합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다.

   - `<ILBIP>`: ILB IP 주소입니다. 이 주소는 Azure Portal에서 ILB 프런트 엔드 주소로 구성됩니다. 또한 SQL Server FCI IP 주소입니다. 이 주소는 `<SQL Server FCI IP Address Resource Name>`이 있는 동일한 속성 페이지의 **장애 조치(Failover) 클러스터 관리자**에서 확인할 수 있습니다.  

   - `<nnnnn>`: 로드 밸러저 상태 프로브에서 구성한 프로브 포트입니다. 사용하지 않는 모든 TCP 포트는 유효합니다.

>[!IMPORTANT]
>클러스터 매개 변수에 대한 서브넷 마스크는 TCP IP 브로드캐스트 주소여야 합니다(`255.255.255.255`).

클러스터 프로브를 설정한 후 PowerShell의 모든 클러스터 매개 변수를 볼 수 있습니다. 다음 스크립트를 실행합니다.

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>8단계: FCI 장애 조치 테스트

FCI의 장애 조치(failover)를 테스트하여 클러스터 기능의 유효성을 검사합니다. 다음과 같은 단계를 수행합니다.

1. RDP를 사용하여 SQL Server FCI 클러스터 노드 중 하나에 연결합니다.

1. **장애 조치 클러스터 관리자**를 엽니다. **역할**을 선택합니다. SQL Server FCI 역할을 소유하는 노드를 살펴봅니다.

1. SQL Server FCI 역할을 마우스 오른쪽 단추로 클릭합니다.

1. **이동을**선택한 다음 **최상의 노드를**선택합니다.

**장애 조치 클러스터 관리자는** 역할을 표시하고 해당 리소스는 오프라인 상태가 됩니다. 그런 다음 리소스가 이동하고 다른 노드에서 다시 온라인 상태가 됩니다.

### <a name="test-connectivity"></a>연결 테스트

연결을 테스트하려면 동일한 가상 네트워크의 다른 가상 시스템에 로그인합니다. **SQL Server Management Studio**를 열고 SQL Server FCI 이름에 연결합니다.

>[!NOTE]
>필요한 경우 [SQL 서버 관리 스튜디오를 다운로드할](https://msdn.microsoft.com/library/mt238290.aspx)수 있습니다.

## <a name="limitations"></a>제한 사항

Azure 가상 머신은 클러스터된 공유 볼륨(CSV) 및 [표준 로드 밸런서에](../../../load-balancer/load-balancer-standard-overview.md)대한 저장소를 통해 Windows Server 2019에서 Microsoft 분산 트랜잭션 코디네이터(MSDTC)를 지원합니다.

Azure 가상 컴퓨터에서 MSDTC는 Windows Server 2016 또는 이전 버전에서 지원되지 않습니다.

- 클러스터된 MSDTC 리소스는 공유 저장소를 사용하도록 구성할 수 없습니다. Windows Server 2016에서 MSDTC 리소스를 만드는 경우 저장소를 사용할 수 있는 경우에도 사용할 수 있는 공유 저장소가 표시되지 않습니다. 이 문제는 Windows Server 2019에서 수정되었습니다.
- 기본 로드 밸러울러는 RPC 포트를 처리하지 않습니다.

## <a name="see-also"></a>참조

- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL 서버 장애 조치 클러스터 인스턴스](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
