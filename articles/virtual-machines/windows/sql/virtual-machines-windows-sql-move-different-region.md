---
title: 가상 컴퓨터를 다른 지역으로 이동 (Azure Site Recovery)
description: Azure 내에서 한 지역에서 다른 지역으로 SQL Server 가상 머신을 마이그레이션하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3b84119cdcc1bb7e8603de64e3d23c69dac70cc3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022299"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>Azure Site Recovery 서비스를 사용 하 여 Azure 내의 다른 지역으로 SQL Server VM 이동

이 문서에서는 Azure Site Recovery를 사용 하 여 Azure 내에서 한 지역에서 다른 지역으로 SQL Server VM (가상 머신)을 마이그레이션하는 방법을 설명 합니다. 

SQL Server VM을 다른 지역으로 이동 하려면 다음을 수행 해야 합니다.
1. [**준비**](#prepare-to-move): 원본 SQL Server VM 및 대상 지역이 이동에 적절 하 게 준비 되었는지 확인 합니다. 
1. [**구성**](#configure-azure-site-recovery-vault): SQL Server VM 이동 하려면 Azure Site Recovery 자격 증명 모음 내에서 복제 된 개체 여야 합니다. Azure Site Recovery 자격 증명 모음에 SQL Server VM를 추가 해야 합니다. 
1. [**테스트**](#test-move-process): SQL Server VM 마이그레이션하려면 원본 지역에서 복제 된 대상 지역으로 장애 조치 (failover)를 수행 해야 합니다. 이동 프로세스가 성공 하려면 먼저 대상 지역으로 SQL Server VM 성공적으로 장애 조치 (failover) 할 수 있는지 테스트 해야 합니다. 이렇게 하면 문제를 노출 하 고 실제 이동을 수행할 때이를 방지할 수 있습니다. 
1. [**이동**](#move-the-sql-server-vm): 테스트 장애 조치 (failover)가 성공 하 고 SQL Server VM를 마이그레이션하는 것이 안전 하다는 것을 알게 되 면 VM을 대상 지역으로 이동할 수 있습니다. 
1. [**정리**](#clean-up-source-resources): 청구 요금을 방지 하려면 자격 증명 모음에서 SQL Server VM를 제거 하 고 리소스 그룹에서 남은 불필요 한 리소스를 제거 합니다. 

## <a name="verify-prerequisites"></a>필수 조건 확인 

- 원본 지역에서 대상 지역으로의 이동 [이 지원](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)되는지 확인 합니다.  
- [시나리오 아키텍처 및 구성 요소](../../../site-recovery/azure-to-azure-architecture.md) 뿐만 아니라 [지원 제한 사항 및 요구 사항을](../../../site-recovery/azure-to-azure-support-matrix.md)검토 합니다. 
- 계정 권한을 확인합니다. Azure 체험 계정을 만든 경우 자신이 구독에 대한 관리자입니다. 구독 관리자가 아닌 경우에는 관리자와 협력하여 필요한 권한을 할당받습니다. VM에 대 한 복제를 사용 하도록 설정 하 고 Azure Site Recovery를 사용 하 여 데이터를 복사 하려면 다음이 있어야 합니다. 
    - VM을 만들 수 있는 권한 기본 제공되는 *Virtual Machine 참가자* 역할에는 다음을 포함하여 이러한 권한이 있습니다. 
        - 선택한 리소스 그룹에 VM을 만들 수 있는 권한 
        - 선택한 가상 네트워크에서 VM을 만들 수 있는 권한 
        - 선택한 저장소 계정에 쓸 수 있는 권한입니다. 
      - Azure Site Recovery 작업을 관리할 수 있는 권한 *Site Recovery 참여자* 역할에는 Recovery Services 자격 증명 모음에서 Site Recovery 작업을 관리 하는 데 필요한 모든 사용 권한이 있습니다.  

## <a name="prepare-to-move"></a>이동할 준비
이동에 대 한 원본 SQL Server VM와 대상 지역을 모두 준비 합니다. 

### <a name="prepare-the-source-sql-server-vm"></a>원본 SQL Server VM 준비

- 이동 하려는 SQL Server VM에 모든 최신 루트 인증서가 있는지 확인 합니다. 최신 루트 인증서가 없는 경우 보안 제약 조건으로 인해 대상 지역에 대 한 데이터 복사가 차단 됩니다. 
- Windows Vm의 경우 모든 신뢰할 수 있는 루트 인증서가 컴퓨터에 있도록 모든 최신 Windows 업데이트를 VM에 설치 합니다. 연결 되지 않은 환경에서 조직의 표준 Windows 업데이트 및 인증서 업데이트 프로세스를 따릅니다. 
- Linux VM의 경우 Linux 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 인증서 해지 목록을 VM에 가져옵니다. 
- 이동 하려는 Vm에 대 한 네트워크 연결을 제어 하기 위해 인증 프록시를 사용 하 고 있지 않은지 확인 합니다. 
- 이동 하려는 VM이 인터넷에 액세스할 수 없거나 방화벽 프록시를 사용 하 여 아웃 바운드 액세스를 제어 하는 경우에는 요구 사항을 확인 하세요. 
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 여기에는 부하 분산 장치, 네트워크 보안 그룹(NSG) 및 공용 IP를 포함하되 국한되지 않습니다. 

### <a name="prepare-the-target-region"></a>대상 Azure 지역 준비

- Azure 구독에서 재해 복구에 사용되는 대상 Azure 지역에 VM을 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요. 
- 원본 Vm과 일치 하는 크기의 Vm을 지원 하기에 충분 한 리소스가 구독에 있는지 확인 합니다. Site Recovery를 사용 하 여 대상에 데이터를 복사 하는 경우 Site Recovery 같은 크기 또는 대상 VM에 가장 가까운 크기를 선택 합니다. 
- 원본 네트워킹 레이아웃에서 식별된 모든 구성 요소의 대상 리소스를 만듭니다. 그래야만 VM이 원본 지역서 사용하던 모든 기능을 대상 지역에서 그대로 사용할 수 있으므로 이 단계는 중요합니다. 
    - 사용자가 원본 VM에 대해 복제를 사용하도록 설정하면 Azure Site Recovery는 가상 네트워크를 자동으로 검색하고 만듭니다. 또한 네트워크를 미리 만들고 복제를 사용 하도록 설정 하기 위해 사용자 흐름의 VM에 할당할 수 있습니다. 대상 지역에서 다른 리소스를 수동으로 만들어야 합니다.
- 원본 VM 구성에 따라 관련성이 있는 가장 일반적으로 사용되는 네트워크 리소스를 만들려면 다음 문서를 참조하세요. 
    - [네트워크 보안 그룹](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [부하 분산 장치](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [공용 IP 주소](../../../virtual-network/virtual-network-public-ip-address.md)
    - 추가 네트워킹 구성 요소는 [네트워킹 설명서](../../../virtual-network/virtual-networks-overview.md)를 참조 하십시오.
- 대상 지역으로 최종 이동을 수행 하기 전에 구성을 테스트 하려는 경우 대상 지역에 비프로덕션 네트워크를 수동으로 만듭니다. 이 단계는 프로덕션 네트워크를 최소로만 중단하므로 권장됩니다. 

## <a name="configure-azure-site-recovery-vault"></a>Azure Site Recovery 자격 증명 모음 구성

다음 단계에서는 Azure Site Recovery를 사용하여 대상 Azure 지역에 데이터를 복사하는 방법을 보여줍니다. 원본 지역이 아닌 모든 지역에 Recovery Services 자격 증명 모음을 만듭니다. 

1. [Azure 포털](https://portal.azure.com)할 수 있습니다. 
1. 탐색 창의 왼쪽 위 모서리에서 **리소스를 만들도록** 선택 합니다. 
1. **& 관리 도구** 를 선택한 다음 **백업 및 Site Recovery**를 선택 합니다. 
1. **기본** 정보 탭의 **프로젝트 세부 정보**에서 대상 지역에 새 리소스 그룹을 만들거나 대상 지역에서 기존 리소스 그룹을 선택 합니다. 
1. **인스턴스 세부 정보**에서 자격 증명 모음에 대 한 이름을 지정 하 고 드롭다운에서 대상 **지역을** 선택 합니다. 
1. **검토 + 만들기** 를 선택 하 여 Recovery Services 자격 증명 모음을 만듭니다. 
1. 탐색 창의 왼쪽 위 모서리에 있는 **모든 서비스** 를 선택 하 고 검색 상자에 `recovery services`을 입력 합니다. 
1. 생략할 **Recovery Services 자격 증명 모음** 옆의 별표를 선택 하 여 빠른 탐색 모음에 추가 합니다. 
1. **Recovery services 자격 증명 모음** 을 선택 하 고 만든 Recovery Services 자격 증명 모음을 선택 합니다. 
1. **개요** 창에서 **복제**를 선택 합니다. 

   ![복제 구성](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. **원본** 을 선택 하 고 **Azure** 를 원본으로 선택 합니다. 원본 Vm의 위치와 같은 다른 드롭다운 필드에 적절 한 값을 선택 합니다. 원본 **위치** 영역에 있는 리소스 그룹만 **원본 리소스 그룹** 필드에 표시 됩니다. 
1. **가상 컴퓨터** 를 선택한 다음 마이그레이션할 가상 컴퓨터를 선택 합니다. **확인** 을 선택 하 여 VM 선택 항목을 저장 합니다. 
1. **설정**을 선택한 다음 드롭다운에서 **대상 위치** 를 선택 합니다. 이는 앞에서 준비한 리소스 그룹 이어야 합니다. 
1. 복제를 사용자 지정한 후에는 **대상 리소스 만들기** 를 선택 하 여 새 위치에 리소스를 만듭니다. 
1. 리소스 만들기가 완료 되 면 **복제 사용** 을 선택 하 여 원본에서 대상 지역으로 SQL Server VM 복제를 시작 합니다.
1. 복구 자격 증명 모음으로 이동 하 여 복제 된 **항목** 을 선택 하 고 SQL Server VM **상태** 를 확인 하 여 복제 상태를 확인할 수 있습니다. **보호** 됨 상태는 복제가 완료 되었음을 나타냅니다. 

   ![복제 상태 확인](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>테스트 이동 프로세스
다음 단계에서는 Azure Site Recovery 사용 하 여 이동 프로세스를 테스트 하는 방법을 보여 줍니다. 

1. [Azure Portal](https://portal.azure.com) 에서 **Recovery Services 자격 증명 모음** 으로 이동 하 여 **복제 된 항목**을 선택 합니다. 
1. 이동 하려는 SQL Server VM를 선택 하 고 **복제 상태가** **정상** 으로 표시 되는지 확인 한 후 **테스트 장애 조치 (Failover)** 를 선택 합니다. 

   ![VM에 대 한 테스트 장애 조치](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. **테스트 장애 조치 (Failover)** 페이지에서 장애 조치 (failover)에 사용할 **최신 앱 일치** 복구 지점을 선택 합니다 .이는 SQL Server 데이터 일관성을 보장할 수 있는 유일한 스냅숏 유형입니다. 
1. **Azure 가상 네트워크** 에서 가상 네트워크를 선택 하 고 **확인** 을 선택 하 여 장애 조치 (failover)를 테스트 합니다. 
   
   >[!IMPORTANT]
   > 장애 조치 (failover) 테스트를 위해 별도의 Azure VM 네트워크를 사용 하는 것이 좋습니다. 복제 활성화 시 설정된 프로덕션 네트워크와 결과적으로 VM을 이동하려는 네트워크를 사용하지 마세요. 

1. 진행률을 모니터링 하려면 자격 증명 모음으로 이동 하 고, **모니터링**에서 **Site Recovery 작업** 을 선택 하 고, 진행 중인 **테스트 장애 조치 (failover)** 작업을 선택 합니다.

   ![장애 조치 (failover) 테스트 진행률 모니터링](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. 테스트가 완료 되 면 포털에서 **virtual machines** 로 이동 하 여 새로 만든 가상 머신을 검토 합니다. SQL Server VM 실행 중이 고, 적절 하 게 크기를 조정 하 고, 적절 한 네트워크에 연결 되어 있는지 확인 합니다. 
1. 장애 **조치 (failover) 옵션은** 장애 조치 (failover) 테스트 리소스가 정리 될 때까지 회색으로 표시 되기 때문에 테스트의 일부로 생성 된 VM을 삭제 합니다. 자격 증명 모음으로 다시 이동 하 여 **복제 된 항목**을 선택 하 고 SQL Server VM를 선택한 후 **테스트 장애 조치 (failover) 정리**를 선택 합니다. **메모** 섹션에서 테스트와 관련 된 관찰을 기록 하 고 저장 한 다음 테스트 완료 옆의 확인란을 선택 **합니다. 테스트 장애 조치 (failover) 가상 컴퓨터를 삭제**합니다. **확인** 을 선택 하 여 테스트 후 리소스를 정리 합니다. 

   ![장애 조치 (failover) 테스트 후 항목 정리](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>SQL Server VM 이동 
다음 단계에서는 SQL Server VM를 원본 지역에서 대상 지역으로 이동 하는 방법을 보여 줍니다. 

1. **Recovery Services** 자격 증명 모음으로 이동 하 여 **복제 된 항목**을 선택 하 고 VM을 선택한 다음 **장애 조치 (Failover)** 를 선택 합니다. 

   ![장애 조치 시작](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. **복구 지점**에서 **최신 앱 일치** 복구 지점을 선택 합니다. 
1. **장애 조치 (failover)를 시작 하기 전에 컴퓨터 종료**옆의 확인란을 선택 합니다. Site Recovery는 장애 조치 (failover)를 트리거하기 전에 원본 VM을 종료 하려고 시도 합니다. 종료가 실패 하더라도 장애 조치 (Failover)가 계속 됩니다. 
1. **확인** 을 선택 하 여 장애 조치 (failover)를 시작 합니다.
1. 이전 섹션에서 장애 조치 (failover) 테스트를 모니터링할 때 표시 된 것과 동일한 **Site Recovery 작업** 페이지에서 장애 조치 (failover) 프로세스를 모니터링할 수 있습니다. 
1. 작업이 완료 되 면 예상 대로 SQL Server VM 대상 지역에 표시 되는지 확인 합니다. 
1. 자격 증명 모음으로 다시 이동 하 고, **복제 된 항목**을 선택 하 고, SQL Server VM를 선택 하 고, **커밋** 을 선택 하 여 대상 지역으로 이동 프로세스를 완료 합니다. 커밋 작업이 완료될 때까지 기다립니다. 
1. SQL VM 리소스 공급자에 SQL Server VM를 등록 하 여 리소스 공급자와 관련 된 Azure Portal 및 기능에서 **sql 가상 컴퓨터** 관리 효율성을 사용 하도록 설정 합니다. 자세한 내용은 [SQL VM 리소스 공급자를 사용 하 여 SQL Server VM 등록](virtual-machines-windows-sql-register-with-rp.md)을 참조 하세요. 

  > [!WARNING]
  > SQL Server 데이터 일관성은 앱 일치 스냅숏으로만 보장 됩니다. SQL Server 장애 조치 (failover)는 충돌 복구 스냅숏으로 SQL Server 데이터 일관성을 보장할 수 없으므로 **최신 처리** 된 스냅숏은 장애 조치에 사용할 수 없습니다. 

## <a name="clean-up-source-resources"></a>원본 리소스 정리
청구 요금을 방지 하려면 자격 증명 모음에서 SQL Server VM를 제거 하 고 불필요 하 게 연결 된 리소스를 삭제 합니다. 

1. **Site Recovery** 자격 증명 모음으로 다시 이동 하 여 **복제 된 항목**을 선택 하 고 SQL Server VM를 선택 합니다. 
1. **복제 사용 안 함**을 선택합니다. 보호를 사용 하지 않도록 설정 하는 이유를 선택 하 고 **확인** 을 선택 하 여 복제를 해제 합니다. 

   >[!IMPORTANT]
   > Azure Site Recovery 복제에 대 한 요금이 부과 되지 않도록 하려면이 단계를 수행 하는 것이 중요 합니다. 

1. 원본 지역의 리소스를 다시 사용할 계획이 없는 경우 모든 관련 네트워크 리소스 및 해당 저장소 계정을 삭제 합니다. 


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server 가격 책정 가이드](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


