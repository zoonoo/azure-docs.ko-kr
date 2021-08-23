---
title: 관리형 프라이빗 엔드포인트를 만들어 데이터 원본 결과에 연결
description: 이 문서에서는 Azure Synapse 작업 영역에서 데이터 원본에 대한 관리형 프라이빗 엔드포인트를 만드는 방법을 설명합니다.
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: seshin
ms.reviewer: jrasnick
ms.openlocfilehash: 6ca00f185c3bfc0a10a949953dfa0e2b1e7a09a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566834"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source"></a>데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기

이 문서에서는 Azure에서 데이터 원본에 대한 관리형 프라이빗 엔드포인트를 만드는 방법을 설명합니다. 자세한 내용은 [관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)를 참조하세요.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>1단계: Azure Portal에서 Azure Synapse 작업 영역 열기

Azure Synapse Studio에서 데이터 원본에 대한 관리형 프라이빗 엔드포인트를 만들 수 있습니다. Azure Portal에서 **개요** 탭을 선택하고 시작 섹션의 Synapse Studio 카드 열기에서 **열기** 를 선택합니다.

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>2단계: Synapse Studio에서 관리형 가상 네트워크 탭으로 이동

Azure Synapse Studio의 왼쪽 탐색에서 **관리** 탭을 선택합니다. **관리형 프라이빗 엔드포인트** 를 선택한 다음 **+ 새로 만들기** 를 선택합니다.
![새 관리형 프라이빗 엔드포인트 만들기](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>3단계: 데이터 원본 유형 선택

데이터 원본 유형을 선택합니다. 이 경우, 대상 데이터 원본은 ADLS Gen2 계정입니다. **계속** 을 선택합니다.
![대상 데이터 원본 유형 선택](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>4단계: 데이터 원본에 대한 정보 입력

다음 창에서 데이터 원본에 대한 정보를 입력합니다. 이 예에서는 ADLS Gen2 계정에 대한 관리형 프라이빗 엔드포인트를 만듭니다. 관리형 프라이빗 엔드포인트의 **이름** 을 입력합니다. **Azure 구독** 및 **스토리지 계정 이름** 을 제공합니다. **만들기** 를 선택합니다.
![대상 데이터 원본 세부 정보 입력](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>5단계: 관리형 프라이빗 엔드포인트가 생성되었는지 확인

요청을 제출한 후에 해당 상태가 표시됩니다. 관리형 프라이빗 엔드포인트가 생성되었는지 확인하려면 *프로비전 상태* 를 확인합니다. 1분 동안 기다린 후 **새로 고침** 을 선택하여 프로비전 상태를 업데이트해야 할 수 있습니다. ADLS Gen2 계정에 대한 관리형 프라이빗 엔드포인트가 생성되었는지 확인할 수 있습니다.

또한 *승인 상태* 가 *보류 중* 으로 표시됩니다. 대상 리소스의 소유자는 프라이빗 엔드포인트 연결 요청을 승인하거나 거부할 수 있습니다. 소유자가 프라이빗 엔드포인트 연결 요청을 승인하면 프라이빗 링크가 설정됩니다. 거부하면 프라이빗 링크가 설정되지 않습니다.
![관리형 프라이빗 엔드포인트 만들기 요청 상태](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>다음 단계

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보