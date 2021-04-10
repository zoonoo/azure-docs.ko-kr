---
title: Linux 기반 Azure Virtual Machines에서 실행되는 DH2i DxEnterprise를 사용하여 Always On 가용성 그룹 설정
description: DH2i DxEnterprise를 클러스터 관리자로 사용하여 Linux Azure Virtual Machines의 SQL Server에서 가용성 그룹을 통해 고가용성을 달성합니다.
ms.date: 03/04/2021
ms.service: virtual-machines-linux
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 07752eb5c7f18a8952c43e77afed78b06432aca6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568539"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>자습서 - Linux 기반 Azure Virtual Machines에서 실행되는 DH2i DxEnterprise를 사용하여 3개 노드 Always On 가용성 그룹 설정

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 자습서에서는 Linux 기반 Azure VMs(Virtual Machines)에서 실행되는 DH2i DxEnterprise를 사용하여 SQL Server Always On 가용성 그룹을 구성하는 방법에 대해 설명합니다. 

DxEnterprise에 대한 자세한 내용은 [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/)를 참조하세요.

> [!NOTE]
> Microsoft에서는 데이터 이동, 가용성 그룹, SQL Server 구성 요소를 지원합니다. 클러스터 및 쿼럼 관리에 대한 DH2i DxEnterprise 클러스터 설명서와 관련된 지원은 DH2i에 문의하세요.
 

이 자습서는 다음 단계로 구성됩니다.

> [!div class="checklist"]
> * SQL Server를 가용성 그룹에 포함될 모든 Azure VM(가상 머신)에 설치합니다.
> * DxEnterprise를 모든 VM에 설치하고, DxEnterprise 클러스터를 구성합니다.
> * 장애 조치(failover) 지원 및 고가용성을 제공할 가상 호스트를 만들고, 가용성 그룹 및 데이터베이스를 가용성 그룹에 추가합니다.
> * 가용성 그룹 수신기에 대한 내부 Azure 부하 분산 장치를 만듭니다(선택 사항).
> * 수동 또는 자동 장애 조치(failover)를 수행합니다.

이 자습서에서는 [DxAdmin 클라이언트 UI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/)를 사용하여 DxEnterprise 클러스터를 설정합니다. 필요에 따라 [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) 명령줄 인터페이스를 사용하여 클러스터를 설정할 수도 있습니다. 이 예에서는 4개의 VM을 사용했습니다. 이러한 VM 중 3개는 Ubuntu 18.04를 실행하며 3개 노드 클러스터에 포함됩니다. 네 번째 VM은 DxAdmin 도구를 통해 Windows 10을 실행하여 클러스터를 관리하고 구성합니다.

## <a name="prerequisites"></a>전제 조건

