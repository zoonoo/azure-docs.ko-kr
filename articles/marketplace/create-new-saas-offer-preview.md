---
title: Microsoft 상업적 marketplace에서 SaaS 제품에 대 한 미리 보기 대상을 추가 하는 방법
description: Microsoft 파트너 센터에서 SaaS (software as a service) 제품에 대 한 미리 보기 대상을 추가 하는 방법입니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: ed0c7ef98e70774350c9a3ff12b0cc3f4186e1bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380981"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>SaaS 제품에 대 한 미리 보기 대상을 추가 하는 방법

파트너 센터에서 SaaS (software as a service) 제품을 만들 때 제품 목록이 라이브 되기 전에 검토할 수 있는 미리 보기 대상을 정의 해야 합니다. 이 문서에서는 미리 보기 대상 그룹을 구성 하는 방법을 설명 합니다.

> [!NOTE]
> 트랜잭션을 독립적으로 처리 하도록 선택 하면이 옵션이 표시 되지 않습니다. 대신 [SaaS 제품을 출시 하는 방법](create-new-saas-offer-marketing.md)으로 건너뛰십시오.

## <a name="define-a-preview-audience"></a>미리 보기 대상 정의

**사용자 미리 보기** 탭에서 SaaS 제품을 광범위 한 marketplace 대상에 게시 하기 전에 검토할 수 있는 제한 된 사용자를 정의할 수 있습니다. Microsoft 계정 (MSA) 또는 Azure Active Directory (Azure AD) 전자 메일 주소로 초대를 보낼 수 있습니다. 하나 이상의 전자 메일 주소를 수동으로 추가 하거나 .csv 파일을 사용 하 여 최대 20 개까지 가져오세요. 언제 든 지 미리 보기 대상 목록을 업데이트할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 프라이빗 대상 그룹과 다릅니다. 온라인 상점에 게시 하기 전에 미리 보기 대상 사용자가 제품에 대 한 액세스를 허용 합니다. 플랜을 만들어 비공개 대상 그룹만 사용할 수 있도록 설정할 수도 있습니다. 개인 계획은 [SaaS 제품에 대 한 계획을 만드는 방법](create-new-saas-offer-plans.md)에 설명 되어 있습니다.

### <a name="add-email-addresses-manually"></a>수동으로 전자 메일 주소 추가

1. **대상 미리 보기** 페이지에서 단일 Azure AD 또는 MSA 전자 메일 주소와 제공 된 상자에 설명 (선택 사항)을 추가 합니다.
1. 다른 전자 메일 주소를 추가 하려면 **다른 전자 메일 추가** 링크를 선택 합니다.
1. 다음 탭을 계속 하기 전에 **초안 저장** 을 선택 합니다. **기술 구성**.
1. [SaaS 제품에 대 한 기술 세부 정보를 추가 하는 방법을](create-new-saas-offer-technical.md)계속 진행 합니다.

### <a name="add-email-addresses-using-the-csv-file"></a>CSV 파일을 사용 하 여 전자 메일 주소 추가

1. **대상 미리 보기** 페이지에서 **대상 그룹 (csv)** 링크를 선택 합니다.
1. 를 엽니다. Microsoft Excel과 같은 응용 프로그램의 CSV 파일입니다.
1. 안에. CSV 파일의 **ID** 열에 미리 보기 대상 그룹에 추가할 전자 메일 주소를 입력 합니다.
1. **설명** 열에서 필요에 따라 각 전자 메일 주소에 대 한 설명을 추가할 수 있습니다.
1. **유형** 열에서 전자 메일 주소를 포함 하는 각 행에 **MixedAadMsaId** 를 추가 합니다.
1. 로 파일을 저장 합니다. CSV 파일.
1. **사용자 미리 보기** 페이지에서 **대상 그룹 가져오기 (csv)** 링크를 선택 합니다.
1. **확인** 대화 상자에서 **예**를 선택 합니다.
1. 을 선택 합니다. CSV 파일을 선택 하 고 **열기**를 선택 합니다.
1. 다음 탭을 계속 하기 전에 **초안 저장** 을 선택 합니다. **기술 구성**.

## <a name="next-steps"></a>다음 단계

- [SaaS 제품에 대 한 기술 세부 정보를 추가 하는 방법](create-new-saas-offer-technical.md)
