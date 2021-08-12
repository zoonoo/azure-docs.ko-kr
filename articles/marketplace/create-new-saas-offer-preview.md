---
title: Azure Marketplace의 SaaS 제품에 대한 미리 보기 대상 그룹 추가
description: Azure Marketplace의 SaaS(Software as a Service) 제품에 대한 미리 보기 대상을 추가합니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: fe42b5c7d9953a3e8bc15c4dbd351d3d2beb4d33
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110614408"
---
# <a name="add-a-preview-audience-for-a-saas-offer"></a>SaaS 제품에 대한 미리 보기 대상 추가

파트너 센터에서 SaaS(Software as a Service) 제품을 만들 때는 라이브가 되기 전에 제품 목록을 검토할 수 있는 미리 보기 대상을 정의해야 합니다. 이 문서에서는 미리 보기 대상을 구성하는 방법을 설명합니다.

> [!NOTE]
> 트랜잭션을 독립적으로 처리하도록 선택하면 이 옵션이 표시되지 않습니다. 대신, [SaaS 제품을 판매하는 방법](create-new-saas-offer-marketing.md)으로 건너뜁니다.

## <a name="define-a-preview-audience"></a>미리 보기 대상 정의

**미리 보기 대상** 에서 SaaS 제품을 광범위한 마켓플레이스 대상 그룹에 라이브로 게시하기 전에 검토할 수 있는 제한된 대상을 정의할 수 있습니다. MSA(Microsoft 계정) 또는 Azure AD(Azure Active Directory) 이메일 주소로 초대를 보낼 수 있습니다. 1~10개의 메일 주소를 수동으로 추가하거나 .csv 파일로 최대 20개를 가져옵니다. 언제든지 미리 보기 대상 그룹 목록을 업데이트할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 프라이빗 대상 그룹과 다릅니다. 미리 보기 대상 그룹은 제품이 온라인 스토어에 라이브로 게시되기 ‘전에’ 해당 제품에 액세스할 수 있습니다. 플랜을 만들어 비공개 대상 그룹만 사용할 수 있도록 설정할 수도 있습니다. 프라이빗 플랜은 [SaaS 제품에 대한 플랜을 만드는 방법](create-new-saas-offer-plans.md)에 설명되어 있습니다.

### <a name="add-email-addresses-manually"></a>수동으로 이메일 주소 추가

1. **미리 보기 대상** 페이지에서 제공된 상자에 단일 zure AD 또는 MSA 메일 주소와 선택적 설명을 추가합니다.
1. 다른 메일 주소를 추가하려면 **다른 메일 추가** 링크를 선택합니다.
1. 다음 탭(**기술 구성**)으로 진행하기 전에 **초안 저장** 을 선택합니다.
1. [SaaS 제품에 대한 기술 세부 정보를 추가하는 방법](create-new-saas-offer-technical.md)을 계속 진행합니다.

### <a name="add-email-addresses-using-the-csv-file"></a>CSV 파일을 사용하여 메일 주소 추가

1. **미리 보기 대상** 페이지에서 **대상 내보내기(csv)** 링크를 선택합니다.
1. Microsoft Excel과 같은 애플리케이션에서 .CSV 파일을 엽니다.
1. .CSV 파일의 **ID** 열에서 미리 보기 대상에 추가할 메일 주소를 입력합니다.
1. **설명** 열에서 각 메일 주소에 대한 설명을 추가할 수 있습니다(선택 사항).
1. **유형** 열에서 메일 주소가 있는 각 행에 **MixedAadMsaId** 를 추가합니다.
1. 파일을 .CSV로 저장합니다.
1. **미리 보기 대상** 페이지에서 **대상 가져오기(csv)** 링크를 선택합니다.
1. **확인** 대화 상자에서 **예** 를 선택합니다.
1. .CSV 파일을 선택한 다음, **열기** 를 선택합니다.
1. 다음 탭(**기술 구성**)으로 진행하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [SaaS 제품에 대한 기술 세부 정보 추가](create-new-saas-offer-technical.md)
