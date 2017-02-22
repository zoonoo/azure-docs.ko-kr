---
title: "XML 처리 - Azure Logic Apps | Microsoft Docs"
description: "엔터프라이즈 통합 팩을 사용하여 Azure Logic Apps 및 B2B 시나리오에서 XML 메시지 처리 및 유효성 검사"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2016
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 683fe55c16862c3f7df8741d491edd9ba238351d
ms.openlocfilehash: 015e387c1b252165017c4a68b26d07ef89d54ef6


---
# <a name="xml-processing"></a>XML 처리
Azure Logic Apps에서 엔터프라이즈 통합 팩을 사용하면 B2B 시나리오에서 파트너와 교환하는 XML 문서 유효성을 보다 쉽게 검사하고 문서를 처리할 수 있습니다. XML 메시지를 처리할 수 있는 방법은 다음과 같습니다.

* [XML 유효성 검사](logic-apps-enterprise-integration-xml-validation.md "XML 메시지 유효성 검사에 대해 알아보기") - 특정 스키마에 대해 원본 끝점에서 시작된 메시지의 유효성을 검사합니다.
* [XML 변환](../logic-apps/logic-apps-enterprise-integration-transform.md "XML 메시지 변환 및 맵에 대해 알아보기") - 대상 끝점의 요구 사항에 따라 XML 메시지를 변환합니다.
* [플랫 파일 인코딩 및 플랫 파일 디코딩](logic-apps-enterprise-integration-flatfile.md "플랫 파일 인코딩/디코딩에 대해 알아보기") - 플랫 파일을 인코딩 또는 디코딩합니다. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) - 메시지를 보완하고 메시지에서 특정 속성을 추출합니다. 그런 후에는 추출된 속성을 사용하여 메시지를 대상 또는 중간 끝점으로 라우팅할 수 있습니다.

## <a name="try-out-xml-processing"></a>XML 처리 시도
Azure Logic Apps의 XML 기능을 사용하여 [완벽하게 작동하는 논리 앱을 배포합니다](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline).

## <a name="learn-more"></a>자세한 정보
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")



<!--HONumber=Feb17_HO1-->


