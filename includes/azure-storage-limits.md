---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d5ce4c094da3a411168c7fe4c282b15ceac7bb86
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70036745"
---
다음 표에서는 Azure 범용 v1, v2 및 Blob storage 계정에 대 한 기본 제한을 설명 합니다. *수신* 제한은 저장소 계정으로 전송 되는 요청의 모든 데이터를 나타냅니다. *송신* 한도는 저장소 계정에서 받은 응답의 모든 데이터를 나타냅니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 표준 및 프리미엄 계정 모두를 포함하는 구독당 지역당 스토리지 계정 수 | 250 |
| 최대 저장소 계정 용량 | 미국 및 유럽 2 PB 및 기타 모든 지역 (영국<sup>포함)에</sup> 대해 500 TB|
| 저장소 계정 당 blob 컨테이너, blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지의 최대 수 | 제한 없음 |
| 스토리지 계정당 최대 요청 속도<sup>1</sup> | 초당 20,000회 요청 |
| 저장소 계정 당 최대 수신<sup>1</sup> (미국, 유럽 지역) | 25gbps |
| 저장소 계정 당 최대 수신<sup>1</sup> (미국과 유럽 이외의 지역) | GRS/GRS를 사용 하는 경우 5gbps, LRS/ZRS<sup>2</sup> 의 경우 10gbps |
| 범용 v2 및 Blob 저장소 계정에 대 한 최대 송신 (모든 지역) | 50Gbps |
| 범용 v1 저장소 계정에 대 한 최대 송신 (미국 지역) | GRS/GRS를 사용 하는 경우 20gbps, LRS/ZRS<sup>2</sup> 의 경우 30gbps |
| 범용 v1 저장소 계정에 대 한 최대 송신 (미국 외 지역) | GRS/GRS를 사용 하는 경우 10gbps, LRS/ZRS<sup>2</sup> 의 경우 15gbps |

<sup>1</sup> Azure Standard Storage 계정은 요청에의 한 수신에 대해 더 높은 용량 제한과 더 높은 제한을 지원 합니다. 수신에 대한 계정 제한을 늘리도록 요청하려면 [Azure 고객 지원팀](https://azure.microsoft.com/support/faq/)에 문의하세요. 자세한 내용은 더 [큰 규모의 저장소 계정 발표](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)를 참조 하세요.

<sup>2</sup>[Azure Storage 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 옵션은 다음을 포함합니다.

- **RA-GRS**: 읽기 액세스 지역 중복 스토리지입니다. RA-GRS를 사용하는 경우 보조 위치에 대한 송신 대상은 기본 위치에 대한 송신 대상과 동일합니다.
- **GRS**: 지역 중복 스토리지입니다.
- **ZRS**: 영역 중복 스토리지입니다.
- **LRS**: 로컬 중복 스토리지입니다.

> [!NOTE]
> 대부분의 시나리오에서는 범용 v2 저장소 계정을 사용 하는 것이 좋습니다. 가동 중지 시간 없이 데이터를 복사할 필요 없이 일반적인 용도의 v1 또는 Azure Blob storage 계정을 범용 v2 계정으로 쉽게 업그레이드할 수 있습니다.
>
> Azure Storage 계정에 대 한 자세한 내용은 [저장소 계정 개요](../articles/storage/common/storage-account-overview.md)를 참조 하세요.

애플리케이션의 요구가 단일 스토리지 계정의 확장성 목표를 초과하는 경우 여러 스토리지 계정을 사용하도록 애플리케이션을 빌드하면 됩니다. 그런 다음 해당 스토리지 계정 간에 데이터 개체를 분할할 수 있습니다. 볼륨 가격 책정에 대 한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조 하세요.

모든 스토리지 계정은 플랫 네트워크 토폴로지에서 실행되고 작성된 시기에 관계 없이 이 문서에 설명된 확장성 및 성능 목표를 지원합니다. Azure Storage 플랫 네트워크 아키텍처 및 확장성에 대한 자세한 내용은 [Microsoft Azure Storage: 강력한 일관성과 함께 항상 사용 가능한 클라우드 스토리지 서비스](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) 문서를 참조하세요.

