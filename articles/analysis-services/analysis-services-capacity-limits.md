---
title: Azure Analysis Services 리소스 및 개체 제한 | Microsoft Docs
description: 이 문서에서는 Azure 분석 서비스 서버에 대한 리소스 및 개체 제한에 대해 설명합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573207"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services 리소스 및 개체 제한

이 문서에서는 리소스 및 모델 개체 제한에 대해 설명합니다.

## <a name="tier-limits"></a>계층 제한

개발자, 기본 및 표준 계층에 대한 QPU 및 메모리 제한은 [Azure 분석 서비스 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/analysis-services/)참조하십시오.

## <a name="object-limits"></a>개체 제한

이러한 제한은 이론적입니다. 이보다 낮은 수치일 때는 성능이 저하됩니다.

|Object|최대 크기/개수|  
|------------|----------------------------|  
|인스턴스의 데이터베이스|16,000|  
|데이터베이스 내 테이블 및 열을 합한 수|16,000|  
|테이블의 행|제한 없음<br /><br /> **경고:** 테이블에 있는 단일 열이 1,999,999,997개가 넘는 고유 값을 가질 수 없는 제한 사항이 있습니다.|  
|테이블의 계층|15,999|  
|계층의 수준|15,999|  
|관계|8,000|  
|모든 테이블의 키 열|15,999|  
|테이블의 측정값|2^31-1 = 2,147,483,647|  
|쿼리에서 반환된 셀|2^31-1 = 2,147,483,647|  
|원본 쿼리의 레코드 크기|64K|  
|개체 이름의 길이|512자|  


