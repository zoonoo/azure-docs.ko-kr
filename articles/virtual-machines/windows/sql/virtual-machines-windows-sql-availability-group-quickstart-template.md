---
title: Azure 빠른 시작 템플릿을 사용 하 여 Azure VM에서 SQL Server에 대 한 Always On 가용성 그룹 구성
description: Azure 빠른 시작 템플릿을 사용 하 여 Windows 장애 조치 (failover) 클러스터를 만들고, 클러스터에 SQL Server Vm을 연결 하 고, 수신기를 만들고, Azure에서 내부 부하 분산 장치를 구성 합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f95d3487adecb17e0f4b79e81a08e16bafe4594f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855248"
---
# <a name="use-azure-quickstart-templates-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Azure 빠른 시작 템플릿을 사용 하 여 Azure VM에서 SQL Server에 대 한 Always On 가용성 그룹 구성
이 문서에서는 azure 빠른 시작 템플릿을 사용 하 여 Azure에서 SQL Server 가상 머신에 대 한 Always On 가용성 그룹 구성의 배포를 부분적으로 자동화 하는 방법을 설명 합니다. 이 프로세스에는 두 가지 Azure 빠른 시작 템플릿이 사용 됩니다. 

   | 템플릿 | Description |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Windows 장애 조치 (failover) 클러스터를 만들고 SQL Server Vm을이 클러스터에 조인 합니다. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | 가용성 그룹 수신기를 만들고 내부 부하 분산 장치를 구성 합니다. 이 템플릿은 Windows 장애 조치 (failover) 클러스터가 **101--ag-ag 설치** 템플릿으로 만들어진 경우에만 사용할 수 있습니다. |
   | &nbsp; | &nbsp; |

가용성 그룹 구성의 다른 부분을 수동으로 수행 해야 합니다. 예를 들어 가용성 그룹을 만들고 내부 부하 분산 장치를 만들 수 있습니다. 이 문서에서는 자동 및 수동 단계의 시퀀스를 제공합니다.
 

