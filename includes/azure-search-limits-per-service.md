---
title: 포함 파일
description: 포함 파일
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83682630"
---
검색 서비스는 디스크 공간이나 인덱스 또는 인덱서 최대 수의 하드 제한 중에서 먼저 충족되는 조건에 따라 제한됩니다. 다음 표에서는 스토리지 제한에 대해 설명합니다. 최대 개체 제한은 [리소스별 제한](../articles/search/search-limits-quotas-capacity.md#index-limits)을 참조하세요.

| 리소스 | 무료 | 기본<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| SLA(서비스 수준 계약)<sup>2</sup>  |예 |예 |예 |예 |예 |예 |예 |예 |
| 파티션당 스토리지 |50MB |2GB |25GB |100GB |200GB |200GB |1TB |2TB |
| 서비스당 파티션 |해당 없음 |1 |12 |12 |12 |3 |12 |12 |
| 파티션 크기 |해당 없음 |2GB |25GB |100GB |200GB |200GB |1TB |2TB |
| 복제본 |해당 없음 |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> 기본 계층에는 고정된 파티션 하나만 있습니다. 추가 검색 단위를 사용하여 더 큰 쿼리 볼륨의 복제본을 추가할 수 있습니다.

<sup>2</sup> 서비스 수준 계약은 전용 리소스에서 청구 가능한 서비스에 적용됩니다. 체험 서비스 및 미리 보기 기능에는 SLA가 없습니다. 청구 가능한 서비스의 경우 서비스에 충분한 중복성을 프로비전할 때 SLA가 적용됩니다. 쿼리(읽기) SLA에는 두 개 이상의 복제본이 필요합니다. 쿼리 및 인덱싱(읽기-쓰기) SLA에는 세 개 이상의 복제본이 필요합니다. 파티션의 수는 SLA 고려사항이 아닙니다. 