---
title: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: c222869df561a9a36ebd69eb9ae09fa688ba0086
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518686"
---
보안 주체에 RBAC 역할을 할당 하기 전에 보안 주체에 게 부여 해야 하는 액세스 범위를 결정 합니다. 모범 사례는 항상 가장 좁은 범위를 부여 하는 것이 가장 좋습니다.

다음 목록에서는 가장 좁은 범위에서 시작 하 여 Azure blob 및 큐 리소스에 대 한 액세스 범위를 지정할 수 있는 수준을 설명 합니다.

- **개별 컨테이너입니다.** 이 범위에서 역할 할당은 컨테이너 속성 및 메타 데이터 뿐만 아니라 컨테이너의 모든 blob에 적용 됩니다.
- **개별 큐입니다.** 이 범위에서 역할 할당은 큐 속성 및 메타 데이터 뿐만 아니라 큐의 메시지에 적용 됩니다.
- **저장소 계정입니다.** 이 범위에서 역할 할당은 모든 컨테이너 및 해당 blob에 적용 되거나 모든 큐 및 메시지에 적용 됩니다.
- **리소스 그룹입니다.** 이 범위에서 역할 할당은 리소스 그룹에 있는 모든 저장소 계정의 모든 컨테이너 또는 큐에 적용 됩니다.
- **구독입니다.** 이 범위에서 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 저장소 계정의 모든 컨테이너 또는 큐에 적용 됩니다.
- **관리 그룹입니다.** 이 범위에서 역할 할당은 관리 그룹에 있는 모든 구독의 모든 리소스 그룹에 있는 모든 저장소 계정의 모든 컨테이너 또는 큐에 적용 됩니다.

RBAC 역할 할당 및 범위에 대 한 자세한 내용은 [azure 역할 기반 액세스 제어 (AZURE RBAC) 란?](../articles/role-based-access-control/overview.md)을 참조 하세요.