## <a name="prerequisites"></a>필수 구성 요소 
빠른 시작 템플릿을 사용 하 여 Always On 가용성 그룹의 설치를 자동화 하려면 다음 필수 구성 요소가 있어야 합니다. 
- [Azure 구독](https://azure.microsoft.com/free/).
- 도메인 컨트롤러를 포함하는 리소스 그룹 
- 동일한 가용성 집합 또는 가용성 영역에 있고 [SQL VM 리소스 공급자에 등록](virtual-machines-windows-sql-register-with-resource-provider.md)된 [SQL Server 2016 이상 Enterprise edition을 실행 하는 Azure의 도메인에](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 가입 된 하나 이상의 vm입니다.  
- 사용 가능한 두 개의 IP 주소: 내부 부하 분산 장치에 대 한 IP 주소 및 가용성 그룹의 동일한 서브넷 내 가용성 그룹 수신기에 대 한 하나입니다. 기존 부하 분산 장치를 사용 하는 경우 사용 가능한 IP 주소가 하나만 필요 합니다.  

## <a name="permissions"></a>사용 권한
Azure 빠른 시작 템플릿을 사용 하 여 Always On 가용성 그룹을 구성 하려면 다음 권한이 필요 합니다. 

- 도메인에 대 한 **컴퓨터 개체 만들기** 권한이 있는 기존 도메인 사용자 계정  예를 들어 도메인 관리자 계정에는 일반적으로 충분 한 권한 (예 account@domain.com:)이 있습니다. 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.
- SQL Server 서비스를 제어 하는 도메인 사용자 계정입니다. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>1단계: 빠른 시작 템플릿을 사용 하 여 장애 조치 (failover) 클러스터 만들기 및 클러스터에 SQL Server Vm 연결 
SQL Server Vm을 SQL VM 리소스 공급자에 등록 한 후에는 SQL Server Vm을 *Sqlvirtualmachinegroups*에 조인할 수 있습니다. 이 리소스는 Windows 장애 조치 (failover) 클러스터의 메타 데이터를 정의 합니다. 메타 데이터에는 버전, 버전, 정규화 된 도메인 이름, 클러스터와 SQL Server 서비스를 모두 관리 하기 위한 Active Directory 계정, 클라우드 감시로 저장소 계정이 포함 되어 있습니다. 

*SqlVirtualMachineGroups* 리소스 그룹에 SQL Server VM을 추가하면 Windows 장애 조치(failover) 클러스터 서비스가 클러스터를 만들도록 부트스트랩된 다음, 해당 SQL Server VM이 클러스터에 연결됩니다. 이 단계는 **101-sql-vm-ag-설치** 빠른 시작 템플릿을 사용 하 여 자동화 됩니다. 다음 단계를 사용 하 여 구현할 수 있습니다.

1. [**101-sql-vm-ag-설치**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) 빠른 시작 템플릿으로 이동 합니다. 그런 다음 **Azure에 배포** 를 선택 하 여 Azure Portal 빠른 시작 템플릿을 엽니다.
1. Windows 장애 조치 (failover) 클러스터에 대 한 메타 데이터를 구성 하는 데 필요한 필드를 입력 합니다. 선택적 필드는 비워 둘 수 있습니다.

   다음 표에서는 템플릿에 대 한 필수 값을 보여 줍니다. 

   | **필드** | 값 |
   | --- | --- |
   | **구독** |  SQL Server VM이 있는 구독입니다. |
   |**리소스 그룹** | SQL Server VM이 있는 리소스 그룹입니다. | 
   |**장애 조치(failover) 클러스터 이름** | 새 Windows 장애 조치 (failover) 클러스터에 사용할 이름입니다. |
   | **기존 VM 목록** | 가용성 그룹에 참여 하 고이 새 클러스터에 포함 하려는 SQL Server Vm입니다. 이러한 값은 쉼표와 공백으로 구분 합니다. 예를 들면 다음과 같습니다. *SQLVM1, SQLVM2*). |
   | **SQL Server 버전**: | SQL Server Vm의 SQL Server 버전입니다. 드롭다운 목록에서 선택 합니다. 현재 SQL Server 2016 및 SQL Server 2017 이미지만 지원 됩니다. |
   | **기존 정규화된 도메인 이름** | SQL Server VM이 있는 도메인의 기존 FQDN입니다. |
   | **기존 도메인 계정** | [CNO](/windows-server/failover-clustering/prestage-cluster-adds) 로 도메인에서 **컴퓨터 개체 만들기** 권한이 있는 기존 도메인 사용자 계정은 템플릿 배포 중에 생성 됩니다. 예를 들어 도메인 관리자 계정에는 일반적으로 충분 한 권한 (예 account@domain.com:)이 있습니다. 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.| 
   | **도메인 계정 암호** | 이전에 언급한 도메인 사용자 계정의 암호입니다. | 
   | **기존 SQL 서비스 계정** | 가용성 그룹 배포 중에 [SQL Server 서비스](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) 를 제어 하는 도메인 사용자 계정입니다 (예 account@domain.com:). |
   | **SQL 서비스 암호** | SQL Server 서비스를 제어하는 도메인 사용자 계정에서 사용하는 암호입니다. |
   | **클라우드 감시 이름** | 클라우드 감시에 만들고 사용 하는 새 Azure storage 계정입니다. 이 이름은 수정할 수 있습니다. |
   | **\_artifacts 위치** | 이 필드는 기본적으로 설정되어 있으며 수정하면 안 됩니다. |
   | **\_artifacts 위치 SAS 토큰** | 이 필드는 의도적으로 비어 있습니다. |
   | &nbsp; | &nbsp; |

1. 사용 약관에 동의 하는 경우 위에 명시 된 사용 **약관에 동의 함** 확인란을 선택 합니다. 그런 다음 **구매** 를 선택 하 여 빠른 시작 템플릿 배포를 완료 합니다. 
1. 배포를 모니터링 하려면 위쪽 탐색 배너의 **알림** 종 아이콘에서 배포를 선택 하거나 Azure Portal의 **리소스 그룹** 으로 이동 합니다. **설정**에서 **배포** 를 선택 하 고 **Microsoft. 템플릿** 배포를 선택 합니다. 

>[!NOTE]
> 템플릿 배포 중에 제공 된 자격 증명은 배포 기간 동안만 저장 됩니다. 배포가 완료 되 면 해당 암호가 제거 됩니다. 클러스터에 SQL Server Vm을 더 추가 하는 경우 다시 입력 하 라는 메시지가 표시 됩니다. 


## <a name="step-2-manually-create-the-availability-group"></a>2단계: 수동으로 가용성 그룹 만들기 
일반적으로 [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)또는 [transact-sql](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)을 사용 하 여 가용성 그룹을 수동으로 만듭니다. 

>[!IMPORTANT]
> 이 경우에는 4 단계에서 자동으로 **101-sql-vm-aglistener-설치** 퀵 스타트 템플릿이 수행 되기 때문에 수신기를 만들지 마세요. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>3단계: 수동으로 내부 부하 분산 장치 만들기
Always On 가용성 그룹 수신기에는 Azure Load Balancer의 내부 인스턴스가 필요 합니다. 내부 부하 분산 장치는 더 빠른 장애 조치 (failover) 및 다시 연결을 허용 하는 가용성 그룹 수신기에 대 한 "부동" IP 주소를 제공 합니다. 가용성 그룹의 SQL Server Vm이 동일한 가용성 집합의 일부인 경우 기본 부하 분산 장치를 사용할 수 있습니다. 그렇지 않으면 표준 부하 분산 장치를 사용 해야 합니다. 

> [!IMPORTANT]
> 내부 부하 분산 장치는 SQL Server VM 인스턴스와 동일한 가상 네트워크에 있어야 합니다. 

내부 부하 분산 장치를 만들기만 하면 됩니다. 4 단계에서 **101-sql-vm-aglistener-설치** 퀵 스타트 템플릿은 나머지 구성 (예: 백 엔드 풀, 상태 프로브 및 부하 분산 규칙)을 처리 합니다. 

1. Azure 포털에서 SQL Server 가상 머신을 포함하는 리소스 그룹을 엽니다. 
2. 리소스 그룹에서 **추가**를 선택 합니다.
3. **부하 분산 장치**를 검색합니다. 검색 결과에서 **Microsoft**에서 게시 하는 **Load Balancer**를 선택 합니다.
4. **Load Balancer** 블레이드에서 **만들기**를 선택 합니다.
5. **부하 분산 장치 만들기** 대화 상자에서 다음과 같이 부하 분산 장치를 구성합니다.

   | 설정 | 값 |
   | --- | --- |
   | **이름** |부하 분산 장치를 나타내는 텍스트 이름을 입력 합니다. 예를 들어 **Sqllb**를 입력 합니다. |
   | **형식** |**내부**: 대부분의 구현에서는 동일한 가상 네트워크 내에 있는 애플리케이션이 가용성 그룹에 연결할 수 있도록 하는 내부 부하 분산 장치를 사용합니다.  </br> **외부**: 응용 프로그램에서 공용 인터넷 연결을 통해 가용성 그룹에 연결할 수 있습니다. |
   | **가상 네트워크** | SQL Server 인스턴스가 있는 가상 네트워크를 선택합니다. |
   | **서브넷** | SQL Server 인스턴스가 있는 서브넷을 선택합니다. |
   | **IP 주소 할당** |**정적** |
   | **개인 IP 주소** | 서브넷에서 사용 가능한 IP 주소를 지정합니다. |
   | **구독** |구독이 여러 개인 경우 이 필드가 나타날 수 있습니다. 이 리소스와 연결할 구독을 선택합니다. 일반적으로 가용성 그룹에 대 한 모든 리소스와 동일한 구독입니다. |
   | **리소스 그룹** |SQL Server 인스턴스가 있는 리소스 그룹을 선택합니다. |
   | **위치** |SQL Server 인스턴스가 있는 Azure 위치를 선택합니다. |
   | &nbsp; | &nbsp; |

6. **만들기**를 선택합니다. 


>[!IMPORTANT]
> 각 SQL Server VM에 대 한 공용 IP 리소스에 표준 부하 분산 장치와 호환 되는 표준 SKU가 있어야 합니다. VM의 공용 IP 리소스의 SKU를 확인 하려면 **리소스 그룹**으로 이동 하 여 SQL Server VM에 대 한 **공용 ip 주소** 리소스를 선택 하 고 **개요** 창의 **SKU** 아래에서 값을 찾습니다. 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>4단계: 가용성 그룹 수신기 만들기 및 빠른 시작 템플릿을 사용 하 여 내부 부하 분산 장치 구성

가용성 그룹 수신기를 만들고 **101-sql-vm-aglistener-설치** 빠른 시작 템플릿을 사용 하 여 내부 부하 분산 장치를 자동으로 구성 합니다. 템플릿은 SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener 리소스를 프로 비전 합니다. SQL VM 리소스 공급자를 통해 **101-sql-vm-aglistener-setup** 빠른 시작 템플릿은 다음 작업을 수행합니다.

- 배포 중에 제공한 IP 주소 값을 기준으로, 수신기에 대한 새 프런트 엔드 IP 리소스를 만듭니다. 
- 클러스터 및 내부 부하 분산 장치에 대 한 네트워크 설정을 구성 합니다. 
- 내부 부하 분산 장치, 상태 프로브 및 부하 분산 규칙에 대 한 백 엔드 풀을 구성 합니다.
- 지정 된 IP 주소와 이름을 사용 하 여 가용성 그룹 수신기를 만듭니다.
 
>[!NOTE]
> Windows 장애 조치 (failover) 클러스터를 101-101- **ag-ag 설정** 템플릿으로 만든 경우에만 **-sql-v m a m** -i-i-i-i-i-i-i n a m
   
   
내부 부하 분산 장치를 구성 하 고 가용성 그룹 수신기를 만들려면 다음을 수행 합니다.
1. [101-sql-vm-aglistener-설치](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) 빠른 시작 템플릿으로 이동 하 고 **Azure에 배포** 를 선택 하 여 Azure Portal에서 빠른 시작 템플릿을 시작 합니다.
1. 필수 필드를 입력 하 여 내부 부하 분산 장치를 구성 하 고 가용성 그룹 수신기를 만듭니다. 선택적 필드는 비워 둘 수 있습니다. 

   다음 표에서는 템플릿에 대 한 필수 값을 보여 줍니다. 

   | **필드** | 값 |
   | --- | --- |
   |**리소스 그룹** | SQL Server VM 및 가용성 그룹이 있는 리소스 그룹입니다. | 
   |**기존 장애 조치(failover) 클러스터 이름** | SQL Server VM이 연결된 클러스터의 이름입니다. |
   | **기존 SQL 가용성 그룹**| SQL Server VM이 속한 가용성 그룹의 이름입니다. |
   | **기존 VM 목록** | 이전에 언급한 가용성 그룹에 속한 SQL Server VM의 이름입니다. 이름을 쉼표와 공백으로 구분 합니다. 예를 들면 다음과 같습니다. *SQLVM1, SQLVM2*). |
   | **수신기** | 수신기에 할당 하려는 DNS 이름입니다. 기본적으로이 템플릿은 "aglistener" 라는 이름을 지정 하지만 변경할 수 있습니다. 이름은 15자를 초과할 수 없습니다. |
   | **수신기 포트** | 수신기에서 사용 하려는 포트입니다. 일반적으로이 포트의 기본값은 1433입니다. 템플릿에서 지정 하는 포트 번호입니다. 그러나 기본 포트가 변경 된 경우 수신기 포트에서 해당 값을 대신 사용 해야 합니다. | 
   | **수신기 IP** | 수신기에서 사용할 IP 주소입니다. 이 주소는 템플릿 배포 중에 생성 되므로 아직 사용 하지 않는 항목을 제공 합니다.  |
   | **기존 서브넷** | SQL Server Vm의 내부 서브넷 이름입니다 (예: *기본값*). **리소스 그룹**으로 이동 하 고, 가상 네트워크를 선택 하 고, **설정** 창에서 **서브넷** 을 선택 하 고, **이름**아래에 값을 복사 하 여이 값을 확인할 수 있습니다. |
   | **기존 내부 Load Balancer** | 3 단계에서 만든 내부 부하 분산 장치의 이름입니다. |
   | **프로브 포트** | 내부 부하 분산 장치에서 사용할 프로브 포트입니다. 템플릿은 기본적으로 59999를 사용 하지만이 값을 변경할 수 있습니다. |
   | &nbsp; | &nbsp; |

