---
title: SQL Server 2008 & 2008 R2에 대한 지원 확장
description: SQL Server 인스턴스를 Azure로 마이그레이션하거나 확장 지원을 구입하여 SQL Server 2008 및 SQL Server 2008 R2에 대한 지원을 확장하여 인스턴스를 온-프레미스로 유지합니다.
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
ms.openlocfilehash: 6c27fa8f80140e6db92ea0f998852507260cc278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159543"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Azure를 사용하여 SQL Server 2008 및 SQL Server 2008 R2에 대한 지원을 확장합니다.

SQL Server 2008 및 SQL Server 2008 R2는 모두 [EOS(지원) 수명 주기가 끝났습니다.](https://www.microsoft.com/sql-server/sql-server-2008) 많은 고객이 여전히 두 버전을 모두 사용하고 있기 때문에 지원을 계속받을 수 있는 몇 가지 옵션을 제공하고 있습니다. 온-프레미스 SQL Server 인스턴스를 Azure 가상 시스템(VM)으로 마이그레이션하거나, Azure SQL Database로 마이그레이션하거나, 온-프레미스상태를 유지하고 확장된 보안 업데이트를 구입할 수 있습니다.

관리되는 인스턴스와 달리 Azure VM으로 마이그레이션할 때 응용 프로그램을 다시 인증할 필요가 없습니다. 온-프레미스와 달리 Azure VM으로 마이그레이션하여 무료 확장 보안 패치를 받게 됩니다.

이 문서의 나머지 부분에서는 SQL Server 인스턴스를 Azure VM으로 마이그레이션하기 위한 고려 사항을 제공합니다.

지원 종료 옵션에 대한 자세한 내용은 [지원 종료](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)를 참조하십시오.

## <a name="provisioning"></a>프로비전

Azure 마켓플레이스에서 사용할 수 있는 **Windows Server 2008 R2 이미지에는 종량제 SQL Server 2008 R2가** 있습니다.

SQL Server 2008에 있는 고객은 SQL Server 2008 R2를 자체 설치하거나 업그레이드해야 합니다. 마찬가지로 Windows Server 2008의 고객은 사용자 지정 VHD에서 VM을 배포하거나 Windows Server 2008 R2로 업그레이드해야 합니다.

Azure 마켓플레이스를 통해 배포된 이미지는 SQL IaaS 확장이 사전 설치된 상태로 제공됩니다. SQL IaaS 확장은 유연한 라이선싱 및 자동화된 패치를 위한 요구 사항입니다. 자체 설치된 VM을 배포하는 고객은 SQL IaaS 확장을 수동으로 설치해야 합니다. SQL IaaS 확장은 Windows Server 2008에서 지원되지 않습니다.

> [!NOTE]
> SQL Server **만들기** 및 **관리** 블레이드는 Azure 포털에서 SQL Server 2008 R2 이미지와 함께 작동하지만 자동 백업, Azure 키 볼트 통합, R 서비스 및 저장소 구성과 같은 기능은 _지원되지 않습니다._

## <a name="licensing"></a>라이선스
종량제 SQL Server 2008 R2 배포는 [Azure 하이브리드 혜택으로](https://azure.microsoft.com/pricing/hybrid-benefit/)변환할 수 있습니다.

SA(소프트웨어 보증) 기반 라이선스를 종량제 라이선스로 변환하려면 고객은 SQL VM [리소스 공급자에](virtual-machines-windows-sql-register-with-resource-provider.md)등록해야 합니다. 등록 후 SQL 라이선스 유형은 Azure 하이브리드 혜택과 종량제 간에 상호 교환이 가능합니다.

Azure VM에서 자체 설치된 SQL Server 2008 또는 SQL Server 2008 R2 인스턴스는 SQL VM 리소스 공급자에 등록하고 라이선스 유형을 종량제로 변환할 수 있습니다.

## <a name="migration"></a>마이그레이션
수동 백업/복원 방법을 사용하여 EOS SQL Server 인스턴스를 Azure VM으로 마이그레이션할 수 있습니다. 온-프레미스에서 Azure VM으로 의 가장 일반적인 마이그레이션 방법입니다.

### <a name="azure-site-recovery"></a>Azure Site Recovery

대량 마이그레이션의 경우 [Azure 사이트 복구](/azure/site-recovery/site-recovery-overview) 서비스를 권장합니다. Azure 사이트 복구를 통해 고객은 온-프레미스에서 Azure VM에 SQL Server를 포함하여 전체 VM을 복제할 수 있습니다.

SQL Server는 복구를 보장하기 위해 앱 일관된 Azure 사이트 복구 스냅숏이 필요합니다. Azure 사이트 복구는 최소 1시간 간격으로 앱 일관된 스냅숏을 지원합니다. Azure 사이트 복구 마이그레이션을 사용 하 고 SQL Server에 대 한 가능한 최소 복구 지점 목표 (RPO) 1 시간입니다. RTO(복구 시간 목표)는 2시간 및 SQL Server 복구 시간입니다.

### <a name="database-migration-service"></a>Database Migration Service

[데이터베이스 마이그레이션 서비스는](/azure/dms/dms-overview) SQL Server를 2012 버전 이상으로 업그레이드하여 온-프레미스에서 Azure VM으로 마이그레이션하는 경우 고객에게 옵션입니다.

## <a name="disaster-recovery"></a>재해 복구

Azure VM의 EOS SQL Server에 대한 재해 복구 솔루션은 다음과 같습니다.

- **SQL Server 백업**: Azure Backup을 사용하여 EOS SQL Server 2008 및 2008 R2를 15분 RPO 및 시점 복구로 랜섬웨어, 우발적인 삭제 및 손상으로부터 보호합니다. 자세한 내용은 [이 문서를](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support)참조하십시오.
- **로그 전달**: RTO를 줄이기 위해 연속 복원을 사용하여 다른 영역 또는 Azure 영역에서 로그 전달 복제본을 만들 수 있습니다. 로그 전달을 수동으로 구성해야 합니다.
- **Azure 사이트 복구**: Azure 사이트 복구 복제를 통해 영역과 지역 간에 VM을 복제할 수 있습니다. SQL Server는 재해 발생 시 복구를 보장하기 위해 앱 일관된 스냅숏이 필요합니다. Azure 사이트 복구는 EOS SQL Server 재해 복구를 위한 최소 1시간 RPO 및 2시간(SQL 서버 복구 시간 포함) RTO를 제공합니다.

## <a name="security-patching"></a>보안 패치
SQL Server VM에 대한 확장된 보안 업데이트는 SQL Server VM이 SQL VM [리소스 공급자에](virtual-machines-windows-sql-register-with-resource-provider.md)등록된 후 Microsoft 업데이트 채널을 통해 제공됩니다. 패치는 수동으로 또는 자동으로 다운로드할 수 있습니다.

*Automated patching*이 사용됩니다. Azure에서는 자동화된 패치를 통해 SQL Server와 운영 체제를 자동으로 패치합니다. SQL Server IaaS 확장이 설치된 경우 유지 관리 기간에 대한 요일, 시간 및 기간을 지정할 수 있습니다. Azure에서 유지 관리 기간에 패치를 수행합니다. 유지 관리 기간 일정에서는 VM 로캘 시간을 사용합니다.  자세한 내용은 [Azure 가상 컴퓨터에서 SQL Server에 대한 자동 패치를](virtual-machines-windows-sql-automated-patching.md)참조하십시오.


## <a name="next-steps"></a>다음 단계

SQL Server VM을 Azure로 마이그레이션합니다.

* [Azure VM에서 SQL Server로 SQL Server 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)

Azure 가상 컴퓨터에서 SQL Server를 시작하십시오.

* [Azure Portal에서 SQL Server VM 만들기](quickstart-sql-vm-create-portal.md)

SQL Server VM에 대한 자주 묻는 질문에 대한 답변을 얻을 수 있습니다.

* [Azure 가상 머신의 SQL 서버에 대한 자주 묻는 질문](virtual-machines-windows-sql-server-iaas-faq.md)

지원 옵션의 끝 및 확장된 보안 업데이트에 대해 자세히 알아보십시오.

* [지원 종료](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [확장 보안 업데이트](/sql/sql-server/end-of-support/sql-server-extended-security-updates)