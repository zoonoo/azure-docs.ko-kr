---
title: 포함 파일
description: 포함 파일
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 179f525b7b6a7e51889b14b66df6c537ca56bd75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272610"
---
스토리지는 디스크 공간이나 인덱스, 문서 또는 기타 고급 리소스의 *최대 수*의 하드 제한 중에서 먼저 충족되는 조건에 따라 제한됩니다. 다음 표에서는 스토리지 제한에 대해 설명합니다. 인덱스, 문서 및 기타 개체에 대한 최대 제한은 [리소스별 제한을](../articles/search/search-limits-quotas-capacity.md#index-limits)참조하십시오.

| 리소스 | 무료 | 기본<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| 서비스 수준 계약(SLA)<sup>3</sup>  |예 |yes |yes |yes |yes |yes |yes |yes |
| 파티션당 스토리지 |50MB |2GB |25GB |100GB |200GB |200GB |1TB |2TB |
| 서비스당 파티션 |해당 없음 |1 |12 |12 |12 |3 |12 |12 |
| 파티션 크기 |해당 없음 |2GB |25GB |100GB |200GB |200GB |1TB |2TB |
| 복제본 |해당 없음 |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> 기본 계층에는 고정된 파티션 하나만 있습니다. 이 계층에서는 쿼리 워크로드 증가를 위해 더 많은 복제본을 할당하는 데 추가 검색 단위가 사용됩니다.

<sup>2</sup> S3 HD는 S3의 파티션 제한보다 낮은 세 개의 파티션의 하드 제한을 가지고 있습니다. S3 HD에 대한 인덱스 수가 매우 높기 때문에 낮은 파티션 제한이 설정되었습니다. 컴퓨팅 리소스(스토리지 및 처리) 및 콘텐츠(인덱스 및 문서) 모두에 대해 서비스 제한이 존재하는 경우 콘텐츠 제한에 먼저 도달합니다.

<sup>3</sup> 전용 리소스에 대한 청구 가능한 서비스에 대해 서비스 수준 계약이 제공됩니다. 체험 서비스 및 미리 보기 기능에는 SLA가 없습니다. 청구 가능한 서비스의 경우 서비스에 충분한 중복성을 프로비전할 때 SLA가 적용됩니다. SLA를 쿼리(읽기)하려면 두 개 이상의 복제본이 필요합니다. 쿼리 및 인덱싱(읽기-쓰기) SLA에 세 개 이상의 복제본이 필요합니다. 파티션 수는 SLA를 고려하지 않습니다. 