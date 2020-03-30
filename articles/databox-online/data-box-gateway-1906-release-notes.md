---
title: Azure 데이터 상자 게이트웨이 & Azure 데이터 상자 가장자리 1906 릴리스 정보 | 마이크로 소프트 문서
description: 1906 릴리스를 실행하는 Azure 데이터 상자 게이트웨이 및 Azure Data Box Edge에 대한 중요한 열린 문제 및 해결 에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099487"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure 데이터 상자 가장자리 및 Azure 데이터 상자 게이트웨이 1906 릴리스 정보

다음 릴리스 노트는 Azure 데이터 상자 가장자리 및 Azure Data Box 게이트웨이에 대한 1906 릴리스에 대한 중요한 열려 있는 문제와 해결된 문제를 식별합니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. 데이터 상자 가장자리/데이터 상자 게이트웨이를 배포 하기 전에 주의 깊게 릴리스 정보에 포함 된 정보를 검토 합니다.

이 릴리스는 소프트웨어 버전에 해당합니다.

- **데이터 박스 게이트웨이 1906 (1.6.978.743)**
- **데이터 상자 가장자리 1906 (1.6.978.743)**

> [!NOTE]
> 업데이트 1906은 일반 가용성(GA) 또는 1905 버전의 소프트웨어를 실행하는 Data Box Edge 장치에만 적용할 수 있습니다.

## <a name="whats-new"></a>새로운 기능

- **복구 키 관리 워크플로의 버그 수정** - 이전 릴리스에서는 복구 키가 적용되지 않아 버그가 있었습니다. 이 버그는 이 릴리스에서 수정되었습니다. 복구 키를 사용하면 장치가 부팅되지 않는 경우 장치에서 데이터를 복구할 수 있으므로 이 업데이트를 적용하는 것이 좋습니다. 자세한 내용은 [데이터 박스 에지 또는 데이터 박스 게이트웨이를 배포할 때 복구 키를 저장하는](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)방법을 참조하세요.
- **필드 프로그래밍 가능한 게이트 어레이(FPGA) 로깅 개선** - 1905년 릴리스부터 FPGA와 관련된 로깅 및 경고 개선사항이 개선되었습니다. FPGA에서 Edge 계산 기능을 사용하는 경우 데이터 상자 가장자리에 필요한 업데이트가 계속됩니다. 자세한 내용은 [데이터 상자 가장자리에서 Edge 계산을 사용하여 데이터를 변환하는 방법을 참조하세요.](data-box-edge-deploy-configure-compute-advanced.md)

## <a name="known-issues-in-ga-release"></a>GA 릴리스에서 알려진 문제

이 릴리스에 대한 새로운 문제는 언급되지 않습니다. 언급 된 모든 릴리스는 이전 릴리스에서 이월되었습니다. 알려진 문제 목록을 보려면 GA [릴리스에서 알려진 문제로](data-box-gateway-release-notes.md#known-issues-in-ga-release)이동하십시오.


## <a name="next-steps"></a>다음 단계

- [Azure 데이터 박스 게이트웨이 배포 준비](data-box-gateway-deploy-prep.md)
- [Azure Data Box Edge 배포 준비](data-box-edge-deploy-prep.md)
