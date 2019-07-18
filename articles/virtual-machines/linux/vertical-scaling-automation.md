---
title: Azure Automation를 사용하여 Azure 가상 머신을 수직으로 확장 | Microsoft Docs
description: Azure Automation을 사용하여 모니터링 경고에 대한 응답으로 Linux Virtual Machine을 수직으로 확장하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e0317344fd8ee1eb415b61d4f5035219e649b18d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695470"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Azure Automation을 사용하여 Azure Linux 가상 컴퓨터를 수직으로 확장
수직 확장은 워크로드에 응답하여 컴퓨터의 리소스를 늘리거나 줄이는 프로세스입니다. Azure에서는 Virtual Machine의 크기를 변경하여 이를 수행할 수 있습니다. 이는 다음과 같은 시나리오에 유용합니다.

* Virtual Machine이 자주 사용되지 않는 경우 크기를 줄여 월별 비용을 절감할 수 있습니다.
* Virtual Machine에서 최대 부하가 발생하는 경우 크기를 늘려 용량을 증가시킬 수 있습니다.

이 작업을 수행하는 개략적인 단계는 다음과 같습니다.

1. Virtual Machines에 액세스하도록 Azure Automation 설정
2. 구독으로 Azure Automation 수직 확장 runbook 가져오기
3. Runbook에 Webhook 추가
4. Virtual Machine에 경고 추가

## <a name="scale-limitations"></a>확장 제한

첫 번째 Virtual Machine의 크기로 인해 확장할 수 있는 크기가 제한될 수 있습니다. 이는 현재 Virtual Machine이 배포된 클러스터에서 다른 크기의 가용성 때문입니다. 이 문서에서 사용된 게시된 자동화 runbook에서는 이 점을 염두에 두고 VM 크기 쌍 이내에서만 확장합니다. 따라서 Standard_D1v2 Virtual Machine이 갑자기 Standard_G5로 확장되거나 Basic_A0으로 축소되지 않습니다. 또한 제한 된 가상 머신 크기 확장/축소는 지원 되지 않습니다. 

다음 규모 쌍 범위로 규모 조정하도록 선택할 수 있습니다.

