---
title: "SQL Always On에 대해 부하 분산 장치 구성 | Microsoft Docs"
description: "SQL Always On에서 작동하도록 부하 분산 장치를 구성하며, PowerShell을 활용하여 SQL 구현에 대한 부하 분산 장치를 만드는 방법입니다."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 0c23ee550d8ac88994e8c7c54a33d348ffc24372
ms.openlocfilehash: 68aad6253f185d53fdd7f11c8660c7287ef12655
ms.lasthandoff: 01/11/2017

---

# <a name="configure-load-balancer-for-sql-always-on"></a>SQL Always On에 대해 부하 분산 장치 구성

이제 ILB에서 SQL Server AlwaysOn 가용성 그룹을 실행할 수 있습니다. 가용성 그룹은 고가용성 및 재해 복구를 위한 SQL Server의 주력 솔루션입니다. 가용성 그룹 수신기를 통해 클라이언트 응용 프로그램은 구성의 복제본 수에 관계없이 주 복제본에 매끄럽게 연결할 수 있습니다.

수신기(DNS) 이름이 부하 분산 IP 주소에 매핑되고, Azure Load Balancer가 들어오는 트래픽을 복제본 세트의 주 서버로만 보냅니다.

SQL Server AlwaysOn(수신기) 끝점에 대해 ILB 지원을 사용할 수 있습니다. 이제 수신기의 접근성을 제어할 수 있으며, VNet(가상 네트워크)의 특정 서브넷에서 부하 분산 IP 주소를 선택할 수 있습니다.

수신기에서 ILB를 사용하면 다음 항목만 SQL Server 끝점(예: Server=tcp:ListenerName,1433;Database=DatabaseName)에 액세스할 수 있습니다.

* 동일한 가상 네트워크에 있는 서비스 및 VM
* 연결된 온-프레미스 네트워크의 서비스 및 VM
* 상호 연결된 VNet의 서비스 및 VM

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

그림 1 - 인터넷 연결 부하 분산 장치로 구성된 SQL AlwaysOn

## <a name="add-internal-load-balancer-to-the-service"></a>서비스에 내부 부하 분산 장치 추가

1. 다음 예제에서는 'Subnet-1'이라는 서브넷을 포함하는 가상 네트워크를 구성합니다.

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. 각 VM에서 ILB에 대한 부하 분산 끝점 추가

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    위의 예에서는 "sqlsvc1" 및 "sqlsvc2"라는 2개의 VM이 클라우드 서비스 "Sqlsvc"에서 실행 중입니다. `DirectServerReturn` 스위치를 사용하여 ILB를 만든 후 ILB에 부하 분산 끝점을 추가하여 SQL에서 가용성 그룹에 대한 수신기를 구성할 수 있게 합니다.

SQL AlwaysOn에 대한 자세한 내용은 [Azure에서 AlwaysOn 가용성 그룹에 대한 내부 부하 분산 장치 구성](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
[인터넷 연결 부하 분산 장치 구성 시작](load-balancer-get-started-internet-arm-ps.md)

[내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-ps.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

