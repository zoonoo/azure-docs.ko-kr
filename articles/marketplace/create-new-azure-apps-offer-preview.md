---
title: Azure 애플리케이션 제품에 대 한 미리 보기 대상을 추가 하는 방법
description: 파트너 센터에서 Azure 응용 프로그램 제품에 대 한 미리 보기 대상을 추가 하는 방법에 대해 알아봅니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370253"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>Azure 애플리케이션 제품에 대 한 미리 보기 대상을 추가 하는 방법

이 문서에서는 상업적 marketplace에서 Azure 애플리케이션 제품에 대 한 미리 보기 대상을 구성 하는 방법을 설명 합니다. 제품 목록을 실시간으로 전환 하기 전에 검토할 수 있는 미리 보기 대상 그룹을 정의 해야 합니다.

## <a name="define-a-preview-audience"></a>미리 보기 대상 정의

사용자 **미리 보기** 페이지에서 광범위 한 marketplace 사용자에 게 게시 하기 전에 Azure 애플리케이션 제안을 검토할 수 있는 제한 된 사용자를 정의할 수 있습니다. Azure 구독 Id를 사용 하 여 미리 보기 대상 그룹을 정의 하 고 각각에 대 한 선택적 설명을 정의 합니다. 해당 필드는 고객에게 표시되지 않습니다. Azure 구독 ID는 Azure Portal의 **구독** 페이지에서 찾을 수 있습니다.

하나 이상의 Azure 구독 Id를 개별적으로 (최대 10 개) 또는 CSV 파일을 업로드 하 여 (최대 100), 제품을 게시 하기 전에 미리 볼 수 있는 사람을 정의 합니다. 제품이 이미 라이브 상태인 경우 제품의 변경 내용 또는 업데이트를 테스트하기 위한 미리 보기 대상 그룹을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 프라이빗 대상 그룹과 다릅니다. 온라인 상점에 게시 하기 전에 미리 보기 대상 사용자가 제품에 대 한 액세스를 허용 합니다. 제품이 마켓플레이스에 완전히 게시된 후에 미리 보기 대상 그룹에서 모든 플랜(프라이빗 대상 그룹만 사용할 수 있는 플랜 포함)을 보고 유효성을 검사할 수 있습니다. 전용 사용자 에게만 요금제를 제공할 수 있습니다. 요금제의 **가용성** 탭에 정의 된 개인 사용자는 특정 요금제에 단독으로 액세스할 수 있습니다.

### <a name="add-email-addresses-manually"></a>수동으로 전자 메일 주소 추가

1. **대상 미리 보기** 페이지에서 단일 AZURE 구독 ID와 제공 된 상자에 설명 (선택 사항)을 추가 합니다.
1. 다른 전자 메일 주소를 추가 하려면 **ID 추가 (최대 10 개)** 링크를 선택 합니다.
1. 다음 탭을 계속 하기 전에 **초안 저장** 을 선택 합니다. 기술 구성.
1. [다음 단계로](#next-steps)이동 합니다.

### <a name="add-email-addresses-using-the-csv-file"></a>CSV 파일을 사용 하 여 전자 메일 주소 추가

1. **대상 미리 보기** 페이지에서 **대상 그룹 (csv)** 링크를 선택 합니다.
1. 를 엽니다. Microsoft Excel과 같은 응용 프로그램의 CSV 파일입니다.
1. 안에. CSV 파일의 **ID** 열에 미리 보기 대상 그룹에 추가 하려는 Azure 구독 id를 입력 합니다.
1. **설명** 열에서 필요에 따라 각 전자 메일 주소에 대 한 설명을 추가할 수 있습니다.
1. **유형** 열에서 전자 메일 주소가 있는 각 행에 **SubscriptionID** 를 추가 합니다.
1. 로 파일을 저장 합니다. CSV 파일.
1. **사용자 미리 보기** 페이지에서 **대상 그룹 가져오기 (csv)** 링크를 선택 합니다.
1. **확인** 대화 상자에서 **예** 를 선택 합니다.
1. 을 선택 합니다. CSV 파일을 선택 하 고 **열기** 를 선택 합니다.
1. 다음 탭을 계속 하기 전에 **초안 저장** 을 선택 합니다. 기술 구성.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 제품에 대 한 기술 세부 정보를 추가 하는 방법](create-new-azure-apps-offer-technical.md)
