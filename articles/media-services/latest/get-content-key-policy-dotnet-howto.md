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
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 882f4650c0a3d558ee06c96658b779f9f0c76f76
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322486"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>기존 정책에서 서명 키 가져오기

v3 API의 핵심 디자인 원칙 중 하나는 API를 더 안전하게 만드는 것입니다. v3 API는 **가져오기** 또는 **나열** 작업에서 비밀 또는 자격 증명을 반환하지 않습니다. 키는 항상 null이거나, 비어 있거나, 응답에서 삭제됩니다. 비밀 또는 자격 증명을 가져오는 별도 작업 메서드를 호출해야 합니다. 다른 API와 달리 일부 API가 비밀을 검색/표시하는 경우에 별도 동작을 사용하면 다른 RBAC 보안 권한을 설정할 수 있습니다. RBAC를 사용하여 액세스를 관리하는 방법에 대한 정보는 [RBAC를 사용하여 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)를 참조하세요.

이 예제에는 다음이 포함됩니다. 

* StreamingLocator의 Get에 ContentKey 값을 반환하지 않음 
* ContentKeyPolicy의 Get에서 제한 키를 반환하지 않음 
* 작업 HTTP 입력 URL의 서명을 제거할 쿼리 문자열의 URL 부분을 반환하지 않음

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
