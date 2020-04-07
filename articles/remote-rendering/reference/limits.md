---
title: 제한
description: SDK 기능에 대한 코드 제한
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6e23c716a965cdf178c9f847900e66c141d5d7eb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680337"
---
# <a name="limits"></a>제한

실행 중인 시스템의 내부 세부 정보로 인해 여러 기능에 크기 또는 개수 제한이 있습니다.

## <a name="azure-frontend"></a>Azure 프런트 엔드

* 프로세스당 총 AzureFrontend 인스턴스: 16.
* AzureFrontend당 총 AzureSession 인스턴스 수: 16.

## <a name="objects"></a>개체

* 단일 형식의 허용 가능한 총 개체(엔터티, 절단 평면 구성 요소 등): 16,777,215.
* 총 허용 활성 절단 평면: 8.

## <a name="materials"></a>원자재

* 자산의 총 허용 재료: 65,535.

## <a name="overall-number-of-polygons"></a>전체 다각형 수

로드된 모든 모델에 대해 허용되는 다각형 수는 [세션 관리 REST API에](../how-tos/session-rest-api.md#create-a-session)전달되는 VM의 크기에 따라 달라집니다.

| VM 크기 | 다각형의 최대 개수 |
|:--------|:------------------|
|표준| 2천만 명 |
|프리미엄| 제한 없음 |