* [A 시리즈](#a-series)
* [B-Series](#b-series)
* [D 시리즈](#d-series)
* [E 시리즈](#e-series)
* [F 시리즈](#f-series)
* [G-Series](#g-series)
* [H 시리즈](#h-series)
* [L-Series](#l-series)
* [M 시리즈](#m-series)
* [N 시리즈](#n-series)

### <a name="a-series"></a>A 시리즈

| 처음 크기 | 크기 확장 | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standard_A0 | Standard_A1 |
| Standard_A1 | Standard_A2 |
| Standard_A2 | Standard_A3 |
| Standard_A3 | Standard_A4 |
| Standard_A5 | Standard_A6 |
| Standard_A6 | Standard_A7 |
| Standard_A8 | Standard_A9 |
| Standard_A10 | Standard_A11 |
| Standard_A1_v2 | Standard_A2_v2 |
| Standard_A2_v2 | Standard_A4_v2 |
| Standard_A4_v2 | Standard_A8_v2 |
| Standard_A2m_v2 | Standard_A4m_v2 |
| Standard_A4m_v2 | Standard_A8m_v2 |

### <a name="b-series"></a>B 시리즈

| 처음 크기 | 크기 확장 | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>D 시리즈

| 처음 크기 | 크기 확장 | 
| --- | --- |
| Standard_D1 | Standard_D2 |
| Standard_D2 | Standard_D3 |
| Standard_D3 | Standard_D4 |
| Standard_D11 | Standard_D12 |
| Standard_D12 | Standard_D13 |
| Standard_D13 | Standard_D14 |
| Standard_DS1 | Standard_DS2 |
| Standard_DS2 | Standard_DS3 |
| Standard_DS3 | Standard_DS4 |
| Standard_DS11 | Standard_DS12 |
| Standard_DS12 | Standard_DS13 |
| Standard_DS13 | Standard_DS14 |
| Standard_D1_v2 | Standard_D2_v2 |
| Standard_D2_v2 | Standard_D3_v2 |
| Standard_D3_v2 | Standard_D4_v2 |
| Standard_D4_v2 | Standard_D5_v2 |
| Standard_D11_v2 | Standard_D12_v2 |
| Standard_D12_v2 | Standard_D13_v2 |
| Standard_D13_v2 | Standard_D14_v2 |
| Standard_DS1_v2 | Standard_DS2_v2 |
| Standard_DS2_v2 | Standard_DS3_v2 |
| Standard_DS3_v2 | Standard_DS4_v2 |
| Standard_DS4_v2 | Standard_DS5_v2 |
| Standard_DS11_v2 | Standard_DS12_v2 |
| Standard_DS12_v2 | Standard_DS13_v2 |
| Standard_DS13_v2 | Standard_DS14_v2 |
| Standard_D2_v3 | Standard_D4_v3 |
| Standard_D4_v3 | Standard_D8_v3 |
| Standard_D8_v3 | Standard_D16_v3 |
| Standard_D16_v3 | Standard_D32_v3 |
| Standard_D32_v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standard_D8s_v3 |
| Standard_D8s_v3 | Standard_D16s_v3 |
| Standard_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>E 시리즈

| 처음 크기 | 크기 확장 | 
| --- | --- |
| Standard_E2_v3 | Standard_E4_v3 |
| Standard_E4_v3 | Standard_E8_v3 |
| Standard_E8_v3 | Standard_E16_v3 |
| Standard_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standard_E32_v3 |
| Standard_E32_v3 | Standard_E64_v3 |
| Standard_E2s_v3 | Standard_E4s_v3 |
| Standard_E4s_v3 | Standard_E8s_v3 |
| Standard_E8s_v3 | Standard_E16s_v3 |
| Standard_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standard_E32s_v3 |
| Standard_E32s_v3 | Standard_E64s_v3 |

### <a name="f-series"></a>F 시리즈

| 처음 크기 | 크기 확장 | 
| --- | --- |
| Standard_F1 | Standard_F2 |
| Standard_F2 | Standard_F4 |
| Standard_F4 | Standard_F8 |
| Standard_F8 | Standard_F16 |
| Standard_F1s | Standard_F2s |
| Standard_F2s | Standard_F4s |
| Standard_F4s | Standard_F8s |
| Standard_F8s | Standard_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>G 시리즈

| 처음 크기 | 크기 확장 | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard_G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Standard_GS5 |

### <a name="h-series"></a>H 시리즈

| 처음 크기 | 크기 확장 | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>L 시리즈

| 처음 크기 | 크기 확장 | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>M 시리즈

| 처음 크기 | 크기 확장 | 
| --- | --- |
| Standard_M8ms | Standard_M16ms |
| Standard_M16ms | Standard_M32ms |
| Standard_M32ms | Standard_M64ms |
| Standard_M64ms | Standard_M128ms |
| Standard_M32ls | Standard_M64ls |
| Standard_M64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>N 시리즈

| 처음 크기 | 크기 확장 | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Virtual Machines에 액세스하도록 Azure Automation 설정
가장 먼저 해야 할 일은 VM 규모 집합 인스턴스의 크기를 조정하는 데 사용하는 Runbook을 호스트할 Azure Automation 계정을 만드는 것입니다. 최근 Automation 서비스에서는 사용자 대신 Runbook을 자동으로 매우 쉽게 실행하기 위한 서비스 주체를 설정하는 "실행 계정" 기능을 도입했습니다. 이에 대한 자세한 내용은 아래 문서를 참조하세요.

* [Azure 실행 계정으로 Runbook 인증](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>구독으로 Azure Automation 수직 확장 runbook 가져오기
Virtual Machine의 수직 확장에 필요한 runbook은 Azure Automation Runbook 갤러리에 이미 게시되어 있습니다. 이를 구독으로 가져와야 합니다. runbook을 가져오는 방법은 다음 문서에서 확인할 수 있습니다.

* [Azure Automation용 Runbook 및 모듈 갤러리](../../automation/automation-runbook-gallery.md)

가져와야 하는 runbook이 아래 이미지에 표시되어 있습니다.

![Runbook 가져오기](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Runbook에 Webhook 추가
Runbook을 가져온 후에는 Virtual Machine에서 경고를 통해 트리거될 수 있도록 runbook에 Webhook를 추가해야 합니다. Runbook에 대한 Webhook를 만드는 자세한 방법은 여기에서 확인할 수 있습니다.

* [Azure Automation Webhook](../../automation/automation-webhooks.md)

다음 섹션에서 필요하므로 Webhook 대화 상자를 닫기 전에 Webhook를 복사해야 합니다.

## <a name="add-an-alert-to-your-virtual-machine"></a>Virtual Machine에 경고 추가
1. Virtual Machine 설정 선택
2. "경고 규칙" 선택
3. "경고 추가" 선택
4. 경고를 실행할 메트릭 선택
5. 충족된 경우 경고를 실행할 조건 선택
6. 5단계의 조건을 충족하는 임계값 선택
7. 모니터링 서비스에서 5단계와 6단계의 조건 및 임계값을 확인할 기간 선택
8. 이전 섹션에서 복사한 Webhook에 붙여넣기

![Virtual Machine 1에 경고 추가](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Virtual Machine 2에 경고 추가](./media/vertical-scaling-automation/add-alert-webhook-2.png)

