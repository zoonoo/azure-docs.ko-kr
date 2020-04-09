---
title: Azure 컨테이너 인스턴스 레시피
titleSuffix: Azure Cognitive Services
description: Azure 컨테이너 인스턴스에서 코그너티브 서비스 컨테이너를 배포하는 방법 알아보기
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876660"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Azure Container Instance에서 컨테이너 배포 및 실행

다음 단계를 통해 Azure [컨테이너 인스턴스를](https://docs.microsoft.com/azure/container-instances/)사용하면 클라우드에서 Azure Cognitive Services 응용 프로그램을 쉽게 확장할 수 있습니다. 컨테이너화를 사용하면 인프라를 관리하는 대신 응용 프로그램을 빌드하는 데 집중할 수 있습니다. 컨테이너 사용에 대한 자세한 내용은 [기능 및 이점을](../cognitive-services-container-support.md#features-and-benefits)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

레시피는 모든 코그너티브 서비스 컨테이너와 함께 작동합니다. 레시피를 사용하기 전에 Azure 포털에서 인지 서비스 리소스를 만들어야 합니다. 컨테이너를 지원하는 각 코그너티브 서비스에는 컨테이너에 대한 서비스를 설치하고 구성하기 위한 "설치 방법" 문서가 있습니다. 일부 서비스는 컨테이너에 대한 입력으로 파일 또는 파일 집합이 필요하므로 이 솔루션을 사용하기 전에 컨테이너를 이해하고 성공적으로 사용하는 것이 중요합니다.

* Azure 포털에서 만든 인지 서비스 리소스입니다.
* 인지 서비스 **끝점 URL** - 컨테이너에 대한 특정 서비스의 "설치 방법"을 검토하여 Azure 포털 내에서 엔드포인트 URL의 위치와 URL의 올바른 예가 어떻게 보이는지 확인합니다. 정확한 형식은 서비스에서 서비스로 변경될 수 있습니다.
* 인지 서비스 **키** - 키는 Azure 리소스의 **키** 페이지에 있습니다. 두 키 중 하나만 필요합니다. 키는 32개의 알파 숫자 문자 문자열입니다.
* 로컬 호스트(컴퓨터)의 단일 코그너티브 서비스 컨테이너입니다. 다음을 수행할 수 있는지 확인합니다.
  * 명령으로 이미지를 아래로 `docker pull` 당깁니다.
  * 명령으로 필요한 모든 구성 설정을 통해 `docker run` 로컬 컨테이너를 성공적으로 실행합니다.
  * HTTP 2xx응답과 JSON 응답을 다시 받고 컨테이너의 끝점을 호출합니다.

각도 대괄호의 모든 `<>`변수는 , 자신의 값으로 대체해야합니다. 이 교체에는 각 브래킷이 포함됩니다.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>컨테이너 인스턴스 사용

1. **개요를** 선택하고 IP 주소를 복사합니다. `55.55.55.55`와 같은 숫자 IP 주소가 됩니다.
1. 새 브라우저 탭을 열고 IP 주소를 사용합니다(예: `http://<IP-address>:5000 (http://55.55.55.55:5000`). 컨테이너의 홈 페이지가 표시되어 컨테이너가 실행 중임을 알 수 있습니다.

1. **컨테이너의** 스웨거 페이지를 보려면 서비스 API 설명을 선택합니다.

1. **POST** API 중 어느 한 개라도 선택하고 **시도해 선택합니다.**  입력을 포함하여 매개 변수가 표시됩니다. 매개 변수를 입력합니다.

1. **실행을** 선택하여 컨테이너 인스턴스에 요청을 보냅니다.

    Azure 컨테이너 인스턴스에서 코그너티브 서비스 컨테이너를 성공적으로 만들고 사용했습니다.
