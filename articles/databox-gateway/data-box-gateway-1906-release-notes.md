---
title: Azure Data Box Gateway & Azure Data Box Edge 1906 릴리스 노트| Microsoft Docs
description: 1906 릴리스를 실행하는 Azure Data Box Gateway 및 Azure Data Box Edge에 대한 중요한 미해결 문제 및 해결 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 51f4995ffd6a86022d95df15ae0eb7694d878c60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582840"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge 및 Azure Data Box Gateway 1906 릴리스 노트

다음 릴리스 정보에서는 Azure Data Box Edge 및 Azure Data Box Gateway의 1906 릴리스에 대한 중요한 미해결 문제와 해결된 문제를 식별합니다. 

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. Data Box Edge/Data Box Gateway를 배포하기 전에 릴리스 정보에 포함된 정보를 주의 깊게 검토하세요.

이 릴리스는 소프트웨어 버전에 해당합니다.

- **Data Box Gateway 1906(1.6.978.743)**
- **Data Box Edge 1906(1.6.978.743)**

> [!NOTE]
> 업데이트 1906은 GA(일반 공급) 또는 소프트웨어의 1905 버전을 실행하는 Data Box Edge 디바이스에만 적용할 수 있습니다.

## <a name="whats-new"></a>새로운 기능

- **복구 키 관리 워크플로의 버그 수정** - 이전 릴리스에서는 복구 키가 적용되지 않은 버그가 발생했습니다. 이 버그는 이 릴리스에서 수정되었습니다. 복구 키를 사용하면 디바이스가 부팅되지 않는 경우 디바이스에서 데이터를 복구할 수 있으므로 이 업데이트를 적용하는 것이 좋습니다. 자세한 내용은 [Data Box Edge 또는 Data Box Gateway를 배포할 때 복구 키를 저장](../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)하는 방법을 참조하세요.
- **FPGA(필드 프로그래머블 게이트 어레이) 로깅 개선** - 1905 릴리스부터 FPGA와 관련된 로깅 및 경고가 개선되었습니다. FPGA와 함께 Edge 컴퓨팅 기능을 사용하는 경우 이 업데이트는 Data Box Edge에 계속 필요합니다. 자세한 내용은 [Data Box Edge의 Edge 컴퓨팅으로 데이터를 변환](../databox-online/azure-stack-edge-deploy-configure-compute-advanced.md)하는 방법을 참조하세요.

## <a name="known-issues-in-ga-release"></a>GA 릴리스의 알려진 문제

이 릴리스에 대해 언급된 새로운 문제는 없습니다. 모든 릴리스 언급된 문제는 이전 릴리스에서 넘어 왔습니다. 알려진 문제 목록을 보려면 [GA 버전의 알려진 문제](data-box-gateway-release-notes.md#known-issues-in-ga-release)로 이동합니다.


## <a name="next-steps"></a>다음 단계

- [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)
- [Azure Data Box Edge 배포 준비](../databox-online/azure-stack-edge-deploy-prep.md)
