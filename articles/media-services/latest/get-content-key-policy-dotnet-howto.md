---
title: Azure 미디어 서비스 v3.NET을 사용하는 정책에서 서명 키를 가져옵니다.
description: 이 항목에서는 Media Services v3 .NET SDK를 사용하여 기존 정책에서 서명 키를 가져오는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2dea262fadb61adc9e219b76f9ac048c11e650d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065973"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>기존 정책에서 서명 키 가져오기

v3 API의 핵심 디자인 원칙 중 하나는 API를 더 안전하게 만드는 것입니다. v3 API는 **Get** 또는 **List** 작업에서 비밀 또는 자격 증명을 반환하지 않습니다. 자세한 설명은 여기를 참조하세요: 자세한 내용은 [RBAC 및 미디어 서비스 계정을](rbac-overview.md) 참조하십시오.

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
