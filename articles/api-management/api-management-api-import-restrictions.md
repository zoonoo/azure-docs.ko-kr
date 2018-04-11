---
title: Azure API Management 가져오기에서 제한 사항 및 알려진 문제 | Microsoft Docs
description: Open API, WSDL 또는 WADL 형식을 사용하여 Azure API Management로 가져오기 시 알려진 문제 및 제한 사항입니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: d4229a3ecbcf8aa044eb448290c243e9920bd5cb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="api-import-restrictions-and-known-issues"></a>API 가져오기 제한 사항 및 알려진 문제
## <a name="about-this-list"></a>다음 목록 정보
API를 가져올 때 성공하기 위해 먼저 해결해야 하는 몇 가지 제한 사항이나 문제가 있을 수 있습니다. 이 문서에서는 이러한 내용을 API의 가져오기 형식별로 구성하여 설명합니다.

## <a name="open-api"> </a>Open API/Swagger
Open API 문서를 가져오는 중 오류를 수신하면 Azure Portal에서 디자이너를 사용하거나(Design - Front End - Open API Specification Editor) <a href="http://www.swagger.io">Swagger Editor</a>와 같은 타사 도구를 사용하여 유효성을 검사해야 합니다.

* **호스트 이름** APIM에는 호스트 이름 특성이 필요합니다.
* **기본 경로** APIM에는 기본 경로 특성이 필요합니다.
* **구성표** APIM에는 구성표 배열이 필요합니다.

> [!IMPORTANT]
> OpenAPI 가져오기와 관련된 중요한 정보 및 팁은 이 [문서](https://blogs.msdn.microsoft.com/apimanagement/2018/03/28/important-changes-to-openapi-import-and-export/)를 참조하세요.

## <a name="wsdl"> </a>WSDL
SOAP 통과 API를 생성하는 데 WSDL 파일이 사용되며 SOAP-to-REST API의 백 엔드로 사용됩니다.

* **WSDL:Import** - 현재 APIM은 이 특성을 사용한 API를 지원하지 않습니다. 고객은 가져온 요소를 문서 하나로 병합해야 합니다.
* **여러 부분으로 된 메시지** 현재, APIM은 이러한 유형의 메시지를 지원하지 않습니다.
* **WCF wsHttpBinding** Windows Communication Foundation으로 생성된 SOAP 서비스는 basicHttpBinding을 사용해야 합니다. wsHttpBinding은 지원되지 않습니다.
* **MTOM** MTOM을 사용한 서비스는 <em>작동할 수 있습니다</em>. 현재는 공식적으로 지원되지 않습니다.
* 재귀적으로 정의된(예: 자체의 배열을 참조) **재귀** 형식은 APIM에서 지원되지 않습니다.

## <a name="wadl"> </a>WADL
현재, 알려진 WADL 가져오기 문제가 없습니다.
