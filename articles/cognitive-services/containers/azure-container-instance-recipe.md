---
title: Azure Container Instance 레시피
titleSuffix: Azure Cognitive Services
description: Azure Container Instance에 Cognitive Services 컨테이너를 배포하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, devx-track-azurecli
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: d37b01750c1d5cd900378fcb5ea7f4d1ffb0d597
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287251"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Azure Container Instance에서 컨테이너 배포 및 실행

다음 단계를 통해 Azure [Container Instance](../../container-instances/index.yml)를 사용하여 클라우드에서 Azure Cognitive Services 애플리케이션을 쉽게 스케일링할 있습니다. 컨테이너화를 사용하면 인프라를 관리하는 대신 애플리케이션 빌드에 집중할 수 있습니다. 컨테이너 사용에 대한 자세한 내용은 [기능 및 이점](../cognitive-services-container-support.md#features-and-benefits)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

레시피는 모든 Cognitive Services 컨테이너와 함께 작동합니다. 레시피를 사용하기 전에 Cognitive Service 리소스를 만들어야 합니다. 컨테이너를 지원하는 각 Cognitive Service에는 컨테이너에 대한 서비스를 설치 및 구성하기 위한 "설치 방법" 문서가 있습니다. 일부 서비스에는 컨테이너에 대한 입력으로 파일 또는 파일 집합이 필요합니다 .이 솔루션을 사용하기 전에 컨테이너를 이해하고 성공적으로 사용하는 것이 중요합니다.

* 사용 중인 Azure Cognitive Service에 대한 Azure 리소스
* Cognitive Service **엔드포인트 URL** - 컨테이너에 대한 특정 서비스의 "설치 방법"을 검토하여 Azure Portal 내에서 엔드포인트 URL의 위치와 URL의 올바른 예를 찾습니다. 정확한 형식은 서비스마다 변경될 수 있습니다.
* Cognitive Service **키** - 키는 Azure 리소스의 **키** 페이지에 있습니다. 두 키 중 하나만 필요합니다. 키는 32개 영숫자 문자로 구성된 문자열입니다.

* 로컬 호스트(사용자 컴퓨터)의 단일 Cognitive Services 컨테이너. 다음을 확인합니다.
  * `docker pull` 명령을 사용하여 이미지를 풀다운합니다.
  * `docker run` 명령을 사용하여 필요한 모든 구성 설정으로 로컬 컨테이너를 실행합니다.
  * 컨테이너의 엔드포인트를 호출하여 HTTP 2xx의 응답과 JSON 응답을 다시 가져옵니다.

꺾쇠 괄호 `<>`의 모든 변수를 고유한 값으로 바꾸어야 합니다. 바꿀 때는 꺾쇠 괄호도 포함됩니다.

> [!IMPORTANT]
> LUIS 컨테이너에는 런타임에 가져온 `.gz` 모델 파일이 필요합니다. 컨테이너는 컨테이너 인스턴스에서 볼륨 탑재를 통해 이 모델 파일에 액세스할 수 있어야 합니다. 모델 파일을 업로드하려면 다음 단계를 수행하세요.
> 1. [Azure 파일 공유 생성](../../storage/files/storage-how-to-create-file-share.md) 나중에 필요하므로 Azure Storage 계정 이름, 키 및 파일 공유 이름을 기록해 둡니다.
> 2. [LUIS 포털에서 LUIS 모델(패키지된 앱)을 내보냅니다](../LUIS/luis-container-howto.md#export-packaged-app-from-luis). 
> 3. Azure Portal에서 스토리지 계정 리소스의 **개요** 페이지로 이동하고 **파일 공유** 를 선택합니다. 
> 4. 최근에 만든 파일 공유 이름을 선택하고 **업로드** 를 선택합니다. 그런 다음, 패키지된 앱을 업로드합니다. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>컨테이너 인스턴스 사용

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. **개요** 탭을 선택하고 IP 주소를 복사합니다. `55.55.55.55`와 같은 숫자 IP 주소입니다.
1. 새 브라우저 탭을 열고 IP 주소(예: `http://<IP-address>:5000 (http://55.55.55.55:5000`)를 사용합니다. 컨테이너의 홈페이지가 표시됩니다. 이를 통해 컨테이너가 실행 중임을 알 수 있습니다.

    ![컨테이너의 홈페이지](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. **서비스 API 설명** 을 선택하여 컨테이너에 대한 Swagger 페이지를 표시합니다.

1. **POST** API 중 하나를 선택하고 **사용해 보기** 를 선택합니다. 입력을 포함한 매개 변수가 표시됩니다. 매개 변수를 작성합니다.

1. **실행** 을 선택하여 컨테이너 인스턴스로 요청을 보냅니다.

    Azure Container Instance에서 Cognitive Services 컨테이너를 성공적으로 만들고 사용했습니다.

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> 상태 컨테이너에 대해 Text Analytics를 실행하는 경우 URL(`http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`)을 사용하여 쿼리를 제출합니다.

---