- Azure에서 4개의 VM을 만듭니다. Linux 기반 가상 머신을 만들려면 [빠른 시작: Azure Portal에서 Linux 가상 머신 만들기](../../../virtual-machines/linux/quick-create-portal.md) 문서를 따르세요. 마찬가지로 Windows 기반 가상 머신을 만들려면 [빠른 시작: Azure Portal에서 Windows 가상 머신 만들기](../../../virtual-machines/windows/quick-create-portal.md) 문서를 따르세요.
- .NET 3.1을 클러스터에 포함될 모든 Linux 기반 VM에 설치합니다. 선택한 Linux 운영 체제에 따라 [여기](/dotnet/core/install/linux)에 설명된 지침을 따르세요.
- 가용성 그룹 관리 기능을 사용하도록 설정된 유효한 DxEnterprise 라이선스가 필요합니다. 평가판을 얻을 수 있는 방법에 대한 자세한 내용은 [DxEnterprise 평가판](https://dh2i.com/trial/)을 참조하세요.

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>가용성 그룹에 포함될 모든 Azure VM에 SQL Server 설치

이 자습서에서는 가용성 그룹을 실행하는 3개 노드 Linux 기반 클러스터를 설정합니다. Linux 플랫폼의 선택에 따라 [Linux에 SQL Server 설치](/sql/linux/sql-server-linux-overview#install) 설명서를 따르세요. 또한 이 자습서를 위해 [SQL Server 도구](/sql/linux/sql-server-linux-setup-tools)를 설치하는 것이 좋습니다.
 
> [!NOTE]
> 선택한 Linux OS가 [DH2i DxEnterprise(최소 시스템 요구 사항 섹션 참조)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) 및 [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms) 모두에서 지원하는 공통 배포인지 확인하세요.
>
> 이 예에서는 DH2i DxEnterprise 및 Microsoft SQL Server 모두에서 지원하는 Ubuntu 18.04를 사용합니다.

이 자습서에서는 SQL Server를 Windows VM에 설치하지 않습니다. 이 노드는 클러스터에 포함되지 않고 DxAdmin을 사용하여 클러스터를 관리하는 데만 사용되기 때문입니다.

이 단계가 완료되면 SQL Server 및 [SQL Server 도구](/sql/linux/sql-server-linux-setup-tools)(선택 사항)가 가용성 그룹에 참가하는 세 개의 Linux 기반 VM 모두에 설치되어 있어야 합니다.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>모든 VM에 DxEnterprise 설치 및 클러스터 구성

이 단계에서는 Linux용 DH2i DxEnterprise를 세 개의 Linux VM에 설치합니다. 다음 표에서는 각 서버가 클러스터에서 수행하는 역할에 대해 설명합니다.

| VM 수 | DH2i DxEnterprise 역할 | Microsoft SQL Server 가용성 그룹 복제본 역할 |
|--|--|--|
| 1 | 클러스터 노드 - Linux 기반 | 기본 |
| 1 | 클러스터 노드 - Linux 기반 | 보조 - 동기 커밋 |
| 1 | 클러스터 노드 - Linux 기반 | 보조 - 동기 커밋 |
| 1 | DxAdmin 클라이언트 | 해당 없음 |


DxEnterprise를 세 개의 Linux 기반 노드에 설치하려면 선택한 Linux 운영 체제에 따라 DH2i DxEnterprise 설명서를 따르세요. 아래에 나열된 방법 중 하나를 사용하여 DxEnterprise를 설치합니다.

- **Ubuntu**
    - [리포지토리 설치 빠른 시작 가이드](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [확장 빠른 시작 가이드](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Marketplace 이미지 빠른 시작 가이드](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [리포지토리 설치 빠른 시작 가이드](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [확장 빠른 시작 가이드](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Marketplace 이미지 빠른 시작 가이드](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

DxAdmin 클라이언트 도구만 Windows VM에 설치하려면 [DxAdmin 클라이언트 UI 빠른 시작 가이드](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/)를 따르세요.

이 단계가 완료되면 DxEnterprise 클러스터가 Linux VM에 만들어지고, DxAdmin 클라이언트가 Windows 클라이언트 컴퓨터에 설치되어 있어야 합니다. 

> [!NOTE]
> [여기](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes)서 설명한 대로 노드 중 하나가 *구성 전용 모드* 로 추가되는 3개 노드 클러스터를 만들어 자동 장애 조치(failover)를 사용하도록 설정할 수도 있습니다. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>장애 조치(failover) 지원 및 고가용성을 제공하는 가상 호스트 만들기

이 단계에서는 DxAdmin UI를 사용하여 가상 호스트, 가용성 그룹을 만든 다음, 데이터베이스를 추가합니다.   

> [!NOTE]
> 이 단계를 수행하는 동안 Always On을 사용하도록 설정하기 위해 SQL Server 인스턴스가 다시 시작됩니다. 

DxAdmin을 실행하는 Windows 클라이언트 컴퓨터에 연결하여 위 단계에서 만든 클러스터에 연결합니다. [DxAdmin을 사용하는 MSSQL 가용성 그룹](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/)에서 설명하는 단계에 따라 Always On을 사용하도록 설정하고 가상 호스트 및 가용성 그룹을 만듭니다. 

> [!TIP]
> 데이터베이스를 추가하기 전에 데이터베이스가 SQL Server의 주 인스턴스에 만들어지고 백업되었는지 확인하세요.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>수신기에 대한 내부 Azure 부하 분산 장치 만들기(선택 사항)

이 선택적 단계에서는 가용성 그룹 수신기에 대한 IP 주소를 보유하는 Azure 부하 분산 장치를 만들고 구성할 수 있습니다. Azure Load Balancer에 대한 자세한 내용은 [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)를 참조하세요. DxAdmin을 사용하여 Azure 부하 분산 장치 및 가용성 그룹 수신기를 구성하려면 DxEnterprise [Azure Load Balancer 빠른 시작 가이드](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/)를 따르세요.

이 단계가 완료되면 가용성 그룹 수신기가 만들어지고 내부 Azure 부하 분산 장치에 매핑됩니다.

## <a name="test-manual-or-automatic-failover"></a>수동 또는 자동 장애 조치(failover) 테스트

자동 장애 조치(failover) 테스트의 경우 계속하여 주 복제본을 해제할 수 있습니다(Azure Portal에서 가상 머신 전원 끄기). 이 경우 주 노드의 갑작스러운 비가용성이 복제됩니다. 예상되는 동작은 다음과 같습니다.
- 클러스터 관리자가 가용성 그룹의 보조 복제본 중 하나를 주 복제본으로 승격합니다.
- 장애가 발생한 주 복제본은 백업 후 자동으로 클러스터에 조인됩니다. 클러스터 관리자가 이를 보조 복제본으로 승격합니다.

 
또한 아래에서 설명하는 단계에 따라 수동 장애 조치(failover)를 수행할 수 있습니다.

1. DxAdmin을 통해 클러스터에 연결합니다.   
1. 가용성 그룹에 대한 가상 호스트를 확장합니다.
1. 마우스 오른쪽 단추로 대상 노드/보조 복제본을 클릭하고, **멤버에서 호스팅 시작** 을 선택하여 장애 조치(failover)를 시작합니다. 

DxEnterprise 내의 추가 작업에 대한 자세한 내용은 [DxEnterprise 관리자 가이드](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) 및 [DxEnterprise DxCLI 가이드](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Linux의 가용성 그룹](/sql/linux/sql-server-linux-availability-group-overview)에 대해 자세히 알아보기
- [빠른 시작: Azure Portal에서 Linux 가상 머신 만들기](../../../virtual-machines/linux/quick-create-portal.md)
- [빠른 시작: Azure 포털에서 Windows 가상 머신 만들기](../../../virtual-machines/windows/quick-create-portal.md)
- [Linux에서 SQL Server 2019에 지원되는 플랫폼](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)