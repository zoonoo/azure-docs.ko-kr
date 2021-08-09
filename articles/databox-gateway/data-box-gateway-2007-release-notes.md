---
title: Azure Stack Edge 및 Azure Data Box Gateway 2007 릴리스 정보 | Microsoft Docs
description: Azure Stack Edge 및 Data Box Gateway 2007 릴리스와 관련된 중요한 미해결 문제와 해결 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582783"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Azure Stack Edge 및 Azure Data Box Gateway 2007 릴리스 정보

다음 릴리스 정보는 Azure Stack Edge 및 Data Box Gateway의 2007 릴리스와 관련된 중요한 미해결 문제와 해결된 문제를 살펴봅니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. Azure Stack Edge와 Data Box Gateway를 배포하기 전에 릴리스 정보에 포함된 정보를 주의 깊게 검토하세요.

이 릴리스는 다음 소프트웨어 버전에 해당합니다.

- **Azure Stack Edge 2007(1.6.1280.1667)** - KB 4566549
- **Data Box Gateway 2007(1.6.1280.1667)** - KB 4566550

> [!NOTE]
> 업데이트 2007은 GA(일반 공급) 버전 이상의 소프트웨어를 실행하는 모든 디바이스에만 적용할 수 있습니다.

## <a name="whats-new"></a>새로운 기능

이 릴리스에 다음 버그 수정이 포함되어 있습니다.

- **업로드 문제** - 이 릴리스는 오류로 인해 업로드가 다시 시작되면 업로드 완료 속도를 저하시킬 수 있는 업로드 문제를 해결합니다. 이 문제는 주로 사용 가능한 대역폭에 비해 크기가 큰 파일로 구성된 데이터 세트를 업로드할 때 발생할 수 있습니다. 특히 대역폭 제한이 활성화된 경우 더욱 그러하나 이에 국한되지는 않습니다. 이 변경 사항은 지정된 파일에 대한 업로드를 다시 시작하기 전에 업로드를 완료할 수 있는 충분한 기회를 제공합니다.

이 릴리스에는 다음 업데이트도 포함되어 있습니다.

- Windows VHD의 기본 이미지가 업데이트되었습니다.
- 2020년 5월까지 릴리스된 모든 누적 Windows 업데이트와 .NET Framework 업데이트가 포함됩니다.
- 이 릴리스는 Azure Stack Edge 디바이스에서 IoT Edge 1.0.9.3을 지원합니다.

## <a name="known-issues-in-this-release"></a>이 릴리스의 알려진 문제

이 릴리스에 대해 언급된 새로운 문제는 없습니다. 발표된 모든 문제는 이전 릴리스에서 넘어온 것입니다. 알려진 문제 목록을 보려면 [GA 릴리스의 알려진 문제](data-box-gateway-release-notes.md#known-issues-in-ga-release)로 이동합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 배포 준비](../databox-online/azure-stack-edge-deploy-prep.md)
- [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)
