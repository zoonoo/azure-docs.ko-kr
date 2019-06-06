---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b75e24e5d59206ee8330c3ca9eaf86eacbad13d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114778"
---
### <a name="premium-performance-block-blob-storage"></a>Premium 성능 블록 blob storage

프리미엄 성능 블록 blob 저장소 계정은 더 작고 킬로바이트 범위 개체를 사용 하는 응용 프로그램에 대해 최적화 됩니다. 높은 트랜잭션 속도 또는 일관 된 대기 시간이 짧은 저장소를 필요로 하는 응용 프로그램에 이상적입니다. Premium 성능 블록 blob storage는 응용 프로그램을 사용 하 여 크기를 조정 하도록 설계 되었습니다. 초당 요청 수십만 또는 페타바이트의 저장소 용량을 필요로 하는 응용 프로그램을 배포 하려는 경우 문의 하시기에서 지원 요청을 제출 합니다 [Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-filestorage-preview"></a>Premium 성능 FileStorage (미리 보기)

프리미엄 파일 라는 고유한 저장소 계정 사용 **FileStorage (미리 보기)** 합니다. 이 계정 유형은 일관 된 짧은 대기 시간을 사용 하 여 높은 처리량, 높은 IOPS 사용 하 여 워크 로드를 위한 것입니다. 프리미엄 파일 저장소 공유를 프로 비전 된 크기를 사용 하 여 확장합니다.

|영역  |대상  |
|---------|---------|
|최대 크기를 프로 비전     |100tib     |
|공유   |Unlimited  |
|IOPS     |100,000    |
|수신|4,136 초     |
|송신|6,204 초 |

 내용은 premium 파일에 대 한 크기 조정 목표를 공유 합니다 [Premium 파일 확장 대상](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets) 섹션입니다.

### <a name="premium-performance-page-blob-storage"></a>Premium 성능 페이지 blob storage

프리미엄 성능, 범용 v1 또는 v2 저장소 계정에는 다음과 같은 확장성 목표가 있습니다.

| 총 계정 용량                            | 로컬 중복 저장소 계정의 총 대역폭                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| 디스크 용량: 35TB <br>스냅샷 용량: 10TB | 인바운드<sup>1</sup> + 아웃바운드<sup>2</sup>에 대해 초당 최대 50기가비트 |

<sup>1</sup> 저장소 계정으로 전송되는 모든 데이터(요청)

<sup>2</sup> 저장소 계정에서 수신하는 모든 데이터(응답)

관리 되지 않는 디스크에 대 한 프리미엄 성능 저장소 계정을 사용 하는 경우 응용 프로그램이 단일 저장소 계정의 확장성 목표를 초과 하면 managed disks로 마이그레이션하는 것이 좋습니다. 관리 디스크로 마이그레이션하지 않으려면 애플리케이션이 다수의 저장소 계정을 사용하도록 빌드합니다. 그런 다음 이 저장소 계정 간에 데이터를 분할합니다. 예를 들어 51TB 디스크를 여러 VM에 연결하려는 경우에는 두 개의 저장소 계정에 분산합니다. 단일 Premium Storage 계정의 한도는 35TB입니다. 단일 프리미엄 성능 저장소 계정에 35TB 이상의 프로 비전 된 디스크는 있는지 확인 합니다.