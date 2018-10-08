---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a777151216cb70b696da088b8a0d34779ebc39
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396130"
---
다음 표에서는 Azure Storage에 대한 기본 제한을 설명합니다. *수신* 제한은 저장소 계정에 보내는 모든 데이터(요청)를 나타냅니다. *송신* 제한은 저장소 계정에서 수신되는 모든 데이터(응답)를 가리킵니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 표준 및 프리미엄 계정 모두를 포함하는 구독당 지역당 저장소 계정 수 | 200 |
| 최대 저장소 계정 용량<sup>1</sup> | 500TiB |
| 저장소 계정당 Blob 컨테이너, Blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지의 최대 수 | 제한 없음 |
| 저장소 계정당 최대 요청 속도<sup>1</sup> | 초당 20,000회 요청 |
| 저장소 계정당<sup>1</sup> 최대 수신(미국 지역) | RA-GRS/GRS를 사용하는 경우 10Gbps, LRS/ZRS의 경우 20Gbps<sup>2</sup> |
| 저장소 계정당<sup>1</sup> 최대 송신(미국 지역) | RA-GRS/GRS를 사용하는 경우 20Gbps, LRS/ZRS의 경우 30Gbps |
| 저장소 계정당 최대 수신<sup>1</sup>(미국 이외 지역) | RA-GRS/GRS를 사용하는 경우 5Gbps, LRS/ZRS의 경우 10Gbps<sup>2</sup> |
| 저장소 계정당 최대 송신<sup>1</sup>(미국 이외 지역) | RA-GRS/GRS를 사용하는 경우 10Gbps, LRS/ZRS의 경우 15Gbps |

<sup>1</sup> Azure 저장소 계정은 요청에 따른 용량, 요청 속도, 수신 및 송신에 대해 더 높은 한도를 지원합니다. 향상된 제한에 대한 자세한 내용은 [큰, 더 높은 크기 조정 저장소 계정 도입](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)을 참조하세요. 계정 제한을 늘리려면 [Azure 지원](https://azure.microsoft.com/support/faq/)에 문의하세요.

<sup>2</sup>[Azure Storage 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 옵션은 다음을 포함합니다.
* **RA-GRS**: 읽기 액세스 지역 중복 저장소. RA-GRS를 사용하는 경우 보조 위치에 대한 송신 대상은 기본 위치에 대한 송신 대상과 동일합니다.
* **GRS**: 지역 중복 저장소 
* **ZRS**: 영역 중복 저장소.
* **LRS**: 로컬 중복 저장소 

