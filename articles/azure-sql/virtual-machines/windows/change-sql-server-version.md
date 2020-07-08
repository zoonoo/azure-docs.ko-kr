---
title: SQL Server 버전의 현재 위치의 변경
description: Azure에서 SQL Server 가상 컴퓨터의 버전을 변경 하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: 63c4c7b596a32901d127642118d224c2b318510f
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963022"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Azure VM에서 SQL Server 버전의 내부 변경

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Microsoft Azure의 Windows VM (가상 머신)에서 Microsoft SQL Server 버전을 변경 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

SQL Server에 대 한 전체 업그레이드를 수행 하려면 다음 조건이 적용 됩니다.

- 원하는 SQL Server 버전의 설치 미디어가 필요 합니다. [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)가 있는 고객은 [볼륨 라이선싱 센터](https://www.microsoft.com/Licensing/servicecenter/default.aspx)에서 설치 미디어를 구할 수 있습니다. 소프트웨어 보증이 없는 고객은 최신 버전의 SQL Server (일반적으로 C:\SQLServerFull에 있음)를 포함 하는 Azure Marketplace SQL Server VM 이미지에서 설치 미디어를 사용할 수 있습니다.
- 버전 업그레이드는 [지원 업그레이드 경로](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15?view=sql-server-ver15)를 따라야 합니다.

## <a name="planning-for-version-change"></a>버전 변경 계획

버전을 변경 하기 전에 다음 항목을 검토 하는 것이 좋습니다.

1. 업그레이드할 계획인 버전의 새로운 기능을 확인 합니다.

   - [SQL 2019](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-ver15?view=sql-server-ver15) 의 새로운 기능
   - [SQL 2017](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2017?view=sql-server-ver15) 의 새로운 기능
   - [SQL 2016](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2016?view=sql-server-ver15) 의 새로운 기능
   - [SQL 2014](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2016?view=sql-server-2014) 의 새로운 기능

1. 업그레이드의 영향을 최소화 하기 위해 데이터베이스 호환성 모드를 사용할 수 있도록 변경할 버전에 대 한 [호환성 인증](https://docs.microsoft.com/sql/database-engine/install-windows/compatibility-certification?view=sql-server-ver15) 을 확인 하는 것이 좋습니다.
1. 성공적인 결과를 확인 하는 데 도움이 되도록 다음 문서를 검토할 수 있습니다.

   - [비디오: 현대화 SQL Server | Pam Lahoud & Pedro Lopes | 20 년의 통과](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [AB 테스트에 대 한 데이터베이스 실험 도우미](https://docs.microsoft.com/sql/dea/database-experimentation-assistant-overview?view=sql-server-ver15)
   - [쿼리 튜닝 길잡이를 사용하여 데이터베이스 업그레이드](https://docs.microsoft.com/sql/relational-databases/performance/upgrade-dbcompat-using-qta?view=sql-server-ver15)
   - [데이터베이스 호환성 수준 변경 및 쿼리 저장소 사용](https://docs.microsoft.com/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store?view=sql-server-ver15)

## <a name="upgrade-sql-version"></a>SQL 버전 업그레이드

> [!WARNING]
> SQL Server 버전을 업그레이드 하면 Analysis Services 및 R Services와 같은 관련 서비스 외에도 SQL Server 서비스를 다시 시작 합니다.

SQL Server 버전을 업그레이드 하려면 SQL Server의 [업그레이드 경로를 지 원하는](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15?view=sql-server-ver15) 최신 버전의 SQL Server 설치 미디어를 다운로드 하 고 다음 단계를 수행 합니다.

1. 프로세스를 시작 하기 전에 시스템 (tempdb 제외) 및 사용자 데이터베이스를 포함 하 여 데이터베이스를 백업 합니다. Azure Backup 서비스를 사용 하 여 응용 프로그램 일치 VM 수준 백업을 만들 수도 있습니다.
1. SQL Server 설치 미디어에서 Setup.exe를 시작 합니다.
1. 설치 마법사가 SQL Server 설치 센터를 시작 합니다. SQL Server의 기존 인스턴스를 업그레이드 하려면 탐색 창에서 **설치** 를 선택한 다음 **이전 버전의 SQL Server에서 업그레이드**를 선택 합니다.

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="SQL Server 버전을 업그레이드 하기 위한 선택 항목":::

1. **제품 키** 페이지에서 무료 버전의 SQL Server로 업그레이드할지 아니면 제품의 프로덕션 버전에 대 한 PID 키가 있는지를 나타내는 옵션을 선택 합니다. 자세한 내용은 [버전 및 지원 되는 기능 SQL Server 2019 ()](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15) 및 지원 [되는 버전 및 버전 업그레이드 (SQL Server 2016)](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades?view=sql-server-ver15)를 참조 하세요.
1. **업그레이드 준비 완료** 페이지가 표시 될 때까지 **다음** 을 선택 하 고 **업그레이드**를 선택 합니다. 변경 내용을 적용 하는 동안 몇 분 동안 설치 창이 응답 하지 않을 수 있습니다. **전체** 페이지에서 업그레이드가 완료 되었음을 확인할 수 있습니다. 업그레이드 하는 단계별 절차는 [전체 절차](https://docs.microsoft.com/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup?view=sql-server-ver15#procedure)를 참조 하세요.

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="완료 페이지":::

버전을 변경 하는 것 외에도 SQL Server 버전을 변경한 경우 버전을 업데이트 하 고 **포털의 버전 및 버전 확인** 섹션을 참조 하 여 SQL VM 인스턴스를 변경 합니다.

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="버전 메타 데이터 변경":::

## <a name="downgrade-the-version-of-sql-server"></a>SQL Server 버전을 다운 그레이드 합니다.

SQL Server 버전을 다운 그레이드 하려면 SQL Server을 완전히 제거한 후 원하는 버전으로 다시 설치 해야 합니다. 이는 이전 버전의 데이터베이스를 새로 설치 된 이전 버전으로 복원할 수 없기 때문에 SQL Server를 새로 설치 하는 것과 비슷합니다. 데이터베이스를 처음부터 다시 만들어야 합니다. 업그레이드 하는 동안 SQL Server 버전을 변경한 경우 Azure Portal의 SQL Server VM **버전** 속성을 새 버전 값으로 변경 합니다. 이 VM과 연결 된 메타 데이터 및 청구를 업데이트 합니다.

> [!WARNING]
> SQL Server의 내부 다운 그레이드는 지원 되지 않습니다.

다음 단계를 수행 하 여 SQL Server 버전을 다운 그레이드할 수 있습니다.

1. [이후 버전 에서만 제공](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx)되는 기능을 사용 하 고 있지 않은지 확인 합니다.
1. 시스템 (tempdb 제외) 및 사용자 데이터베이스를 포함 하 여 모든 데이터베이스를 백업 합니다.
1. 필요한 서버 수준 개체 (예: 서버 트리거, 역할, 로그인, 연결 된 서버, 작업, 자격 증명 및 인증서)를 모두 내보냅니다.
1. 이전 버전에서 사용자 데이터베이스를 다시 만드는 스크립트가 없는 경우 모든 개체를 스크립팅 하 고 BCP.exe, SSIS 또는 DACPAC를 사용 하 여 모든 데이터를 내보내야 합니다.

   대상 버전, 종속 개체 및 고급 옵션과 같은 항목을 스크립팅할 때 올바른 옵션을 선택 했는지 확인 합니다.

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="스크립팅 옵션":::

1. SQL Server와 모든 연결된 서비스를 완전히 제거합니다.
1. VM을 다시 시작합니다.
1. 원하는 프로그램 버전의 미디어를 사용 하 여 SQL Server를 설치 합니다.
1. 최신 서비스 팩과 누적 업데이트를 설치합니다.
1. 3 단계에서 내보낸 모든 필요한 서버 수준 개체를 가져옵니다.
1. 만든 스크립트 또는 4 단계의 파일을 사용 하 여 필요한 모든 사용자 데이터베이스를 처음부터 다시 만듭니다.

## <a name="verify-the-version-and-edition-in-the-portal"></a>포털에서 버전 및 버전 확인

SQL Server 버전을 변경한 후에는 Azure Portal를 사용 하 여 SQL Server의 버전을 볼 수 있도록 [SQL VM 리소스 공급자](sql-vm-resource-provider-register.md) 에 SQL Server VM를 다시 등록 합니다. 그러면 나열 된 버전 번호에 새로 업그레이드 된 버전 및 SQL Server 설치 버전이 반영 됩니다.

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="버전 확인":::

> [!NOTE]
> SQL VM 리소스 공급자에 이미 등록 한 경우 [RP에서 등록을 취소](sql-vm-resource-provider-register.md#unregister-from-rp) 한 다음 vm에 설치 된 SQL Server 올바른 버전과 버전을 검색 하도록 [SQL vm 리소스를 다시 등록 합니다](sql-vm-resource-provider-register.md#register-with-rp) . 이 VM과 연결 된 메타 데이터 및 청구 정보를 업데이트 합니다.

## <a name="remarks"></a>설명

- 업그레이드를 완료 한 후에는 백업/통계 업데이트/인덱스 다시 작성/일관성 확인을 시작 하는 것이 좋습니다. 개별 데이터베이스 호환성 수준을 확인 하 여 원하는 수준을 반영할 수도 있습니다.
- VM에서 SQL Server 업데이트 한 후에는 Azure Portal의 SQL Server **버전** 속성이 대금 청구를 위해 설치 된 버전 번호와 일치 하는지 확인 합니다.
- [버전을 변경](change-sql-server-edition.md#change-edition-in-portal) 하는 기능은 SQL VM 리소스 공급자의 기능입니다. Azure Portal을 통해 Azure Marketplace 이미지를 배포하면 SQL Server VM이 자동으로 리소스 공급자에 등록됩니다. 그러나 SQL Server를 자동으로 설치 하는 고객은 SQL Server VM을 수동으로 [등록](sql-vm-resource-provider-register.md)해야 합니다.
- SQL Server VM 리소스를 삭제 하면 이미지의 하드 코드 된 버전 설정이 복원 됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.md)
- [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
- [Windows VM의 SQL Server 릴리스 정보](doc-changes-updates-release-notes.md)
