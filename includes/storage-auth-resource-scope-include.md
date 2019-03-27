---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ad4b244b58d741ad45463297df5bd358f3ae9918
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450028"
---
보안 주체에 RBAC 역할을 할당 하기 전에 보안 주체가 있어야 하는 액세스의 범위를 결정 합니다. 모범 사례는 것이 가장 좋습니다 가능한 가장 좁은 범위로 권한을 부여 하려면을 지정 합니다.

다음 목록에는 수 범위를 Azure blob 및 큐 리소스에 대 한 액세스는 가장 좁은 범위를 사용 하 여 시작 하는 수준을 설명 합니다.

- **개별 컨테이너입니다.** 이 범위의 보안 주체의 모든 컨테이너 뿐만 아니라 컨테이너 속성 및 메타 데이터의 blob에 액세스할 수 있습니다.
- **개별 큐입니다.** 이 범위에서 보안 주체 메시지 큐와 큐 속성 및 메타 데이터에 액세스할 수 있습니다.
- **저장소 계정입니다.** 이 범위에서 보안 주체 또는 모든 큐 및 해당 메시지 모든 컨테이너 및 해당 blob에 액세스할 수 있습니다.
- **리소스 그룹입니다.** 이 범위에서 보안 주체를 사용 하면 컨테이너 또는 리소스 그룹에 저장소 계정의 모든 큐의 모든 권한이 있습니다.
- **구독입니다.** 이 범위에서 보안 주체를 사용 하면 컨테이너 또는 모든 구독에서 리소스 그룹에 저장소 계정의 모든 큐의 모든 권한이 있습니다.
