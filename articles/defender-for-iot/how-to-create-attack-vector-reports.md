---
title: 공격 벡터 보고서 만들기
description: 공격 벡터 보고서는 악용 가능한 장치의 취약점 체인에 대 한 그래픽 표현을 제공 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e9960fc2120add845be8feda9bafdef95a9b5f94
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522328"
---
# <a name="attack-vector-reporting"></a>공격 벡터 보고

## <a name="about-attack-vector-reports"></a>공격 벡터 보고서 정보

공격 벡터 보고서는 악용 가능한 장치의 취약점 체인에 대 한 그래픽 표현을 제공 합니다. 이러한 취약점으로 인해 키 네트워크 장치에 대 한 공격자 액세스 권한이 제공 될 수 있습니다. 공격 벡터 시뮬레이터는 공격 벡터를 실시간으로 계산 하 고 특정 대상에 대 한 모든 공격 벡터를 분석 합니다.

공격 벡터를 사용 하면 공격 순서에서 완화 작업의 영향을 평가할 수 있습니다. 예를 들어, 공격 체인을 중단 하 여 시스템 업그레이드가 공격자의 경로를 중단 하거나 대체 공격 경로가 남아 있는 경우를 결정할 수 있습니다. 이 정보는 재구성 및 완화 작업의 우선 순위를 지정 하는 데 도움이 됩니다.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="제어 센터에서 경고를 확인 합니다.":::

> [!NOTE]
> 관리자 및 보안 분석가는이 섹션에 설명 된 절차를 수행할 수 있습니다.

## <a name="create-an-attack-vector-report"></a>공격 벡터 보고서 만들기

공격 벡터 시뮬레이션을 만들려면 다음을 수행 합니다.

1. 측면 메뉴에서 :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="더하기 기호":::를 선택 하 여 시뮬레이션을 추가 합니다.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="공격 벡터 시뮬레이션입니다.":::

2. 시뮬레이션 속성을 입력 합니다.

   - **이름**: 시뮬레이션 이름입니다.

   - **최대 벡터**: 단일 시뮬레이션의 최대 벡터 수입니다.

   - **장치 맵에 표시**: 공격 벡터를 장치 맵에 필터로 표시 합니다.

   - **모든 원본 장치**: 공격 벡터는 모든 장치를 공격 원본으로 간주 합니다.

   - **공격 원본**: 공격 벡터는 지정 된 장치만 공격 원본으로 간주 합니다.

   - **모든 대상 장치**: 공격 벡터는 모든 장치를 공격 대상으로 간주 합니다.

   - **공격 대상**: 공격 벡터는 지정 된 장치만 공격 대상으로 간주 합니다.

   - **장치 제외**: 지정 된 장치가 공격 벡터 시뮬레이션에서 제외 됩니다.

   - **서브넷 제외**: 지정 된 서브넷은 공격 벡터 시뮬레이션에서 제외 됩니다.

3. **시뮬레이션 추가** 를 선택 합니다. 시뮬레이션이 시뮬레이션 목록에 추가 됩니다.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="새 시뮬레이션을 추가 합니다.":::

4. :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false":::시뮬레이션을 편집 하려면 선택 합니다.

   :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false":::시뮬레이션을 삭제 하려면 선택 합니다.

   :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false":::시뮬레이션을 즐겨찾기로 표시 하려면 선택 합니다.

5. 공격 벡터 목록이 나타나고 벡터 점수 (100), 공격 원본 장치 및 공격 대상 장치가 포함 됩니다. 공격 벡터의 그래픽 표현에 대 한 특정 공격을 선택 합니다.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="공격 벡터.":::

## <a name="next-steps"></a>다음 단계

[공격 벡터 보고](how-to-create-attack-vector-reports.md)


