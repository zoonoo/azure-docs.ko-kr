---
title: Azure 센티널에 경계 81 데이터 연결 | Microsoft Docs
description: 경계 81 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
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
ms.openlocfilehash: a0eb234d9677c479d2cb404b065da408854168e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368387"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>경계 81 활동 로그를 Azure 센티널에 연결

이 문서에서는 [경계 81 활동 로그](https://www.perimeter81.com/) 어플라이언스를 Azure 센티널에 연결 하는 방법을 설명 합니다. 경계 81 활동 로그 커넥터를 사용 하 여 경계 81 데이터를 Azure 센티널로 쉽게 가져올 수 있습니다. 따라서 통합 문서에서 볼 수 있으며,이를 사용 하 여 사용자 지정 경고를 만든 다음이를 통합 하 여 조사를 개선할 수 있습니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역에 대 한 공유 키에 대 한 읽기 권한이 있어야 합니다.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>경계 81 활동 로그 구성 및 연결

경계 81 활동 로그는 로그를 Azure 센티널로 직접 통합 하 고 내보낼 수 있습니다.

1. Azure 센티널 포털의 탐색 메뉴에서 **데이터 커넥터** 를 클릭 합니다.

1. 갤러리에서 **경계 81 활동 로그** 를 선택 하 고 **커넥터 페이지 열기** 단추를 클릭 합니다.

1. 경계 81 활동 로그 커넥터 페이지에서 **작업 영역 ID** 및 **기본 키** 를 복사 하 고 여기에 설명 된 [대로](https://support.perimeter81.com/hc/en-us/articles/360012680780)경계 81에 붙여넣습니다.

1. 지침을 완료 한 후에는 Azure 센티널 커넥터 페이지에 연결 된 데이터 형식이 표시 됩니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 **customlogs**Perimeter81_CL의 **로그** 에 표시 됩니다  -  **Perimeter81_CL**.

로그가 나타날 때까지 20 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 경계 81 활동 로그를 Azure 센티널에 연결 하는 방법을 알아보았습니다. 이 데이터 커넥터에 기본 제공 되는 기능을 최대한 활용 하려면 데이터 커넥터 페이지에서 **다음 단계** 탭을 클릭 합니다. 여기에서 유용한 정보를 찾을 수 있도록 준비 된 통합 문서와 일부 샘플 쿼리를 찾을 수 있습니다.

Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
