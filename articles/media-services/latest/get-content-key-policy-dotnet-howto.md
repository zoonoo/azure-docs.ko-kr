---
title: Media Services v3 .NET SDK를 사용하여 기존 정책에서 서명 키 가져오기 - Azure | Microsoft Docs
description: 이 항목에서는 Media Services v3 .NET SDK를 사용하여 기존 정책에서 서명 키를 가져오는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 58b6f49f4bbbd93fefb9b616f92baf7ef30f7deb
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615832"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>기존 정책에서 서명 키 가져오기

v3 API의 핵심 디자인 원칙 중 하나는 API를 더 안전하게 만드는 것입니다. v3 Api 반환 하지 않는 암호 또는 자격 증명에 **가져옵니다** 하거나 **목록** 작업 합니다. 자세한 설명은 다음을 참조 하세요. 자세한 내용은 참조 하세요. [RBAC 및 Media Services 계정](rbac-overview.md)

이 문서의 예제에서는 .NET을 사용하여 기존 정책에서 서명 키를 가져오는 방법을 보여 줍니다. 
 
## <a name="download"></a>다운로드 

다음 명령을 사용하여 전체 .NET 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
비밀이 있는 ContentKeyPolicy 예제는 [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) 폴더에 있습니다.

## <a name="get-contentkeypolicy-with-secrets"></a>비밀이 있는 ContentKeyPolicy 가져오기 

키로 이동하려면 아래 예제와 같이 **GetPolicyPropertiesWithSecretsAsync**를 사용합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>다음 단계

[액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md) 
