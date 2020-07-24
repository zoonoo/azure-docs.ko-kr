---
title: 관리 되는 개인 끝점을 만들어 데이터 원본 결과에 연결
description: 이 문서에서는 Azure Synapse 작업 영역에서 데이터 원본에 대 한 관리 되는 개인 끝점을 만드는 방법을 설명 합니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: fae958542f1bc391f285104d80d1e19131470abe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065481"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>데이터 원본에 대 한 관리 되는 개인 끝점 만들기 (미리 보기)

이 문서에서는 Azure에서 데이터 원본에 대 한 관리 되는 개인 끝점을 만드는 방법을 설명 합니다. 자세한 내용은 [관리 되는 전용 끝점](./synapse-workspace-managed-private-endpoints.md) 을 참조 하세요.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>1 단계: Azure Portal에서 Azure Synapse 작업 영역을 엽니다.

Azure Synapse Studio에서 데이터 원본에 대 한 관리 되는 개인 끝점을 만들 수 있습니다. Azure Portal에서 **개요** 탭을 선택 하 고 **Synapse Studio 시작**을 선택 합니다.
![Azure Synapse Studio 시작](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>2 단계: Synapse Studio에서 관리 되는 가상 네트워크 탭으로 이동

Azure Synapse Studio의 왼쪽 탐색 모음에서 **관리** 탭을 선택 합니다. **관리 되는 가상 네트워크** 를 선택 하 고 **+ 새로 만들기**를 선택 합니다.
![관리 되는 새 개인 끝점 만들기](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>3 단계: 데이터 원본 유형 선택

데이터 원본 유형을 선택 합니다. 이 경우 대상 데이터 원본은 ADLS Gen2 계정입니다. **계속**을 선택합니다.
![대상 데이터 원본 유형 선택](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>4 단계: 데이터 원본에 대 한 정보 입력

다음 창에서 데이터 원본에 대 한 정보를 입력 합니다. 이 예제에서는 ADLS Gen2 계정에 대 한 관리 되는 개인 끝점을 만듭니다. 관리 되는 개인 끝점의 **이름을** 입력 합니다. **Azure 구독** 및 **저장소 계정 이름을**제공 합니다. **만들기**를 선택합니다.
![대상 데이터 원본 정보 입력](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>5 단계: 관리 되는 개인 끝점이 성공적으로 만들어졌는지 확인 합니다.

요청을 제출 하면 해당 상태가 표시 됩니다. 관리 되는 개인 끝점이 성공적으로 만들어졌는지 확인 하려면 *프로 비전 상태*를 확인 합니다. 1 분 동안 기다린 후 **새로 고침** 을 선택 하 여 프로 비전 상태를 업데이트 해야 할 수 있습니다. ADLS Gen2 계정에 대 한 관리 되는 개인 끝점이 성공적으로 만들어졌는지 확인할 수 있습니다.

*승인 상태가* *보류 중*으로 표시 될 수도 있습니다. 대상 리소스의 소유자는 개인 끝점 연결 요청을 승인 하거나 거부할 수 있습니다. 소유자가 개인 끝점 연결 요청을 승인 하면 개인 링크가 설정 됩니다. 거부 된 경우 개인 링크가 설정 되지 않습니다.
![관리 되는 개인 끝점 만들기 요청 상태](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>다음 단계

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보