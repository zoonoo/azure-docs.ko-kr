---
title: Oracle 워크로드의 BareMetal SKU
description: Oracle BareMetal Infrastructure 워크로드의 SKU에 관해 알아봅니다.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/15/2021
ms.openlocfilehash: a578de920654aa7b3a298ed92a67efbe2bc2071f
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578537"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>Oracle 워크로드의 BareMetal SKU

이 문서에서는 Oracle 워크로드의 특수화된 BareMetal Infrastructure SKU를 살펴봅니다.

Oracle SKU의 BareMetal Infrastructure 범위는 2~4개 소켓입니다. 워크로드 요구 사항에 맞게 다양한 CPU 코어 및 메모리 크기 중에서 선택할 수도 있습니다. 다음 표에서는 사용 가능한 SKU의 기능을 간단히 설명합니다.
 
| **Oracle 인증** **하드웨어** | **모델** | **총 메모리** | **스토리지** | **가용성** |
| --- | --- | --- | --- | --- |
| YES | Azure의 SAP HANA S32m- 2 x Intel® Xeon® I6234 Processor 16개 CPU 코어 및 32개 CPU 스레드 | 1.5 TB | --- | 사용 가능 |
| YES | Azure의 SAP HANA S64m- 4 x Intel® Xeon® I6234 Processor 32개 CPU 코어 및 64개 CPU 스레드 | 3.0 TB | --- | 사용 가능 |
| YES | Azure의 SAP HANA S96– 2 x Intel® Xeon® E7-8890 v4 Processor 48개 CPU 코어 및 96개 CPU 스레드 | 768 GB | 3.0 TB | 사용 가능 |
| YES | Azure의 SAP HANA S224 – 4 x Intel® Xeon® Platinum 8276 Processor 112개 CPU 코어 및 224개 CPU 스레드 | 3.0 TB | 6.3TB | 사용 가능 |
| YES | Azure의 SAP HANA S224m– 4 x Intel® Xeon® Platinum 8276 Processor 112개 CPU 코어 및 224개 CPU 스레드 | 6.0 TB | 10.5TB | 사용 가능 |

- CPU 코어 = 서버 단위 비하이퍼 스레드 CPU 코어(총 실제 프로세서 수)의 합계. 
- CPU 스레드 = 서버 단위 하이퍼 스레드 CPU 코어(총 논리 프로세서 수)에서 제공하는 컴퓨팅 스레드 합계. 대부분의 단위는 기본적으로 하이퍼 스레딩 기술을 사용하도록 구성됩니다.
- 서버는 고객 전용입니다.
- 고객에게 루트 액세스 권한이 있습니다(하이퍼바이저 없음).
- 서버는 직접 Azure VNET에 포함되지 않습니다.

## <a name="next-steps"></a>다음 단계

Oracle용 BareMetal Infrastructure에서 제공하는 스토리지에 관해 알아봅니다.

> [!div class="nextstepaction"]
> [Oracle 워크로드에 대한 BareMetal의 스토리지](oracle-baremetal-storage.md)
