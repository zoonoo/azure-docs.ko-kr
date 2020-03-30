---
title: Azure 컨테이너 인스턴스 를 관리하기 & 배포
description: Azure Logic Apps를 사용하여 Azure 컨테이너 인스턴스에서 컨테이너 배포를 만들고 관리하는 작업 및 워크플로 자동화
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046293"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Azure 논리 앱을 사용하여 Azure 컨테이너 인스턴스 배포 및 관리

Azure 논리 앱 및 Azure 컨테이너 인스턴스 커넥터를 사용하면 [컨테이너 그룹을](../container-instances/container-instances-container-groups.md)배포하고 관리하는 자동화된 작업 및 워크플로를 설정할 수 있습니다. 컨테이너 인스턴스 커넥터는 다음 작업을 지원합니다.

* 컨테이너 그룹 만들기 또는 삭제
* 컨테이너 그룹의 속성 가져옵니다.
* 컨테이너 그룹 목록 받기
* 컨테이너 인스턴스의 로그 받기

논리 앱 트리거에 대한 응답으로 컨테이너 워크로드를 실행하는 등의 작업에 논리 앱에서 이러한 작업을 사용합니다. 컨테이너 인스턴스 작업의 출력을 사용하는 다른 작업을 가질 수도 있습니다. 

이 커넥터는 작업만 제공하므로 논리 앱을 시작하려면 **되풀이** 트리거와 같은 별도의 트리거를 사용하여 정기적인 일정에 따라 컨테이너 워크로드를 실행합니다. 또는 Outlook 전자 메일의 도착과 같은 이벤트 이후에 컨테이너 그룹 배포를 트리거해야 할 수 있습니다. 

로직 앱을 새로 접하는 경우 [Azure 논리 앱이란 무엇입니까?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* [논리 앱을 만드는 방법과](../logic-apps/quickstart-create-first-logic-app-workflow.md) 컨테이너 [인스턴스를 만들고 관리하는 방법에 대한](../container-instances/container-instances-quickstart.md) 기본 지식

* 컨테이너 인스턴스에 액세스하려는 논리 앱입니다. 작업을 사용하려면 되풀이 **트리거와** 같은 다른 트리거를 사용하여 논리 앱을 시작합니다.

## <a name="add-a-container-instance-action"></a>컨테이너 인스턴스 작업 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 경로를 선택합니다. 

   * 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 

     또는

   * 작업을 추가하려는 단계 사이에서, 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
   표시되는 더하기**+** 기호 () 를 선택한 다음 **작업 추가를**선택합니다.

1. 검색 상자에 "컨테이너 인스턴스"를 필터로 입력합니다. 작업 목록에서 원하는 Azure 컨테이너 인스턴스 커넥터 작업을 선택합니다.

1. 연결 이름을 입력합니다. 

1. 선택한 작업에 대해 필요한 세부 정보를 입력하고 논리 앱의 워크플로를 계속 빌드합니다.

  예를 들어 **컨테이너 그룹 만들기를** 선택하고 다음 이미지(부분 세부 정보)와 같이 컨테이너 그룹에 대한 속성과 그룹에 하나 이상의 컨테이너 인스턴스를 입력합니다.

  ![컨테이너 그룹 만들기](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전 Swagger) 설명에 설명된 트리거, 작업 및 제한에 대한 기술적 세부 정보는 커넥터의 [참조 페이지](/connectors/aci/) 또는 컨테이너 그룹 [YAML 참조를](../container-instances/container-instances-reference-yaml.md)검토합니다.

## <a name="next-steps"></a>다음 단계

* Azure 컨테이너 인스턴스에서 컨테이너를 실행하는 [샘플 논리 앱을](https://github.com/Azure-Samples/aci-logicapps-integration) 참조하여 전자 메일 또는 Twitter 텍스트의 감정을 분석합니다.

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.