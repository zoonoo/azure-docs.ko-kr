---
title: Azure 센티널에 Alcide kAudit 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Alcide kAudit 데이터를 연결 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 121c9258353505b6147aa059d327bc711e9fede4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368396"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Azure 센티널에 Alcide kAudit 연결

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) 를 사용 하 여 비정상적인 Kubernetes 동작을 식별 하 고, 검색 시간을 줄이면서 Kubernetes 위반 및 인시던트에 집중할 수 있습니다. 이 문서에서는 Alcide kAudit 솔루션을 Azure 센티널에 연결 하는 방법을 설명 합니다. Alcide kAudit 데이터 커넥터를 사용 하면 kAudit 로그 데이터를 Azure 센티널로 쉽게 가져올 수 있습니다. 그러면 통합 문서에서이를 보고,이를 사용 하 여 사용자 지정 경고를 만들고, 조사를 개선 하는 데 통합할 수 있습니다. Alcide kAudit와 Azure 센티널 간의 통합은 REST API를 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역에 대 한 공유 키에 대 한 읽기 권한이 있어야 합니다.

## <a name="configure-and-connect-alcide-kaudit"></a>Alcide 감사 구성 및 연결

Alcide kAudit는 로그를 Azure 센티널로 직접 내보낼 수 있습니다.

1. Azure 센티널 포털의 탐색 메뉴에서 **데이터 커넥터** 를 클릭 합니다.

1. 갤러리에서 **Alcide kAudit** 를 선택 하 고 **커넥터 페이지 열기** 단추를 클릭 합니다.

1. [Alcide kAudit 설치 가이드](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf)에 제공 된 단계별 지침을 따릅니다.

1. 작업 영역 ID 및 기본 키를 묻는 메시지가 표시 되 면 Alcide kAudit 데이터 커넥터 페이지에서 복사할 수 있습니다.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="작업 영역 ID 및 기본 키":::

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 **Customlogs**의 다음 데이터 형식 아래에 있는 **로그** 에 표시 됩니다.

- **alcide_kaudit_detections_1_CL** alcide kaudit 검색 
- **alcide_kaudit_activity_1_CL** -alcide kaudit 활동 로그
- **alcide_kaudit_selections_count_1_CL** alcide kaudit 활동 수
- **alcide_kaudit_selections_details_1_CL** -alcide kaudit 활동 세부 정보

Alcide kAudit에 대 한 로그에서 관련 스키마를 사용 하려면 위에서 언급 한 데이터 형식을 검색 합니다.

로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Alcide kAudit를 Azure 센티널에 연결 하는 방법을 알아보았습니다. 이 데이터 커넥터에 기본 제공 되는 기능을 최대한 활용 하려면 데이터 커넥터 페이지에서 **다음 단계** 탭을 클릭 합니다. 유용한 정보를 찾을 수 있도록 몇 가지 미리 만들어진 샘플 쿼리를 찾을 수 있습니다.

Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
