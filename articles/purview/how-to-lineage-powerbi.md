---
title: Power BI의 메타데이터 및 계보
description: 이 문서에서는 Power BI 원본에서 데이터 계보 추출을 설명합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b7143f7ccb0cb4c91cbb86cadb3cfa78c5c9bec3
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107912556"
---
# <a name="how-to-get-lineage-from-power-bi-into-azure-purview"></a>Power BI의 계보를 Azure Purview로 가져오는 방법

이 문서에서는 Azure Purview에서 Power BI 원본의 데이터 계보 양상을 설명합니다. Purview에서 Power BI의 데이터 계보를 확인하기 위한 필수 요소는 [Power BI 검사](../purview/register-scan-power-bi-tenant.md)입니다. 

## <a name="common-scenarios"></a>일반적인 시나리오

1. Power BI 원본을 검사하면 데이터 소비자는 Purview에서 보고서 또는 대시보드의 근본 원인을 분석할 수 있습니다. 보고서에서 데이터 불일치가 발견된 경우 사용자는 간단하게 업스트림 데이터 세트를 식별하고 필요한 경우 소유자에게 연락할 수 있습니다.

2. 데이터 제작자는 해당 데이터 세트를 사용하는 다운스트림 보고서나 대시보드를 확인할 수 있습니다. 데이터 세트를 변경하기 전에 데이터 소유자는 합리적인 결정을 내릴 수 있습니다.

2. 사용자는 이름, 인증 상태, 민감도 레이블, 소유자, 설명 및 기타 비즈니스 패싯별로 검색하여 관련 Power BI 아티팩트를 반환할 수 있습니다.

## <a name="power-bi-artifacts-in-azure-purview"></a>Azure Purview의 Power BI 아티팩트

[Power BI 검사](../purview/register-scan-power-bi-tenant.md)가 완료되면 다음 Power BI 아티팩트가 Purview에 인벤토리됩니다.

* 용량
* 작업 영역
* 데이터 흐름
* 데이터 세트 
* 보고서
* 대시보드

작업 영역 아티팩트에 데이터 흐름 -> 데이터 세트 -> 보고서 -> 대시보드의 계보가 표시됩니다.

:::image type="content" source="./media/how-to-lineage-powerbi/powerbi-overview.png" alt-text="개요 탭이 Power BI 자산에 렌더링되는 방식을 보여주는 스크린샷" lightbox="./media/how-to-lineage-powerbi/powerbi-overview.png":::

>[!Note]
> * 현재 PowerBI 데이터 세트 내부의 열 계보와 변환은 지원되지 않습니다.
> * 현재 PowerBI 데이터 흐름이나 PowerBI 데이터 세트가 생성된 데이터 원본에 대한 제한된 정보가 표시됩니다. 예: PowerBI 데이터 세트의 SQL 서버 원본의 경우 서버 이름만 캡처됩니다. 

## <a name="lineage-of-power-bi-artifacts-in-azure-purview"></a>Azure Purview의 Power BI 아티팩트 계보

사용자는 Power BI 아티팩트를 이름, 설명 또는 다른 세부 정보별로 검색하여 관련 결과를 확인할 수 있습니다. 자산 개요 및 속성 탭에 설명, 분류 및 기타 정보와 같은 기본 세부 정보가 표시됩니다. 계보 탭에는 업스트림 및 다운스트림 종속성과 함께 자산 관계가 표시됩니다.

:::image type="content" source="./media/how-to-lineage-powerbi/powerbi-lineage.png" alt-text="계보가 Power BI에 렌더링되는 방식을 보여주는 스크린샷" lightbox="./media/how-to-lineage-powerbi/powerbi-lineage.png":::

## <a name="next-steps"></a>다음 단계

- [Azure Purview의 데이터 계보에 대해 알아보기](catalog-lineage-user-guide.md)
- [Azure Data Factory를 연결하여 자동화된 계보 푸시](how-to-link-azure-data-factory.md)
