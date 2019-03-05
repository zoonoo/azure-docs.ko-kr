---
title: Azure Media Services v3 SDK - Azure
description: 이 문서에서는 SDK/도구를 사용하여 Media Services v3 API로 개발을 시작하는 방법의 개요를 제공합니다.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/22/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 97123ec795353073b912bd83e0abb45e23549a75
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749803"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>SDK/도구를 사용하여 Media Services v3 API로 개발 시작

개발자인 경우 Media Services [REST API](https://aka.ms/ams-v3-rest-ref) 또는 REST API와 상호 작용할 수 있도록 하는 클라이언트 라이브러리를 사용하여 사용자 지정 미디어 워크플로를 손쉽게 만들고, 관리하고 유지할 수 있습니다. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API는 OpenAPI 사양(이전 명칭 Swagger)에 기반을 두고 있습니다.

이 항목에서는 SDK, 도구, 설명서에 대한 링크를 제공합니다. 또한 다른 개발 환경에 유용한 정보를 제공합니다.

## <a name="prerequisites"></a>필수 조건

Media Services에 대한 개발을 시작하려면 다음이 필요합니다.

- 활성 Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- [Media Services 계정 만들기](create-account-cli-how-to.md)

## <a name="start-developing"></a>개발 시작

Azure Media Services는 다음 SDK/도구를 지원합니다. 

- [Azure CLI](https://aka.ms/ams-v3-cli) 
- [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)
- [Python SDK](https://aka.ms/ams-v3-python-sdk)
- [SDK로 이동](https://aka.ms/ams-v3-go-sdk)
- [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Azure Media Services 탐색기

[Azure Media Services 탐색기](https://github.com/Azure/Azure-Media-Services-Explorer)(AMSE)는 Media Services에 대해 알아보고자 하는 Windows 고객이 사용할 수 있는 도구입니다. AMSE는 Media Services에서 VOD 및 실시간 콘텐츠의 업로드, 다운로드, 인코딩, 스트리밍을 수행하는 Winforms/C# 애플리케이션입니다. AMSE 도구는 코드를 작성하지 않고 Media Services를 테스트하려는 고객을 위한 도구입니다. AMSE 코드는 Media Services를 사용하여 개발하려는 고객을 위한 리소스로 제공됩니다.

AMSE는 오픈 소스 프로젝트이며, 커뮤니티에서 지원을 제공합니다(문제는 https://github.com/Azure/Azure-Media-Services-Explorer/issues)에 보고할 수 있음). 이 프로젝트에는 [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/)(Microsoft 오픈 소스 준수 사항)이 적용됩니다. 자세한 내용은 [Code of Conduct FAQ(준수 사항 FAQ)](https://opensource.microsoft.com/codeofconduct/faq/)를 참조하거나 opencode@microsoft.com에 추가 질문 또는 의견을 알려주세요.

## <a name="rest"></a>REST (영문)

Media Services REST API로 개발을 시작하려면 REST 클라이언트를 설치합니다. 예를 들어 **Postman**, REST 플러그인이 포함된 **Visual Studio Code** 또는 **Telerik Fiddler**가 있습니다. Media Services v3 샘플에 [Postman](media-rest-apis-with-postman.md)을 사용 중입니다.

Media Services [REST API 참조](https://aka.ms/ams-v3-rest-ref) 설명서를 살펴보고 이러한 예제를 확인하세요.

- [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - REST](stream-files-tutorial-with-rest.md)
- [Media Services 계정에 파일 업로드 - REST](upload-files-rest-how-to.md)
- [Media Services로 필터 만들기 - REST](filters-dynamic-manifest-rest-howto.md)
- [Azure Resource Manager 기반 REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

Media Services [Azure CLI 참조](https://aka.ms/ams-v3-cli-ref) 설명서를 살펴보고 이러한 예제를 확인하세요.

- [미디어 예약 단위 크기 조정 - CLI](media-reserved-units-cli-how-to.md)
- [Media Services 계정 만들기 - CLI](./scripts/cli-create-account.md) 
- [계정 자격 증명 다시 설정 - CLI](./scripts/cli-reset-account-credentials.md)
- [자산 만들기 - CLI](./scripts/cli-create-asset.md)
- [파일 업로드 - CLI](./scripts/cli-upload-file-asset.md)
- [변환 만들기 - CLI](./scripts/cli-create-transform.md)
- [작업 만들기 - CLI](./scripts/cli-create-jobs.md)
- [EventGrid 만들기 - CLI](./scripts/cli-create-event-grid.md)
- [자산 게시 - CLI](./scripts/cli-publish-asset.md)
- [필터링 - CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

Media Services [.NET 참조](https://aka.ms/ams-v3-dotnet-ref) 설명서를 살펴보고 이러한 예제를 확인하세요.

- [자습서: 비디오 업로드, 인코딩 및 스트리밍 - .NET](stream-files-tutorial-with-api.md) 
- [자습서: Media Services v3으로 라이브 스트리밍 - .NET](stream-live-tutorial-with-api.md)
- [자습서: Media Services v3으로 비디오 분석 - .NET](analyze-videos-tutorial-with-api.md)
- [로컬 파일에서 작업 입력 만들기 - .NET](job-input-from-local-file-how-to.md)
- [HTTPS URL에서 작업 입력 만들기 - .NET](job-input-from-http-how-to.md)
- [사용자 지정 변환으로 인코딩 - .NET](customize-encoder-presets-how-to.md)
- [AES-128 동적 암호화 및 키 전달 서비스 사용 - .NET](protect-with-aes128.md)
- [DRM 동적 암호화 및 라이선스 배달 서비스 사용 - .NET](protect-with-drm.md)
- [기존 정책에서 서명 키 가져오기 - .NET](get-content-key-policy-dotnet-howto.md)
- [Media Services로 필터 만들기 - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Media Services v3 및 Azure Functions v2의 고급 비디오 주문형 예제](http://aka.ms/ams3functions)

## <a name="java"></a>자바

Media Services [Java 참조](https://aka.ms/ams-v3-java-ref) 설명서를 검토하세요.

## <a name="nodejs"></a>Node.js

Media Services [Node.js 참조](https://aka.ms/ams-v3-nodejs-ref) 설명서를 살펴보고 node.js와 함께 Media Services API를 사용하는 방법을 보여주는 [샘플](https://github.com/Azure-Samples/media-services-v3-node-tutorials)을 확인하세요.

## <a name="python"></a>Python

Media Services [Python 참조](https://aka.ms/ams-v3-python-ref) 설명서를 검토하세요.

## <a name="go"></a>Go

Media Services [Go 참조](https://aka.ms/ams-v3-go-ref) 설명서를 검토하세요.

## <a name="next-steps"></a>다음 단계

- [계정 만들기 - CLI](create-account-cli-how-to.md)
- [API 액세스 - CLI](access-api-cli-how-to.md)

