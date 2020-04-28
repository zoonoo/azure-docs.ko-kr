---
title: 파일 포함
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75460517"
---
보안 주체에 RBAC 역할을 할당 하기 전에 보안 주체에 게 부여 해야 하는 액세스 범위를 결정 합니다. 모범 사례는 항상 가장 좁은 범위를 부여 하는 것이 가장 좋습니다.

다음 목록에서는 가장 좁은 범위에서 시작 하 여 Azure blob 및 큐 리소스에 대 한 액세스 범위를 지정할 수 있는 수준을 설명 합니다.

- **개별 컨테이너입니다.** 이 범위에서 역할 할당은 컨테이너 속성 및 메타 데이터 뿐만 아니라 컨테이너의 모든 blob에 적용 됩니다.
- **개별 큐입니다.** 이 범위에서 역할 할당은 큐 속성 및 메타 데이터 뿐만 아니라 큐의 메시지에 적용 됩니다.
- **스토리지 계정** 이 범위에서 역할 할당은 모든 컨테이너 및 해당 blob에 적용 되거나 모든 큐 및 메시지에 적용 됩니다.
- **리소스 그룹입니다.** 이 범위에서 역할 할당은 리소스 그룹에 있는 모든 저장소 계정의 모든 컨테이너 또는 큐에 적용 됩니다.
- **구독입니다.** 이 범위에서 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 저장소 계정의 모든 컨테이너 또는 큐에 적용 됩니다.

> [!IMPORTANT]
> 구독에 Azure DataBricks 네임 스페이스가 포함 된 경우 구독으로 범위가 지정 된 역할은 blob 및 큐 데이터에 대 한 액세스 권한을 부여 하지 않습니다. 대신 리소스 그룹, 저장소 계정 또는 컨테이너 또는 큐로 역할 범위를 합니다.     
