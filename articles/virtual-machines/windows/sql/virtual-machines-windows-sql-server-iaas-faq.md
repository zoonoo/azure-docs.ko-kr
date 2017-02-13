---
title: "Azure 가상 컴퓨터의 SQL Server FAQ | Microsoft Docs"
description: "이 문서는 Azure VM에서 SQL Server를 실행하는 방법에 대한 질문과 대답을 제공합니다."
services: virtual-machines-windows
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/07/2017
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 0c23ee550d8ac88994e8c7c54a33d348ffc24372
ms.openlocfilehash: a7d1878931ccf45e8e3192d5d03a4292c5291366


---
# <a name="sql-server-on-azure-virtual-machines-faq"></a>Azure 가상 컴퓨터의 SQL Server FAQ
이 항목에서는 [Azure Virtual Machines에서 SQL Server 실행](https://azure.microsoft.com/services/virtual-machines/sql-server/)에 대해 가장 일반적인 질문 중 몇 가지에 대한 답변을 제공합니다.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>질문과 대답
1. **SQL Server를 사용하여 Azure 가상 컴퓨터를 만들려면 어떻게 해야 합니까?**
   
    가장 쉬운 해결 방법은 SQL Server가 포함된 가상 컴퓨터를 만드는 것입니다. Azure에 등록하고 포털에서 SQL VM을 만드는 방법에 대한 자습서는 [Azure Portal에 SQL Server 가상 컴퓨터 프로비저닝](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요. 분 단위로 요금이 부과되는 SQL Server 라이선스를 사용하는 가상 컴퓨터 이미지를 선택하거나 사용자 SQL Server 라이선스를 가져오도록 허용하는 이미지를 사용할 수 있습니다. 또한 VM에서 SQL Server를 수동으로 설치하고 온-프레미스 라이선스를 다시 사용할 수도 있습니다. 사용자 라이선스가 필요하면 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)이 가능해야 합니다.
2. **SQL VM과 SQL 데이터베이스는 어떤 점이 다릅니까?**
   
    개념적으로 Azure 가상 컴퓨터에서 SQL Server를 실행하는 것은 원격 데이터 센터에 SQL Server를 실행하는 것과 크게 다르지 않습니다. 반면, [SQL 데이터베이스](../../../sql-database/sql-database-technical-overview.md) 는 DaaS(Database-as-a-Service)를 제공합니다. SQL 데이터베이스에서는 데이터베이스를 호스팅하고 있는 컴퓨터에 액세스할 수 없습니다. 전체 비교를 보려면 [클라우드 SQL Server 옵션 선택: Azure SQL(PaaS) 데이터베이스 또는 Azure VM의 SQL Server(IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)를 참조하세요.
3. **온-프레미스 SQL Server 데이터베이스를 클라우드로 마이그레이션하려면 어떻게 해야 합니까?**
   
    가장 먼저 SQL Server 인스턴스를 사용하여 Azure 가상 컴퓨터를 만듭니다. 그런 다음 온-프레미스 데이터베이스를 해당 인스턴스로 마이그레이션합니다. 데이터 마이그레이션 전략에 대해서는 [Azure VM의 SQL Server로 SQL Server 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)을 참조하세요.
4. **설치된 기능을 변경하거나 동일한 VM에 두 번째 SQL Server 인스턴스를 설치할 수 있습니까?**
   
    예. SQL Server 설치 미디어는 **C** 드라이브의 폴더에 있습니다. 이 위치에서 **Setup.exe** 를 실행하여 새 SQL Server 인스턴스를 실행하거나 컴퓨터에 설치된 다른 SQL Server 기능을 변경합니다.
5. **Azure VM에서 SQL Server의 새 버전을 업그레이드하려면 어떻게 합니까?**
   
    현재는 Azure VM에서 실행 중인 SQL Server를 전체 업그레이드할 수 없습니다. 원하는 SQL Server 버전으로 새 Azure 가상 컴퓨터를 만든 다음 표준 [데이터 마이그레이션 기법](virtual-machines-windows-migrate-sql.md)을 사용하여 데이터베이스를 새 서버로 마이그레이션합니다.
6. **Azure VM에 라이선스가 있는 내 SQL Server 사본을 설치하려면 어떻게 해야 합니까?**
   
    두 가지 방법으로 이 작업을 수행할 수 있습니다. [라이선스를 지원하는 가상 컴퓨터 이미지](virtual-machines-windows-sql-server-iaas-overview.md#BYOL) 중 하나를 프로비전할 수 있습니다. 다른 옵션은 SQL Server 설치 미디어를 Windows Server VM으로 복사한 다음 VM에 SQL Server를 설치하는 것입니다. 라이선싱의 이유로 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)이 가능해야 합니다.
7. **종량제 갤러리 이미지 중 하나에서 만들어진 경우 사용자 고유의 SQL Server 라이선스를 사용하도록 VM을 변경할 수 있나요?**

    번호 분 단위로 요금이 부과되는 라이선스에서 사용자 라이선스 사용으로 전환할 수 없습니다. [BYOL 이미지](virtual-machines-windows-sql-server-iaas-overview.md#BYOL) 중 하나를 사용하여 새 Azure 가상 컴퓨터를 만든 다음 표준 [데이터 마이그레이션 기법](virtual-machines-windows-migrate-sql.md)을 사용하여 데이터베이스를 새 서버로 마이그레이션합니다.

7. **대기/장애 조치(failover)에만 사용되는 경우 Azure VM에서 SQL Server 라이선스 비용을 지불해야 하나요?**
   
    [가상 컴퓨터 라이선스 FAQ](http://azure.microsoft.com/pricing/licensing-faq/)에 설명된 대로 Software Assurance가 있고 License Mobility를 사용할 경우 HA 배포에서 수동 보조 복제본으로 참여하는 하나의 SQL Server 라이선스에 대해 비용을 지불할 필요가 없습니다.
    
8. **업데이트와 서비스 팩은 SQL Server VM에 어떻게 적용됩니까?**
   
    가상 컴퓨터에서는 호스트 컴퓨터에 업데이트를 적용할 시기와 방법 등을 제어할 수 있습니다. 운영 체제의 경우 Windows 업데이트를 수동으로 적용하거나 [자동 패칭](virtual-machines-windows-sql-automated-patching.md)이라는 예약 서비스를 사용할 수 있습니다. 자동 패칭은 해당 범주의 SQL Server 업데이트를 포함하여 중요함으로 표시된 업데이트를 설치합니다. SQL Server에 대한 기타 선택적 업데이트는 수동으로 설치해야 합니다.
9. **가상 컴퓨터 갤러리에 표시되지 않은 구성을 설정할 수 있습니까(예: Windows 2008 R2 + SQL Server 2012)?**
   
    아니요. SQL Server가 포함된 가상 컴퓨터 갤러리 이미지의 경우 제공된 이미지 중 하나를 선택해야 합니다.
10. **Azure VM에 SQL Data Tools를 설치하려면 어떻게 해야 합니까?**
    
     [Microsoft SQL Server Data Tools - Visual Studio 2013용 비즈니스 인텔리전스](https://www.microsoft.com/en-us/download/details.aspx?id=42313)에서 SQL Data Tools를 다운로드하고 설치하세요.

## <a name="resources"></a>리소스
Azure 가상 컴퓨터의 SQL Server에 대한 개요는 [Azure VM은 SQL Server 2016에 가장 적합한 플랫폼입니다](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)동영상을 시청하세요. 또한 [Azure Virtual Machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md) 항목에서도 간략한 소개를 확인할 수 있습니다.

기타 리소스는 다음과 같습니다.

* [Azure Portal에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)
* [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)
* [Azure Virtual Machines의 SQL Server에 대한 응용 프로그램 패턴 및 개발 전략](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)




<!--HONumber=Jan17_HO2-->


