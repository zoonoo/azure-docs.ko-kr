---
title: SQL Server 2008 & 2008 R2 지원 확장
description: SQL Server 인스턴스를 Azure로 마이그레이션하거나 확장 된 지원을 구매 하 여 온-프레미스 인스턴스를 유지 함으로써 SQL Server 2008 및 SQL Server 2008 r 2에 대 한 지원을 확장 합니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 7c93538982b7fd7bb2f5ac25027ed92cc6ccbfa3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357852"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Azure를 사용 하 여 SQL Server 2008 및 SQL Server 2008 R2 지원 확장

SQL Server 2008 및 SQL Server 2008 R2는 모두 [해당 지원 (EOS) 수명 주기 끝](https://www.microsoft.com/sql-server/sql-server-2008)에 도달 했습니다. 많은 고객이 계속 해 서 두 버전을 모두 사용 하 고 있으므로 계속 지원을 받기 위한 몇 가지 옵션을 제공 하 고 있습니다. 온-프레미스 SQL Server 인스턴스를 Azure Vm (가상 머신)으로 마이그레이션하거나, Azure SQL Database로 마이그레이션하거나, 확장 된 보안 업데이트를 구매할 수 있습니다.

관리 되는 인스턴스와 달리 Azure VM으로 마이그레이션하려면 응용 프로그램을 recertifying 필요가 없습니다. 온-프레미스를 유지 하는 것과 달리 Azure VM으로 마이그레이션하여 확장 된 보안 패치를 무료로 얻을 수 있습니다.

이 문서의 나머지 부분에서는 SQL Server 인스턴스를 Azure VM으로 마이그레이션하기 위한 고려 사항을 제공 합니다.

지원 종료 옵션에 대 한 자세한 내용은 [지원 종료](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)를 참조 하세요.

## <a name="provisioning"></a>프로비저닝

Azure Marketplace에서 제공 되는 **Windows Server 2008 r2의 종 량 제 SQL Server 2008 r2** 이미지가 있습니다.

SQL Server 2008을 보유 하 고 있는 고객은 SQL Server 2008 r 2로 자체 설치 하거나 업그레이드 해야 합니다. 마찬가지로, Windows Server 2008의 고객은 사용자 지정 VHD에서 VM을 배포 하거나 Windows Server 2008 r 2로 업그레이드 해야 합니다.

Azure Marketplace를 통해 배포 된 이미지는 SQL IaaS 확장이 미리 설치 된 상태로 제공 됩니다. SQL IaaS 확장은 유연한 라이선스 및 자동화 된 패치를 위한 요구 사항입니다. 자체 설치 된 Vm을 배포 하는 고객은 SQL IaaS 확장을 수동으로 설치 해야 합니다. SQL IaaS 확장은 Windows Server 2008에서 지원 되지 않습니다.

> [!NOTE]
> SQL Server **만들기** 및 **관리** 블레이드가 Azure Portal의 SQL Server 2008 R2 이미지와 함께 작동 하지만, 자동 백업, Azure Key Vault 통합, R 서비스 및 저장소 구성과 같은 기능은 _지원 되지 않습니다_.

## <a name="licensing"></a>라이선스
종 량 제 SQL Server 2008 R2 배포를 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)으로 변환할 수 있습니다.

소프트웨어 보증 (SA) 기반 라이선스를 종 량 제로 변환 하려면 고객은 SQL VM [리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md)에 등록 해야 합니다. 등록 후에는 Azure 하이브리드 혜택와 종 량 제 간에 SQL 라이선스 유형을 교환할 수 있습니다.

Azure VM에서 자동으로 설치 된 SQL Server 2008 또는 SQL Server 2008 R2 인스턴스는 SQL VM 리소스 공급자에 등록 하 고 해당 라이선스 유형을 종 량 제로 전환할 수 있습니다.

## <a name="migration"></a>마이그레이션
수동 백업/복원 방법을 사용 하 여 EOS SQL Server 인스턴스를 Azure VM으로 마이그레이션할 수 있습니다. 이는 온-프레미스에서 Azure VM으로의 가장 일반적인 마이그레이션 방법입니다.

