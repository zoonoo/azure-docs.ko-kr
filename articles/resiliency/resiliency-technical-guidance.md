---
title: "복원력 기술 지침 목록 | Microsoft 문서"
description: "재해 복구 및 비즈니스 연속성에 대한 계획 뿐만 아니라 복원력 있고 항상 사용 가능한 내결함성 응용 프로그램을 이해하고 설계하는 방법에 대한 기술 문서의 인덱스입니다."
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: 0eafb464-810a-4539-905e-8c91e5f3c09e
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: d0cae49b3d45be153daf53cd3ff352e25c447ed1


---
# <a name="azure-resiliency-technical-guidance"></a>Azure 복원력 기술 지침
## <a name="introduction"></a>소개
고가용성 및 재해 복구 요구 사항을 충족하려면 두 가지 정보가 필요합니다.

* 클라우드 플랫폼의 기능에 대해 자세한 기술적 이해
* 제대로 분산된 서비스를 설계하는 방법 정보

이 시리즈 문서에서는 전자인 복원력(때로는 비즈니스 연속성이라고 함)과 관련하여 Azure 플랫폼의 기능 및 제한 사항을 다룹니다. 후자에 관심이 있는 경우 [Azure 응용 프로그램에 대한 재해 복구 및 고가용성](https://aka.ms/drtechguide)에 초점을 맞춘 시리즈 문서를 참조하세요. 이 문서 시리즈가 아키텍처와 설계 패턴에 대해 언급하지만 초점을 맞춘 것은 아닙니다. 설계 지침의 경우 [추가 리소스](#additional-resources) 섹션의 내용을 참고해야 합니다.

정보는 다음 문서에 구성되어 있습니다.

* [로컬 오류로부터 복구](resiliency-technical-guidance-recovery-local-failures.md)에 초점을 맞춘 시리즈 문서를 참조하세요.
  물리적 하드웨어(예: 드라이브, 서버 및 네트워크 장치)가 실패할 수 있습니다. 갑작스럽게 부하가 증가하면 리소스가 소진될 수 있습니다. 이 문서에서는 이러한 조건에서 고가용성을 유지하기 위해 Azure가 제공하는 기능을 설명합니다.
* [Azure 지역 전체의 서비스 중단으로부터 복구](resiliency-technical-guidance-recovery-loss-azure-region.md) -
  광범위한 오류는 거의 없지만 이론적으로는 가능합니다. 전체 지역은 네트워크 오류로 인해 격리되거나 자연 재해로 인해 물리적으로 손상될 수 있습니다. 이 문서에서는 Azure를 사용하여 여러 지리적 지역에 걸쳐 응용 프로그램을 만드는 방법을 설명합니다.
* [온-프레미스에서 Azure로 복구](resiliency-technical-guidance-recovery-on-premises-azure.md) -
  클라우드는 재해 복구의 경제를 크게 변경하여 조직에서 Azure를 사용하여 복구를 위해 두 번째 사이트를 설정할 수 있도록 합니다. 보조 데이터 센터를 구축하고 유지 관리하는 비용의 일부로 수행할 수 있습니다. 이 문서에서는 Azure가 온-프레미스 데이터 센터를 클라우드로 확장하기 위해 제공하는 기능을 설명합니다.
* [데이터 손상 또는 우발적 삭제로부터 복구](resiliency-technical-guidance-recovery-data-corruption.md) -
  응용 프로그램에는 데이터를 손상시키는 버그가 있을 수 있습니다. 연산자는 중요한 데이터를 잘못 삭제할 수 있습니다. 이 문서에서는 데이터를 백업하고 이전의 특정 시점으로 복원하기 위해 Azure에서 제공하는 기능을 설명합니다.

## <a name="additional-resources"></a>추가 리소스
* [Microsoft Azure에서 빌드된 응용 프로그램에 대한 재해 복구 및 고가용성](resiliency-disaster-recovery-high-availability-azure-applications.md) -
  이 문서는 고가용성 및 재해 복구의 자세한 개요입니다. 참조 및 트랜잭션 데이터에 대한 수동 복제의 과제를 다룹니다. 마지막 섹션에서는 가장 높은 수준의 가용성을 위해 Azure 지역에 확장된 재해 복구 토폴로지에 대한 다양한 유형의 요약을 제공합니다.
* [고가용성 검사 목록](resiliency-high-availability-checklist.md) -
  이 문서는 응용 프로그램의 복원력 및 가용성을 향상시키는 데 도움이 될 수 있는 기능, 서비스 및 설계의 목록입니다.
* [Microsoft Azure 서비스 복원력 지침](resiliency-service-guidance-index.md) -
  이 문서는 Azure 서비스의 인덱스이며 재해 복구 지침 및 설계 지침에 대한 연결을 제공합니다.
* [개요: SQL Database를 통한 클라우드 무중단 업무 방식 및 데이터베이스 재해 복구](../sql-database/sql-database-business-continuity.md) -
  이 문서에서는 Azure SQL 데이터베이스를 사용할 수 있습니다. 백업 및 복원 전략에 주로 집중합니다. 클라우드 서비스에서 Azure SQL 데이터베이스를 사용하는 경우 이 문서 및 관련된 리소스를 검토해야 합니다.
* [Azure Virtual Machines에서 SQL Server의 고가용성 및 재해 복구](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) -
  이 문서는 IaaS(Infrastructure-as-a-Service)를 사용하여 데이터베이스 서비스를 호스트하는 경우 탐색할 수 있는 가용성 옵션을 설명합니다. AlwaysOn 가용성 그룹, 데이터베이스 미러링, 로그 전달 및 백업/복원에 대해 설명합니다. 여러 자습서에는 이러한 기술을 사용하는 방법을 보여 줍니다.
* [Azure Cloud Services에서 대규모 서비스를 설계하는 모범 사례](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/) -
  이 문서는 확장성이 뛰어난 클라우드 아키텍처의 개발을 집중 설명하니다. 확장성을 개선하기 위해 사용하는 대부분의 기술은 가용성도 개선합니다. 또한 응용 프로그램이 부하가 증가한 상태에서 확장할 수 없으면 확장성은 가용성 문제가 됩니다.
* [Azure Virtual Machines에서 SQL Server 백업 및 복원](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) -
  이 문서는 Azure 가상 컴퓨터에서 실행되는 Microsoft SQL Server를 백업하고 복원하는 방법에 대한 기술 지침을 제공합니다.
* [Failsafe: 복원력 있는 클라우드 아키텍처 지침](https://channel9.msdn.com/Series/FailSafe) -
  이 문서는 복원력 있는 클라우드 아키텍처를 구축하기 위한 지침, Microsoft의 기술에 해당 아키텍처를 구현하기 위한 지침 및 특정 시나리오에 이러한 아키텍처를 구현하기 위한 작성 방버을 제공합니다.
* [기술 사례 연구: 클라우드 기술로 재해 복구 개선](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery) -
  이 사례 연구에서는 Microsoft IT에서 Azure를 사용하여 재해 복구를 개선하는 방법을 보여 줍니다.

## <a name="next-steps"></a>다음 단계
이 문서는 Azure 복원력을 위한 기술 지침에 집중된 시리즈의 일부입니다. 이 시리즈의 다른 문서를 읽고 싶다면 [로컬 오류로부터 복구](resiliency-technical-guidance-recovery-local-failures.md)부터 시작합니다.




<!--HONumber=Nov16_HO3-->


