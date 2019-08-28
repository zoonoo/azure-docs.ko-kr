---
title: '자습서: 장애 조치 (failover) 그룹에 SQL Database 관리 되는 인스턴스 추가'
description: Azure SQL Database 관리 되는 인스턴스에 대해 장애 조치 (failover) 그룹을 구성 하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: 5169fe5eef416812c399b421f59305f6cb1e7b62
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035795"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>자습서: 장애 조치 (failover) 그룹에 SQL Database 관리 되는 인스턴스 추가

장애 조치 (failover) 그룹에 SQL Database 관리 되는 인스턴스를 추가 합니다. 이 아티클에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> - 기본 관리 되는 인스턴스 만들기
> - [장애 조치 (failover) 그룹](sql-database-auto-failover-group.md)의 일부로 보조 관리 되는 인스턴스를 만듭니다. 
> - 테스트 장애 조치(failover)

  > [!NOTE]
  > - 이 자습서를 진행 하는 경우 [관리 되는 인스턴스에 대해 장애 조치 그룹을 설정 하기 위한 필수 구성 요소](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)를 사용 하 여 리소스를 구성 하 고 있는지 확인 합니다. 
  > - 관리 되는 인스턴스를 만드는 데는 상당한 시간이 걸릴 수 있습니다. 따라서이 자습서를 완료 하는 데 몇 시간 정도 걸릴 수 있습니다. 프로 비전 시간에 대 한 자세한 내용은 [관리 되는 인스턴스 관리 작업](sql-database-managed-instance.md#managed-instance-management-operations)을 참조 하세요. 
  > - 관리 되는 인스턴스에서 장애 조치 그룹을 사용 하는 것은 현재 미리 보기 상태입니다. 

## <a name="prerequisites"></a>전제 조건

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다. 

- Azure 구독, 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/) . 


## <a name="1----create-resource-group-and-primary-managed-instance"></a>1-리소스 그룹 및 기본 관리 되는 인스턴스 만들기
이 단계에서는 Azure Portal를 사용 하 여 장애 조치 (failover) 그룹에 대 한 리소스 그룹 및 기본 관리 되는 인스턴스를 만듭니다. 

