---
title: 포함 파일
description: 포함 파일
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 30c6fc189ebcd497a214828f65213a55cefdf03f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632965"
---
저장소는 디스크 공간이나 인덱스, 문서 또는 기타 고급 리소스의 *최대 수*의 하드 제한 중에서 먼저 충족되는 조건에 따라 제한됩니다. 다음 표에서는 저장소 제한에 대해 설명합니다. 인덱스, 문서 및 기타 개체의 최대 제한에 대해서 [리소스별 제한](../articles/search/search-limits-quotas-capacity.md#index-limits)합니다.

| 리소스 | 무료 | Basic<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| 서비스 수준 계약 (SLA)<sup>3</sup>  |아닙니다. |예 |예 |예 |예 |예 |예 |예 |
| 파티션당 저장소 |50MB |2 GB |25GB |100GB |200GB |200GB |1TB |2TB |
| 서비스당 파티션 |N/A |1 |12 |12 |12 |3 |12 |12 |
| 파티션 크기 |N/A |2GB |25GB |100GB |200GB |200GB |1TB |2TB |
| 복제본 |N/A |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> 기본 계층에는 고정된 파티션 하나만 있습니다. 이 계층에서 향상 된 쿼리 워크 로드에 대 한 더 많은 복제본을 할당 하는 것에 대 한 추가 검색 단위가 사용 됩니다.

<sup>2</sup> S3 HD는 S3에 대 한 파티션 제한 보다 낮은 세 개의 파티션 하드 제한이 있습니다. S3 HD에 대한 인덱스 수가 매우 높기 때문에 낮은 파티션 제한이 설정되었습니다. 컴퓨팅 리소스(스토리지 및 처리) 및 콘텐츠(인덱스 및 문서) 모두에 대해 서비스 제한이 존재하는 경우 콘텐츠 제한에 먼저 도달합니다.

<sup>3</sup> 전용된 리소스에서 청구 가능한 서비스에 대 한 서비스 수준 계약으로 제공 됩니다. 체험 서비스 및 미리 보기 기능에는 SLA가 없습니다. 청구 가능한 서비스의 경우 서비스에 충분한 중복성을 프로비전할 때 SLA가 적용됩니다. 두 개 이상의 복제본이 쿼리 (읽기) Sla에 대 한 필요 합니다. 3 개 이상의 복제본이 쿼리 및 인덱싱 (읽기 / 쓰기) Sla에 대 한 필요 합니다. 파티션 수는 SLA 고려 사항이 아닙니다. 