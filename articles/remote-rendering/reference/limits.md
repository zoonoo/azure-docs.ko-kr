---
title: 제한 사항
description: SDK 기능에 대한 코드 제한
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417694"
---
# <a name="limitations"></a>제한 사항

여러 피처에 크기, 개수 또는 기타 제한 사항이 있습니다.

## <a name="azure-frontend"></a>Azure 프런트 엔드

* 프로세스당 총 AzureFrontend 인스턴스: 16.
* AzureFrontend당 총 AzureSession 인스턴스 수: 16.

## <a name="objects"></a>개체

* 단일 형식의 허용 가능한 총 개체(엔터티, 절단 평면 구성 요소 등): 16,777,215.
* 총 허용 활성 절단 평면: 8.

## <a name="materials"></a>재질

* 자산의 총 허용 재료: 65,535.

## <a name="overall-number-of-polygons"></a>전체 다각형 수

로드된 모든 모델에 대해 허용되는 다각형 수는 [세션 관리 REST API에](../how-tos/session-rest-api.md#create-a-session)전달되는 VM의 크기에 따라 달라집니다.

| VM 크기 | 다각형의 최대 개수 |
|:--------|:------------------|
|표준| 2천만 명 |
|프리미엄| 제한 없음 |


## <a name="platform-limitations"></a>플랫폼 제한 사항

**윈도우 10 바탕 화면**

* Unity의 "PC 독립 실행형" 배포는 지원되지 않습니다. 대신 UWP를 사용합니다.
* UWP/x86은 유일한 지원되는 UWP 플랫폼입니다. UWP/x64는 지원되지 않습니다.

**홀로렌즈 2**

* [PV 카메라 기능의 렌더링은](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) 지원되지 않습니다.
