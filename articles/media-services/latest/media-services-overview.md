---
title: Azure Media Services v3 개요 | Microsoft Docs
description: 이 문서에서는 Media Services에 대한 높은 수준의 개요를 제공하고 자세한 설명을 위해 문서에 대한 링크를 제공합니다.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: Azure Media Services, 스트림, 브로드캐스트, 라이브, 오프라인
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 06/14/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 5205a6746f6a698768a60375e2e77db9cb535a71
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971910"
---
# <a name="what-is-azure-media-services-v3"></a>Azure Media Services v3란?

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [버전 2-GA](../previous/media-services-overview.md)
> * [버전 3-미리 보기](media-services-overview.md)

> [!NOTE]
> Azure Media Services의 최신 버전은 미리보기에 있으며 v3라고도 합니다.

Azure Media Services는 브로드캐스트 품질의 비디오 스트리밍을 구현하고, 접근성과 배포를 향상시키며, 콘텐츠를 분석하는 등 여러 작업을 수행하는 솔루션을 빌드할 수 있게 하는 클라우드 기반 플랫폼입니다. 응용 프로그램 개발자, 콜센터, 정부 기관, 엔터테인먼트 회사이든 Media Services는 현재 가장 인기있는 모바일 장치 및 브라우저를 통해 뛰어난 품질의 미디어 경험을 많은 사용자에게 제공하는 응용 프로그램을 만들 수 있도록 도와줍니다. 

## <a name="what-can-i-do-with-media-services"></a>Media Services로 무엇을 할 수 있나요?

Media Services를 사용하면 클라우드에서 다양한 미디어 워크플로를 빌드할 수 있습니다. 다음은 Media Services로 수행할 수 있는 몇 가지 예입니다.  

