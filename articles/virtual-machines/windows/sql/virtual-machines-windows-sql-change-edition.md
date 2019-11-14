---
title: SQL Server 버전의 전체 업그레이드
description: Azure에서 SQL Server VM 버전을 변경 하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 1db45097b0416b680571cb47ec1d9b52f9275c43
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022213"
---
# <a name="perform-an-in-place-upgrade-of-a-sql-server-edition-on-an-azure-vm"></a>Azure VM에서 SQL Server 버전의 전체 업그레이드를 수행 합니다.

이 문서에서는 Azure의 Windows 가상 머신에서 SQL Server 버전을 변경 하는 방법을 설명 합니다. 

SQL Server 버전은 제품 키에 따라 결정 되며 설치 과정에서 지정 됩니다. 버전은 SQL Server 제품에서 사용할 수 있는 [기능](/sql/sql-server/editions-and-components-of-sql-server-2017) 을 결정 합니다. 설치 미디어를 사용 하 여 SQL Server 버전을 변경 하 고 비용을 줄이거나 업그레이드 하 여 더 많은 기능을 사용할 수 있습니다.

SQL VM 리소스 공급자를 사용 하 여 등록 한 후에 설치 미디어를 사용 하 여 SQL Server 버전을 업데이트 한 경우 Azure 청구를 적절 하 게 업데이트 하려면 SQL VM 리소스의 SQL Server edition 속성을 다음과 같이 설정 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. SQL Server 가상 컴퓨터 리소스로 이동 합니다. 
1. **설정**아래에서 **구성**을 선택 합니다. 그런 다음 **버전**아래의 드롭다운 목록에서 원하는 SQL Server 버전을 선택 합니다. 

   ![버전 메타 데이터 변경](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. 먼저 SQL Server 버전을 변경 해야 한다는 경고를 검토 하 고 edition 속성이 SQL Server 버전과 일치 해야 합니다. 
1. **적용** 을 선택 하 여 버전 메타 데이터 변경 내용을 적용 합니다. 


## <a name="prerequisites"></a>선행 조건

SQL Server 버전의 현재 위치를 변경 하려면 다음이 필요 합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- [SQL VM 리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md)에 등록 된 [Windows의 SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 입니다.
- 원하는 SQL Server 버전으로 미디어를 설치 합니다. [소프트웨어 보증이](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) 있는 고객은 [볼륨 라이선스 센터](https://www.microsoft.com/Licensing/servicecenter/default.aspx)에서 설치 미디어를 가져올 수 있습니다. 소프트웨어 보증이 없는 고객은 원하는 버전이 설치 된 Azure Marketplace SQL Server VM 이미지에서 설치 미디어를 사용할 수 있습니다.


## <a name="upgrade-an-edition"></a>버전 업그레이드

> [!WARNING]
> SQL Server 버전을 업그레이드 하면 Analysis Services 및 R Services와 같은 관련 서비스와 함께 SQL Server 서비스를 다시 시작 합니다. 

SQL Server를 업그레이드 하려면 원하는 버전의 SQL Server에 대 한 SQL Server 설치 미디어를 가져온 후 다음을 수행 합니다.

1. SQL Server 설치 미디어에서 Setup.exe를 엽니다. 
1. **유지 관리** 로 이동 하 여 **버전 업그레이드** 옵션을 선택 합니다. 

   ![SQL Server 버전을 업그레이드 하기 위한 선택 항목](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. **업그레이드 버전 준비** 페이지가 표시 될 때까지 **다음** 을 선택 하 고 **업그레이드**를 선택 합니다. 변경 내용이 적용 되는 동안 설치 창이 몇 분 동안 응답 하지 않을 수 있습니다. **전체** 페이지에서 버전 업그레이드가 완료 되었음을 확인할 수 있습니다. 

SQL Server 버전을 업그레이드 한 후에는 앞에서 설명한 대로 Azure Portal에서 SQL Server 가상 머신의 버전 속성을 수정 합니다. 이 VM과 연결 된 메타 데이터 및 청구를 업데이트 합니다.

## <a name="downgrade-an-edition"></a>버전 다운 그레이드

SQL Server 버전을 다운 그레이드 하려면 SQL Server를 완전히 제거 하 고 원하는 버전의 설치 미디어를 사용 하 여 다시 설치 해야 합니다.

> [!WARNING]
> SQL Server를 제거 하면 추가 가동 중지 시간이 발생할 수 있습니다. 

다음 단계를 수행 하 여 SQL Server 버전을 다운 그레이드할 수 있습니다.

1. 시스템 데이터베이스를 포함 하 여 모든 데이터베이스를 백업 합니다. 
1. 시스템 데이터베이스 (master, model 및 msdb)를 새 위치로 이동 합니다. 
1. SQL Server와 관련 된 모든 서비스를 완전히 제거 합니다. 
1. 가상 머신을 다시 시작합니다. 
1. 원하는 SQL Server 버전의 미디어를 사용 하 여 SQL Server를 설치 합니다.
1. 최신 서비스 팩 및 누적 업데이트를 설치 합니다.  
1. 설치 하는 동안 생성 된 새 시스템 데이터베이스를 이전에 다른 위치로 이동한 시스템 데이터베이스로 바꿉니다. 

SQL Server 버전을 다운 그레이드 한 후에는 앞에서 설명한 대로 Azure Portal에서 SQL Server 가상 머신의 버전 속성을 수정 합니다. 이 VM과 연결 된 메타 데이터 및 청구를 업데이트 합니다.

## <a name="remarks"></a>설명

- SQL Server VM에 대 한 버전 속성은 종 량 제 및 사용자의 라이선스 가져오기 라이선스를 포함 하 여 모든 SQL Server 가상 머신에 설치 된 SQL Server 인스턴스 버전과 일치 해야 합니다.
- SQL Server VM 리소스를 삭제 하는 경우 이미지의 하드 코드 된 버전 설정으로 돌아갑니다.
- 버전을 변경 하는 기능은 SQL VM 리소스 공급자의 기능입니다. Azure Portal를 통해 Azure Marketplace 이미지를 배포 하면 리소스 공급자에 SQL Server VM 자동으로 등록 됩니다. 그러나 SQL Server를 자동으로 설치 하는 고객은 SQL Server VM을 수동으로 [등록](virtual-machines-windows-sql-register-with-resource-provider.md)해야 합니다.
- 가용성 집합에 SQL Server VM를 추가 하려면 VM을 다시 만들어야 합니다. 가용성 집합에 추가 된 모든 Vm은 기본 버전으로 돌아가서 버전을 다시 수정 해야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server에 대 한 FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server에 대 한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server에 대 한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


