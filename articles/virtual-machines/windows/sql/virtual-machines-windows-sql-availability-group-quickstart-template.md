---
title: 가용성 그룹 구성(Azure 빠른 시작 템플릿)
description: Azure 퀵스타트 템플릿을 사용하여 Windows 장애 조치 클러스터를 만들고, SQL Server VM을 클러스터에 조인하고, 수신기를 만들고, Azure에서 내부 로드 밸런서를 구성합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022373"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Azure 빠른 시작 템플릿을 사용하여 Azure VM에서 SQL Server에 대한 가용성 그룹을 구성합니다.
이 문서에서는 Azure 빠른 시작 템플릿을 사용하여 Azure의 SQL Server 가상 시스템에 대한 Always On 가용성 그룹 구성의 배포를 부분적으로 자동화하는 방법을 설명합니다. 이 프로세스에는 두 개의 Azure 빠른 시작 템플릿이 사용됩니다. 

   | 템플릿 | 설명 |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Windows 장애 조치 클러스터를 만들고 SQL Server VM을 조인합니다. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | 가용성 그룹 리스너를 만들고 내부 로드 밸러워기를 구성합니다. 이 템플릿은 Windows 장애 조치 클러스터가 **101 sql-vm-ag-setup** 템플릿으로 만들어진 경우에만 사용할 수 있습니다. |
   | &nbsp; | &nbsp; |

가용성 그룹 구성의 다른 부분은 가용성 그룹을 만들고 내부 로드 밸러버를 만드는 등 수동으로 수행해야 합니다. 이 문서에서는 자동 및 수동 단계의 시퀀스를 제공합니다.
 

