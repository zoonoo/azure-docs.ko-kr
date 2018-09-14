---
title: 포함 파일
description: 포함 파일
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: bddb17a5163333a5aeb86b296a21867f0611d12f
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43303849"
---
| 리소스 | Basic | Standard | Premium |
|---|---|---|---|---|
| Storage<sup>1</sup> | 10GiB | 100GiB| 500GiB |
| 최대 이미지 계층 크기 | 20GiB | 20GiB | 50GiB |
| 분당 ReadOps<sup>2, 3</sup> | 1,000 | 3,000 | 10000 |
| 분당 WriteOps<sup>2, 4</sup> | 100 | 500 | 2,000 |
| 다운로드 대역폭 MBps<sup>2</sup> | 30 | 60 | 100 |
| 업로드 대역폭 MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| 지역에서 복제 | 해당 없음 | 해당 없음 | [지원됨][geo-replication] |
| 콘텐츠 신뢰(미리 보기) | 해당 없음 | 해당 없음 | [지원됨][content-trust] |

<sup>1</sup> 지정된 저장소 제한은 각 계층에 대한 *포함된* 저장소의 양입니다. 이러한 한도 초과 이미지 저장소에 대해 GiB당 추가 일별 요금이 청구됩니다. 요금 정보는 [Container Registry 가격 책정][pricing]을 참조하세요.

<sup>2</sup> *ReadOps*, *WriteOps* 및 *Bandwidth*는 최소 추정치입니다. ACR은 사용량 요구에 따라 성능을 개선하려고 합니다.

<sup>3</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image)은 이미지의 레이어 수와 매니페스트 검색을 더한 값에 따라 여러 읽기 작업으로 변환됩니다.

<sup>4</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image)는 푸시해야 하는 레이어 수에 따라 여러 쓰기 작업으로 변환됩니다. `docker push`에는 기존 이미지에 대해 매니페스트를 검색할 *ReadOps*가 포함되어 있습니다.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md