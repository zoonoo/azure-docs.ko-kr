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
ms.openlocfilehash: a369eb7000fb8622a69f4205ffcc232ae9c9d242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95545937"
---
보관 스토리지에서 데이터를 읽으려면 먼저 핫 또는 쿨로 Blob의 계층을 변경해야 합니다. 이 프로세스는 리하이드레이션이라고 하며 완료하는 데 몇 시간이 걸릴 수 있습니다. 최적의 리하이드레이션 성능을 위해 큰 Blob 크기를 권장합니다. 동시에 여러 가지 소규모 Blob을 리하이드레이션하면 시간이 더 소요될 수 있습니다. 현재 리하이드레이션 우선 순위는 높음과 표준이라는 두 가지가 있습니다. 이는 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 또는 [Blob 복사](/rest/api/storageservices/copy-blob) 작업에서 선택적 *x-ms-rehydrate-priority* 속성을 통해 설정할 수 있습니다.

* **표준 우선 순위**: 리하이드레이션 요청은 수신된 순서대로 처리되고 최대 15시간이 걸릴 수 있습니다.
* **높은 우선 순위**: 리하이드레이션 요청은 표준 요청보다 우선되면 크기가 10GB 미만인 개체의 경우 1시간 이내에 완료될 수 있습니다. 

> [!NOTE]
> 표준 우선 순위는 보관에 대한 기본 리하이드레이션 옵션입니다. 높은 우선 순위는 표준 우선 순위 리하이드레이션보다 비용이 더 많이 드는 빠른 옵션이며, 일반적으로 응급 데이터 복원 상황에서 사용하도록 예약되어 있습니다.
>
> 높은 우선 순위는 Blob 크기와 현재 수요에 따라 1시간 이상 걸릴 수 있습니다. 높은 우선 순위의 요청은 표준 우선 순위 요청보다 우선 순위가 보장됩니다.

리하이드레이션 요청이 시작되면 취소할 수 없습니다. 리하이드레이션 프로세스 중에 *x-ms-access-tier* Blob 속성은 온라인 계층에 대한 리하이드레이션이 완료될 때까지 계속 보관으로 표시됩니다. 리하이드레이션 상태와 진행률을 확인하려면 [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties)를 호출하여 *x-ms-archive-status* 및 *x-ms-rehydrate-priority* Blob 속성을 확인할 수 있습니다. 보관 상태는 리하이드레이션 대상 계층에 따라 "rehydrate-pending-to-hot" 또는 "rehydrate-pending-to-cool"을 읽을 수 있습니다. 리하이드레이션 우선 순위는 "높음" 또는 "표준"의 속도를 표시합니다. 완료되면 보관 상태 및 리하이드레이션 우선 순위 속성이 제거되고 액세스 계층 Blob 속성이 선택한 핫 또는 쿨 계층을 반영하도록 업데이트됩니다.