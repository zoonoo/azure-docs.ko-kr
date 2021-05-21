---
title: Alcide kAudit 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: Azure Sentinel에 Alcide kAudit 데이터를 연결하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: b146e228de13109975a76b0e4c6c9fd183fd362d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600407"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Alcide kAudit를 Azure Sentinel에 연결

> [!IMPORTANT]
> Azure Sentinel의 Alcide kAudit 데이터 커넥터는 현재 공개 미리 보기로 제공됩니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/)를 사용하면 비정상적인 Kubernetes 동작을 식별하고, 검색 시간을 줄이면서 Kubernetes 위반 및 인시던트에 집중할 수 있습니다. 이 문서에서는 Alcide kAudit 솔루션을 Azure Sentinel에 연결하는 방법을 설명합니다. Alcide kAudit 데이터 커넥터를 사용하면 kAudit 로그 데이터를 Azure Sentinel로 쉽게 가져올 수 있으므로 통합 문서에서 보고, 이를 사용하여 사용자 지정 경고를 만들고 통합하여 조사를 개선할 수 있습니다. Alcide kAudit와 Azure Sentinel 간의 통합에는 REST API가 사용됩니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>전제 조건

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역 공유 키에 대한 읽기 권한이 있어야 합니다.

## <a name="configure-and-connect-alcide-kaudit"></a>Alcide kAudit 구성 및 연결

Alcide kAudit는 로그를 Azure Sentinel로 직접 내보낼 수 있습니다.

1. Azure Sentinel 포털의 탐색 메뉴에서 **데이터 커넥터** 를 클릭합니다.

1. 갤러리에서 **Alcide kAudit** 를 선택한 다음, **커넥터 페이지 열기** 단추를 클릭합니다.

1. [Alcide kAudit 설치 가이드](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit)에 제공된 단계별 지침을 따릅니다.

1. 작업 영역 ID 및 기본 키를 묻는 메시지가 표시되면 Alcide kAudit 데이터 커넥터 페이지에서 복사할 수 있습니다.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="작업 영역 ID 및 기본 키":::

## <a name="find-your-data"></a>데이터 찾기

성공적으로 연결되면 **CustomLogs** 의 다음 데이터 형식 아래에 있는 **로그** 에 데이터가 표시됩니다.

- **alcide_kaudit_detections_1_CL** - Alcide kAudit 검색 
- **alcide_kaudit_activity_1_CL** - Alcide kAudit 활동 로그
- **alcide_kaudit_selections_count_1_CL** - Alcide kAudit 활동 수
- **alcide_kaudit_selections_details_1_CL** - Alcide kAudit 활동 세부 정보

Alcide kAudit에 대한 로그에서 관련 스키마를 사용하려면 위에서 언급한 데이터 형식을 검색합니다.

로그가 Log Analytics에 표시될 때까지 최대 20분 가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Alcide kAudit를 Azure Sentinel에 연결하는 방법을 알아보았습니다. 이 데이터 커넥터에 기본 제공되는 기능을 최대한 활용하려면 데이터 커넥터 페이지에서 **다음 단계** 탭을 클릭합니다. 여기에서 유용한 정보를 찾을 수 있도록 몇 가지 미리 만들어진 샘플 쿼리를 찾을 수 있습니다.

Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
