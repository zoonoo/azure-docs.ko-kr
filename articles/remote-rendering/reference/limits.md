---
title: 제한 사항
description: SDK 기능에 대 한 코드 제한 사항
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417694"
---
# <a name="limitations"></a>제한 사항

수많은 기능에는 크기, 개수 또는 기타 제한 사항이 있습니다.

## <a name="azure-frontend"></a>Azure 프런트 엔드

* 프로세스 당 총 AzureFrontend 인스턴스 수: 16.
* AzureFrontend 당 총 AzureSession 인스턴스 수: 16.

## <a name="objects"></a>개체

* 단일 형식 (엔터티, CutPlaneComponent 등)에 대해 허용 되는 총 개체: 16777215.
* 허용 되는 총 활성 잘라내기 평면: 8.

## <a name="materials"></a>재질

* 자산에서 허용 되는 총 재질: 65535.

## <a name="overall-number-of-polygons"></a>전체 다각형 수

로드 된 모든 모델에 대해 허용 되는 다각형 수는 [세션 관리 REST API](../how-tos/session-rest-api.md#create-a-session)에 전달 되는 VM 크기에 따라 달라 집니다.

| VM 크기 | 최대 다각형 수 |
|:--------|:------------------|
|표준| 2000만 |
|프리미엄| 제한 없음 |


## <a name="platform-limitations"></a>플랫폼 제한 사항

**Windows 10 desktop**

* Unity의 "PC 독립 실행형" 배포는 지원 되지 않습니다. 대신 UWP를 사용 하십시오.
* UWP/x86은 유일 하 게 지원 되는 UWP 플랫폼입니다. UWP/x 64는 지원 되지 않습니다.

**Hololens 2**

* [PV 카메라에서 렌더링](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) 기능은 지원 되지 않습니다.
