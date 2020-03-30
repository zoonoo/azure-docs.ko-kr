---
title: SQL Server 버전의 현재 변경
description: Azure에서 SQL Server 가상 컴퓨터의 버전을 변경하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605449"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Azure VM에서 SQL Server 에디션의 현재 변경

이 문서에서는 Azure의 Windows 가상 컴퓨터에서 SQL Server 버전을 변경하는 방법을 설명합니다. 

SQL Server 버전은 제품 키에 의해 결정되며 설치 미디어를 사용하여 설치 프로세스 중에 지정됩니다. 이 에디션은 SQL Server 제품에서 사용할 수 있는 [기능을](/sql/sql-server/editions-and-components-of-sql-server-2017) 지정합니다. 설치 미디어를 사용하여 SQL Server 버전을 변경하고 다운그레이드하여 비용을 줄이거나 업그레이드하여 더 많은 기능을 활성화할 수 있습니다.

SQL Server 버전이 SQL Server VM으로 내부적으로 변경되면 청구를 위해 Azure Portal에서 SQL Server의 에디션 속성을 업데이트해야 합니다. 

## <a name="prerequisites"></a>사전 요구 사항

SQL Server 에디션을 현재 변경하려면 다음이 필요합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- SQL [VM 리소스 공급자에](virtual-machines-windows-sql-register-with-resource-provider.md)등록된 [Windows의](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) SQL 서버 VM .
- **원하는 버전의** SQL Server를 통해 미디어를 설정합니다. [소프트웨어 보증이](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) 있는 고객은 볼륨 [라이선싱 센터에서](https://www.microsoft.com/Licensing/servicecenter/default.aspx)설치 미디어를 구할 수 있습니다. 소프트웨어 보증이 없는 고객은 원하는 버전(일반적으로 위치)이 있는 Azure Marketplace SQL Server VM `C:\SQLServerFull`이미지의 설치 미디어를 사용할 수 있습니다. 


## <a name="upgrade-an-edition"></a>에디션 업그레이드

> [!WARNING]
> SQL Server 버전을 업그레이드하면 분석 서비스 및 R 서비스와 같은 모든 관련 서비스와 함께 SQL Server용 서비스가 다시 시작됩니다. 

SQL Server 버전을 업그레이드하려면 원하는 SQL Server 버전에 대한 SQL Server 설치 미디어를 가져온 다음 다음을 수행합니다.

1. SQL Server 설치 미디어에서 Setup.exe를 엽니다. 
1. 유지 **관리로** 이동하여 **에디션 업그레이드** 옵션을 선택합니다. 

   ![SQL Server 버전 업그레이드 선택](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. **업그레이드 준비 버전** 페이지에 도달할 때까지 **다음을** 선택한 다음을 선택한 다음 **업그레이드**를 선택합니다. 변경이 적용되는 동안 설정 창이 몇 분 동안 응답하지 않을 수 있습니다. **전체** 페이지에서 에디션 업그레이드가 완료되었습니다. 

SQL Server 버전을 업그레이드한 후 Azure 포털에서 SQL Server 가상 시스템의 에디션 속성을 수정합니다. 이렇게 하면 이 VM과 연결된 메타데이터 및 청구가 업데이트됩니다.

## <a name="downgrade-an-edition"></a>에디션 다운그레이드

SQL Server 버전을 다운그레이드하려면 SQL Server를 완전히 제거하고 원하는 에디션 설치 미디어로 다시 설치해야 합니다. 

> [!WARNING]
> SQL Server를 제거하면 추가 가동 중지 시간이 발생할 수 있습니다. 

다음 단계를 수행하여 SQL Server 버전을 다운그레이드할 수 있습니다.

1. 시스템 데이터베이스를 포함한 모든 데이터베이스를 백업합니다. 
1. 시스템 데이터베이스(마스터, 모델 및 msdb)를 새 위치로 이동합니다. 
1. SQL Server 및 모든 관련 서비스를 완전히 제거합니다. 
1. 가상 머신을 다시 시작합니다. 
1. 원하는 버전의 SQL Server와 함께 미디어를 사용하여 SQL Server를 설치합니다.
1. 최신 서비스 팩과 누적 업데이트를 설치합니다.  
1. 설치 중에 생성된 새 시스템 데이터베이스를 이전에 다른 위치로 이동한 시스템 데이터베이스로 바꿉습니다. 

SQL Server 에디션을 다운그레이드한 후 Azure 포털에서 SQL Server 가상 시스템의 에디션 속성을 수정합니다. 이렇게 하면 이 VM과 연결된 메타데이터 및 청구가 업데이트됩니다.

## <a name="change-edition-in-portal"></a>포털에서 에디션 변경 

설치 미디어를 사용하여 SQL Server 버전을 변경하고 SQL [VM 리소스 공급자에](virtual-machines-windows-sql-register-with-resource-provider.md)SQL Server VM을 등록한 후에는 Azure Portal을 사용하여 청구를 위해 SQL Server VM의 Edition 속성을 수정할 수 있습니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 
1. SQL Server 가상 시스템 리소스로 이동합니다. 
1. **설정에서** **구성을**선택합니다. 그런 다음 **에디션**아래의 드롭다운 목록에서 원하는 SQL Server 버전을 선택합니다. 

   ![에디션 메타데이터 변경](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. 먼저 SQL Server 버전을 변경해야 하며 에디션 속성이 SQL Server 버전과 일치해야 한다는 경고를 검토합니다. 
1. 에디션 메타데이터 변경 내용을 적용하려면 **적용을** 선택합니다. 


## <a name="remarks"></a>설명

- SQL Server VM의 에디션 속성은 종량제 및 라이선스 보유자 를 포함한 모든 SQL Server 가상 컴퓨터에 설치된 SQL Server 인스턴스 의 버전과 일치해야 합니다.
- SQL Server VM 리소스를 삭제하면 이미지의 하드 코딩된 버전 설정으로 돌아갑니다.
- 에디션을 변경하는 기능은 SQL VM 리소스 공급자의 기능입니다. Azure 포털을 통해 Azure Marketplace 이미지를 배포하는 것은 리소스 공급자와 SQL Server VM을 자동으로 등록합니다. 그러나 SQL Server를 자체 설치하는 고객은 [SQL Server VM을](virtual-machines-windows-sql-register-with-resource-provider.md)수동으로 등록해야 합니다.
- SQL Server VM을 가용성 집합에 추가하려면 VM을 다시 만들어야 합니다. 가용성 집합에 추가된 모든 VM은 기본 버전으로 돌아가고 에디션을 다시 수정해야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM의 SQL 서버 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL 서버에 대한 자주 묻는 질문](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL 서버에 대한 가격 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM에서 SQL 서버에 대한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


