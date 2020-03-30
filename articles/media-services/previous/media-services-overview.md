---
title: Azure Media Services 개요 | Microsoft Docs
description: Microsoft Azure Media Services는 개발자가 확장 가능한 미디어 관리 및 배달 애플리케이션을 빌드할 수 있는 확장 가능한 클라우드 기반 플랫폼입니다. 이 문서에서는 Azure 미디어 서비스에 대한 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: juliako
ms.openlocfilehash: 1c2d6287a89c7816c30cf26978859c07dba0251d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197507"
---
# <a name="azure-media-services-overview"></a>Azure Media Services 개요 

> [!div class="op_single_selector" title1="사용 중인 미디어 서비스의 버전을 선택합니다."]
> * [버전 3](../latest/media-services-overview.md)
> * [버전 2](media-services-overview.md)

> [!NOTE]
> Media Services v2에는 새로운 기능이 추가되지 않습니다. <br/>최신 버전, [미디어 서비스 v3을](https://docs.microsoft.com/azure/media-services/latest/)확인하십시오. 또한 [v2에서 v3로의 마이그레이션 지침을](../latest/migrate-from-v2-to-v3.md) 참조하십시오.

Microsoft AMS(Azure Media Services)는 개발자가 확장 가능한 미디어 관리 및 배달 애플리케이션을 빌드할 수 있는 확장 가능한 클라우드 기반 플랫폼입니다. Media Services는 다양한 클라이언트(예: TV, PC 및 모바일 디바이스)로의 주문형 및 라이브 스트리밍 배달을 위해 비디오 또는 오디오 콘텐츠를 안전하게 업로드, 저장, 인코딩 및 패키지할 수 있는 REST API를 기반으로 합니다.

전체 Media Services를 사용하여 엔드투엔드 워크플로를 작성할 수 있습니다. 또한 워크플로의 일부에 타사 구성 요소를 사용하도록 선택할 수 있습니다. 예를 들어 타사 인코더를 사용하여 인코딩합니다. 그런 다음 Media Services를 사용하여 업로드, 보호, 패키징 및 배달합니다. 콘텐츠를 라이브로 스트리밍하고 주문형 콘텐츠를 배달하도록 선택할 수 있습니다. 


## <a name="compliance-privacy-and-security"></a>규정 준수, 개인 정보 보호 및 보안

중요한 알림으로, 귀하는 Azure Media Services 사용에 관한 모든 관련 법률을 준수해야 하며, 다른 사람의 권리를 침해하거나 타인에게 해로울 수 있는 방식으로 미디어 서비스 또는 Azure 서비스를 사용할 수 없습니다.

미디어 서비스에 비디오/이미지를 업로드하기 전에, 귀하는 미디어 서비스 및 Azure에서 자신의 데이터를 사용, 처리 및 저장하기 위해 법에서 요구하는 경우 비디오/이미지의 모든 필요한 동의(있는 경우)를 포함하여 비디오/이미지를 사용할 수 있는 모든 적절한 권리를 가져야 합니다. 일부 관할권에서는 생체 인식 데이터와 같은 특정 범주의 데이터를 수집, 온라인 처리 및 저장하기 위한 특별한 법적 요구 사항을 부과할 수 있습니다. Media Services 및 Azure를 사용하여 특별한 법적 요구 사항이 적용되는 모든 데이터의 처리 및 저장을 위해 사용하기 전에 귀하는 귀하에게 적용될 수 있는 그러한 법적 요구 사항을 준수해야 합니다.

미디어 서비스의 규정 준수, 개인 정보 보호 및 보안에 대해 알아보려면 Microsoft [트러스트 센터를](https://www.microsoft.com/trust-center/?rtc=1)방문하십시오. 데이터를 삭제하는 방법을 포함하여 Microsoft의 개인 정보 보호 의무, 데이터 처리 및 보존 관행에 대해서는 Microsoft의 [개인 정보 보호 정책에](https://privacy.microsoft.com/PrivacyStatement)따라 온라인 서비스 약관("OST") 및 [데이터 처리 부록("DPA")을](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) 검토하십시오. [Online Services Terms](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) 미디어 서비스를 이용함으로써 귀하는 OST, DPA 및 개인정보 취급방침에 구속되는 데 동의합니다.
 
## <a name="prerequisites"></a>사전 요구 사항

Azure Media Services 사용을 시작하려면 다음이 있어야 합니다.

* Azure 계정. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판을](https://azure.microsoft.com)참조하십시오.
* Azure Media Services 계정. 자세한 내용은 [계정 만들기](media-services-portal-create-account.md)를 참조하세요.
* (선택 사항) 개발 환경 설정. 개발 환경에 .NET 또는 REST API를 선택합니다. 자세한 내용은 [환경 설정](media-services-dotnet-how-to-use.md)을 참조하세요.

    [프로그래밍 방식으로 AMS API에 연결](media-services-use-aad-auth-to-access-ams-api.md)하는 방법도 알아봅니다.
* 시작된 상태에 있는 표준 또는 프리미엄 스트리밍 엔드포인트.  자세한 내용은 [스트리밍 엔드포인트 관리를](media-services-portal-manage-streaming-endpoints.md) 참조하세요.

## <a name="sdks-and-tools"></a>SDK 및 도구

Media Services 솔루션을 빌드하려면 다음을 사용할 수 있습니다.

* [Media Services REST API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* 사용 가능한 클라이언트 SDK 중 하나:
    * .NET용 Azure 미디어 서비스 SDK
    
        * [NuGet 패키지](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [GitHub 소스 코드](https://github.com/Azure/azure-sdk-for-media-services)
    * [Java용 Azure SDK](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Node.js용 Azure Media Services](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Node.js SDK의 Microsoft가 아닌 타사 버전입니다. 커뮤니티에서 유지 관리하고 현재 AMS API를 100% 포함하지 않습니다).
* 기존 도구:
    * [Azure 포털](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE(Azure Media Services 탐색기)는 Windows용 Winforms/C# 애플리케이션임)

> [!NOTE]
> 최신 버전의 Java SDK를 가져와서 Java를 사용하여 개발을 시작하려면 [Media Services용 Java 클라이언트 SDK 시작](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)을 참조하세요. <br/>
> Media Services용 최신 PHP SDK를 다운로드하려면 [Packagist 리포지토리](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)에서 Microsoft/WindowAzure 패키지 버전 0.5.7을 찾습니다.  

## <a name="code-samples"></a>코드 샘플

**Azure 코드 샘플** 갤러리: [Azure Media Services 코드 샘플](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0)에서 여러 코드 샘플을 찾습니다.

## <a name="concepts"></a>개념

Azure Media Services 개념은 [개념](media-services-concepts.md)을 참조하세요.

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>지원되는 시나리오 및 데이터 센터에서 Media Services의 사용 가용성

자세한 내용은 [AMS 시나리오 및 데이터 센터에서 기능 및 서비스의 사용 가용성](scenarios-and-availability.md)을 참조하세요.

## <a name="service-level-agreement-sla"></a>SLA(서비스 수준 계약)

자세한 내용은 [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)를 참조하세요.

데이터 센터에서 사용 가용성에 대한 정보는 [사용 가능성](scenarios-and-availability.md#availability) 섹션을 참조하세요.

## <a name="support"></a>고객 지원팀

[Azure 지원](https://azure.microsoft.com/support/options/) 에서는 Media Services를 포함하여 Azure에 대한 지원 옵션을 제공합니다.

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