1. 사용 약관에 동의 하는 경우 위에 명시 된 사용 **약관에 동의 함** 확인란을 선택 합니다. **구매** 를 선택 하 여 빠른 시작 템플릿 배포를 완료 합니다. 
1. 배포를 모니터링 하려면 위쪽 탐색 배너의 **알림** 종 아이콘에서 배포를 선택 하거나 Azure Portal의 **리소스 그룹** 으로 이동 합니다. **설정**에서 **배포** 를 선택 하 고 **Microsoft. 템플릿** 배포를 선택 합니다. 

>[!NOTE]
>배포가 중간에 실패 하는 경우 **101-sql-vm-aglistener-설치** 빠른 시작 템플릿을 다시 배포 하기 전에 PowerShell을 사용 하 여 [새로 만든 수신기](#remove-the-availability-group-listener) 를 수동으로 제거 해야 합니다. 

## <a name="remove-the-availability-group-listener"></a>가용성 그룹 수신기 제거
나중에 템플릿에서 구성 된 가용성 그룹 수신기를 제거 해야 하는 경우에는 SQL VM 리소스 공급자를 통해 이동 해야 합니다. 수신기는 SQL VM 리소스 공급자를 통해 등록 되기 때문에 SQL Server Management Studio를 통해 삭제 하는 것 만으로는 충분 하지 않습니다. 

가장 좋은 방법은 PowerShell에서 다음 코드 조각을 사용 하 여 SQL VM 리소스 공급자를 통해 삭제 하는 것입니다. 이렇게 하면 SQL VM 리소스 공급자에서 가용성 그룹 수신기 메타 데이터가 제거 됩니다. 또한 가용성 그룹에서 수신기를 물리적으로 삭제 합니다. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>일반 오류
이 섹션에서는 몇 가지 알려진 문제와 가능한 해결 방법을 설명합니다. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>가용성 그룹 ‘\<AG-Name>’의 가용성 그룹 수신기가 이미 있음
가용성 그룹 수신기에 대 한 Azure 빠른 시작 템플릿에 사용 된 선택한 가용성 그룹에 이미 수신기가 포함 되어 있습니다. 실제로 가용성 그룹 내에 있거나 해당 메타 데이터가 SQL VM 리소스 공급자 내에 유지 됩니다. **101-sql-vm-aglistener-설치** 빠른 시작 템플릿을 다시 배포 하기 전에 [PowerShell](#remove-the-availability-group-listener) 을 사용 하 여 수신기를 제거 합니다. 

### <a name="connection-only-works-from-primary-replica"></a>연결이 주 복제본에서만 작동함
이 동작은 내부 부하 분산 장치의 구성을 일관 되지 않은 상태로 유지 하는 실패 한 **101-v m-v m** i-i-i-i-i-i-i-i-a 수신기-설치 템플릿 배포에서 백 엔드 풀에 가용성 집합이 나열되는지, 상태 프로브 및 부하 분산 규칙에 대한 규칙이 있는지 확인합니다. 누락 된 항목이 있으면 내부 부하 분산 장치의 구성이 일관 되지 않은 상태입니다. 

이 동작을 해결 하려면 [PowerShell](#remove-the-availability-group-listener)을 사용 하 여 수신기를 제거 하 고, Azure Portal를 통해 내부 부하 분산 장치를 삭제 하 고, 3 단계에서 다시 시작 합니다. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - SQL 가상 머신 목록만 업데이트할 수 있습니다.
이 오류는 SQL Server Management Studio (SSMS)를 통해 수신기를 삭제 했지만 SQL VM 리소스 공급자에서 삭제 하지 않은 경우 **101---v m a m-i-v 수신기-설치** 템플릿을 배포 하는 경우에 발생할 수 있습니다. SSMS를 통해 수신기를 삭제 해도 SQL VM 리소스 공급자에서 수신기의 메타 데이터는 제거 되지 않습니다. [PowerShell](#remove-the-availability-group-listener)을 통해 리소스 공급자에서 수신기를 삭제 해야 합니다. 

### <a name="domain-account-does-not-exist"></a>도메인 계정이 없음
이 오류에는 두 가지 원인이 있을 수 있습니다. 지정 된 도메인 계정이 없거나 [UPN (사용자 계정 이름)](/windows/desktop/ad/naming-properties#userprincipalname) 데이터가 누락 되었습니다. **101-sql-ag-ag 설치** 템플릿은 UPN 형식의 도메인 계정 (즉, *user@domain.com* )을 예상 하지만 일부 도메인 계정에는 누락 된 도메인 계정이 있을 수 있습니다. 이는 일반적으로 로컬 사용자가 서버를 도메인 컨트롤러로 수준을 올린 경우 또는 PowerShell을 통해 만들어진 경우에 발생 합니다. 

계정이 있는지 확인 합니다. 이 경우 두 번째 상황에서 실행 중일 수 있습니다. 이 문제를 해결 하려면 다음을 수행 합니다.

1. 도메인 컨트롤러에서 **서버 관리자**의 **도구** 옵션을 통해 **Active Directory 사용자 및 컴퓨터** 창을 엽니다. 
2. 왼쪽 창에서 **사용자** 를 선택 하 여 계정으로 이동 합니다.
3. 계정을 마우스 오른쪽 단추로 클릭 하 고 **속성**을 선택 합니다.
4. **계정** 탭을 선택 합니다. **사용자 로그온 이름** 상자가 비어 있으면 오류의 원인이 됩니다. 

    ![비어 있는 사용자 계정은 UPN 누락을 나타냅니다.](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. 사용자 **로그온 이름** 상자에 사용자 이름과 일치 하는 항목을 입력 하 고 드롭다운 목록에서 적절 한 도메인을 선택 합니다. 
6. **적용**을 선택하여 변경 내용을 저장한 다음, **확인**을 선택하여 대화 상자를 닫습니다. 

이러한 변경을 수행한 후에는 Azure 빠른 시작 템플릿을 한 번 더 배포 해 보세요. 



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [SQL Server Vm 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Vm에 대 한 FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server Vm에 대 한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server Vm에 대 한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [SQL Server VM에 대한 라이선스 모델 전환](virtual-machines-windows-sql-ahb.md)



