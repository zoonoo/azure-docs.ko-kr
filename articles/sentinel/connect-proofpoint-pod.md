---
title: Proofpoint On Demand 메일 보안 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: Proofpoint On Demand 메일 보안 데이터 커넥터를 사용하여 POD 메일 보안 로그를 Azure Sentinel로 가져오는 방법에 대해 알아봅니다. 통합 문서에서 POD 메일 보안 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 73a9d9c7ab321aebd615922e5d4395c0318e809c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580447"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>POD(Proofpoint On Demand) 메일 보안 솔루션을 Azure Sentinel에 연결

> [!IMPORTANT]
> Proofpoint On Demand 메일 보안 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Proofpoint On Demand 메일 보안 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. POD 데이터 커넥터를 통해 POD 로그를 Azure Sentinel에 쉽게 연결하여 통합 문서에서 데이터를 보고, 사용자 지정 경고를 만드는 데 사용하고, 통합하여 조사를 개선할 수 있습니다.  Proofpoint On Demand 메일 보안 및 Azure Sentinel 간의 통합은 Websocket API를 이용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역 공유 키에 대한 읽기 권한이 있어야 합니다. [작업 영역 키에 대한 자세한 정보를 알아보세요](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- 함수 앱을 만들려면 Azure Functions에 대한 읽기 및 쓰기 권한이 있어야 합니다. [Azure Functions에 대한 자세한 정보를 알아보세요](../azure-functions/index.yml).

- ProofpointClusterID와 ProofpointToken의 Websocket API 자격 증명이 있어야 합니다. [Web API에 대한 자세한 정보를 알아보세요](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>Proofpoint On Demand 메일 보안 구성 및 연결

Proofpoint On Demand 메일 보안은 로그를 통합하여 Azure Sentinel로 직접 내보낼 수 있습니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **Proofpoint On Demand 메일 보안(미리 보기)** 을 선택한 다음 **커넥터 페이지를 엽니다**.

1. 커넥터 페이지의 **구성** 섹션에 설명된 단계를 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정되면 데이터는 다음 테이블의 *사용자 지정 로그* 에 있는 **로그** 에 표시됩니다.
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

몇 가지 유용한 샘플 쿼리는 커넥터 페이지의 **다음 단계** 탭을 참조하세요.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 60분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Proofpoint On Demand 메일 보안을 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.