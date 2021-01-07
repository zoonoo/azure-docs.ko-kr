---
title: Microsoft 파트너 센터에서 컨설팅 서비스 가격 책정 및 가용성을 구성 하는 방법
description: 파트너 센터를 사용 하 여 Microsoft 상업적 marketplace에서 컨설팅 서비스 제공 가격 세부 정보 및 시장 가용성을 구성 하는 방법에 대해 알아봅니다.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 6b386238bd759714bc0c8ad81d67c29aa1774aba
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780232"
---
# <a name="how-to-configure-your-consulting-service-pricing-and-availability"></a>컨설팅 서비스 가격 책정 및 가용성을 구성 하는 방법

이 문서에서는 Microsoft 상업적 marketplace에서 컨설팅 서비스 제품에 대 한 시장 가용성 및 가격 정보를 정의 하는 방법을 설명 합니다.

> [!NOTE]
> 컨설팅 서비스 제공은 목록 전용입니다. 모든 트랜잭션은 상업적 marketplace 외부 사용자와 고객 간에 관리 되어야 합니다.

## <a name="markets"></a>시장

**시장** 섹션에서 컨설팅 서비스를 사용할 수 있는 국가 또는 지역을 선택 합니다.

1. **시장** 에서 **시장 편집** 링크를 선택 합니다.
2. 표시 되는 대화 상자에서 제품을 사용할 수 있도록 하려는 시장 위치를 선택 합니다. 최소 1 자에서 최대 141 시장을 선택 해야 합니다.
3. **저장** 을 선택 하 여 대화 상자를 닫습니다.

## <a name="preview-audience"></a>미리 보기 대상 그룹

컨설팅 서비스 제품을 게시 하거나 업데이트 하는 경우 파트너 센터는 목록의 미리 보기 버전을 만듭니다. 이 미리 보기는 **숨기기 키** 가 있는 사용자 에게만 표시 됩니다.

**키 숨기기** 필드에 제품의 미리 보기 버전에 액세스 하는 데 사용할 영숫자 문자열을 입력 합니다.

## <a name="pricing-informational-only"></a>가격 책정 (정보 제공 용)

**가격 책정** 섹션에서 무료 또는 유료 제품 인지 여부를 정의 합니다.

유료 제품의 경우 가격이 고정 인지 예상 인지를 지정 합니다. 가격이 예상 되는 경우 제품 설명에서 가격에 영향을 주는 요인을 설명 해야 합니다.

**시장** 섹션에서 단일 국가 또는 지역을 선택한 경우 해당 시장에 대해 지원 되는 통화로 가격을 제공 하 고 **초안 저장** 을 선택 합니다. 지원 되는 통화 목록은 [상용 marketplace에 대 한 지리적 가용성 및 통화 지원](./marketplace-geo-availability-currencies.md) 을 참조 하세요.

**시장** 섹션에서 여러 국가 또는 지역을 선택한 경우 미국 달러 (USD)로 가격을 제공 하 고 **초안 저장** 을 선택 합니다. 파트너 센터는 초안을 저장할 때 사용할 수 있는 환율을 사용 하 여 선택한 모든 시장의 현지 통화로 해당 가격을 변환 합니다.

변환의 유효성을 검사 하거나 개별 시장에서 사용자 지정 가격을 설정 하려면 가격 책정 스프레드시트를 내보내고 수정한 후 가져와야 합니다.

1. **가격 책정** 에서 **가격 데이터 내보내기** 링크를 선택 합니다. 그러면 장치에 파일이 다운로드 됩니다.
1. Microsoft Excel에서 exportedPrice.xlsx 파일을 엽니다.
1. 스프레드시트에서 각 시장의 가격과 통화를 조정할 수 있습니다. 지원 되는 통화 목록은 [상용 marketplace에 대 한 지리적 가용성 및 통화 지원](./marketplace-geo-availability-currencies.md) 을 참조 하세요. 완료 되 면 파일을 저장 합니다.
1. 파트너 센터의 **가격 책정** 에서 **가격 데이터 가져오기** 링크를 선택 합니다. 파일을 가져오면 이전 가격 책정 정보를 덮어씁니다.

> [!IMPORTANT]
> 파트너 센터에서 정의한 가격은 고정 되어 있으며 환율이 변동 되지 않습니다. 게시 후 하나 이상의 시장에서 가격을 변경 하려면 파트너 센터에서 제품을 업데이트 하 고 다시 제출 하세요.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [검토 및 게시](review-publish-offer.md)