1. [Azure 포털](https://portal.azure.com)할 수 있습니다. 
1. Azure Portal의 왼쪽 위 모퉁이에 **리소스를 만들도록** 선택 합니다. 
1. 검색 `managed instance` 상자에를 입력 하 고 Azure SQL Managed Instance에 대 한 옵션을 선택 합니다. 
1. **만들기** 를 선택 하 여 **SQL 관리 되는 인스턴스** 생성 페이지를 시작 합니다. 
1. **Azure SQL Database Managed Instance 만들기** 페이지의 **기본 사항** 탭에서
    1. **프로젝트 세부 정보**아래의 드롭다운에서 **구독** 을 선택한 다음 새 리소스 그룹을 **만들도록** 선택 합니다. 리소스 그룹의 이름 (예: `myResourceGroup`)을 입력 합니다. 
    1. **Managed Instance 세부 정보**에서 관리 되는 인스턴스의 이름 및 관리 되는 인스턴스를 배포할 영역을 제공 합니다. **계산 + 저장소** 는 기본값을 유지 합니다. 
    1. **관리자 계정**에서와 같은 `azureuser`관리자 로그인 및 복잡 한 관리자 암호를 제공 합니다. 

    ![기본 MI 만들기](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. 나머지 설정은 기본값으로 그대로 두고 **검토 + 만들기** 를 선택 하 여 관리 되는 인스턴스 설정을 검토 합니다. 
1. **만들기** 를 선택 하 여 기본 관리 되는 인스턴스를 만듭니다. 


## <a name="2---create-a-virtual-network"></a>2-가상 네트워크 만들기
이 단계에서는 보조 관리 되는 인스턴스에 대 한 가상 네트워크를 만듭니다. 이 단계는 기본 및 보조 관리 되는 인스턴스의 서브넷에 겹치지 않는 주소 범위가 있어야 하기 때문에 필요 합니다. 

주 가상 네트워크의 서브넷 범위를 확인 하려면 다음 단계를 수행 합니다.
1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동 하 고 기본 인스턴스에 대 한 가상 네트워크를 선택 합니다. 
1. **설정** 아래에서 **서브넷** 을 선택 하 고 **주소 범위**를 확인 합니다. 보조 관리 되는 인스턴스에 대 한 가상 네트워크의 서브넷 주소 범위는이 범위와 겹칠 수 없습니다. 


   ![기본 서브넷](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

가상 네트워크를 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택 하 고 *가상 네트워크*를 검색 합니다. 
1. Microsoft에서 게시 한 **Virtual Network** 옵션을 선택 하 고 다음 페이지에서 **만들기** 를 선택 합니다. 
1. 보조 관리 되는 인스턴스에 대 한 가상 네트워크를 구성 하는 데 필요한 필드를 입력 한 다음 **만들기**를 선택 합니다. 

   다음 표에서는 보조 가상 네트워크에 필요한 값을 보여 줍니다.

    | **필드** | 값 |
    | --- | --- |
    | **이름** |  와 `vnet-sql-mi-secondary`같은 보조 관리 되는 인스턴스에서 사용할 가상 네트워크의 이름입니다. |
    | **주소 공간** | 과 `10.128.0.0/16`같은 가상 네트워크의 주소 공간입니다. | 
    | **구독** | 기본 관리 되는 인스턴스 및 리소스 그룹이 상주 하는 구독입니다. |
    | **Region** | 보조 관리 되는 인스턴스를 배포할 위치입니다. |
    | **서브넷** | 서브넷의 이름입니다. `default`는 기본적으로 제공 됩니다. |
    | **주소 범위**| 서브넷의 주소 범위입니다. 이는와 `10.128.0.0/24`같이 기본 관리 되는 인스턴스의 가상 네트워크에서 사용 하는 서브넷 주소 범위와 달라 야 합니다.  |
    | &nbsp; | &nbsp; |

    ![보조 가상 네트워크 값](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3-보조 관리 되는 인스턴스 만들기
이 단계에서는 두 개의 관리 되는 인스턴스 간에 네트워킹을 구성 하는 Azure Portal에서 보조 관리 되는 인스턴스를 만듭니다. 

두 번째 관리 되는 인스턴스는 다음을 수행 해야 합니다.
- 비어 있어야 합니다. 
- 기본 관리 되는 인스턴스와 다른 서브넷 및 IP 범위를 포함 합니다. 

보조 관리 되는 인스턴스를 만들려면 다음 단계를 수행 합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택 하 고 *Azure SQL Managed Instance*를 검색 합니다. 
1. Microsoft에서 게시 한 **AZURE SQL Managed Instance** 옵션을 선택 하 고 다음 페이지에서 **만들기** 를 선택 합니다.
1. **Azure SQL Database Managed Instance 만들기** 페이지의 **기본 사항** 탭에서 필수 필드를 입력 하 여 보조 관리 되는 인스턴스를 구성 합니다. 

   다음 표에서는 보조 관리 되는 인스턴스에 필요한 값을 보여 줍니다.
 
    | **필드** | 값 |
    | --- | --- |
    | **구독** |  기본 관리 되는 인스턴스가 있는 구독입니다. |
    | **리소스 그룹**| 기본 관리 되는 인스턴스가 있는 리소스 그룹입니다. |
    | **관리되는 인스턴스 이름** | 새 보조 관리 되는 인스턴스의 이름입니다 (예:).`sql-mi-secondary`  | 
    | **Region**| 보조 관리 되는 인스턴스의 위치입니다.  |
    | **Managed Instance 관리자 로그인** | 와 `azureuser`같이 새 보조 관리 되는 인스턴스에 사용할 로그인입니다. |
    | **암호** | 새 보조 관리 되는 인스턴스의 관리자 로그인에 사용 되는 복잡 한 암호입니다.  |
    | &nbsp; | &nbsp; |

1. **네트워킹** 탭의 **Virtual Network**에 대해 드롭다운에서 보조 관리 되는 인스턴스에 대해 만든 가상 네트워크를 선택 합니다.

   ![보조 MI 네트워킹](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. **추가 설정** 탭에서 **지역에서 복제**에 대해 **예** 를 선택 하 여를 _장애 조치 (failover) 보조로 사용_합니다. 드롭다운에서 기본 관리 되는 인스턴스를 선택 합니다. 
    1. 데이터 정렬과 표준 시간대는 기본 관리 되는 인스턴스의 데이터 정렬과 일치 해야 합니다. 이 자습서에서 만든 기본 관리 되는 인스턴스는 기본 `SQL_Latin1_General_CP1_CI_AS` 데이터 정렬과 `(UTC) Coordinated Universal Time` 표준 시간대를 사용 했습니다. 

   ![보조 MI 네트워킹](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. **검토 + 만들기** 를 선택 하 여 보조 관리 되는 인스턴스에 대 한 설정을 검토 합니다. 
1. **만들기** 를 선택 하 여 보조 관리 되는 인스턴스를 만듭니다. 


## <a name="4---create-primary-virtual-network-gateway"></a>4-기본 가상 네트워크 게이트웨이 만들기 

두 개의 관리 되는 인스턴스가 장애 조치 (failover) 그룹에 참여 하려면 네트워크 통신을 허용 하도록 두 관리 되는 인스턴스의 가상 네트워크 간에 구성 된 게이트웨이가 있어야 합니다. Azure Portal를 사용 하 여 기본 관리 되는 인스턴스에 대 한 게이트웨이를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동 하 여 기본 관리 되는 인스턴스에 대 한 **가상 네트워크** 리소스를 선택 합니다. 
1. **설정** 아래에서 **서브넷** 을 선택 하 고 새 **게이트웨이 서브넷**을 추가 하려면 선택 합니다. 기본값을 그대로 둡니다. 

   ![기본 관리 되는 인스턴스에 대 한 게이트웨이 추가](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. 서브넷 게이트웨이를 만든 후 왼쪽 탐색 창에서 **리소스 만들기** 를 선택 하 고 검색 상자에를 `Virtual network gateway` 입력 합니다. **Microsoft**에서 게시 한 **가상 네트워크 게이트웨이** 리소스를 선택 합니다. 

   ![새 가상 네트워크 게이트웨이 만들기](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. 필수 필드를 입력 하 여 게이트웨이를 기본 관리 되는 인스턴스로 구성 합니다. 

   다음 표에서는 기본 관리 되는 인스턴스의 게이트웨이에 필요한 값을 보여 줍니다.
 
    | **필드** | 값 |
    | --- | --- |
    | **구독** |  기본 관리 되는 인스턴스가 있는 구독입니다. |
    | **이름** | 과 `primary-mi-gateway`같은 가상 네트워크 게이트웨이의 이름입니다. | 
    | **Region** | 보조 관리 되는 인스턴스가 있는 지역입니다. |
    | **게이트웨이 유형** | **VPN**을 선택합니다. |
    | **VPN 유형** | **경로 기반** 선택 |
    | **SKU**| 기본값은 `VpnGw1`그대로 둡니다. |
    | **위치**| 기본 관리 되는 인스턴스 및 기본 가상 네트워크가 있는 위치입니다.   |
    | **가상 네트워크**| 섹션 2에서 만든 가상 네트워크 (예: `vnet-sql-mi-primary`)를 선택 합니다. |
    | **공용 IP 주소**| **새로 만들기**를 선택합니다. |
    | **공용 IP 주소 이름**| IP 주소에 대 한 이름 (예: `primary-gateway-IP`)을 입력 합니다. |
    | &nbsp; | &nbsp; |
1. 다른 값은 기본값으로 두고 **검토 + 만들기** 를 선택 하 여 가상 네트워크 게이트웨이에 대 한 설정을 검토 합니다.

   ![기본 게이트웨이 설정](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. **만들기** 를 선택 하 여 새 가상 네트워크 게이트웨이를 만듭니다. 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5-보조 가상 네트워크 게이트웨이 구성 

이전 섹션의 단계를 반복 하 여 보조 관리 되는 인스턴스에 대 한 가상 네트워크 서브넷 및 게이트웨이를 만듭니다. 보조 관리 되는 인스턴스에 대 한 게이트웨이를 구성 하는 데 필요한 필드를 입력 합니다. 

   다음 표에서는 보조 관리 되는 인스턴스의 게이트웨이에 필요한 값을 보여 줍니다.

   | **필드** | 값 |
   | --- | --- |
   | **구독** |  보조 관리 되는 인스턴스가 있는 구독입니다. |
   | **이름** | 과 `secondary-mi-gateway`같은 가상 네트워크 게이트웨이의 이름입니다. | 
   | **Region** | 보조 관리 되는 인스턴스가 있는 지역입니다. |
   | **게이트웨이 유형** | **VPN**을 선택합니다. |
   | **VPN 유형** | **경로 기반** 선택 |
   | **SKU**| 기본값은 `VpnGw1`그대로 둡니다. |
   | **위치**| 보조 관리 되는 인스턴스와 보조 가상 네트워크가 있는 위치입니다.   |
   | **가상 네트워크**| 섹션 2에서 만든 가상 네트워크 (예: `vnet-sql-mi-secondary`)를 선택 합니다. |
   | **공용 IP 주소**| **새로 만들기**를 선택합니다. |
   | **공용 IP 주소 이름**| IP 주소에 대 한 이름 (예: `secondary-gateway-IP`)을 입력 합니다. |
   | &nbsp; | &nbsp; |

   ![보조 게이트웨이 설정](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6-게이트웨이 연결
이 단계에서는 게이트웨이 간의 연결을 만듭니다. 기본에서 보조 게이트웨이로 연결을 설정 하 고 보조 데이터베이스에서 기본 게이트웨이로의 연결을 설정 해야 합니다. 두 게이트웨이 간의 연결을 구성할 때 동일한 **공유 키** 를 사용 해야 합니다. 

연결을 구성 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 에서 리소스 그룹으로 이동 하 고 4 단계에서 만든 기본 게이트웨이를 선택 합니다. 
1. **설정** 아래에서 **연결** 을 선택한 다음 **추가** 를 선택 하 여 새 연결을 만듭니다. 

   ![기본 게이트웨이에 연결 추가](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. 연결에 대 한 이름 (예: `Primary-connection`)을 입력 하 고 **공유** `mi1mi2psk`키 값 (예:)을 입력 합니다. 
1. **두 번째 가상 네트워크 게이트웨이** 를 선택한 후와 `secondary-mi-gateway`같은 보조 관리 되는 인스턴스의 게이트웨이를 선택 합니다. 

   ![보조 연결에 대 한 기본 연결 만들기](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. **확인** 을 선택 하 여 새 기본-보조 게이트웨이 연결을 추가 합니다.
1. 이러한 단계를 반복 하 여 보조 관리 되는 인스턴스의 게이트웨이에서 기본 관리 되는 인스턴스의 게이트웨이로의 연결을 만듭니다. 

   ![보조에서 기본 연결 만들기](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7-장애 조치 (failover) 그룹 만들기
이 단계에서는 장애 조치 (failover) 그룹을 만들고 여기에 관리 되는 인스턴스를 모두 추가 합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **모든 서비스** 로 이동 하 여 검색 상자 `managed instance` 에을 입력 합니다. 
1. 필드 **SQL 관리** 되는 인스턴스 옆의 별표를 선택 하 여 관리 되는 인스턴스를 왼쪽 탐색 모음의 바로 가기로 추가 합니다. 
1. **SQL 관리 되는 인스턴스** 를 선택 하 고와 `sql-mi-primary`같은 기본 관리 되는 인스턴스를 선택 합니다. 
1. **설정**에서 **인스턴스 장애 조치 (failover) 그룹** 으로 이동한 다음 **그룹 추가** 를 선택 하 여 **인스턴스 장애 조치 (failover) 그룹** 페이지를 엽니다. 

   ![장애 조치 (failover) 그룹 추가](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. **인스턴스 장애 조치 (failover) 그룹** 페이지에서 장애 조치 (failover) 그룹의 이름 `failovergrouptutorial` (예:)을 입력 하 고 드롭다운 메뉴 `sql-mi-secondary` 와 같은 보조 관리 되는 인스턴스를 선택 합니다. **만들기** 를 선택 하 여 장애 조치 (failover) 그룹을 만듭니다. 

   ![장애 조치 (failover) 그룹 만들기](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 장애 조치 (failover) 그룹 배포가 완료 되 면 **장애 조치 (Failover) 그룹** 페이지로 돌아갑니다. 

## <a name="8---test-failover"></a>8-테스트 장애 조치
이 단계에서는 장애 조치 (failover) 그룹을 보조 서버로 장애 조치 (failover) 한 다음 Azure Portal를 사용 하 여 장애 복구 (failback) 합니다. 

1. [Azure Portal](https://portal.azure.com) 내에서 관리 되는 인스턴스로 이동 하 고 설정 아래에서 **인스턴스 장애 조치 (Failover) 그룹** 을 선택 합니다. 
1. 첫 번째 관리 되는 인스턴스를 검토 하 고 보조 복제본을 관리 하는 인스턴스를 검토 합니다. 
1. **장애 조치 (Failover)** 를 선택한 다음 연결을 끊을 TDS 세션 경고에 대해 **예** 를 선택 합니다. 

   ![장애 조치 (failover) 그룹 장애 조치 (failover)](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 주 복제본이 관리 되 인스턴스를 검토 합니다. 장애 조치 (failover)가 성공 하는 경우 두 인스턴스는 역할을 전환 해야 합니다. 

   ![장애 조치 (failover) 후 관리 되는 인스턴스의 역할이 전환 됨](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. **장애 조치 (Failover)** 를 다시 한 번 선택 하 여 주 인스턴스가 주 역할로 다시 장애 조치 (Failover) 합니다. 


## <a name="clean-up-resources"></a>리소스 정리
먼저 관리 되는 인스턴스를 삭제 한 다음 가상 클러스터, 나머지 리소스 및 리소스 그룹을 삭제 하 여 리소스를 정리 합니다. 

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동 합니다. 
1. 관리 되는 인스턴스를 선택 하 고 **삭제**를 선택 합니다. 텍스트 `yes` 상자에를 입력 하 여 리소스 삭제를 확인 하 고 **삭제**를 선택 합니다. 이 프로세스는 백그라운드에서 완료 하는 데 약간의 시간이 걸릴 수 있으며 완료 될 때까지 *가상 클러스터* 나 다른 종속 리소스를 삭제할 수 없습니다. 작업 탭에서 삭제를 모니터링 하 여 관리 되는 인스턴스가 삭제 되었는지 확인 합니다. 
1. 관리 되는 인스턴스를 삭제 한 후에는 리소스 그룹에서 *가상 클러스터* 를 선택 하 고 **삭제**를 선택 하 여 삭제 합니다. 텍스트 `yes` 상자에를 입력 하 여 리소스 삭제를 확인 하 고 **삭제**를 선택 합니다. 
1. 나머지 리소스를 삭제 합니다. 텍스트 `yes` 상자에를 입력 하 여 리소스 삭제를 확인 하 고 **삭제**를 선택 합니다. 
1. 리소스 그룹 **삭제**를 선택 하 고 리소스 `myResourceGroup`그룹의 이름을 입력 한 다음 **삭제**를 선택 하 여 리소스 그룹을 삭제 합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 두 개의 관리 되는 인스턴스 간에 장애 조치 (failover) 그룹을 구성 했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> - 기본 관리 되는 인스턴스 만들기
> - [장애 조치 (failover) 그룹](sql-database-auto-failover-group.md)의 일부로 보조 관리 되는 인스턴스를 만듭니다. 
> - 테스트 장애 조치(failover)

관리 되는 인스턴스에 연결 하는 방법 및 데이터베이스를 관리 되는 인스턴스로 복원 하는 방법에 대 한 다음 빠른 시작으로 이동 합니다. 

> [!div class="nextstepaction"]
> [관리 되는 인스턴스에](sql-database-managed-instance-configure-vm.md)
> 연결[데이터베이스를 관리 되는 인스턴스로 복원](sql-database-managed-instance-get-started-restore.md)