### <a name="azure-site-recovery"></a>Azure Site Recovery

대량 마이그레이션의 경우 [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) 서비스를 권장 합니다. Azure Site Recovery를 통해 고객은 온-프레미스에서 Azure VM으로 SQL Server를 포함 하 여 전체 VM을 복제할 수 있습니다.

SQL Server 복구를 보장 하기 위해 앱에 일관 된 Azure Site Recovery 스냅숏이 필요 합니다. Azure Site Recovery는 최소 1 시간 간격으로 앱 일치 스냅숏을 지원 합니다. Azure Site Recovery 마이그레이션의 SQL Server에 사용할 수 있는 최소 RPO (복구 지점 목표)는 1 시간입니다. RTO (복구 시간 목표)는 2 시간에 SQL Server 복구 시간을 더한 값입니다.

### <a name="database-migration-service"></a>Database Migration Service

[Database Migration Service](/azure/dms/dms-overview) 은 SQL Server를 2012 이상 버전으로 업그레이드 하 여 온-프레미스에서 Azure VM으로 마이그레이션하는 경우 고객을 위한 옵션입니다.

## <a name="disaster-recovery"></a>재해 복구

Azure VM에서 EOS SQL Server에 대 한 재해 복구 솔루션은 다음과 같습니다.

- **SQL Server 백업**: Azure Backup를 사용 하 여, 랜 섬 웨어, 실수로 인 한 삭제 및 손상 으로부터 EOS SQL Server을 보호 합니다. 이 솔루션은 현재 EOS SQL Server 미리 보기로 제공 되며 Windows 2008 R2 s p 1에서 실행 되는 SQL Server 2008 및 2008 R2를 지원 합니다. 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support)를 참조 하세요.
- **로그 전달**: RTO를 줄이기 위해 연속 복원을 사용 하 여 다른 영역 또는 Azure 지역에 로그 전달 복제본을 만들 수 있습니다. 로그 전달을 수동으로 구성 해야 합니다.
- **Azure Site Recovery**: Azure Site Recovery 복제를 통해 영역 및 지역 간에 VM을 복제할 수 있습니다. 재해 발생 시 복구를 보장 하기 위해 SQL Server에는 앱 일치 스냅숏이 필요 합니다. Azure Site Recovery는 최소 1 시간 RPO와 EOS SQL Server 재해 복구를 위한 2 시간 (+ SQL Server 복구 시간) RTO를 제공 합니다.

## <a name="security-patching"></a>보안 패치
SQL Server Vm에 대 한 확장 된 보안 업데이트는 SQL VM [리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md)에 SQL Server VM 등록 된 후 Microsoft 업데이트 채널을 통해 전달 됩니다. 패치는 수동으로 또는 자동으로 다운로드할 수 있습니다.

*Automated patching*이 사용됩니다. Azure에서는 자동화된 패치를 통해 SQL Server와 운영 체제를 자동으로 패치합니다. SQL Server IaaS 확장이 설치 된 경우 유지 관리 기간에 대 한 요일, 시간 및 기간을 지정할 수 있습니다. Azure에서 유지 관리 기간에 패치를 수행합니다. 유지 관리 기간 일정에서는 VM 로캘 시간을 사용합니다.  자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대 한 자동화 된 패치](virtual-machines-windows-sql-automated-patching.md)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

SQL Server VM를 Azure로 마이그레이션합니다.

* [Azure VM에서 SQL Server로 SQL Server 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)

Azure Virtual Machines에서 SQL Server 시작:

* [Azure Portal에서 SQL Server VM 만들기](quickstart-sql-vm-create-portal.md)

SQL Server Vm에 대 한 일반적인 질문과 대답을 확인 합니다.

* [Azure Virtual Machines의 SQL Server에 대 한 FAQ](virtual-machines-windows-sql-server-iaas-faq.md)

지원의 끝 옵션과 확장 된 보안 업데이트에 대해 자세히 알아보세요.

* [확장 된 보안 업데이트](/sql/sql-server/end-of-support/sql-server-extended-security-updates) & [지원 종료](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)