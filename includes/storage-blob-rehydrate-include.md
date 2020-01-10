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
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780246"
---
보관 스토리지에서 데이터를 읽으려면 먼저 핫 또는 쿨로 Blob의 계층을 변경해야 합니다. 이 프로세스를 리하이드레이션 라고 하며 완료 하는 데 몇 시간 정도 걸릴 수 있습니다. 최적의 리하이드레이션 성능을 위해 blob 크기를 크게 권장 합니다. 동시에 여러 가지 소규모 Blob을 리하이드레이션하면 시간이 더 소요될 수 있습니다. 현재 두 가지 리하이드레이션 우선 순위 (미리 보기)와 표준이 있습니다 .이 우선 순위는 [Set Blob 계층](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) 또는 [blob 복사](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 작업의 선택적 *x-리하이드레이션* 속성을 통해 설정할 수 있습니다.

* **표준 우선 순위**: 리하이드레이션 요청은 수신 된 순서 대로 처리 되 고 최대 15 시간이 걸릴 수 있습니다.
* **높은 우선 순위 (미리 보기)** : 리하이드레이션 요청은 표준 요청을 통해 우선 순위가 지정 되 고 1 시간 이내에 완료 될 수 있습니다. 높은 우선 순위는 blob 크기와 현재 수요에 따라 1 시간 이상 걸릴 수 있습니다. 높은 우선 순위의 요청은 표준 우선 순위 요청에 우선 순위가 지정 됩니다.

> [!NOTE]
> 표준 우선 순위는 보관에 대 한 기본 리하이드레이션 옵션입니다. 높은 우선 순위는 표준 우선 순위 보다 더 많은 비용을 리하이드레이션, 일반적으로 응급 데이터 복원 상황에서 사용 하기 위해 예약 되는 더 빠른 옵션입니다.

리하이드레이션 요청을 시작한 후에는 취소할 수 없습니다. 리하이드레이션 중에 계층이 변경되었는지 여부를 확인하려면 *보관 상태* Blob 속성을 확인할 수 있습니다. 상태는 대상 계층에 따라 "rehydrate-pending-to-hot" 또는 "rehydrate-pending-to-cool"을 읽습니다. 완료되면 보관 상태 속성이 제거되고 *액세스 계층* Blob 속성은 새로운 핫 또는 쿨 계층을 반영합니다.
