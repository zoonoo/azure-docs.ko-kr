---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e4fa42b6c32c3eb383eea4489ea109c0d496bdb9
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392681"
---
다음 표에서는 Azure Storage에 대한 기본 제한을 설명합니다. *수신* 제한은 저장소 계정에 보내는 모든 데이터(요청)를 나타냅니다. *송신* 제한은 저장소 계정에서 수신되는 모든 데이터(응답)를 가리킵니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 표준 및 프리미엄 계정 모두를 포함하는 구독당 지역당 저장소 계정 수 | 250 |
| 최대 저장소 계정 용량 | 미국 및 유럽용 2 PB, 영국을 비롯한 다른 모든 지역용 500TB |
| 저장소 계정당 Blob 컨테이너, Blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지의 최대 수 | 제한 없음 |
| 저장소 계정당 최대 요청 속도<sup>1</sup> | 초당 20,000회 요청 |
| 저장소 계정당<sup>1</sup> 최대 수신(미국 지역) | RA-GRS/GRS를 사용하는 경우 10Gbps, LRS/ZRS의 경우 20Gbps<sup>2</sup> |
| 저장소 계정당 최대 수신<sup>1</sup>(미국 이외 지역) | RA-GRS/GRS를 사용하는 경우 5Gbps, LRS/ZRS의 경우 10Gbps<sup>2</sup> |
| 범용 v2 및 Blob Storage 계정에 대한 최대 송신(모든 지역) | 50Gbps |
| 범용 v1 스토리지 계정에 대한 최대 송신(미국 지역) | RA-GRS/GRS를 사용하는 경우 20Gbps, LRS/ZRS의 경우 30Gbps<sup>2</sup> |
| 범용 v1 스토리지 계정에 대한 최대 송신(미국 외 지역) | RA-GRS/GRS를 사용하는 경우 10Gbps, LRS/ZRS의 경우 15Gbps<sup>2</sup> |

<sup>1</sup> Azure 표준 Storage 계정은 요청에 따른 수신에 대해 더 높은 한도를 지원합니다. 수신에 대한 계정 제한을 늘리도록 요청하려면 [Azure 고객 지원팀](https://azure.microsoft.com/support/faq/)에 문의하세요.

<sup>2</sup>[Azure Storage 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 옵션은 다음을 포함합니다.
* **RA-GRS**: 읽기 액세스 지역 중복 저장소입니다. RA-GRS를 사용하는 경우 보조 위치에 대한 송신 대상은 기본 위치에 대한 송신 대상과 동일합니다.
* **GRS**: 지역 중복 저장소입니다. 
* **ZRS**: 영역 중복 저장소입니다.
* **LRS**: 로컬 중복 저장소입니다. 

> [!NOTE]
> 대부분의 시나리오에서 범용 v2 저장소 계정을 사용하는 것이 좋습니다. 가동 중지 시간이 없고 데이터를 복사할 필요 없이 범용 v1 또는 Blob Storage 계정을 범용 v2 계정으로 쉽게 업그레이드할 수 있습니다.
>
> Azure Storage 계정에 대한 자세한 내용은 [Storage 계정 개요](../articles/storage/common/storage-account-overview.md)를 참조하세요. 

애플리케이션의 요구가 단일 스토리지 계정의 확장성 목표를 초과하는 경우 여러 스토리지 계정을 사용하도록 애플리케이션을 빌드하면 됩니다. 그런 다음 해당 저장소 계정 간에 데이터 개체를 분할할 수 있습니다. 볼륨 가격에 대한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 을 참조하세요.

모든 스토리지 계정은 플랫 네트워크 토폴로지에서 실행되고 작성된 시기에 관계 없이 이 문서에 설명된 확장성 및 성능 목표를 지원합니다. Azure Storage 플랫 네트워크 아키텍처 및 확장성에 대한 자세한 내용은 [Microsoft Azure Storage: 강력한 일관성과 함께 항상 사용 가능한 클라우드 스토리지 서비스](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) 문서를 참조하세요.

