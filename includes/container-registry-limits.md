---
title: 포함 파일
description: 포함 파일
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117093"
---
| 리소스 | Basic | Standard | Premium |
|---|---|---|---|
| Storage<sup>1</sup> | 10GiB | 100GiB| 500GiB |
| 최대 이미지 레이어 크기 | 200GiB | 200GiB | 200GiB |
| 분당 ReadOps<sup>2, 3</sup> | 1,000 | 3,000 | 10000 |
| 분당 WriteOps<sup>2, 4</sup> | 100 | 500 | 2,000 |
| 다운로드 대역폭 MBps<sup>2</sup> | 30 | 60 | 100 |
| 업로드 대역폭 MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 500 |
| 지역에서 복제 | 해당 없음 | 해당 없음 | [지원됨][geo-replication] |
| 콘텐츠 신뢰 | 해당 없음 | 해당 없음 | [지원됨][content-trust] |
| 가상 네트워크 액세스 | 해당 없음 | 해당 없음 | [미리 보기][vnet] |
| 프라이빗 링크 통합 | 해당 없음 | 해당 없음 | [미리 보기][plink] |
| 고객 관리형 키 | 해당 없음 | 해당 없음 | [미리 보기][cmk] |
| 리포지토리 범위 권한 | 해당 없음 | 해당 없음 | [미리 보기][token]|
| &bull; 토큰 | 해당 없음 | 해당 없음 | 20,000 |
| &bull; 범위 맵 | 해당 없음 | 해당 없음 | 20,000 |
| &bull; 범위 맵당 리포지토리 | 해당 없음 | 해당 없음 | 500 |


<sup>1</sup> 지정된 스토리지 제한은 각 계층에 *포함되는* 스토리지의 양입니다. 이러한 한도 초과 이미지 스토리지에 대해 GiB당 추가 일별 요금이 청구됩니다. 요금 정보는 [Azure Container Registry 가격 책정][pricing]을 참조하세요.

<sup>2</sup>*ReadOps*, *WriteOps* 및 *Bandwidth*는 최소 추정치입니다. Azure Container Registry는 필요한 사용량에 따라 성능을 개선하려고 합니다.

<sup>3</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image)은 이미지의 레이어 수와 매니페스트 검색을 더한 값에 따라 여러 읽기 작업으로 변환됩니다.

<sup>4</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image)는 푸시해야 하는 레이어 수에 따라 여러 쓰기 작업으로 변환됩니다. `docker push`에는 기존 이미지에 대해 매니페스트를 검색할 *ReadOps*가 포함되어 있습니다.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md