---
title: 포함 파일
description: 포함 파일
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: 2106a48a583f120f8b4dde4eb32a30f1a1b1d85b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98947916"
---
### <a name="provider-limits--quota"></a>공급자 제한 및 할당량

Azure Quantum Service는 자사 및 타사 서비스 공급자를 모두 지원합니다. 타사 공급자는 제한 및 할당량을 소유합니다. 사용자는 공급자 블레이드에서 타사 공급자를 구성할 때 Azure Portal에서 제품 및 제한을 볼 수 있습니다. 

Microsoft 자사 최적화 솔루션 공급자에 대해 게시된 할당량 제한은 아래에서 확인할 수 있습니다. 

#### <a name="learn--develop-sku"></a>SKU 학습 및 개발

| 리소스 | 제한 |
| --- | --- |
| CPU 기반 동시 작업 | 최대 5개의 동시 작업 |
| FPGA 기반 동시 작업 | 최대 2개의 동시 작업 |
| CPU 기반 해 찾기 시간 | 매월 20시간  |
| FPGA 기반 해 찾기 시간 | 매월 1시간  |

학습 및 개발 SKU를 사용하는 경우 할당량 한도 증가를 요청할 수 **없습니다**. 대신 SKU 규모의 성능으로 전환해야 합니다.

#### <a name="performance-at-scale-sku"></a>SKU 규모의 성능

| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| CPU 기반 동시 작업 | 최대 100개의 동시 작업 | 기본 제한과 동일 |
| FPGA 기반 동시 작업 | 최대 10개의 동시 작업 | 기본 제한과 동일 |
| 해 찾기 시간 | 매월 1,000시간  | 매월 최대 50,000 시간 |

한도 증가를 요청해야 하는 경우 Azure 지원에 문의하세요. 

자세한 내용은 [Azure Quantum 가격 책정 페이지](https://aka.ms/AQ/Pricing)를 참조하세요.
타사 제품에 대한 자세한 내용은 Azure Portal의 관련 공급자 페이지를 검토하세요.
