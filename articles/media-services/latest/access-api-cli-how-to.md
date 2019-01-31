---
title: Azure Media Services API 액세스 - Azure CLI | Microsoft Docs
description: Azure Media Services API에 액세스하려면 이 방법의 단계를 따르도록 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 43f9443e4b5cd700500bd9803f2737ed9e0aa633
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223167"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Azure CLI를 사용하여 Azure Media Services API 액세스
 
Azure Media 서비스 API에 연결하려면 Azure AD 서비스 주체 인증을 사용해야 합니다. 애플리케이션이 다음 매개 변수가 있는 Azure AD 토큰을 요청해야 합니다.

* Azure AD 테넌트 엔드포인트
* Media Services 리소스 URI
* REST Media Services의 리소스 URI
* Azure AD 애플리케이션 값: 클라이언트 ID 및 클라이언트 암호.

이 문서에서는 Azure CLI를 사용하여 Azure AD 애플리케이션과 서비스 주체를 만들고 Azure Media Services 리소스에 액세스하는 데 필요한 값을 얻는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건 

[Media Services 계정 만들기](create-account-cli-how-to.md)

리소스 그룹 이름 및 Media Services 계정 이름에 사용한 값을 기억해 두세요.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>참고 항목

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
