---
title: 엔터프라이즈 통합 팩의 XML 처리 개요 | Microsoft Docs
description: 엔터프라이즈 통합 팩 및 논리 앱을 사용하여 XML 메시지를 처리하고 유효성을 검사하는 방법에 알아봅니다.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2016
ms.author: deonhe

---
# XML 처리
엔터프라이즈 통합 팩을 사용하면 비즈니스 파트너와 교환하는 XML 문서의 유효성을 쉽게 검사하고 처리할 수 있습니다. 다음은 논리 앱을 사용하여 이러한 XML 메시지를 처리할 수 있는 방법입니다.

* [XML 유효성 검사](app-service-logic-enterprise-integration-xml-validation.md "XML 메시지 유효성 검사에 대해 알아보기") - XML 유효성 검사는 특정 스키마에 대해 원본 끝점에서 시작된 메시지의 유효성을 검사하는 기능을 제공합니다.
* [XML 변환](app-service-logic-enterprise-integration-transform.md "XML 메시지 변환 및 맵에 대해 알아보기") - XML 변환은 대상 끝점의 요구 사항에 따라 XML 메시지를 변환하는 기능을 제공합니다.
* [플랫 파일 인코딩 및 플랫 파일 디코딩](app-service-logic-enterprise-integration-flatfile.md "플랫 파일 인코딩/디코딩에 대해 알아보기") - 플랫 파일 인코딩/디코딩은 플랫 파일을 인코딩하거나 디코딩하는 기능을 제공합니다.
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) - 메시지를 보완하고 메시지에서 특정 속성을 추출하는 기능을 제공합니다. 그런 후에는 추출된 속성을 사용하여 메시지를 대상 또는 중간 끝점으로 라우팅할 수 있습니다.

## 직접 체험해 보기
체험해 보세요. [여기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline)를 클릭하여 논리 앱의 XML 기능을 사용하여 완벽하게 작동하는 논리 앱을 배포합니다.

## 자세한 정보
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](app-service-logic-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")

<!---HONumber=AcomDC_0817_2016-->