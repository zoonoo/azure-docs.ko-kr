---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d5915d8628254f24343571c1adc254c548558415
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110077711"
---
- 이 기능은 현재 프리미엄 SSD에서만 지원됩니다.
- 디스크의 계층을 변경하려면 먼저 VM의 할당을 취소하거나 실행 중인 VM에서 디스크를 분리해야 합니다.
- P60, P70, P80 성능 계층은 4,096GiB보다 큰 디스크에서만 사용할 수 있습니다.
- 디스크의 성능 계층은 12시간마다 한 번만 다운그레이드할 수 있습니다.

## <a name="change-performance-tier-without-downtime-preview"></a>가동 중지 시간 없이 성능 계층 변경(미리 보기)

일반적으로 성능 계층을 변경하려면 VM의 할당을 취소하거나 디스크를 분리해야 합니다. 하지만 이 미리 보기 기능을 사용하도록 설정하면 계층 변경을 위해 VM의 할당을 취소하거나 디스크를 분리할 필요가 없습니다.

미리 보기에는 다음과 같은 제한 사항이 적용됩니다.
- 미국 중서부 지역에서만 사용할 수 있습니다.
- 현재 공유 디스크에 사용할 수 없습니다.
- 가동 중지 시간 없이 계층을 변경하려면 다음 방법 중 하나를 사용해야 합니다.
    - 가동 중지 시간 없이 성능 계층을 변경하려면 `2020-12-01` API와 함께 Azure Resource Manager 템플릿을 사용합니다.
    - [https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview) 링크를 통해 Azure Portal에 액세스합니다.
    - 최신 Azure CLI.
- 현재 Azure PowerShell 모듈에서 사용할 수 없습니다.