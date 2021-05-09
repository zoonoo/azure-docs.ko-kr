---
title: BareMetal 기반 Oracle을 위한 고가용성 및 재해 복구
description: Azure BareMetal Infrastructure 기반 Oracle을 위한 고가용성 및 재해 복구에 관해 알아봅니다.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: ab0337622eaa8c1368760fcbcd28533dacb3adce
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589790"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>BareMetal 기반 Oracle을 위한 고가용성 및 재해 복구

이 문서에서는 고가용성 및 재해 복구의 기반 사항을 살펴봅니다. 그런 다음, BareMetal Infrastructure 기반 Oracle 환경에서 고가용성 및 재해 복구를 달성할 수 있는 방법을 소개합니다.

## <a name="high-availability-vs-disaster-recovery"></a>고가용성 및 재해 복구 비교

고가용성 및 재해 복구는 둘 다 검사를 제공하지만 서로 다른 유형의 오류에 관련된 검사입니다. 두 항목은 Oracle Database의 다양한 기능과 옵션을 사용합니다.

고가용성을 사용하면 시스템은 애플리케이션의 사용자 환경에 영향을 주지 않고 여러 오류를 해결할 수 있습니다. 고가용성 시스템의 일반적인 특성은 다음과 같습니다.

- 단일 실패 지점이 없는 중복 하드웨어.
- 실패한 디스크 드라이브 또는 결함이 있는 네트워크 케이블과 같이 심각하지 않은 오류에서 자동 복구.
- 처리에 크게 영향을 미치지 않고 하드웨어 및 소프트웨어 변경 내용을 롤링하는 기능.
- RTO(복구 시간 목표) 및 RPO(복구 지점 목표)의 목표 충족 또는 초과.

고가용성을 위해 사용되는 Oracle의 가장 일반적인 기능은 [Oracle RAC(Real Application Clusters)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92)입니다.

재해 복구는 기본 고가용성 전략을 손상시키는 복구 불가능한 지역화된 오류로부터 사용자를 보호합니다. Oracle 에코시스템에서 재해 복구는 [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590)라고도 하는 데이터베이스 복제를 통해 제공됩니다.

## <a name="next-steps"></a>다음 단계

Oracle의 고가용성 기능에 관해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [BareMetal Infrastructure 기반 Oracle의 고가용성 기능](high-availability-features.md)
