---
title: 포함 파일
description: 포함 파일
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 53d2d47143c5a2cefbd50faca9a02af18ffae315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84754693"
---
보관 스토리지에서 데이터를 읽으려면 먼저 핫 또는 쿨로 Blob의 계층을 변경해야 합니다. 이 프로세스를 리하이드레이션 라고 하며 완료 하는 데 몇 시간 정도 걸릴 수 있습니다. 최적의 리하이드레이션 성능을 위해 blob 크기를 크게 권장 합니다. 동시에 여러 가지 소규모 Blob을 리하이드레이션하면 시간이 더 소요될 수 있습니다. 현재 두 개의 리하이드레이션 우선 순위, 즉 High 및 Standard가 있습니다 .이 우선 순위는 [Set Blob 계층](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) 또는 [blob 복사](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 작업의 선택적 *x-리하이드레이션* 속성을 통해 설정할 수 있습니다.

* **표준 우선 순위**: 리하이드레이션 요청은 수신 된 순서 대로 처리 되 고 최대 15 시간이 걸릴 수 있습니다.
* **높은 우선 순위**: 리하이드레이션 요청은 표준 요청 보다 우선 순위가 지정 되 고 크기가 10gb 인 개체에 대해 1 시간 미만으로 완료 될 수 있습니다. 

> [!NOTE]
> 표준 우선 순위는 보관에 대 한 기본 리하이드레이션 옵션입니다. 높은 우선 순위는 표준 우선 순위 보다 더 많은 비용을 리하이드레이션, 일반적으로 응급 데이터 복원 상황에서 사용 하기 위해 예약 되는 더 빠른 옵션입니다.
>
> 높은 우선 순위는 blob 크기와 현재 수요에 따라 1 시간 이상 걸릴 수 있습니다. 높은 우선 순위의 요청은 표준 우선 순위 요청에 우선 순위가 지정 됩니다.

리하이드레이션 요청을 시작한 후에는 취소할 수 없습니다. 리하이드레이션 프로세스 중에 리하이드레이션가 온라인 계층에 완료 될 때까지 *x-y 액세스 계층* blob 속성이 보관으로 계속 표시 됩니다. 리하이드레이션 상태 및 진행 상황을 확인 하려면 [Blob 속성 가져오기](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) 를 호출 *하 여* *리하이드레이션* blob 속성을 확인 합니다. 보관 상태는 리하이드레이션 대상 계층에 따라 "리하이드레이션-hot" 또는 "리하이드레이션"를 읽을 수 있습니다. 리하이드레이션 우선 순위는 "High" 또는 "Standard"의 속도를 표시 합니다. 완료 되 면 archive status 및 리하이드레이션 priority 속성이 제거 되 고 액세스 계층 blob 속성이 선택 된 핫 또는 쿨 계층을 반영 하도록 업데이트 됩니다.
