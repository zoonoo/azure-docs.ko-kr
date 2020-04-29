---
title: Forcepoint DLP를 Azure 센티널에 연결 | Microsoft Docs
description: Forcepoint DLP를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588249"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Forcepoint DLP를 Azure 센티널에 연결

> [!IMPORTANT]
> Azure 센티널의 Forcepoint 데이터 손실 방지 (DLP) 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다. 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.



Forcepoint DLP 커넥터를 사용 하 여 DLP 인시던트 데이터를 Azure 센티널로 자동으로 내보낼 수 있습니다. 이를 사용 하 여 사용자 활동 및 데이터 손실 인시던트에 대 한 가시성을 얻을 수 있습니다. 또한 Azure 워크 로드 및 다른 피드의 데이터와의 상관 관계를 설정 하 고 Azure 센티널 내 통합 문서에 대 한 모니터링 기능을 향상 시킵니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-forcepoint-dlp"></a>Forcepoint DLP 구성 및 연결

Forcepoint dlp [통합 가이드](https://frcpnt.com/dlp-sentinel)에 설명 된 대로 REST API를 통해 JSON 형식의 인시던트 데이터를 Azure 작업 영역에 전달 하도록 FORCEPOINT dlp를 구성 합니다.


## <a name="find-your-data"></a>데이터 찾기

Forcepoint DLP 커넥터를 설정 하면 데이터는 CustomLogs **ForcepointDLPEvents_CL**의 Log Analytics에 표시 됩니다.


Forcepoint DLP에 대해 Log Analytics에서 관련 스키마를 사용 하려면 **ForcepointDLPEvents_CL**를 검색 합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Forcepoint DLP를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.