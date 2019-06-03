---
title: SQL Server 2008 및 Azure 사용 하 여 SQL Server 2008 R2에 대 한 지원 확장
description: Azure에 SQL Server 인스턴스를 마이그레이션 또는 인스턴스가 온-프레미스를 유지 하려면 확장 된 지원을 구입 하 고 SQL Server 2008 및 SQL Server 2008 R2에 대 한 지원을 확장 하는 방법에 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 62261e46dc4744597acd10c32f0a835f4a597d4d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243964"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>SQL Server 2008 및 Azure 사용 하 여 SQL Server 2008 R2에 대 한 지원 확장

SQL Server 2008 및 SQL Server 2008 R2는 둘 다에 근접 하 고는 [남았는지 지원 수명 주기 끝](https://www.microsoft.com/sql-server/sql-server-2008)합니다. 여전히 두 버전을 사용 하는 대부분의 고객, 이후 계속 지원을 받으려면 몇 가지 옵션을 제공할 합니다. Azure virtual machines (Vm)에 온-프레미스 SQL Server 인스턴스를 마이그레이션, Azure SQL Database로 마이그레이션 또는 온-프레미스를 유지 하 수 확장 된 보안 업데이트를 구매 합니다.

와 달리 관리 되는 인스턴스를 사용 하 여 Azure VM에 마이그레이션할 필요가 없습니다 recertifying 응용 프로그램. 와 달리 온-프레미스에 유지 된 받게 무료 확장 된 보안 패치는 Azure VM을 마이그레이션하여 하며 

이 문서의 나머지 부분에서는 Azure VM에 SQL Server 인스턴스를 마이그레이션하기 위한 고려 사항을 제공 합니다. 

## <a name="provisioning"></a>프로비전 

종 량 제는 `SQL Server 2008 R2 on Windows Server 2008 R2` Azure marketplace에서 제공 되는 이미지입니다. 

고객은 SQL Server 2008에는 자체 설치 또는 SQL Server 2008 R2로 업그레이드 해야 하거나 합니다. 마찬가지로, Windows Server 2008에서 고객이 하거나 사용자 지정 VHD에서 VM을 배포 하거나 Windows Server 2008 R2로 업그레이드 해야 합니다. 

Marketplace를 통해 배포 된 이미지는 SQL IaaS 확장을 미리 설치 되어 제공 됩니다. SQL IaaS 확장은 융통성 있는 라이선스 및 자동화 된 패치 요구 사항입니다. 자체 설치 된 Vm을 배포 하는 고객은 SQL IaaS 확장을 수동으로 설치 해야 합니다. SQL IaaS 확장은 Windows 2008에서 지원 되지 않습니다. 

  > [!NOTE]
  > SQL server `Create` 하 고 `Manage` 블레이드에서 Azure portal에서 SQL Server 2008 r2 이미지를 사용 하 여 작동, 다음 기능은 _지원 되지 않습니다_: 자동 백업, Azure Key Vault 통합, R Services 및 저장소 구성 합니다.

## <a name="licensing"></a>라이선스
종 량 제 SQL Server 2008R2 배포 변환할 수 있습니다 [Azure 하이브리드 혜택 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/)합니다.

SQL VM을 사용 하 여 등록 해야 고객은 SA (Software Assurance) 기반 라이선스에 종 량 제를 변환할 [리소스 공급자](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)합니다. SQL VM 리소스 공급자에 등록 되 면 SQL 라이선스 유형을 AHB 및 종 량 제 간에 서로 바꿔 사용할 수 됩니다. 

Azure VM에서 자체 설치 된 SQL Server 2008 또는 SQL Server 2008 R2 인스턴스는 SQL 리소스 공급자를 등록 하 고 종 량 제의 라이선스 유형을 변환할 수 있습니다.

## <a name="migration"></a>마이그레이션
수동 백업/복원 메서드를 사용 하 여 Azure VM에 EOS SQL Server 인스턴스를 마이그레이션할 수 있습니다. 이것이 Azure VM에 온-프레미스에서 가장 일반적인 마이그레이션 방법입니다.

### <a name="azure-site-recovery"></a>Azure Site Recovery

대량 마이그레이션에 대 한 것이 좋습니다 [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) 서비스입니다. Azure Site Recovery를 사용 하 여 고객은 온-프레미스에서 Azure VM SQL Server를 비롯 하 여 전체 VM을 복제할 수 있습니다.

SQL Server 복구를 보장 하기 위해 응용 프로그램에 일관 된 Azure Site Recovery 스냅숏이 필요 및 Azure Site Recovery는 최소 1 시간 간격을 사용 하 여 앱 일치 스냅숏을 지원 합니다. 마이그레이션은 Azure Site Recovery를 사용 하 여 SQL Server에 대 한 가능한 최소 RPO가 1 시간 및 RTO는 SQL Server 복구 시간을 더한 2 시간입니다.

### <a name="database-migration-service"></a>Database Migration Service

합니다 [Database Migration Service](/azure/dms/dms-overview) 고객에 대 한 옵션은 SQL Server SQL Server 2012로 업그레이드 하 여 Azure VM에 온-프레미스에서 마이그레이션 및 큰 경우.

## <a name="disaster-recovery"></a>재해 복구

EOS SQL Server Azure VM에 대 한 재해 복구 솔루션은 다음과 같습니다.

- **SQL Server 백업을**: 지역의 경우 SQL Server 또는 영역 오류를 복구 하려면 SQL Server 백업은 사용할 수 있습니다. EOS SQL Server에 대 한 관리 되는 백업 기능이 지원 되지 않습니다, 하므로 고객은 백업을 수동으로 수행 해야 합니다.
- **로그 전달**: 다른 로그 전달 복제본을 만들 수 있습니다 영역 또는 연속 복원의 RTO를 줄이기 위해 사용 하 여 Azure 지역입니다. 고객이 수동으로 로그 전달을 구성 해야 합니다.
- **Azure Site Recovery**: 영역 및 Azure Site Recovery 복제를 통해 지역 간의 VM을 복제할 수 있습니다. SQL Server 재해 발생 시 복구를 보장 하기 위해 응용 프로그램 일치 스냅숏이 필요 합니다. Azure Site Recovery는 최소 1 시간 RPO 및 2 시간 + SQL Server 복구 시간 RTO EOS SQL Server DR에 대 한 제공합니다.

## <a name="security-patching"></a>보안 패치
SQL Server Vm에 대 한 확장 된 보안 업데이트 Microsoft 업데이트 채널을 통해 제공 될 SQL을 사용 하 여 SQL Server VM을 등록 되 면 [리소스 공급자](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)합니다. 패치를 수동으로 또는 자동으로 다운로드할 수 있습니다. 

**Automated patching**이 사용됩니다. Azure에서는 자동화된 패치를 통해 SQL Server와 운영 체제를 자동으로 패치합니다. SQL IaaS 확장을 설치 하는 경우 하루에 요일, 시간 및 유지 관리 기간에 대 한 기간을 지정할 수 있습니다. Azure에서 유지 관리 기간에 패치를 수행합니다. 유지 관리 기간 일정에서는 VM 로캘 시간을 사용합니다.  자세한 내용은 [Azure Virtual Machines에서 SQL Server의 자동화된 패치](virtual-machines-windows-sql-automated-patching.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

SQL Server VM을 Azure로 마이그레이션

* [Azure VM에서 SQL Server로 SQL Server 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)

Azure 가상 머신에서 SQL Server 시작:

* [Azure Portal에서 SQL Server VM 만들기](quickstart-sql-vm-create-portal.md)

SQL VM에 대한 일반적인 질문에 대한 답변 구하기:

* [Azure Virtual Machines의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
