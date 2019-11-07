---
title: Azure Blockchain 토큰 composability
description: Azure Blockchain 토큰 composability는 고급 시나리오에 대 한 토큰을 만들 수 있는 유연성을 제공 합니다.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: a82d7ba606eac5dcafc26b1a8527810a5a21840d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577112"
---
# <a name="azure-blockchain-tokens-composability"></a>Azure Blockchain 토큰 composability

[!INCLUDE [Preview note](./includes/preview.md)]

토큰 composability 고급 시나리오에 대 한 토큰을 만들 수 있는 유연성을 제공 합니다. [미리 빌드된 4 개의 토큰 템플릿을](templates.md#base-token-types)사용 하 여 구현할 수 없는 복잡 한 시나리오가 있을 수 있습니다. Token composability을 사용 하면 고유한 토큰 템플릿을 작성 하는 정의 된 동작을 추가 또는 제거 하 여 고유한 토큰 템플릿을 디자인할 수 있습니다. 새 토큰 템플릿을 만들 때 Azure Blockchain 토큰은 모든 토큰 문법 규칙을 확인 합니다. 구성 된 템플릿은 연결 된 Blockchain 네트워크에서 발급 하기 위해 Azure Blockchain 토큰 서비스에 저장 됩니다.

토큰 템플릿을 디자인 하려면 다음 섹션의 [토큰 동작](templates.md#token-behaviors) 을 사용할 수 있습니다.

## <a name="burnable-b"></a>Burnable (b)

제공에서 토큰을 제거 하는 기능입니다.

예를 들어 선물 카드의 온라인 신용 카드 점수를 사용할 경우 신용 카드 지점이 표시 됩니다.

## <a name="delegable-g"></a>위임 가능한 (g)

소유 하 고 있는 토큰에서 수행 되는 작업을 위임 하는 기능.

대리자는 토큰의 소유자로 동작을 수행할 수 있습니다. 예를 들어 위임 가능한 토큰을 사용 하 여 투표를 구현할 수 있습니다. 위임 가능한 토큰을 통해 투표 토큰 소유자는 다른 사용자에 게 응답할 수 있습니다.

## <a name="logable-l"></a>Logable (l)

기록할 수 있습니다.

예를 들어 특정 영화를 표시 하는 각 극장에 대 한 영화 배포에 대해 logable 토큰을 발급할 수 있습니다. 동영상이 재생 될 수 있도록 동영상의 릴리스 실행 중에 로열티 지급 표시 되기 때문에 표시 되는는 각 표시에 대해 트랜잭션을 기록 해야 합니다. 행위자 빌드는 영화 토큰을 사용 하 여 분포의 극장 당 표시 되는 지급의 유효성을 검사할 수 있습니다.

## <a name="mint-able-m"></a>Mint (m)

토큰 클래스에 대 한 추가 토큰을 mint 수 있습니다. Minter 역할은 mintable 동작을 포함 합니다.

예를 들어, 충성도 프로그램을 구현 하려는 소매 회사는 해당 충성도 프로그램에 mintable 토큰을 사용할 수 있습니다. 고객 기반이 증가 함에 따라 고객에 대 한 추가 충성도 점수를 mint 수 있습니다.  

## <a name="non-subdividable-or-whole-d"></a>비 subdividable 또는 전체 (~ d)

토큰을 더 작은 부분으로 나눌 수 없도록 하는 제한 사항입니다.

예를 들어 단일 아트 그리기를 여러 개의 작은 파트로 나눌 수는 없습니다. 

## <a name="non-transferable-t"></a>양도할 없음 (~ t)

초기 토큰 소유자의 소유권 변경을 방지 하는 제한입니다.

예를 들어 대학 학위는 전송할 수 없는 토큰입니다. 졸업에 학위을 제공 하 고 나면 졸업에서 다른 사람에 게 전송할 수 없습니다.

## <a name="roles-r"></a>역할 (r)

특정 동작에 대 한 토큰 템플릿 클래스 내에서 역할을 정의할 수 있습니다.

토큰이 토큰을 만들 때 지 원하는 역할 이름 목록을 제공할 수 있습니다. 역할을 지정 하면 사용자는 이러한 동작에 역할을 할당할 수 있습니다. 현재는 minter 역할만 지원 됩니다.

## <a name="singleton-s"></a>단일 항목

하나의 토큰을 제공할 수 있는 제한

예를 들어, 박물관 아티팩트는 단일 토큰입니다. 박물관 아티팩트는 고유 합니다. 아티팩트를 나타내는 토큰은 공급망에 단일 항목만 있습니다.

## <a name="subdividable-d"></a>Subdividable (d)

토큰을 더 작은 부분으로 나눌 수 있습니다.

예를 들어 달러를 센트로 세분화할 수 있습니다.

## <a name="transferable-t"></a>양도 (t)

토큰의 소유권을 이전할 수 있습니다.

예를 들어 속성 제목은 전송할 수 있는 토큰으로, 속성이 판매 될 때 한 사용자에서 다른 사용자로 전송 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Blockchain 토큰 계정 관리](account-management.md)에 대해 알아봅니다.
