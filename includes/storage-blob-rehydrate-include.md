---
title: 포함 파일
description: 포함 파일
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75780246"
---
보관 스토리지에서 데이터를 읽으려면 먼저 핫 또는 쿨로 Blob의 계층을 변경해야 합니다. 이 프로세스는 재수화라고 하며 완료하는 데 몇 시간이 걸릴 수 있습니다. 최적의 수분 공급 성능을 위해 큰 Blob 크기를 권장합니다. 동시에 여러 가지 소규모 Blob을 리하이드레이션하면 시간이 더 소요될 수 있습니다. 현재 두 개의 재수화 우선 순위가 있습니다, 높음 (미리 보기) 및 표준, [설정 Blob 계층](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) 또는 [복사 Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 작업에 선택적 *x-ms-rehydrate 우선 순위* 속성을 통해 설정할 수 있습니다.

* **표준 우선 순위**: 재수화 요청은 수신 된 순서대로 처리되며 최대 15 시간이 걸릴 수 있습니다.
* **우선 순위가 높은(미리 보기)**: 재수화 요청은 표준 요청보다 우선 순위가 지정되며 1시간 이내에 완료될 수 있습니다. 우선 순위가 높은 경우 Blob 크기 및 현재 수요에 따라 1시간 이상 걸릴 수 있습니다. 우선 순위가 높은 요청은 표준 우선 순위 요청보다 우선 순위가 지정됩니다.

> [!NOTE]
> 표준 우선 순위는 아카이브의 기본 수화 옵션입니다. 우선 순위가 높은 옵션은 표준 우선 순위 재수화보다 더 많은 비용이 들며 일반적으로 긴급 데이터 복원 상황에서 사용하기 위해 예약되어 있는 더 빠른 옵션입니다.

재수화 요청이 시작되면 취소할 수 없습니다. 리하이드레이션 중에 계층이 변경되었는지 여부를 확인하려면 *보관 상태* Blob 속성을 확인할 수 있습니다. 상태는 대상 계층에 따라 "rehydrate-pending-to-hot" 또는 "rehydrate-pending-to-cool"을 읽습니다. 완료되면 보관 상태 속성이 제거되고 *액세스 계층* Blob 속성은 새로운 핫 또는 쿨 계층을 반영합니다.
