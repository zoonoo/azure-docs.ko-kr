---
title: Azure BareMetal Infrastructure에서 Oracle 데이터베이스 복구
description: Azure BareMetal Infrastructure에서 Oracle 데이터베이스를 복구하는 방법에 대해 알아봅니다.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/15/2021
ms.openlocfilehash: c93159476403ac01ea9622b438fb36e5c423ecb4
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578485"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>Azure BareMetal Infrastructure에서 Oracle 데이터베이스 복구

모든 실패 시나리오에서 보호하는 단일 기술은 없지만 기능을 결합하면 데이터베이스 관리자가 거의 모든 상황에서 데이터베이스를 복구할 수 있습니다.

## <a name="causes-of-database-failure"></a>데이터베이스 오류의 원인

데이터베이스 오류는 다양한 이유로 발생할 수 있지만 일반적으로 몇 가지 범주로 나뉩니다.

- 데이터 조작 오류입니다.
- 온라인 다시 실행 로그의 손실.
- 데이터베이스 제어 파일의 손실.
- 데이터베이스 데이터 파일의 손실.
- 물리적 데이터 손상.

## <a name="choose-your-method-of-recovery"></a>복구 방법 선택

복구 유형은 오류 유형에 따라 달라집니다. 개체가 삭제되었거나 데이터가 잘못 수정된 경우를 가정해 보겠습니다. 그러면 일반적으로 플래시백 데이터베이스 작업을 수행하는 방법이 가장 빠릅니다. 다른 경우에는 Azure NetApp Files 스냅샷을 통해 복구하면 원하는 복구를 제공할 수 있습니다. 다음 그림의 의사 결정 트리는 위에 설명된 모든 데이터 보호 옵션이 구현된 경우 일반적인 오류 및 복구 시나리오를 나타냅니다.

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="데이터베이스 복구 결정 트리 다이어그램." lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

이 의사 결정 트리 예는 데이터베이스 관리자의 렌즈에서만 볼 수 있다는 점에 유의하세요. 각 배포에는 선택 순서를 변경할 수 있는 다른 요구 사항이 있을 수 있습니다. 예를 들어 데이터베이스 역할을 Data Guard를 통해 다른 지역으로 전환하면 애플리케이션 성능에 부정적인 영향을 줄 수 있습니다. 스냅샷 복구 방법에 낮은 RTO를 제공할 수 있습니다. RTO/RPO 요구 사항이 충족되도록 하려면 이러한 작업을 수행하고, 필요한 경우 문서화된 절차를 수행하여 실행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

BareMetal Infrastructure에 대해 자세히 알아봅니다.

- [Azure의 BareMetal Infrastructure란?](../../concepts-baremetal-infrastructure-overview.md)
- [Azure에서 BareMetal Infrastructure 인스턴스 연결](../../connect-baremetal-infrastructure.md)
