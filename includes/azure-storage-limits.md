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
ms.openlocfilehash: 2957b45a0a26c590e2db1b402f4956ae9c3b8000
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59291784"
---
다음 표에서는 Azure Storage에 대한 기본 제한을 설명합니다. 합니다 *수신* 제한은 저장소 계정에 전송 되는 요청에서 모든 데이터를 가리킵니다. 합니다 *송신* 제한은 저장소 계정에서 수신 되는 응답에서 모든 데이터를 가리킵니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 표준 및 프리미엄 계정 모두를 포함하는 구독당 지역당 저장소 계정 수 | 250 |
| 최대 저장소 계정 용량 | 2 PB 미국 및 유럽, 영국을 포함 하는 다른 모든 지역의 경우 500 TB |
| Blob 컨테이너, blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지 저장소 계정당 최대 수 | 제한 없음 |
| FileStorage (미리 보기) 저장소 계정의 최대 크기 | 100,000 giB |
| FileStorage (미리 보기) 저장소 계정에 대 한 최대 IOPS | 100,000 |
| FileStorage (미리 보기) 저장소 계정에 대 한 최대 대역폭 | 5 GB/s |
| 저장소 계정당 최대 요청 속도<sup>1</sup> | 초당 20,000회 요청 |
| 최대 수신<sup>1</sup> 저장소 계정당 (미국 지역) | RA-GRS/GRS를 사용 하는 경우 10gbps, LRS/ZRS의 경우 20gbps<sup>2</sup> |
| 최대 수신<sup>1</sup> 저장소 계정당 (미국 이외 지역) | RA-GRS/GRS를 사용 하는 경우 5gbps, LRS/ZRS의 경우 10gbps<sup>2</sup> |
| 최대 수신 (미리 보기) FileStorage 저장소 계정당 (미국 지역) | 20gbps (LRS에만 해당) |
| 최대 수신 (미리 보기) FileStorage 저장소 계정당 (미국 이외 지역) | 10gbps (LRS에만 해당)|
| 범용 v2, FileStorage (미리 보기) 및 Blob storage에 대 한 최대 송신 (모든 영역) 계정 | 50Gbps |
| 범용 v1 storage 계정 (미국 지역)에 대 한 최대 송신 | RA-GRS/GRS를 사용 하는 경우 20gbps, LRS/ZRS의 경우 30gbps<sup>2</sup> |
| 범용 v1 storage 계정 (미국 이외 지역)에 대 한 최대 송신 | RA-GRS/GRS를 사용 하는 경우 10gbps, LRS/ZRS 경우 15gbps<sup>2</sup> |

<sup>1</sup>azure Standard Storage 계정 수신에 대 한 요청에 따라 더 높은 한도 지원 합니다. 수신에 대한 계정 제한을 늘리도록 요청하려면 [Azure 고객 지원팀](https://azure.microsoft.com/support/faq/)에 문의하세요.

<sup>2</sup>[Azure Storage 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 옵션은 다음을 포함합니다.
* **RA-GRS**: 읽기 액세스 지역 중복 저장소입니다. RA-GRS를 사용하는 경우 보조 위치에 대한 송신 대상은 기본 위치에 대한 송신 대상과 동일합니다.
* **GRS**: 지역 중복 저장소입니다. 
* **ZRS**: 영역 중복 저장소입니다.
* **LRS**: 로컬 중복 저장소입니다. 

> [!NOTE]
> 대부분의 시나리오에 대 한 범용 v2 저장소 계정을 사용 하는 것이 좋습니다. 범용 v1 또는 Azure Blob storage 계정 데이터를 복사할 필요가 없으며 가동 중지 시간 없이 범용 v2 계정으로 쉽게 업그레이드할 수 있습니다.
>
> Azure Storage 계정에 대 한 자세한 내용은 참조 하세요. [저장소 계정 개요](../articles/storage/common/storage-account-overview.md)합니다. 

애플리케이션의 요구가 단일 스토리지 계정의 확장성 목표를 초과하는 경우 여러 스토리지 계정을 사용하도록 애플리케이션을 빌드하면 됩니다. 그런 다음 해당 저장소 계정 간에 데이터 개체를 분할할 수 있습니다. 볼륨 가격에 대 한 내용은 참조 하세요 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)합니다.

모든 스토리지 계정은 플랫 네트워크 토폴로지에서 실행되고 작성된 시기에 관계 없이 이 문서에 설명된 확장성 및 성능 목표를 지원합니다. Azure Storage 플랫 네트워크 아키텍처 및 확장성에 대한 자세한 내용은 [Microsoft Azure Storage: 강력한 일관성과 함께 항상 사용 가능한 클라우드 스토리지 서비스](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) 문서를 참조하세요.

