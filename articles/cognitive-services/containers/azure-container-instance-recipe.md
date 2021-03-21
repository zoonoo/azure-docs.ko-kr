---
title: Azure Container Instance 조리법
titleSuffix: Azure Cognitive Services
description: Azure Container Instance에 Cognitive Services 컨테이너를 배포 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 003b4411ac791898f4a7467b9b03f29aadba2fc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97704846"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Azure Container Instance에서 컨테이너 배포 및 실행

다음 단계를 통해 Azure [Container Instances](../../container-instances/index.yml)를 사용 하 여 클라우드에서 azure Cognitive Services 응용 프로그램을 쉽게 확장할 있습니다. 컨테이너 화를 사용 하면 인프라를 관리 하는 대신 응용 프로그램 빌드에 집중할 수 있습니다. 컨테이너 사용에 대 한 자세한 내용은 [기능 및 이점](../cognitive-services-container-support.md#features-and-benefits)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

조리법은 모든 Cognitive Services 컨테이너와 함께 작동 합니다. 조리법을 사용 하기 전에 인식 서비스 리소스를 만들어야 합니다. 컨테이너를 지 원하는 각 인식 서비스에는 컨테이너에 대 한 서비스 설치 및 구성을 위한 "설치 방법" 문서가 있습니다. 일부 서비스에는 컨테이너에 대 한 입력으로 파일 또는 파일 집합이 필요 합니다 .이 솔루션을 사용 하기 전에 컨테이너를 이해 하 고 사용 하는 것이 중요 합니다.

* 사용 중인 Azure 인식 서비스에 대 한 Azure 리소스입니다.
* 인지 서비스 **끝점 url** -특정 서비스의 "설치 방법"을 검토 하 여 컨테이너에 대 한 특정 서비스의 "설치 방법"을 검토 하 고, Azure Portal 내에서 끝점 url의 위치와 url의 올바른 예를 찾습니다. 정확한 형식은 서비스에서 서비스로 변경 될 수 있습니다.
* 인식 서비스 **키** -키는 Azure 리소스의 **키** 페이지에 있습니다. 두 키 중 하나만 필요합니다. 키는 32 영숫자 문자열입니다.

* 로컬 호스트 (컴퓨터)의 단일 Cognitive Services 컨테이너 다음을 수행할 수 있는지 확인 합니다.
  * 명령을 사용 하 여 이미지를 끌어옵니다 `docker pull` .
  * 명령을 사용 하 여 필요한 모든 구성 설정으로 로컬 컨테이너를 실행 `docker run` 합니다.
  * 컨테이너의 끝점을 호출 하 여 HTTP 2xx 및 JSON 응답의 응답을 다시 가져옵니다.

꺾쇠 괄호의 모든 변수를 `<>` 고유한 값으로 바꾸어야 합니다. 이 대체는 꺾쇠 괄호를 포함 합니다.

> [!IMPORTANT]
> LUIS 컨테이너에는 `.gz` 런타임에 가져온 모델 파일이 필요 합니다. 컨테이너는 컨테이너 인스턴스에서 볼륨 탑재를 통해이 모델 파일에 액세스할 수 있어야 합니다. 모델 파일을 업로드 하려면 다음 단계를 수행 합니다.
> 1. [Azure 파일 공유를 만듭니다](../../storage/files/storage-how-to-create-file-share.md). 나중에 필요 하므로 Azure Storage 계정 이름, 키 및 파일 공유 이름을 적어 둡니다.
> 2. [LUIS 포털에서 LUIS 모델 (패키지 된 앱)을 내보냅니다](../LUIS/luis-container-howto.md#export-packaged-app-from-luis). 
> 3. Azure Portal에서 저장소 계정 리소스의 **개요** 페이지로 이동 하 고 **파일 공유** 를 선택 합니다. 
> 4. 최근에 만든 파일 공유 이름을 선택 하 고 **업로드** 를 선택 합니다. 그런 다음 패키지 된 앱을 업로드 합니다. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>컨테이너 인스턴스 사용

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. **개요** 를 선택 하 고 IP 주소를 복사 합니다. 과 같은 숫자 IP 주소 `55.55.55.55` 입니다.
1. 새 브라우저 탭을 열고 IP 주소 (예:)를 사용 `http://<IP-address>:5000 (http://55.55.55.55:5000` 합니다. 컨테이너의 홈 페이지가 표시 됩니다. 그러면 컨테이너가 실행 중임을 알 수 있습니다.

    ![컨테이너의 홈페이지](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. **서비스 API 설명** 을 선택 하 여 컨테이너에 대 한 swagger 페이지를 표시 합니다.

1. **POST** api 중 하나를 선택 하 고 **사용해 보기** 를 선택 합니다.  매개 변수는 입력을 포함 하 여 표시 됩니다. 매개 변수를 입력 합니다.

1. **실행** 을 선택 하 여 컨테이너 인스턴스로 요청을 보냅니다.

    Azure Container Instance에서 컨테이너 Cognitive Services 성공적으로 만들고 사용 했습니다.

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> 상태 컨테이너에 대해 Text Analytics를 실행 하는 경우 다음 URL을 사용 하 여 쿼리를 제출 합니다. `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