* 다양한 브라우저 및 장치에서 재생할 수 있도록 다양한 형식의 비디오를 제공합니다. 다양한 클라이언트(모바일 장치, TV, PC 등)에 주문형 및 라이브 모두의 스트리밍 전달인 경우 비디오 및 오디오 콘텐츠를 적절하게 인코딩하고 패키징해야 합니다. 이러한 콘텐츠를 전달하고 스트리밍하는 방법을 보려면 [빠른 시작: 파일 인코딩 및 스트리밍](stream-files-dotnet-quickstart.md)을 참조하세요.
* 축구, 야구, 대학 및 고교 스포츠 등과 같은 대규모 온라인 시청자에게 라이브 스포츠 이벤트를 스트리밍합니다. 
* 마을 회관, 시의회 회의 및 입법 기관과 같은 공공 회의 및 행사를 방송합니다.
* 녹화된 비디오 또는 오디오 컨텐츠를 분석합니다. 예를 들어, 고객 만족도를 높이기 위해 조직은 음성-텍스트를 추출하고 검색 인덱스 및 대시 보드를 빌드할 수 있습니다. 그런 다음 일반적인 불만, 불만의 원인 및 기타 관련 데이터 인텔리전스를 추출할 수 있습니다. 
* 고객(예: 영화 스튜디오)이 독점 저작물의 액세스 및 사용을 제한해야 하는 경우 구독 비디오 서비스를 만들고 DRM 보호된 콘텐츠를 스트리밍합니다.
* 항공기, 기차 및 자동차에서 재생할 오프라인 콘텐츠를 제공합니다. 고객은 네트워크에서 연결이 끊길 것으로 예상되는 경우 재생을 위해 휴대 전화나 태블릿에 콘텐츠를 다운로드해야 할 수 있습니다.
* 더 많은 청중(예: 청각 장애인 또는 다른 언어로 따라 읽기를 원하는 사람들)을 수용하기 위해 자막 및 캡션을 비디오에 추가합니다. 
* Azure Media Services 및 [Azure Cognitive Services API](https://docs.microsoft.com/azure/#pivot=products&panel=ai)를 사용하여 음성-텍스트 캡션, 다중 언어로 번역 등을 위한 교육 온라인 학습 비디오 플랫폼을 구현합니다.
* Azure CDN을 사용하여 순간적인 높은 부하(예: 제품 런칭 이벤트 시작)를 더 효율적으로 처리하기 위한 대규모 크기 조정을 구현합니다. 

## <a name="v3-capabilities"></a>v3 기능

v3은 Azure Resource Manager에서 빌드된 관리 및 운영 기능을 모두 제공하는 통합된 API 화면을 기반으로 합니다. 

이 버전은 다음과 같은 기능을 제공합니다.  

* 미디어 처리 또는 분석 작업의 간단한 워크플로를 정의하는 데 도움이 되는 **변환**. 변환은 비디오 및 오디오 파일을 처리하는 방법입니다. 그런 다음 작업을 변환에 제출하여 콘텐츠 라이브러리에 있는 모든 파일을 처리하도록 이를 반복적으로 적용할 수 있습니다.
* 비디오를 처리(인코딩 또는 분석)하는 **작업**. 입력 내용은 HTTP URL, SAS URL 또는 Azure BLOB 저장소에 있는 파일 경로를 사용하여 작업에 지정할 수 있습니다. 
* 작업 진행 또는 상태, 또는 Live Channel 시작/중지 및 오류 이벤트를 모니터링하는 **알림**. 알림은 Azure Event Grid 알림 시스템과 통합됩니다. Azure Media Services에서 여러 리소스의 이벤트를 쉽게 구독할 수 있습니다. 
* **Azure Resource Management** 템플릿은 Transforms, Streaming Endpoints, Channels 등을 만들고 배포할 수 있습니다.
* **역할 기반 액세스 제어**는 리소스 수준에서 설정할 수 있으므로 Transforms, Channels 등과 같은 특정 리소스에 대한 액세스를 잠글 수 있습니다.
* 여러 언어로 된 **클라이언트 SDK**: .NET, .NET core, Python, Go, Java, 및 Node.js.

## <a name="naming-conventions"></a>명명 규칙

Azure Media Services v3 리소스 이름(예: 자산, 작업, 변환)은 Azure Resource Manager 명명 제약 조건에 따라 달라집니다. Azure Resource Manager에 따라 리소스 이름은 항상 고유합니다. 따라서 리소스 이름에 대해 고유 식별자 문자열(예: GUID)을 사용할 수 있습니다. 

Media Services 리소스 이름에는 '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', 작은 따옴표 또는 제어 문자가 포함될 수 없습니다. 다른 문자를 모두 허용합니다. 리소스 이름의 최대 길이는 260자입니다. 

Azure Resource Manager의 이름을 지정하는 방법에 대한 자세한 내용은 [명명 요구 사항](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) 및 [명명 규칙](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.

## <a name="how-can-i-get-started-with-v3"></a>v3는 어떻게 시작하나요?

개발자인 경우 Media Services [REST API](https://go.microsoft.com/fwlink/p/?linkid=873030) 또는 REST API와 상호 작용할 수 있도록 하는 클라이언트 라이브러리를 사용하여 사용자 지정 미디어 워크플로를 손쉽게 만들고, 관리하고 유지할 수 있습니다. [여기](https://github.com/Azure-Samples/media-services-v3-rest-postman)에서 REST Postman 예제를 찾을 수 있습니다. [Azure Resource Manager 기반 REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates)를 사용할 수도 있습니다.

Microsoft는 다음 클라이언트 라이브러리를 생성하고 지원합니다. 

|클라이언트 라이브러리|샘플|
|---|---|
|[Azure CLI SDK](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)|[Azure CLI 샘플](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)|
|[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)|[.NET 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)|
|[.NET Core SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)(**.NET CLI** 탭 선택)|[.NET Core 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/mediaservices)||
|[Node.js SDK](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/index?view=azure-node-latest)|[Node.js 샘플](https://github.com/Azure-Samples/media-services-v3-node-tutorials)|
|[Python SDK](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)||
|[SDK로 이동](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)||

Media Services는 기본 설정된 언어/기술에 대한 SDK를 생성하는 데 사용할 수있는 [Swagger 파일](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media)을 제공합니다.  

## <a name="next-steps"></a>다음 단계

비디오 파일 인코딩 및 스트리밍을 시작하는 것이 얼마나 쉬운 지 보려면 [파일 스트리밍](stream-files-dotnet-quickstart.md)을 확인합니다. 

