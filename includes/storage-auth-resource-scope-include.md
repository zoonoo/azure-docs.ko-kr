---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 518c57bc3327511b70deef143826f2a1b9df8639
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181873"
---
보안 주체에 RBAC 역할을 할당 하기 전에 보안 주체가 있어야 하는 액세스의 범위를 결정 합니다. 모범 사례는 것이 가장 좋습니다 가능한 가장 좁은 범위로 권한을 부여 하려면을 지정 합니다.

다음 목록에는 수 범위를 Azure blob 및 큐 리소스에 대 한 액세스는 가장 좁은 범위를 사용 하 여 시작 하는 수준을 설명 합니다.

- **개별 컨테이너입니다.** 이 범위에서 역할 할당을 모든 컨테이너 뿐만 아니라 컨테이너 속성 및 메타 데이터의 blob에 적용 됩니다.
- **개별 큐입니다.** 이 범위에서 역할 할당을 메시지 큐와 큐 속성 및 메타 데이터에 적용 됩니다.
- **저장소 계정입니다.** 이 범위에서 역할 할당에는 모든 컨테이너 및 해당 blob에 또는 모든 큐 및 해당 메시지에 적용 됩니다.
- **리소스 그룹입니다.** 이 범위에서 역할 할당을 컨테이너 또는 리소스 그룹에 저장소 계정의 모든 큐의 모든 항목에 적용 됩니다.
- **구독입니다.** 이 범위에서 역할 할당을 컨테이너 또는 모든 구독에서 리소스 그룹에 저장소 계정의 모든 큐의 모든 항목에 적용 됩니다.

> [!IMPORTANT]
> 구독을 Azure DataBricks 네임 스페이스에 포함 된 경우 구독 범위에서 할당 된 역할에서 blob 및 큐 데이터에 대 한 액세스 권한을 부여 차단 됩니다.
