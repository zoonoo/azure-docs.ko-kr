---
title: 자산
titleSuffix: Azure Media Services
description: Azure Media 서비스에서 자산이 무엇인지, 어떻게 사용되는지 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087912"
---
# <a name="assets-in-azure-media-services-v3"></a>Azure 미디어 서비스의 자산 v3

Azure 미디어 서비스에서 [자산은](https://docs.microsoft.com/rest/api/media/assets) 핵심 개념입니다. 여기서 는 업로드 또는 라이브 인제스트를 통해 미디어를 입력하고, 출력 미디어(작업 출력에서) 및(스트리밍용) 미디어를 게시합니다. 

자산은 [Azure Storage 계정의](storage-account-concept.md) Blob 컨테이너에 매핑되고 자산의 파일은 해당 컨테이너의 블록 Blob으로 저장됩니다. 자산에는 Azure Storage에 저장된 디지털 파일(비디오, 오디오, 이미지, 썸네일 컬렉션, 텍스트 트랙 및 닫힌 캡션 파일 포함)에 대한 정보가 포함되어 있습니다.

Media Services는 계정이 범용 v2(GPv2) 스토리지를 사용할 때 Blob 계층을 지원합니다. GPv2를 사용하는 경우 파일을 [쿨 또는 보관 스토리지](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)로 이동할 수 있습니다. **아카이브** 스토리지는 더 이상 필요하지 않은 경우(예: 인코딩된 후) 소스 파일을 보관하는 데 적합합니다.

**보관** 스토리지 계층은 이미 인코딩되었고 인코딩 작업 출력이 출력 Blob 컨테이너에 배치된 대용량 원본 파일에만 사용할 것을 권장합니다. 에셋과 연결하고 콘텐츠를 스트리밍하거나 분석하는 데 사용할 출력 컨테이너의 Blob은 **핫** 또는 **쿨** 저장소 계층에 있어야 합니다.

## <a name="naming"></a>이름 지정 

### <a name="assets"></a>자산

자산의 이름은 고유해야 합니다. 미디어 서비스 v3 리소스 이름(예: 자산, 작업, 변환)에는 Azure 리소스 관리자 명명 제약 조건이 적용됩니다. 자세한 내용은 [명명 규칙을](media-services-apis-overview.md#naming-conventions)참조하십시오.

### <a name="blobs"></a>Blob

자산 내의 파일/Blob 이름은 [Blob 이름 요구 사항과](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) [NTFS 이름 요구 사항을](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)모두 따라야 합니다. 이러한 요구 사항에 대 한 이유는 파일을 처리할 수 있는 로컬 NTFS 디스크에 Blob 저장소에서 복사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[미디어 서비스에서 자산 관리](manage-asset-concept.md)

## <a name="see-also"></a>참조

[Media Services v2와 v3의 차이점](migrate-from-v2-to-v3.md)
