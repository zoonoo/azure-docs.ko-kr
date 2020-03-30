---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460517"
---
보안 주체에 RBAC 역할을 할당하기 전에 보안 주체가 가져야 할 액세스 범위를 결정합니다. 모범 사례는 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다.

다음 목록에서는 가장 좁은 범위부터 시작하여 Azure Blob 및 큐 리소스에 대한 액세스 범위를 정할 수 있는 수준에 대해 설명합니다.

- **개별 컨테이너입니다.** 이 범위에서 역할 할당은 컨테이너 속성 및 메타데이터뿐만 아니라 컨테이너의 모든 Blob에 적용됩니다.
- **개별 큐입니다.** 이 범위에서 역할 할당은 큐의 메시지와 큐 속성 및 메타데이터에 적용됩니다.
- **스토리지 계정** 이 범위에서 역할 할당은 모든 컨테이너 및 해당 Blob 또는 모든 큐 및 해당 메시지에 적용됩니다.
- **리소스 그룹입니다.** 이 범위에서 역할 할당은 리소스 그룹의 모든 저장소 계정의 모든 컨테이너 또는 큐에 적용됩니다.
- **구독입니다.** 이 범위에서 역할 할당은 구독의 모든 리소스 그룹의 모든 저장소 계정의 모든 컨테이너 또는 큐에 적용됩니다.

> [!IMPORTANT]
> 구독에 Azure DataBricks 네임스페이스가 포함된 경우 구독으로 범위가 지정된 역할은 Blob 및 큐 데이터에 대한 액세스 권한을 부여하지 않습니다. 대신 리소스 그룹, 저장소 계정 또는 컨테이너 또는 큐에 대한 범위 역할을 제공합니다.     
