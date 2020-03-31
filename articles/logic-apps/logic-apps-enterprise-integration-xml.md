---
title: XML 메시지 및 플랫 파일
description: 엔터프라이즈 통합 팩을 통해 Azure 논리 앱에서 XML 메시지를 처리, 유효성 검사 및 변환
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792142"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps을 사용하는 XML 메시지 및 플랫 파일

[Azure 논리 앱에서](logic-apps-overview.md)엔터프라이즈 통합 팩을 사용하여 보내고 받는 XML 메시지를 처리할 수 있습니다. BizTalk Server를 사용한 경우 엔터프라이즈 통합 팩은 메시지를 변환 및 유효성 검사하고, 플랫 파일로 작업하고, XPath를 사용하여 메시지에서 특정 속성을 보강하거나 추출하는 유사한 기능을 제공합니다. 이 공간을 새로 접하는 경우 이러한 기능은 논리 앱의 워크플로에서 메시지를 처리하는 방법을 확장합니다. 예를 들어 B2B(비즈니스 간) 시나리오가 있고 특정 XML 스키마와 함께 작업하는 경우 엔터프라이즈 통합 팩을 사용하여 회사에서 이러한 메시지를 처리하는 방법을 향상시킬 수 있습니다.

예를 들어 엔터프라이즈 통합 팩에는 다음 기능이 포함되어 있습니다.

* [XML 유효성 검사](logic-apps-enterprise-integration-xml-validation.md): 특정 스키마에 대해 들어오는 XML 메시지의 유효성을 검사합니다.

* [XML 변환](logic-apps-enterprise-integration-transform.md): 맵을 사용하여 요구 사항 또는 파트너의 요구 사항에 따라 XML 메시지를 변환하거나 사용자 지정합니다.

* [플랫 파일 인코딩 및 플랫 파일 디코딩](logic-apps-enterprise-integration-flatfile.md): 플랫 파일을 인코딩하거나 디코딩합니다.

  예를 들어 SAP에서 플랫 파일로 IDOC 파일을 수락하고 보냅니다. 많은 통합 플랫폼에서는 Logic Apps를 포함하여 XML 메시지를 만듭니다. 따라서 XML 파일을 플랫 파일로 "변환"하는 플랫 파일 인코더를 사용하는 논리 앱을 만들 수 있습니다.

* [XPath](workflow-definition-language-functions-reference.md#xpath): 메시지를 보강하고 메시지에서 특정 속성을 추출합니다. 그런 후에는 추출된 속성을 사용하여 메시지를 대상 또는 중간 엔드포인트로 라우팅할 수 있습니다.

## <a name="sample"></a>예제

Azure [논리 앱의](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) XML 기능을 사용하여 완전히 작동하는 논리 앱(GitHub 샘플)을 배포합니다.

## <a name="next-steps"></a>다음 단계

엔터프라이즈 통합 [팩에](logic-apps-enterprise-integration-overview.md) 대해 자세히 알아보기
