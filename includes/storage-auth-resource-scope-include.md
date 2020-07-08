---
title: 파일 포함
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8b97a62626666fa39a5b0622852d9eec47c2410a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024900"
---
보안 주체에 RBAC 역할을 할당 하기 전에 보안 주체에 게 부여 해야 하는 액세스 범위를 결정 합니다. 모범 사례는 항상 가장 좁은 범위를 부여 하는 것이 가장 좋습니다.

다음 목록에서는 가장 좁은 범위에서 시작 하 여 Azure blob 및 큐 리소스에 대 한 액세스 범위를 지정할 수 있는 수준을 설명 합니다.

- **개별 컨테이너입니다.** 이 범위에서 역할 할당은 컨테이너 속성 및 메타 데이터 뿐만 아니라 컨테이너의 모든 blob에 적용 됩니다.
- **개별 큐입니다.** 이 범위에서 역할 할당은 큐 속성 및 메타 데이터 뿐만 아니라 큐의 메시지에 적용 됩니다.
- **저장소 계정입니다.** 이 범위에서 역할 할당은 모든 컨테이너 및 해당 blob에 적용 되거나 모든 큐 및 메시지에 적용 됩니다.
- **리소스 그룹입니다.** 이 범위에서 역할 할당은 리소스 그룹에 있는 모든 저장소 계정의 모든 컨테이너 또는 큐에 적용 됩니다.
- **구독입니다.** 이 범위에서 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 저장소 계정의 모든 컨테이너 또는 큐에 적용 됩니다.
