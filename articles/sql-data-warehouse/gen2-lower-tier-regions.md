---
title: Azure SQL Data Warehouse Gen2, 하위 컴퓨팅 계층 지원 | Microsoft Docs
description: Azure SQL Data Warehouse Gen2, 이제 하위 컴퓨팅 계층 지원
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 12/03/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: d3c42c13e58156013167b8570a8845ce9de08b3b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52885456"
---
# <a name="azure-sql-data-warehouse-gen2-support-for-lower-compute-tiers"></a>하위 컴퓨팅 계층에 대한 Azure SQL Data Warehouse Gen2 지원

Microsoft는 매우 빠른 Azure SQL Data Warehouse Gen2에 대한 하위 컴퓨팅 계층을 추가하여, 까다로운 쿼리를 처리할 수는 데이터 웨어하우스를 실행하는 초급 수준 비용을 낮출 수 있도록 도와줍니다. 고객은 100 cDWU(데이터 웨어하우스 단위)로 시작해서 Azure SQL Data Warehouse의 최고 성능, 유연성 및 보안 기능을 경험할 수 있으며, 몇 분 안에 30,000 cDWU까지 확장할 수 있습니다. 하위 컴퓨팅 계층을 사용하여 고객은 Gen2의 성능과 유연성을 누릴 수 있습니다. 

Microsof는 차세대 데이터 웨어하우징에 대한 진입점을 낮춰 최적의 평가판 환경이 무엇인지 추측하지 않고 안전한 고성능 데이터 웨어하우스의 모든 이점을 평가하려는 가치 중심 고객에게 문을 열어주고 있습니다.  고객은 현재 500 cDWU 진입점에서 최저 100 cDWU로 시작할 수 있습니다.  SQL Data Warehouse Gen2는 일시 중지 및 다시 시작 작업을 계속 지원하며 컴퓨팅 시 뛰어난 유연성을 제공합니다.  또한 Gen2는 쿼리당 2.5배 많은 메모리, 최대 128개의 동시 쿼리 및 어댑티브 캐싱 기능과 함께, 무제한 열-스토어 스토리지 용량을 지원하여 동일한 가격의 동일한 Gen1 기반 데이터 웨어하우스 단위와 비교해 평균 5배 이상의 성능을 경험할 수 있습니다.  지역 중복 백업은 기본 제공되는 데이터 보호 기능과 함께 Gen2 표준입니다. Azure SQL Data Warehouse Gen2는 필요 시 확장 가능합니다.

현재 포털에서는 하위 계층 Gen2 인스턴스의 배포 또는 확장을 지원 하지 않습니다. 이 기능을 지원하기 위해 노력하고 있는 중입니다. 이 새로운 계층의 이점을 활용하려는 경우 [티켓을 제출](sql-data-warehouse-get-started-create-support-ticket.md)해 주세요.

## <a name="getting-started-with-azure-sql-data-warehouse-gen2"></a>Azure SQL Data Warehouse Gen2 시작 

고객은 새 Gen2 인스턴스를 배포하거나 기존 Gen1 데이터 웨어하우스 인스턴스를 업그레이드하여 차세대 데이터 웨어하우징의 유연성과 성능을 경험할 수 있습니다. 

[Azure SQL Data Warehouse Compute Optimized Gen2 계층](https://azure.microsoft.com/services/sql-data-warehouse/?v=17.44)을 사용해 보세요.
[Azure SQL Warehouse Compute Optimized Gen1에서 Gen2](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation)로 업그레이드 이 [Microsoft Mechanics 비디오](https://www.youtube.com/watch?v=Ap8I3UZonzI&feature=youtu.be)에서 Azure SQL Data Warehouse Gen2 작동 방식을 확인하세요.


## <a name="supported-regions-for-lower-compute-tiers"></a>하위 컴퓨팅 계층 지원 지역

- 미국 동부1 
- 미국 동부2
- 서유럽
- 북유럽
- 미국 서부2
- 동남아시아
- 미국 중남부
- 미국 중부 
- 동아시아
- 일본 동부
- 인도 중부
- 오스트레일리아 동부
- 캐나다 중부
- 일본 서부 
- 캐나다 중부

## <a name="next-steps"></a>다음 단계

- SQL Data Warehouse를 업그레이드하여 성능 최적화에 대해 [자세히 알아보세요](upgrade-to-latest-generation.md). 
