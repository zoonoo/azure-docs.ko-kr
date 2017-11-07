---
title: "Linux Azure Virtual Machines의 SQL Server FAQ | Microsoft Docs"
description: "이 문서는 Linux Azure VM에서 SQL Server를 실행하는 방법에 대한 질문과 대답을 제공합니다."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 10/05/2017
ms.author: jroth
ms.openlocfilehash: a001ae116e33e0b7be4431b0bc4c8bb319f4e801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Linux Azure Virtual Machines의 SQL Server에 대한 FAQ(질문과 대답)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

이 항목에서는 [Linux Azure Virtual Machines에서 SQL Server 실행](sql-server-linux-virtual-machines-overview.md)에 대해 가장 일반적인 질문 중 몇 가지에 대한 답변을 제공합니다.

> [!NOTE]
> 이 토픽에서는 Linux VM의 SQL Server에 관련된 문제에 초점을 맞춥니다. Windows VM에서 SQL Server를 실행 하는 경우 [Windows FAQ](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)를 참조하세요.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>질문과 대답

1. **SQL Server를 사용하여 Linux Azure 가상 컴퓨터를 만들려면 어떻게 해야 합니까?**

   가장 쉬운 해결 방법은 SQL Server가 포함된 Linux 가상 컴퓨터를 만드는 것입니다. Azure에 등록하고 포털에서 SQL VM을 만드는 방법에 대한 자습서는 [Azure Portal에 Linux SQL Server 가상 컴퓨터 프로비전](provision-sql-server-linux-virtual-machine.md)을 참조하세요. 무료 라이선스 버전(Developer 또는 Express)을 사용하거나 온-프레미스 라이선스를 재사용하여 VM에 SQL Server를 수동으로 설치할 수도 있습니다. 사용자 라이선스가 필요하면 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility)이 가능해야 합니다.

1. **Azure VM에서 SQL Server의 새 버전을 업그레이드하려면 어떻게 합니까?**

   현재는 Azure VM에서 실행 중인 SQL Server를 전체 업그레이드할 수 없습니다. 원하는 SQL Server 버전으로 새 Azure 가상 컴퓨터를 만든 다음 표준 [데이터 마이그레이션 기법](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview)을 사용하여 데이터베이스를 새 서버로 마이그레이션합니다.

1. **Azure VM에 라이선스가 있는 내 SQL Server 사본을 설치하려면 어떻게 해야 합니까?**

   먼저 Linux OS 전용 가상 컴퓨터를 만듭니다. 그런 다음 Linux 배포에 대한 [SQL Server 설치 단계](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms)를 실행합니다. SQL Server의 무료 라이선스 버전 중 하나를 설치하지 않은 한 SQL Server 라이선스와 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)이 필요합니다.

1. **SQL Server에 대한 BYOL(사용자 라이선스 필요) Linux 가상 컴퓨터 이미지가 있나요?**

   현재 SQL Server에 대한 BYOL(사용자 라이선스 필요) Linux 가상 컴퓨터 이미지는 없습니다. 그러나 앞의 질문에서 설명한 것처럼 Linux 전용 VM에 SQL Server를 수동으로 설치할 수 있습니다.

1. **종량제 갤러리 이미지 중 하나에서 만들어진 경우 사용자 고유의 SQL Server 라이선스를 사용하도록 VM을 변경할 수 있나요?**

   아니요. 분 단위로 요금이 부과되는 라이선스에서 사용자 라이선스 사용으로 전환할 수 없습니다. 새로운 Linux VM을 만들고, SQL Server를 설치하고, 데이터를 마이그레이션해야 합니다. 사용자 고유 라이선스를 가져오기에 대한 자세한 내용은 이전 질문을 참조하세요.

1. **함께 설치되는 관련 SQL Server 패키지는 무엇인가요?**

   SQL Server Linux VM에 기본적으로 설치되는 SQL Server 패키지를 보려면 [설치된 패키지](sql-server-linux-virtual-machines-overview.md#packages)를 참조하세요.

1. **Azure VM에서 SQL Server 고가용성 솔루션이 지원되나요?**

   지금은 없습니다. Always On 가용성 그룹과 장애 조치(failover) 클러스터링에는 모두 Pacemaker 같은 Linux의 클러스터링 솔루션이 필요합니다. SQL Server에 대해 지원되는 Linux 배포는 클라우드에서 고가용성 추가 기능을 지원하지 않습니다.

1. **지출 한도가 있는 Azure 구독에서 RHEL 또는 SLES SQL Server VM을 프로비전하지 못하는 이유는 무엇인가요?**

   RHEL 및 SLES 가상 컴퓨터에는 지출 한도가 없는 구독과, 구독에 연결된 검증된 결제 수단(보통은 신용카드)가 필요합니다. 지출 한도를 제거하지 않고 RHEL 또는 SLES VM을 프로비전하면 구독이 비활성화되고 모든 VM/서비스가 중지됩니다. 이 상태가 되어 구독을 다시 활성화하려면 [지출 한도를 제거](https://account.windowsazure.com/subscriptions)합니다. 현재 청구 주기에 대해 남은 크레딧이 복원되지만 다시 시작하여 실행을 계속하도록 선택하면 RHEL 또는 SLES VM 이미지 추가 요금이 해당 신용카드로 청구됩니다.

## <a name="resources"></a>리소스

**Linux VM**:

* [Linux VM의 SQL Server 개요](sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux VM 프로비전](provision-sql-server-linux-virtual-machine.md)
* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows VM**:

* [Windows VM에서 SQL Server 개요](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM 프로비전](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [FAQ(Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)