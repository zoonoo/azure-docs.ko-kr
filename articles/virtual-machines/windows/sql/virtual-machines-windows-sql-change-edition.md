---
title: Azure VM에 SQL Server 버전의 현재 위치 업그레이드를 수행 하는 방법 | Microsoft Docs
description: Azure에서 SQL Server VM의 버전을 변경 하는 방법에 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607547"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>Azure VM에 SQL Server 버전의 현재 위치 업그레이드를 수행 하는 방법

이 문서에서는 Azure에서 Windows 가상 머신에서 기존 SQL Server에 대 한 SQL Server의 버전을 변경 하는 방법을 설명 합니다. 

버전의 SQL Server 제품 키에 따라 결정 됩니다 및 설치 프로세스를 사용 하 여 지정 됩니다. 어떤 버전 결정 [기능](/sql/sql-server/editions-and-components-of-sql-server-2017) SQL Server 제품 내에서 사용할 수 있습니다. 설치 미디어 및 비용을 절감 하거나 더 많은 기능으로 업그레이드 하거나 다운 그레이드를 사용 하 여 SQL Server 버전을 변경할 수 있습니다.

설치 미디어를 사용 하 여 SQL VM 리소스 공급자를 등록 한 후 SQL Server의 버전을 업데이트 하는 경우 다음 Azure 업데이트 그에 따라 요금을 청구 해야 SQL VM 리소스의 SQL Server Edition 속성 다음과 같이 설정.

1. [Azure 포털](https://portal.azure.com)할 수 있습니다. 
1. SQL Server 가상 머신 리소스를 이동 합니다. 
1. 아래 **설정을**를 선택 **구성** 아래에 있는 드롭다운 목록에서 SQL Server의 원하는 버전을 선택 합니다 **Edition**합니다. 

   ![버전 메타 데이터 변경](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. 먼저 SQL Server 버전을 변경 해야 하 고 edition 속성 SQL Server 버전과 일치 해야 사용자에 게 알리는 경고를 검토 합니다. 
1. 선택 **적용** 버전 메타 데이터 변경 내용을 적용 하려면. 


## <a name="prerequisites"></a>필수 구성 요소

SQL Server 버전의 전체 변경을 위해 다음 해야 합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- Windows [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 등록 된 [SQL VM 리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md)합니다.
- SQL Server의 원하는 버전을 사용 하 여 미디어를 설치 합니다. 고객 [software assurance가 포함](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) 에서 해당 설치 미디어를 가져올 수 합니다 [볼륨 라이선스 센터](https://www.microsoft.com/Licensing/servicecenter/default.aspx)합니다. 고객은 소프트웨어 없는 assurance 해당 버전에는 marketplace SQL Server VM 이미지에서 설치 미디어를 사용할 수 있습니다.


## <a name="upgrade-edition"></a>버전 업그레이드

  > [!WARNING]
  > - **업그레이드 된 버전의 SQL Server Analysis Services 및 R Services와 같은 모든 연결 된 서비스 뿐만 아니라 SQL Server에 대 한 서비스 다시 시작 됩니다.** 

SQL server 버전으로 업그레이드 하려면 SQL Server의 원하는 버전에 대 한 SQL Server 설치 미디어 가져오고 다음을 수행 합니다.

1. SQL Server 설치 미디어에서 Setup.exe를 실행 합니다. 
1. 이동할 **유지 관리** 선택 합니다 **버전 업그레이드** 옵션입니다. 

   ![SQL Server의 버전 업그레이드](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. 선택 **다음** 도달할 때까지 합니다 **버전 업그레이드 준비** 페이지를 선택한 후 **업그레이드**합니다. 변경 내용이 적용 되는 중에 표시 됩니다 하는 동안 잠시 후 설치 창 중단 될 수 있습니다는 **완료** 페이지의 버전 업그레이드가 완료 되었는지 확인 합니다. 

위에 나와 있는 것 처럼 Azure portal에서 Sql 가상 컴퓨터의 "버전" 속성을 수정 해야 SQL Server 버전 업그레이드 되 면 이 메타 데이터 및이 VM과 연결 된 청구 업데이트 됩니다.

## <a name="downgrade-edition"></a>다운 그레이드 버전

  > [!WARNING]
  > - **완전히 작동 중단 시간이 추가로 발생할 수 있는 SQL Server를 제거 해야 SQL Server 버전 다운 그레이드**합니다. 


SQL Server 버전으로 다운 그레이드를 완전히 SQL Server를 제거 하 고 원하는 버전 설치 미디어를 사용 하 여 다시 설치 해야 합니다. 

다음이 단계를 수행 하 여 SQL Server의 버전을 다운 그레이드할 수 있습니다.

1. 시스템 데이터베이스를 포함 한 모든 데이터베이스를 백업 합니다. 
1. 시스템 데이터베이스 (master, model 및 msdb)을 새 위치로 이동 합니다. 
1. SQL Server와 관련 된 모든 서비스를 완전히 제거 하십시오. 
1. 가상 머신을 다시 시작합니다. 
1. SQL Server의 원하는 버전을 사용 하 여 미디어를 사용 하 여 SQL Server를 설치 합니다.
1. 최신 서비스 팩 및 누적 업데이트를 설치 합니다.  
1. 이전에 다른 위치로 이동 하는 시스템 데이터베이스를 사용 하 여 설치 하는 동안 만들어진 새 시스템 데이터베이스를 대체 합니다. 

위에 나와 있는 것 처럼 Azure portal에서 SQL 가상 머신의 '버전' 속성을 수정 해야 SQL Server 버전은 다운 그레이드 되 면 이 메타 데이터 및이 VM과 연결 된 청구 업데이트 됩니다.

## <a name="remarks"></a>설명

 - SQL Server VM에 대 한 edition 속성에 대 한 모든 SQL Virtual Machines PAYG와 BYOL 라이선스 유형을 포함 하 여 가상 컴퓨터에 설치 된 SQL Server 버전이 일치 해야 합니다.
 - SQL Server VM 리소스를 삭제 하면 이동 하 게 다시 이미지의 하드 코드 된 버전 설정 합니다.
  - 버전을 변경 하는 기능에는 SQL VM 리소스 공급자의 기능입니다. Azure portal 통해 marketplace 이미지를 자동으로 배포 리소스 공급자를 사용 하 여 SQL Server VM을 등록 합니다. 그러나 SQL Server 설치 하는 자체 고객 해야 수동으로 [SQL Server VM을 등록](virtual-machines-windows-sql-register-with-resource-provider.md)합니다.
- SQL Server VM을 가용성 집합에 추가 VM을 다시 필요 합니다. 이러한 모든 vm이 가용성에 추가 집합은 기본 버전으로 돌아가서 버전을 다시 수정 해야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server 가격 책정 가이드](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


