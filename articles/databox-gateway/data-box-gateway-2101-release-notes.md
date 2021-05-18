---
title: Azure Data Box Gateway 2101 릴리스 정보 | Microsoft Docs
description: 2101 릴리스를 실행하는 Azure Data Box Gateway에 대한 중요한 미해결 문제 및 해결 방법을 설명합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99072733"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Azure Data Box Gateway 2101 릴리스 정보

다음 릴리스 정보에서는 Azure Data Box Gateway의 2101 릴리스에 대한 중요한 미해결 문제와 해결된 문제를 식별합니다.

릴리스 정보는 지속적으로 업데이트됩니다. 해결 방법이 필요한 중요한 문제가 발견되면 추가됩니다. Azure Data Box Gateway를 배포하기 전에 릴리스 정보의 정보를 주의 깊게 검토하세요.  

이 릴리스는 소프트웨어 버전에 해당합니다.

- **Data Box Gateway 2101(1.6.1475.2528)** - KB 4603462

> [!NOTE]
> 업데이트 2101은 GA(일반 공급) 버전 이상의 소프트웨어를 실행하는 모든 디바이스에만 적용할 수 있습니다.

## <a name="whats-new"></a>새로운 기능

이 릴리스에 다음 버그 수정이 포함되어 있습니다.

- **업로드 문제** - 이 릴리스는 오류로 인해 업로드가 다시 시작되면 업로드 완료 속도를 저하시킬 수 있는 업로드 문제를 해결합니다. 이 문제는 주로 사용 가능한 대역폭에 비해 크기가 큰 파일로 구성된 데이터 세트를 업로드할 때 발생할 수 있습니다. 특히 대역폭 제한이 활성화된 경우 더욱 그러하며 이에 국한되지 않습니다. 이렇게 변경하면 지정된 파일에 대한 업로드를 다시 시작하기 전에 업로드 완료를 위한 충분한 기회가 보장됩니다.

이 릴리스에는 다음 업데이트도 포함되어 있습니다.

- 2020년 10월까지 출시된 모든 누적 Windows 업데이트 및 .NET 프레임워크 업데이트.
- Azure Data Box Gateway의 고정 IP 주소는 소프트웨어 업데이트에서 유지됩니다.

## <a name="known-issues-in-this-release"></a>이 릴리스의 알려진 문제

이 릴리스에 대해 언급된 새로운 문제는 없습니다. 모든 릴리스 언급된 문제는 이전 릴리스에서 넘어 왔습니다. 알려진 문제 목록을 보려면 [GA 버전의 알려진 문제](data-box-gateway-release-notes.md#known-issues-in-ga-release)로 이동합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)
