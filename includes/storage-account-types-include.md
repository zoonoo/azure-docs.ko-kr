---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 14de5283197778464993b80b312a690662e066ab
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443768"
---
Azure Storage는 여러 유형의 저장소 계정이 제공 합니다. 각각의 유형은 서로 다른 기능을 지원하며 고유의 가격 책정 모델이 있습니다. 저장소 계정을 만들기 전에 애플리케이션에 적합한 계정 유형을 결정하는 데 이러한 차이점을 고려합니다. 저장소 계정 유형은 다음과 같습니다.

- **범용 v2 계정**: Blob, 파일, 큐 및 테이블에 대한 기본 스토리지 계정 유형입니다. 대부분의 시나리오에 대해 Azure Storage를 사용하는 것이 좋습니다.
- **범용 v1 계정**: Blob, 파일, 큐 및 테이블에 대한 레거시 계정 유형입니다. 가능한 경우 범용 v2 계정을 대신 사용합니다.
- **Blob storage 계정은 블록**: Premium 성능 특징을 가진 blob 전용 저장소 계정입니다. 높은 트랜잭션 속도 일관 되 게 낮은 저장소 대기 시간을 요구 하거나 더 작은 개체를 사용 하 여 시나리오에 대 한 것이 좋습니다.
- **저장소 계정 FileStorage**: Premium 성능 특징을 가진 파일만 저장소 계정입니다. Enterprise 또는 고성능 규모의 응용 프로그램에 대 한 것이 좋습니다.
- **Blob Storage 계정**: Blob 전용 스토리지 계정입니다. 가능한 경우 범용 v2 계정을 대신 사용합니다.

다음 표는 저장소 계정 유형과 해당 기능을 설명합니다.

| Storage 계정 유형 | 지원되는 서비스                       | 지원되는 성능 계층      | 지원되는 액세스 계층         | 복제 옵션               | 배포 모델<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | 암호화<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| 범용 V2   | Blob, 파일, 큐, 테이블 및 디스크       | 표준, 프리미엄<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | 핫, 쿨, 보관<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | 암호화              |
| 범용 V1   | Blob, 파일, 큐, 테이블 및 디스크       | 표준, 프리미엄<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/A                            | LRS, GRS, RA-GRS                  | Resource Manager, 클래식    | 암호화              |
| 블록 blob storage   | Blob(블록 Blob 및 추가 Blob만) | Premium                       | N/A                            | LRS                               | Resource Manager             | 암호화              |
| FileStorage   | 파일만 | Premium                       | N/A                            | LRS                               | Resource Manager             | 암호화              |
| Blob 저장소         | Blob(블록 Blob 및 추가 Blob만) | Standard                      | 핫, 쿨, 보관<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | 암호화              |

<div id="deployment-model"><sup>1</sup>Azure Resource Manager 배포 모델을 사용하는 것이 좋습니다. 클래식 배포 모델을 사용하는 저장소 계정도 일부 위치에서는 계속 만들 수 있고 기존 클래식 계정도 계속 지원됩니다. 자세한 내용은 <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager 및 클래식 배포: 배포 모델 및 리소스 상태 이해</a>를 참조하세요.</div>

<div id="encryption"><sup>2</sup>모든 저장소 계정은 미사용 데이터에 대해 SSE(저장소 서비스 암호화)를 사용하여 암호화됩니다. 자세한 내용은 <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">미사용 데이터에 대한 Azure Storage 서비스 암호화</a>를 참조하세요.</div>

<div id="archive"><sup>3</sup>보관 계층은 저장소 계정 수준이 아니라 개별 Blob 수준에서만 사용할 수 있습니다. 블록 Blob 및 추가 Blob만 보관할 수 있습니다. 자세한 내용은 <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층</a>을 참조하세요.</div>

<div id="zone-redundant-storage"><sup>4</sup>ZRS(영역 중복 저장소)는 표준 범용 V2 저장소 계정에만 사용할 수 있습니다. ZRS에 대한 자세한 내용은 <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">ZRS(영역 중복 스토리지): 고가용성 Azure Storage 애플리케이션</a>을 참조하세요. 다른 복제 옵션에 대한 자세한 내용은 <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage 복제</a>를 참조하세요.</div>

<div id="premium-performance"><sup>5</sup>범용 v2 및 범용 v1 계정에 대 한 프리미엄 성능은 디스크 및 페이지 blob에만 사용할 수 있습니다.</div>
