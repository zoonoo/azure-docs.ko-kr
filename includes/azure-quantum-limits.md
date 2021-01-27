---
title: 포함 파일
description: 포함 파일
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: c312ee63f9f00e4eef726924fc01f2862ba2884f
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920474"
---
### <a name="provider-limits--quota"></a>공급자 제한 & 할당량

Azure 퀀텀 서비스는 첫 번째 및 타사 서비스 공급자를 모두 지원 합니다. 타사 공급자는 제한 및 할당량을 소유 합니다. 공급자 블레이드에서 타사 공급자를 구성 하는 경우 사용자가 Azure Portal의 제안 및 제한을 볼 수 있습니다. 

아래의 Microsoft 자사 최적화 솔루션 공급자에 대 한 게시 된 할당량 한도를 찾을 수 있습니다. 

#### <a name="learn--develop-sku"></a>SKU에 대 한 자세한 & 개발

| 리소스 | 제한 |
| --- | --- |
| CPU 기반 동시 작업 | 최대 5 개의 동시 작업 |
| FPGA 기반 동시 작업 | 최대 2 개의 동시 작업 |
| CPU 기반 해 찾기 시간 | 매월 20 시간  |
| FPGA 기반 해 찾기 시간 | 매월 1 시간  |

SKU & 개발 하는 방법을 사용 하는 경우 할당량 한도 증가를 요청할 **수 없습니다** . 대신 SKU 규모의 성능으로 전환 해야 합니다.

#### <a name="performance-at-scale-sku"></a>SKU 규모의 성능

| 리소스 | 제한 |
| --- | --- |
| CPU 기반 동시 작업 | 최대 100 동시 작업 |
| FPGA 기반 동시 작업 | 최대 10 개의 동시 작업 |
| 해 찾기 시간 | 월 5만 시간  |

제한 증가를 요청 해야 하는 경우 Azure 지원에 문의 하세요. 

자세한 내용은 [Azure 퀀텀 가격 책정 페이지](https://aka.ms/AQ/Pricing)를 참조 하세요.
타사 제품에 대 한 자세한 내용은 Azure Portal의 관련 공급자 페이지를 참조 하십시오.
