---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334959"
---
다음 표에서는 Azure 범용 v1, v2, Blob 저장소, 블록 Blob 저장소 및 Data Lake Storage Gen2 사용 가능한 저장소 계정에 대한 기본 제한에 대해 설명합니다. *받는 사람* 제한은 저장소 계정으로 전송되는 모든 데이터를 나타냅니다. *송신* 제한은 저장소 계정에서 수신되는 모든 데이터를 나타냅니다.

| 리소스 | 제한 |
| --- | --- |
| 표준, 프리미엄 및 Data Lake Storage Gen2 지원 스토리지 계정을 포함하여 구독당 지역별 저장소 계정 수입니다. <sup>3개</sup> | 250 |
| 최대 스토리지 계정 용량 | 5 PiB <sup>1</sup>|
| 저장소 계정당 최대 Blob 컨테이너, Blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지 수 | 제한 없음 |
| 스토리지 계정당 최대 요청 속도<sup>1</sup> | 초당 20,000회 요청 |
| 저장소 계정당<sup>1</sup> 최대 침투 1(미국, 유럽 지역) | 25Gbps |
| 저장소 계정당 최대 침투<sup>1(미국</sup> 및 유럽 이외의 지역) | RA-GRS/GRS를 사용하는 경우 5Gbps, LRS/ZRS<sup>2의</sup> 경우 10Gbps |
| 범용 v2 및 Blob 저장소 계정(모든 지역)에 대한 최대 송신 | 50Gbps |
| 범용 v1 저장소 계정(미국 지역)에 대한 최대 송신 | RA-GRS/GRS를 사용하는 경우 20Gbps, LRS/ZRS<sup>2의</sup> 경우 30Gbps |
| 범용 v1 저장소 계정(미국 이외의 지역)에 대한 최대 송신 | RA-GRS/GRS를 사용하는 경우 10Gbps, LRS/ZRS<sup>2의</sup> 경우 15Gbps |
| 저장소 계정당 최대 가상 네트워크 규칙 수 | 200 |
| 저장소 계정당 최대 IP 주소 규칙 수 | 200 |

<sup>1</sup> Azure Storage 표준 계정은 요청에 따라 더 높은 용량 제한 및 더 높은 한도를 지원합니다. 계정 제한을 늘리려면 [Azure 지원](https://azure.microsoft.com/support/faq/)에 문의하세요.

<sup>2</sup> 저장소 계정에 지리적 중복 저장소(RA-GRS) 또는 지리적 영역 중복 저장소(RA-GZRS)를 사용하여 읽기 액세스가 활성화된 경우 보조 위치의 송신 대상은 기본 위치의 대상과 동일합니다. [Azure 저장소 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 옵션은 다음과 같습니다.

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure 데이터 레이크 스토리지 Gen2는](../articles/storage/blobs/data-lake-storage-introduction.md) Azure Blob 저장소를 기반으로 구축된 빅 데이터 분석 전용 기능 집합입니다. Azure 저장소 및 Blob 저장소 제한은 데이터 레이크 저장소 Gen2에 적용됩니다.

> [!NOTE]
> 대부분의 시나리오에 대 한 범용 v2 저장소 계정을 사용 하는 것이 좋습니다. 범용 v1 또는 Azure Blob 저장소 계정을 가동 중지 시간 없이 데이터를 복사할 필요 없이 범용 v2 계정으로 쉽게 업그레이드할 수 있습니다. 자세한 내용은 [범용 v2 저장소 계정으로 업그레이드를](../articles/storage/common/storage-account-upgrade.md)참조하십시오.

애플리케이션의 요구가 단일 스토리지 계정의 확장성 목표를 초과하는 경우 여러 스토리지 계정을 사용하도록 애플리케이션을 빌드하면 됩니다. 그런 다음 해당 스토리지 계정 간에 데이터 개체를 분할할 수 있습니다. 볼륨 가격 책정에 대한 자세한 내용은 [Azure 저장소 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하십시오.

모든 저장소 계정은 생성된 시기에 관계없이 플랫 네트워크 토폴로지에서 실행됩니다. Azure Storage 플랫 네트워크 아키텍처 및 확장성에 대한 자세한 내용은 [Microsoft Azure Storage: 일관성과 가용성이 뛰어난 클라우드 Storage 서비스](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets)를 참조하세요. 
