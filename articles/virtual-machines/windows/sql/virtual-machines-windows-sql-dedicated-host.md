---
title: Azure 전용 호스트의 SQL 서버 VM
description: Azure 전용 호스트에서 SQL Server VM을 실행하기 위한 자세한 내용을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834342"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>Azure 전용 호스트의 SQL 서버 VM 

이 문서에서는 [Azure 전용 호스트와](/azure/virtual-machines/windows/dedicated-hosts)함께 SQL Server VM을 사용하는 세부 사항에 대해 자세히 설명합니다. Azure 전용 호스트에 대한 추가 정보는 [Azure 전용](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)호스트 소개 블로그 게시물에서 찾을 수 있습니다. 

## <a name="overview"></a>개요
[Azure 전용 호스트는](/azure/virtual-machines/windows/dedicated-hosts) 하나 이상의 가상 컴퓨터를 호스팅할 수 있는 물리적 서버를 하나의 Azure 구독전용으로 제공하는 서비스입니다. 전용 호스트는 리소스로 제공되는 Microsoft 데이터 센터에서 사용되는 것과 동일한 물리적 서버입니다. 리전, 가용성 영역 및 장애 도메인 내에서 전용 호스트를 프로비전할 수 있습니다. 그런 다음 필요에 가장 적합한 구성으로 프로비저닝된 호스트에 VM을 직접 배치할 수 있습니다.

## <a name="limitations"></a>제한 사항

- 가상 시스템 크기 집합은 현재 전용 호스트에서 지원되지 않습니다.
- DSv3 및 ESv3: 다음 VM 시리즈가 지원됩니다. 

## <a name="licensing"></a>라이선스

Azure 전용 호스트에 SQL Server VM을 추가할 때 두 가지 라이선스 옵션 중에서 선택할 수 있습니다. 

  - **SQL VM 라이선스**: 각 SQL Server VM 라이선스에 대해 개별적으로 비용을 지불하는 기존 라이선스 옵션입니다. 
  - **전용 호스트 라이선스:** SQL Server 라이선스가 호스트 수준에서 번들로 제공되고 지불되는 Azure 전용 호스트에 사용할 수 있는 새 라이선스 모델입니다. 


기존 SQL Server 라이선스를 사용하기 위한 호스트 수준 옵션: 
  - SQL 서버 엔터프라이즈 에디션 Azure 하이브리드 혜택
    - SA 또는 구독이 있는 고객이 사용할 수 있습니다.
    - 사용 가능한 모든 물리적 코어에 라이선스를 부여하고 무제한 가상화를 즐길 수 있습니다(호스트가 지원하는 최대 vCPU까지).
        - Azure 전용 호스트에 Azure 하이브리드 혜택을 적용하는 것에 대한 자세한 내용은 [Azure 하이브리드 혜택 FAQ를](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)참조하십시오. 
  - 10월 1일 이전에 획득한 SQL 서버 라이선스
      - SQL Server 엔터프라이즈 버전에는 호스트 수준 및 VM 별 라이선스 옵션이 모두 있습니다. 
      - SQL Server 표준 에디션에는 VM별 라이선스 옵션만 사용할 수 있습니다. 
          - 자세한 내용은 [Microsoft 제품 약관을](https://www.microsoft.com/licensing/product-licensing/products)참조하십시오. 
  - SQL Server 전용 호스트 수준 옵션을 선택하지 않으면 다중 테넌트 VM과 마찬가지로 SQL Server AHB가 개별 VM 수준에서 선택될 수 있습니다.



## <a name="provisioning"></a>프로비전  
SQL Server VM을 전용 호스트에 프로비전하는 것은 다른 Azure 가상 컴퓨터와 다르지 않습니다. [Azure PowerShell,](../dedicated-hosts-powershell.md) [Azure 포털](../dedicated-hosts-portal.md)및 Azure [CLI를](../../linux/dedicated-hosts-cli.md)사용하여 이 작업을 수행할 수 있습니다.

기존 SQL Server VM을 전용 호스트에 추가하는 프로세스에는 가동 중지 시간이 필요하지만 데이터에 영향을 주지 않으며 데이터 손실이 없습니다. 그럼에도 불구하고 시스템 데이터베이스를 포함한 모든 데이터베이스는 이동하기 전에 백업해야 합니다.

## <a name="virtualization"></a>가상화 

전용 호스트의 이점 중 하나는 무제한 가상화입니다. 예를 들어 64개의 vCore에 대한 라이선스를 가질 수 있지만 호스트에 128개의 vCore를 갖도록 구성할 수 있으므로 vCore를 두 배로 늘리지만 SQL Server 라이선스의 절반만 지불하면 됩니다. 

호스트이기 때문에 가상화를 1:2 비율로 설정할 수 있습니다. 

## <a name="faq"></a>FAQ

**Q: Azure 전용 호스트의 Windows 서버/SQL Server 라이선스에 대해 Azure 하이브리드 혜택은 어떻게 작동합니까?**

A: 고객은 소프트웨어 보증 또는 적격 구독 라이선스를 사용하여 기존 Windows Server 및 SQL Server 라이선스의 값을 사용하여 Azure 하이브리드 혜택을 사용하여 Azure 전용 호스트에서 할인된 요금을 지불할 수 있습니다. Windows Server 데이터 센터 및 SQL Server 엔터프라이즈 에디션 고객은 전체 호스트에 라이선스를 부여할 때 무제한 가상화(기본 서버의 물리적 용량에 따라 호스트에 가능한 한 많은 Windows Server 가상 컴퓨터를 배포)를 받습니다. Azure 하이브리드 혜택을 사용합니다.  Azure 전용 호스트의 모든 Windows 서버 및 SQL Server 워크로드는 추가 비용 없이 Windows Server 및 SQL Server 2008/R2에 대한 확장 보안 업데이트를 받을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM의 SQL 서버 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL 서버에 대한 자주 묻는 질문](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL 서버에 대한 가격 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM에서 SQL 서버에 대한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


