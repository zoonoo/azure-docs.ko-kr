---
title: Azure Sentinel에 Perimeter 81 데이터 연결| Microsoft Docs
description: Azure Sentinel에 Perimeter 81 데이터를 연결하는 방법 알아보기.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: 07c850cf98ffddf01cb4479affdb62d30a5bb522
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530487"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>Perimeter 81 활동 로그를 Azure Sentinel에 연결

> [!IMPORTANT]
> Azure Sentinel의 Perimeter 81 데이터 커넥터는 현재 퍼블릭 미리 보기로 제공됩니다.
> 해당 기능은 별도의 Service Level Agreement(서비스 수준 규약) 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

이 문서는 [Perimeter 81 활동 로그](https://www.perimeter81.com/) 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Perimeter 81 활동 로그 커넥터를 사용하여 Perimeter 81 데이터를 Azure Sentinel로 쉽게 가져올 수 있습니다. 따라서 통합 문서에서 볼 수 있고, 사용자 지정 경고를 만들어, 조사 기능 개선을 위한 통합에 해당 데이터를 사용할 수 있습니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역이 있는 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역 공유 키에 대한 읽기 권한이 있어야 합니다.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>Perimeter 81 활동 로그 구성 및 연결

Perimeter 81 활동 로그는 로그를 Azure Sentinel로 직접 통합하고 내보낼 수 있습니다.

1. Azure Sentinel 포털의 탐색 메뉴에서 **데이터 커넥터** 를 클릭합니다.

1. 갤러리에서 **Perimeter 81 활동 로그** 를 선택하고 **커넥터 페이지 열기** 단추를 클릭합니다.

1. Perimeter 81 활동 로그 커넥터 페이지에서 **작업 영역 ID** 및 **기본 키** 를 복사하고 [여기에 설명된 대로](https://support.perimeter81.com/hc/en-us/articles/360012680780) Perimeter 81에 붙여넣습니다.

1. 지침을 완료한 후 Azure Sentinel 커넥터 페이지에 연결된 데이터 형식이 표시됩니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 데이터는 **CustomLogs** - **Perimeter81_CL** 의 **로그** 에 표시됩니다.

로그가 표시되기 시작하려면 최대 20분이 걸립니다.

## <a name="next-steps"></a>다음 단계

이상 Perimeter 81 활동 로그를 Azure Sentinel에 연결하는 방법을 알아보았습니다. 이 데이터 커넥터에 기본 제공되는 기능을 최대한 활용하려면 데이터 커넥터 페이지에서 **다음 단계** 탭을 클릭합니다. 거기에는 준비된 통합 문서와 일부 샘플 쿼리가 있으며 따라서 유용한 정보를 찾을 수 있습니다.

Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.