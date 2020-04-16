---
title: 관리되는 개인 끝점을 만들어 데이터 원본 결과에 연결합니다.
description: 이 문서에서는 Azure Synapse 작업 영역에서 데이터 원본에 관리되는 개인 끝점을 만드는 방법을 설명합니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428902"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>데이터 원본에 관리되는 비공개 끝점 만들기(미리 보기)

이 문서에서는 Azure의 데이터 원본에 관리되는 개인 끝점을 만드는 방법을 설명합니다. 자세한 내용은 [관리되는 비공개 끝점을](./synapse-workspace-managed-private-endpoints.md) 참조하십시오.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>1단계: Azure 포털에서 Azure 시냅스 작업 영역 열기

Azure Synapse Studio에서 데이터 원본에 관리되는 개인 끝점을 만들 수 있습니다. Azure 포털에서 **개요** 탭을 선택하고 **시냅스 스튜디오 시작을**선택합니다.
![Azure 시냅스 스튜디오 출시](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>2단계: 시냅스 스튜디오에서 관리되는 가상 네트워크 탭으로 이동

Azure 시냅스 스튜디오에서 왼쪽 탐색에서 **관리** 탭을 선택합니다. **관리되는 가상 네트워크를** 선택한 다음 **+ 새 .**
![새 관리되는 개인 끝점 만들기](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>3단계: 데이터 원본 유형 선택

데이터 원본 유형을 선택합니다. 이 경우 대상 데이터 원본은 ADLS gen2 계정입니다. **계속**을 선택합니다.
![대상 데이터 원본 유형 선택](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>4단계: 데이터 원본에 대한 정보 입력

다음 창에서 데이터 원본에 대한 정보를 입력합니다. 이 예제에서는 ADLS gen2 계정에 관리되는 개인 끝점을 만듭니다. 관리되는 개인 끝점의 **이름을** 입력합니다. Azure **구독** 및 **저장소 계정 이름을**제공합니다. **만들기**를 선택합니다.
![대상 데이터 원본 세부 정보 입력](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>5단계: 관리되는 개인 엔드포인트가 성공적으로 만들어졌는지 확인

요청을 제출하면 해당 상태가 표시됩니다. 관리되는 개인 끝점을 성공적으로 만들었는지 확인하려면 *해당 프로비저닝 상태를*확인합니다. 프로비저닝 상태를 업데이트하려면 1분 동안 기다렸다가 **새로 고침을** 선택해야 할 수 있습니다. ADLS gen2 계정에 대한 관리되는 개인 끝점이 성공적으로 만들어졌는지 확인할 수 있습니다.

*또한 승인 상태가* 보류 *중임을*확인할 수 있습니다. 대상 리소스의 소유자는 개인 끝점 연결 요청을 승인하거나 거부할 수 있습니다. 소유자가 개인 끝점 연결 요청을 승인하면 개인 링크가 설정됩니다. 거부된 경우 개인 링크가 설정되지 않습니다.
![관리되는 개인 엔드포인트 만들기 요청 상태](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>다음 단계

[관리되는 비공개 엔드포인트에](./synapse-workspace-managed-private-endpoints.md) 대해 자세히 알아보기