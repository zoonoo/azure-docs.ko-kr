---
title: 관리 서비스 제품에 대 한 미리 보기 대상을 추가 하는 방법
description: Microsoft 파트너 센터에서 관리 서비스 제품에 대 한 미리 보기 대상을 추가 하는 방법에 대해 알아봅니다.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97918275"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>관리 서비스 제품에 대 한 미리 보기 대상을 추가 하는 방법

이 문서에서는 파트너 센터를 사용 하 여 상업적 marketplace에서 관리 되는 서비스 제품에 대 한 미리 보기 대상을 구성 하는 방법을 설명 합니다. 미리 보기 대상은 라이브 상태가 되기 전에 제품을 검토할 수 있습니다.

## <a name="define-a-preview-audience"></a>미리 보기 대상 정의

**사용자 미리 보기** 페이지에서 관리 되는 서비스 제품을 광범위 한 marketplace 대상에 게시 하기 전에 검토할 수 있는 제한 된 사용자를 정의할 수 있습니다. Azure 구독 Id를 사용 하 여 미리 보기 대상 그룹을 정의 하 고 각각에 대 한 선택적 설명을 정의 합니다. 해당 필드는 고객에게 표시되지 않습니다. Azure Portal의 **구독** 페이지에서 AZURE 구독 ID를 찾을 수 있습니다.

하나 이상의 Azure 구독 ID를 개별적으로 (최대 10 개) 또는 CSV 파일을 업로드 하 여 (최대 100) 게시 하기 전에 제품을 미리 볼 수 있는 사람을 정의 합니다. 제품이 이미 라이브 상태인 경우 제품에 대 한 업데이트를 테스트 하기 위한 미리 보기 대상을 계속 정의할 수 있습니다.

> [!NOTE]
> *미리 보기* 대상 사용자는 *개인* 대상과 다릅니다. 온라인 상점에 게시 하기 전에 미리 보기 대상 사용자가 제품에 대 한 액세스를 허용 합니다. 귀하는 제품이 marketplace에 완전히 게시 된 후 개인 사용자 에게만 제공 되는 계획을 포함 하 여 모든 계획을 확인 하 고 유효성을 검사할 수 있습니다. 전용 사용자 에게만 요금제를 제공할 수 있습니다. 요금제의 가용성 탭에 정의 된 개인 사용자는 특정 요금제에 단독으로 액세스할 수 있습니다.

## <a name="add-email-addresses-manually"></a>수동으로 전자 메일 주소 추가

1. **대상 미리 보기** 페이지에서 단일 AZURE 구독 ID와 제공 된 상자에 설명 (선택 사항)을 추가 합니다.
2. 다른 전자 메일 주소를 추가 하려면 **ID 추가 (최대 10 개)** 링크를 선택 합니다.
3. 다음 탭을 계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="add-email-addresses-using-a-csv-file"></a>CSV 파일을 사용 하 여 전자 메일 주소 추가

1. 대상 그룹 미리 보기 **대상** 그룹 페이지에서 **내보내기 대상 (csv)** 링크를 선택 합니다.
2. CSV 파일을 엽니다. **Id** 열에 미리 보기 대상 그룹에 추가 하려는 Azure 구독 id를 입력 합니다.
3. **설명** 열에는 각 항목에 대 한 설명을 추가할 수 있는 옵션이 있습니다.
4. **유형** 열에서 ID가 있는 각 행에 **SubscriptionId** 를 추가 합니다.
5. CSV 파일로 파일을 저장 합니다.
6. **사용자 미리 보기** 페이지에서 **대상 그룹 가져오기 (csv)** 링크를 선택 합니다.
7. **확인** 대화 상자에서 **예** 를 선택한 다음 CSV 파일을 업로드 합니다.
8. 다음 탭을 계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [계획 만들기](create-managed-service-offer-plans.md)
