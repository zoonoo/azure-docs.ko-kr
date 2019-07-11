---
title: Azure Container Instance 레시피
titleSuffix: Azure Cognitive Services
description: Azure Container Instance에 Cognitive Services 컨테이너를 배포 하는 방법 알아보기
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 288894705e1108d6dd511b60cd2bc3bcee4c6d41
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704365"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>배포 하 고 Azure Container Instance에 컨테이너 실행

다음 단계를 사용 하 여 쉽게 Azure 사용 하 여 클라우드에서 Azure Cognitive Services 응용 프로그램을 확장할 [컨테이너 인스턴스](https://docs.microsoft.com/azure/container-instances/)합니다. 인프라 관리 대신 응용 프로그램 개발에 집중할 수이 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 솔루션은 모든 Cognitive Services 컨테이너를 사용 하 여 작동합니다. 이 작성법을 사용 하기 전에 Azure portal에서 Cognitive 서비스 리소스를 생성 되어야 합니다. 컨테이너를 지 원하는 각 Cognitive 서비스 컨테이너에 대 한 서비스 설치 및 구성에 맞게 "설치 하는 방법" 문서를 있습니다. 있기 때문에 컨테이너에 대 한 입력으로 파일 또는 파일 집합이 필요로 하는 일부 서비스를 이해 하 고이 솔루션을 사용 하기 전에 컨테이너를 성공적으로 사용 했던 것이 중요 합니다.

* Azure portal에서 만든 Cognitive Service 리소스입니다.
* Cognitive Service **끝점 URL** -컨테이너에 특정 서비스의 "설치 하는 방법"을 검토, URL의 올바른 예제 같습니다 및 Azure portal 내에서 끝점 URL 인를 찾으려고 합니다. 정확한 형식은 서비스를 변경할 수 있습니다.
* Cognitive Service **키** -키에는 **키** Azure 리소스에 대 한 페이지입니다. 두 키 중 하나만 필요합니다. 키는 32 영숫자 문자의 문자열입니다.
* 로컬 호스트 (컴퓨터)에 단일 Cognitive 서비스 컨테이너입니다. 수 있는지 확인 합니다.
  * 사용 하 여 이미지를 끌어옵니다를 `docker pull` 명령입니다.
  * 로컬 컨테이너를 사용 하 여 모든 필수 구성 설정을 사용 하 여 성공적으로 실행을 `docker run` 명령입니다.
  * 2xx 응답 및 JSON 응답을 다시 시작 하는 컨테이너의 끝점을 호출 합니다.

꺾쇠 괄호 안에 있는 모든 변수 `<>`, 사용자 고유의 값으로 대체 해야 합니다. 이 대체 꺾쇠 괄호를 포함합니다.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>컨테이너 인스턴스를 사용 합니다.

1. 선택 된 **개요** IP 주소를 복사 합니다. 됩니다 숫자 IP 주소와 같은 `55.55.55.55`합니다.
1. 새 브라우저 탭을 열고 예를 들어, IP 주소를 사용 하 여 `http://<IP-address>:5000 (http://55.55.55.55:5000`). 나타납니다 컨테이너의 홈 페이지에서 컨테이너 실행 중인지 여부를 알려 줍니다.

1. 선택 **서비스 API 설명을** 컨테이너에 대 한 swagger 페이지를 표시 합니다.

1. 중 하나를 선택 합니다 **게시물** Api 및 선택 **사용해**합니다.  매개 변수는 입력을 포함 하 여 표시 됩니다. 매개 변수를 설정 합니다.

1. 선택 **Execute** 컨테이너 인스턴스에 요청을 보내도록 합니다.

    성공적으로 만들고 Azure Container Instance에 Cognitive Services 컨테이너를 사용 합니다.