## <a name="prerequisites"></a>사전 요구 사항 
빠른 시작 템플릿을 사용하여 Always On 가용성 그룹의 설정을 자동화하려면 다음 필수 구성 조건이 있어야 합니다. 
- [Azure 구독](https://azure.microsoft.com/free/).
- 도메인 컨트롤러를 포함하는 리소스 그룹 
- [SQL VM 리소스 공급자에 등록된](virtual-machines-windows-sql-register-with-resource-provider.md)동일한 가용성 집합 또는 가용성 영역에 있는 Azure에서 하나 이상의 도메인 에 가입한 [VM이 SQL Server 2016(또는 이후) 엔터프라이즈 에디션입니다.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)  
- 사용 가능한 두 개의 IP 주소(엔터티에서 사용되지 않음) IP 주소는 내부 로드 밸로드커용이고 하나는 가용성 그룹과 동일한 서브넷 내의 가용성 그룹 리스너에 대한 주소입니다. 기존 로드 밸런서를 사용하는 경우 사용 가능한 IP 주소가 하나만 필요합니다.  

## <a name="permissions"></a>사용 권한
Azure 빠른 시작 템플릿을 사용하여 항상 사용 가능 여부 그룹을 구성하는 데 다음 권한이 필요합니다. 

- 도메인에 컴퓨터 개체 **만들기** 권한이 있는 기존 도메인 사용자 계정입니다.  예를 들어 도메인 관리자 계정에는 일반적으로 충분한 account@domain.com권한이 있습니다(예: ). 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.__
- SQL Server 서비스를 제어하는 도메인 사용자 계정입니다. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>1단계: 빠른 시작 템플릿을 사용하여 장애 조치 클러스터를 만들고 SQL Server VM을 클러스터에 조인합니다. 
SQL Server VM이 SQL VM 리소스 공급자에 등록된 후 SQL Server VM을 *SqlVirtualMachineGroups*에 조인할 수 있습니다. 이 리소스는 Windows 장애 조치 클러스터의 메타데이터를 정의합니다. 메타데이터에는 버전, 에디션, 정규화된 도메인 이름, 클러스터와 SQL Server 서비스를 모두 관리하는 Active Directory 계정 및 클라우드 감시자로 있는 저장소 계정이 포함됩니다. 

*SqlVirtualMachineGroups* 리소스 그룹에 SQL Server VM을 추가하면 Windows 장애 조치(failover) 클러스터 서비스가 클러스터를 만들도록 부트스트랩된 다음, 해당 SQL Server VM이 클러스터에 연결됩니다. 이 단계는 **101 sql-vm-ag-설치** 퀵스타트 템플릿으로 자동화됩니다. 다음 단계를 사용하여 구현할 수 있습니다.

1. [**101-sql-vm-ag-설치 퀵스타트**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) 템플릿으로 이동합니다. 그런 다음 **Azure에 배포를** 선택하여 Azure 포털에서 빠른 시작 템플릿을 엽니다.
1. Windows 장애 조치 클러스터에 대한 메타데이터를 구성하는 데 필요한 필드를 채웁니다. 선택적 필드를 비워 둘 수 있습니다.

   다음 표에서는 템플릿에 필요한 값을 보여 주며 있습니다. 

   | **필드** | 값 |
   | --- | --- |
   | **구독** |  SQL Server VM이 있는 구독입니다. |
   |**리소스 그룹** | SQL Server VM이 있는 리소스 그룹입니다. | 
   |**장애 조치(failover) 클러스터 이름** | 새 Windows 장애 조치 클러스터에 대해 원하는 이름입니다. |
   | **기존 VM 목록** | 가용성 그룹에 참여하고 이 새 클러스터의 일부가 되려는 SQL Server VM입니다. 이러한 값을 쉼표와 공백으로 구분합니다(예: *SQLVM1, SQLVM2).* |
   | **SQL Server 버전**: | SQL Server VM의 SQL 서버 버전입니다. 드롭다운 목록에서 선택합니다. 현재 SQL Server 2016 및 SQL Server 2017 이미지만 지원됩니다. |
   | **기존 정규화된 도메인 이름** | SQL Server VM이 있는 도메인의 기존 FQDN입니다. |
   | **기존 도메인 계정** | [CNO가](/windows-server/failover-clustering/prestage-cluster-adds) 템플릿 배포 중에 생성될 때 도메인에 **컴퓨터 개체 만들기** 권한이 있는 기존 도메인 사용자 계정입니다. 예를 들어 도메인 관리자 계정에는 일반적으로 충분한 account@domain.com권한이 있습니다(예: ). 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.**| 
   | **도메인 계정 암호** | 이전에 언급한 도메인 사용자 계정의 암호입니다. | 
   | **기존 SQL 서비스 계정** | 가용성 그룹 배포 중에 [SQL Server 서비스를](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) 제어하는 도메인 사용자 account@domain.com계정입니다(예: ). |
   | **SQL 서비스 암호** | SQL Server 서비스를 제어하는 도메인 사용자 계정에서 사용하는 암호입니다. |
   | **클라우드 감시 이름** | 클라우드 미러링 모니터 서버에서 만들어지고 사용할 새 Azure 저장소 계정입니다. 이 이름을 수정할 수 있습니다. |
   | **\_아티팩트 위치** | 이 필드는 기본적으로 설정되어 있으며 수정하면 안 됩니다. |
   | **\_아티팩트 위치 Sas 토큰** | 이 필드는 의도적으로 비워 둡습니다. |
   | &nbsp; | &nbsp; |

1. 이용 약관에 동의하는 경우 위에 명시된 이용 약관에 **동의함을** 선택합니다. 그런 다음 **구매를** 선택하여 빠른 시작 템플릿의 배포를 완료합니다. 
1. 배포를 모니터링하려면 상단 탐색 배너의 **알림** 벨 아이콘에서 배포를 선택하거나 Azure 포털의 **리소스 그룹으로** 이동합니다. **설정에서**배포를 선택하고 **Microsoft.Template** 배포를 **선택합니다.** 

>[!NOTE]
> 템플릿 배포 중에 제공된 자격 증명은 배포 기간에 대해서만 저장됩니다. 배포가 완료되면 해당 암호가 제거됩니다. 클러스터에 SQL Server VM을 더 추가하면 다시 제공하라는 메시지가 표시됩니다. 


## <a name="step-2-manually-create-the-availability-group"></a>2단계: 가용성 그룹을 수동으로 만듭니다. 
[SQL Server 관리 Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)또는 [Transact-SQL을](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)사용하여 평소와 같이 가용성 그룹을 수동으로 만듭니다. 

>[!IMPORTANT]
> **101 sql-vm-aglistener-설정 퀵스타트** 템플릿이 4단계에서 자동으로 수행되므로 이 때리스어를 *만들지* 마십시오. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>3단계: 내부 부하 분산기 수동으로 작성
Always On 가용성 그룹 수신기에는 Azure 로드 밸런서의 내부 인스턴스가 필요합니다. 내부 로드 밸런서에서는 가용성 그룹 리스너에게 "부동" IP 주소를 제공하여 장애 조치 및 재연결 속도를 높입니다. 가용성 그룹의 SQL Server VM이 동일한 가용성 집합의 일부인 경우 기본 로드 밸런서를 사용할 수 있습니다. 그렇지 않으면 표준 로드 밸러버를 사용해야 합니다. 

> [!IMPORTANT]
> 내부 로드 밸러블러는 SQL Server VM 인스턴스와 동일한 가상 네트워크에 있어야 합니다. 

내부 로드 밸러블러를 작성하기만 하면 됩니다. 4단계에서 **101sql-vm-aglistener-설정 퀵스타트** 템플릿은 나머지 구성(예: 백 엔드 풀, 상태 프로브 및 부하 분산 규칙)을 처리합니다. 

1. Azure 포털에서 SQL Server 가상 머신을 포함하는 리소스 그룹을 엽니다. 
2. 리소스 그룹에서 **추가를**선택합니다.
3. **부하 분산 장치**를 검색합니다. 검색 결과에서 **Microsoft에서**게시한 **로드 밸러서를**선택합니다.
4. 로드 **밸러저** 블레이드에서 **만들기를**선택합니다.
5. **부하 분산 장치 만들기** 대화 상자에서 다음과 같이 부하 분산 장치를 구성합니다.

   | 설정 | 값 |
   | --- | --- |
   | **이름** |로드 밸러버를 나타내는 텍스트 이름을 입력합니다. 예를 들어 **sqlLB를**입력합니다. |
   | **유형** |**내부**: 대부분의 구현에서는 내부 부하 분산 장치를 사용하며, 동일한 가상 네트워크 내에 있는 애플리케이션은 가용성 그룹에 연결할 수 있습니다.  </br> **외부**: 응용 프로그램이 공용 인터넷 연결을 통해 가용성 그룹에 연결할 수 있습니다. |
   | **가상 네트워크** | SQL Server 인스턴스가 있는 가상 네트워크를 선택합니다. |
   | **서브넷** | SQL Server 인스턴스가 있는 서브넷을 선택합니다. |
   | **IP 주소 할당** |**정적** |
   | **개인 IP 주소** | 서브넷에서 사용 가능한 IP 주소를 지정합니다. |
   | **구독** |구독이 여러 개인 경우 이 필드가 나타날 수 있습니다. 이 리소스와 연결할 구독을 선택합니다. 일반적으로 가용성 그룹에 대한 모든 리소스와 동일한 구독입니다. |
   | **리소스 그룹** |SQL Server 인스턴스가 있는 리소스 그룹을 선택합니다. |
   | **위치** |SQL Server 인스턴스가 있는 Azure 위치를 선택합니다. |
   | &nbsp; | &nbsp; |

6. **만들기**를 선택합니다. 


>[!IMPORTANT]
> 각 SQL Server VM의 공용 IP 리소스에는 표준 로드 밸런서와 호환되는 표준 SKU가 있어야 합니다. VM의 공용 IP 리소스의 SKU를 확인하려면 **리소스 그룹으로**이동하여 SQL Server VM에 대한 공용 IP **주소** 리소스를 선택하고 **개요** 창에서 **SKU** 아래의 값을 찾습니다. 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>4단계: 퀵스타트 템플릿을 사용하여 가용성 그룹 리스너를 만들고 내부 로드 밸러블러를 구성합니다.

가용성 그룹 리스너를 만들고 **101sql-vm-aglistener-설정 퀵스타트** 템플릿을 사용하여 내부 로드 밸러서를 자동으로 구성합니다. 템플릿은 Microsoft.SqlVirtualMachine/SqlVirtualMachineGroup/가용성그룹 Listener 리소스를 프로비전합니다. SQL VM 리소스 공급자를 통해 **101-sql-vm-aglistener-setup** 빠른 시작 템플릿은 다음 작업을 수행합니다.

- 배포 중에 제공한 IP 주소 값을 기준으로, 수신기에 대한 새 프런트 엔드 IP 리소스를 만듭니다. 
- 클러스터 및 내부 로드 밸런서에 대한 네트워크 설정을 구성합니다. 
- 내부 로드 밸런서, 상태 프로브 및 부하 분산 규칙에 대한 백 엔드 풀을 구성합니다.
- 지정된 IP 주소와 이름을 가진 가용성 그룹 리스너를 만듭니다.
 
>[!NOTE]
> Windows 장애 조치 클러스터가 101 sql-vm-ag-setup 템플릿을 사용하여 만들어진 경우에만 **101sql-vm-aglistener-설치를** 사용할 수 있습니다. **101-sql-vm-aglistener-setup**
   
   
내부 로드 밸러블러를 구성하고 가용성 그룹 리스너를 만들려면 다음을 수행합니다.
1. [101sql-vm-aglistener-설치 퀵스타트](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) 템플릿으로 이동하여 **Azure에 배포를** 선택하여 Azure 포털에서 빠른 시작 템플릿을 시작합니다.
1. 내부 로드 밸러블러를 구성하는 데 필요한 필드를 작성하고 가용성 그룹 리스너를 만듭니다. 선택적 필드를 비워 둘 수 있습니다. 

   다음 표에서는 템플릿에 필요한 값을 보여 주며 있습니다. 

   | **필드** | 값 |
   | --- | --- |
   |**리소스 그룹** | SQL Server VM 및 가용성 그룹이 있는 리소스 그룹입니다. | 
   |**기존 장애 조치(failover) 클러스터 이름** | SQL Server VM이 연결된 클러스터의 이름입니다. |
   | **기존 SQL 가용성 그룹**| SQL Server VM이 속한 가용성 그룹의 이름입니다. |
   | **기존 VM 목록** | 이전에 언급한 가용성 그룹에 속한 SQL Server VM의 이름입니다. 쉼표와 공백으로 이름을 구분합니다(예: *SQLVM1, SQLVM2).* |
   | **수신기** | 수신기에 할당할 DNS 이름입니다. 기본적으로 이 템플릿은 "aglistener"라는 이름을 지정하지만 변경할 수 있습니다. 이름은 15자를 초과할 수 없습니다. |
   | **수신기 포트** | 수신기를 사용할 포트입니다. 일반적으로 이 포트는 기본값인 1433이어야 합니다. 템플릿에서 지정하는 포트 번호입니다. 그러나 기본 포트가 변경된 경우 수신기 포트는 해당 값을 대신 사용해야 합니다. | 
   | **수신기 IP** | 수신기에서 사용할 IP 주소입니다. 이 주소는 템플릿 배포 중에 만들어지므로 아직 사용되지 않는 주소를 제공합니다.  |
   | **기존 서브넷** | SQL Server VM의 내부 서브넷 이름(예: *default*기본값)입니다. **리소스 그룹으로**이동하여 가상 네트워크를 선택하고, **설정** 창에서 **서브넷을** 선택하고, **이름**아래의 값을 복사하여 이 값을 확인할 수 있습니다. |
   | **기존 내부 Load Balancer** | 3단계에서 만든 내부 로드 밸러블러의 이름입니다. |
   | **프로브 포트** | 내부 로드 밸러버를 사용할 프로브 포트입니다. 템플릿은 기본적으로 59999를 사용하지만 이 값을 변경할 수 있습니다. |
   | &nbsp; | &nbsp; |

1. 이용 약관에 동의하는 경우 위에 명시된 이용 약관에 **동의함을** 선택합니다. 빠른 시작 템플릿의 배포를 완료하려면 **구입을** 선택합니다. 
1. 배포를 모니터링하려면 상단 탐색 배너의 **알림** 벨 아이콘에서 배포를 선택하거나 Azure 포털의 **리소스 그룹으로** 이동합니다. **설정에서**배포를 선택하고 **Microsoft.Template** 배포를 **선택합니다.** 

>[!NOTE]
>배포가 중간에 실패하는 경우 **101sql-vm-aglistener-설정** 퀵스타트 템플릿을 다시 배포하기 전에 PowerShell을 사용하여 [새로 만든 수신기를](#remove-the-availability-group-listener) 수동으로 제거해야 합니다. 

## <a name="remove-the-availability-group-listener"></a>가용성 그룹 리스너 제거
나중에 템플릿이 구성한 가용성 그룹 리스너를 제거해야 하는 경우 SQL VM 리소스 공급자를 거쳐야 합니다. 수신기는 SQL VM 리소스 공급자를 통해 등록되므로 SQL Server 관리 스튜디오를 통해 삭제하는 것만으로는 충분하지 않습니다. 

가장 좋은 방법은 PowerShell에서 다음 코드 조각을 사용하여 SQL VM 리소스 공급자를 통해 삭제하는 것입니다. 이렇게 하면 SQL VM 리소스 공급자에서 가용성 그룹 리스너 메타 데이터가 제거됩니다. 또한 가용성 그룹에서 수신기를 물리적으로 삭제합니다. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>일반 오류
이 섹션에서는 몇 가지 알려진 문제와 가능한 해결 방법을 설명합니다. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>가용성 그룹 ‘\<AG-Name>’의 가용성 그룹 수신기가 이미 있음
가용성 그룹 수신기에 대해 Azure 빠른 시작 템플릿에 사용된 선택한 가용성 그룹에는 이미 수신기가 포함되어 있습니다. 실제로 가용성 그룹 내에 있거나 해당 메타데이터가 SQL VM 리소스 공급자 내에 남아 있습니다. **101 sql-vm-aglistener-설정** 퀵스타트 템플릿을 다시 배포하기 전에 [PowerShell을](#remove-the-availability-group-listener) 사용하여 수신기를 제거합니다. 

### <a name="connection-only-works-from-primary-replica"></a>연결이 주 복제본에서만 작동함
이 동작은 내부 부하 분산 검사의 구성을 일관성 없는 상태로 남겨 둔 실패한 **101 sql-vm-aglistener-설정** 템플릿 배포에서 발생했습니다. 백 엔드 풀에 가용성 집합이 나열되는지, 상태 프로브 및 부하 분산 규칙에 대한 규칙이 있는지 확인합니다. 누락된 것이 있으면 내부 로드 밸런서의 구성이 일치하지 않는 상태입니다. 

이 문제를 해결하려면 [PowerShell을](#remove-the-availability-group-listener)사용하여 리스터를 제거하고 Azure 포털을 통해 내부 로드 밸로드워를 삭제한 다음 3단계에서 다시 시작합니다. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - SQL 가상 머신 목록만 업데이트할 수 있습니다.
이 오류는 SQL Server 관리 스튜디오(SSMS)를 통해 수신기가 삭제되었지만 SQL VM 리소스 공급자에서 삭제되지 않은 경우 **101sql-vm-aglistener-설정** 템플릿을 배포할 때 발생할 수 있습니다. SSMS를 통해 수신기를 삭제해도 SQL VM 리소스 공급자에서 수신기의 메타데이터가 제거되지는 않습니다. 수신기는 [PowerShell](#remove-the-availability-group-listener)을 통해 리소스 공급자에서 삭제해야 합니다. 

### <a name="domain-account-does-not-exist"></a>도메인 계정이 없음
이 오류에는 두 가지 원인이 있을 수 있습니다. 지정된 도메인 계정이 없거나 [UPN(사용자 주체 이름)](/windows/desktop/ad/naming-properties#userprincipalname) 데이터가 없습니다. **101 sql-vm-ag-setup** 템플릿은 UPN 양식(즉,)의 *user@domain.com*도메인 계정을 예상하지만 일부 도메인 계정이 누락되었을 수 있습니다. 이는 일반적으로 로컬 사용자가 서버를 도메인 컨트롤러로 승격할 때 또는 PowerShell을 통해 사용자를 만들 때 로컬 사용자가 첫 번째 도메인 관리자 계정으로 마이그레이션된 경우에 발생합니다. 

계정이 있는지 확인합니다. 이 경우 두 번째 상황이 발생할 수 있습니다. 이 문제를 해결하려면 다음을 수행합니다.

1. 도메인 컨트롤러에서 **서버 관리자**의 **도구** 옵션을 통해 **Active Directory 사용자 및 컴퓨터** 창을 엽니다. 
2. 왼쪽 창에서 **사용자를** 선택하여 계정으로 이동합니다.
3. 계정을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
4. **거래처** 탭을 선택합니다. 사용자 **로그온 이름** 상자가 비어 있으면 오류의 원인입니다. 

    ![비어 있는 사용자 계정은 UPN 누락을 나타냅니다.](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. **사용자 로그온 이름** 상자를 입력하여 사용자 이름과 일치시키고 드롭다운 목록에서 적절한 도메인을 선택합니다. 
6. **적용**을 선택하여 변경 내용을 저장한 다음, **확인**을 선택하여 대화 상자를 닫습니다. 

이러한 변경을 한 후 Azure 빠른 시작 템플릿을 한 번 더 배포해 보십시오. 



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [SQL 서버 VM 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL 서버 VM에 대한 자주 묻는 질문](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL 서버 VM에 대한 가격 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL 서버 VM에 대한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [SQL Server VM에 대한 라이선스 모델 전환](virtual-machines-windows-sql-ahb.md)



