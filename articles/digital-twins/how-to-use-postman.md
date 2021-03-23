---
title: Postman으로 요청하기
titleSuffix: Azure Digital Twins
description: Postman을 구성 하 고 사용 하 여 Azure Digital Twins Api를 테스트 하는 방법을 알아봅니다.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: d4a6e25578cd26b10b34f74a9f859d4957cc553b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783815"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Postman을 사용 하 여 Azure Digital Twins Api에 요청을 보내는 방법

[Postman](https://www.getpostman.com/) 은 데스크톱과 플러그인 기반 GUI에서 주요 HTTP 요청 기능을 제공하는 REST 테스트 도구입니다. 이를 사용 하 여 HTTP 요청을 만들고 [Azure Digital Twins REST api](how-to-use-apis-sdks.md)에 제출할 수 있습니다.

이 문서에서는 다음 단계를 통해 Azure Digital Twins Api와 상호 작용 하도록 [Postman REST 클라이언트](https://www.getpostman.com/) 를 구성 하는 방법을 설명 합니다.

1. Azure CLI를 사용 하 여 Postman에서 API 요청을 수행 하는 데 사용할 [**전달자 토큰을 가져옵니다**](#get-bearer-token) .
1. [**Postman collection**](#about-postman-collections) 을 설정 하 고 전달자 토큰을 사용 하 여 인증을 위해 POSTMAN REST 클라이언트를 구성 합니다. 컬렉션을 설정할 때 다음 옵션 중 하나를 선택할 수 있습니다.
    1. 미리 빌드된 Azure Digital Twins API 요청 컬렉션을 [**가져옵니다**](#import-collection-of-azure-digital-twins-apis) .
    1. 처음부터 직접 컬렉션을 [**만듭니다**](#create-your-own-collection) .
1. 구성 된 컬렉션에 [**요청을 추가**](#add-an-individual-request) 하 고 Azure Digital Twins api로 보냅니다.

Azure Digital Twins에는 사용할 수 있는 두 가지 Api 집합 ( **데이터 평면** 및 **제어 평면**)이 있습니다. 이러한 API 집합 간의 차이점에 대 한 자세한 내용은 [*방법: Azure Digital Twins api 및 Sdk 사용*](how-to-use-apis-sdks.md)을 참조 하세요. 이 문서에는 두 API 집합에 대 한 정보가 포함 되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Postman을 사용 하 여 Azure Digital Twins Api에 액세스 하는 작업을 계속 하려면 Azure Digital Twins 인스턴스를 설정 하 고 Postman을 다운로드 해야 합니다. 이 섹션의 나머지 부분에서는 이러한 단계를 안내합니다.

### <a name="set-up-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 설정

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Postman 다운로드

다음으로, Postman 클라이언트의 데스크톱 버전을 다운로드 합니다. [*Www.getpostman.com/apps*](https://www.getpostman.com/apps) 으로 이동 하 여 메시지를 따라 앱을 다운로드 합니다.

## <a name="get-bearer-token"></a>전달자 토큰 가져오기

이제 Postman과 Azure Digital Twins 인스턴스를 설정 했으므로 Postman 요청이 Azure Digital Twins Api에 대 한 권한을 부여 하는 데 사용할 수 있는 전달자 토큰을 가져와야 합니다.

이 토큰을 얻을 수 있는 몇 가지 방법이 있습니다. 이 문서에서는 [Azure CLI](/cli/azure/install-azure-cli) 를 사용 하 여 Azure 계정에 로그인 하 고 해당 방식으로 토큰을 가져옵니다.

Azure CLI를 [로컬로 설치한](/cli/azure/install-azure-cli)경우 컴퓨터에서 명령 프롬프트를 시작 하 여 다음 명령을 실행할 수 있습니다.
그렇지 않으면 브라우저에서 [Azure Cloud Shell](https://shell.azure.com) 창을 열고 여기에서 명령을 실행할 수 있습니다.

1. 먼저, 다음 명령을 실행 하 여 적절 한 자격 증명을 사용 하 여 Azure에 로그인 했는지 확인 합니다.

    ```azurecli-interactive
    az login
    ```

2. 다음으로 [az account get-access-token](/cli/azure/account#az_account_get_access_token) 명령을 사용 하 여 Azure Digital twins 서비스에 대 한 액세스 권한이 있는 전달자 토큰을 가져옵니다. 이 명령에서는 azure digital twins 리소스에 액세스할 수 있는 액세스 토큰을 가져오기 위해 Azure Digital Twins 서비스 끝점에 대 한 리소스 ID를 전달 합니다. 

    토큰에 필요한 컨텍스트는 사용 중인 Api 집합에 따라 달라 지므로 아래 탭을 사용 하 여 [데이터 평면과](how-to-use-apis-sdks.md#overview-data-plane-apis) [제어 평면](how-to-use-apis-sdks.md#overview-control-plane-apis) api 중에서 선택할 수 있습니다.

    # <a name="data-plane"></a>[데이터 평면](#tab/data-plane)
    
    **데이터 평면** api에서 사용할 토큰을 가져오려면 토큰 컨텍스트에 대해 다음 정적 값을 사용 `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` 합니다. Azure Digital Twins 서비스 끝점에 대 한 리소스 ID입니다.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[제어 평면](#tab/control-plane)
    
    **제어 평면** api에서 사용할 토큰을 가져오려면 토큰 컨텍스트에 대해 다음 값을 사용 `https://management.azure.com/` 합니다.
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---


3. 결과에서 값을 복사 `accessToken` 하 고 다음 섹션에서 사용할 수 있도록 저장 합니다. 요청에 권한을 부여 하는 Postman에 제공할 **토큰 값** 입니다.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Az account get-token 명령의 결과를 보여 주는 콘솔의 스크린샷 AccessToken 필드와 해당 샘플 값이 강조 표시 됩니다.":::

>[!TIP]
>이 토큰은 5 분 이상, 최대 60 분 동안 유효 합니다. 현재 토큰에 대해 할당 된 시간을 초과 하는 경우이 섹션의 단계를 반복 하 여 새 토큰을 가져올 수 있습니다.

다음으로,이 토큰을 사용 하 여 Azure Digital Twins에 대 한 API 요청을 만들도록 Postman을 설정 합니다.

## <a name="about-postman-collections"></a>Postman 컬렉션 정보

Postman의 요청은 **컬렉션** (요청 그룹)에 저장 됩니다. 요청을 그룹화 하는 컬렉션을 만드는 경우 한 번에 여러 요청에 일반 설정을 적용할 수 있습니다. 이렇게 하면 전체 컬렉션에 대해 이러한 세부 정보를 한 번만 구성 하면 되므로 Azure Digital Twins Api에 대해 둘 이상의 요청을 만들려는 경우 권한 부여가 상당히 간단해 집니다.

Azure Digital Twins를 사용 하 여 작업 하는 경우 [모든 Azure Digital twins 요청의 미리 작성 된 컬렉션](#import-collection-of-azure-digital-twins-apis)을 가져와 시작할 수 있습니다. Api를 탐색 하 고 요청 예제를 사용 하 여 프로젝트를 신속 하 게 설정 하려는 경우이 작업을 수행할 수 있습니다.

또는 [고유한 빈 컬렉션을 만들고](#create-your-own-collection) 필요한 api만 호출 하는 개별 요청으로 채워서 처음부터 시작 하도록 선택할 수도 있습니다. 

다음 섹션에서는 이러한 두 프로세스에 대해 설명 합니다. 문서의 나머지 부분은 로컬 Postman 응용 프로그램에서 발생 하므로 이제 컴퓨터에서 Postman 응용 프로그램을 엽니다.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Azure Digital Twins Api의 컬렉션 가져오기

Postman에서 Azure Digital Twins를 빠르게 시작 하는 빠른 방법은 Azure Digital Twins Api에 대 한 미리 작성 된 요청 컬렉션을 가져오는 것입니다.

### <a name="download-the-collection-file"></a>컬렉션 파일 다운로드

API 집합을 가져오는 첫 번째 단계는 컬렉션을 다운로드 하는 것입니다. 미리 작성 된 컬렉션 옵션을 보려면 선택한 데이터 평면 또는 컨트롤 평면에 대해 아래에 있는 탭을 선택 합니다.

# <a name="data-plane"></a>[데이터 평면](#tab/data-plane)

현재 선택할 수 있는 두 개의 Azure Digital Twins 데이터 평면 컬렉션이 있습니다.
* [**Azure Digital Twins Postman collection**](https://github.com/microsoft/azure-digital-twins-postman-samples):이 컬렉션은 Postman의 Azure Digital twins에 대 한 간단한 시작 환경을 제공 합니다. 요청은 샘플 데이터를 포함 하므로 최소한의 편집을 수행 하 여 실행할 수 있습니다. 샘플 정보를 포함 하는 좋게 키 API 요청 집합을 원하는 경우이 컬렉션을 선택 합니다.
    - 컬렉션을 찾으려면 리포지토리 링크로 이동 하 여 *postman_collection.js에서* 이름이 인 파일을 엽니다.
* **[Azure Digital twins 데이터 평면 Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)**:이 리포지토리에는 postman으로 다운로드 하 여 컬렉션으로 가져올 수 있는 Azure Digital TWINS API 집합에 대 한 전체 Swagger 파일이 포함 되어 있습니다. 그러면 모든 API 요청에 대 한 포괄적인 집합이 제공 되지만 샘플 데이터가 아닌 빈 데이터 본문도 제공 됩니다. 모든 API 호출에 대 한 액세스 권한을 보유 하 고 모든 데이터를 직접 입력 하려면이 컬렉션을 선택 합니다.
    - 컬렉션을 찾으려면 리포지토리 링크로 이동 하 여 최신 사양의 버전에 해당 하는 폴더를 선택 합니다. 여기에서 *digitaltwins.js의* 파일을 엽니다.

# <a name="control-plane"></a>[제어 평면](#tab/control-plane)

현재 제어 평면에서 사용할 수 있는 컬렉션은 [**Azure Digital Twins 제어 평면 Swagger**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)입니다. 이 리포지토리에는 Postman으로 다운로드 하 여 컬렉션으로 가져올 수 있는 Azure Digital Twins API 집합에 대 한 전체 Swagger 파일이 포함 되어 있습니다. 그러면 모든 API 요청의 포괄적인 집합이 제공 됩니다.

컬렉션을 찾으려면 리포지토리 링크로 이동 하 여 최신 사양의 버전에 해당 하는 폴더를 선택 합니다. 여기에서 *digitaltwins.js의* 파일을 엽니다.

---

선택한 컬렉션을 Postman으로 가져올 수 있도록 컴퓨터에 다운로드 하는 방법은 다음과 같습니다.
1. 위의 링크를 사용 하 여 브라우저에서 GitHub의 컬렉션 파일을 엽니다.
1. **Raw** 단추를 선택 하 여 파일의 원시 텍스트를 엽니다.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="GitHub의 파일에 digitaltwins.js데이터 평면의 스크린샷 Raw 단추를 중심으로 강조 표시 됩니다." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. 창에서 텍스트를 복사 하 여 컴퓨터의 새 파일에 붙여넣습니다.
1. 확장명을 사용 하 여 파일을 저장 합니다 *.* 나중에 파일을 찾기 위해 기억할 수 있는 경우 파일 이름은 원하는 대로 지정할 수 있습니다.

### <a name="import-the-collection"></a>컬렉션 가져오기

그런 다음, 컬렉션을 Postman로 가져옵니다.

1. 기본 Postman 창에서 **가져오기** 단추를 선택 합니다.
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="새로 열린 Postman 창의 스크린샷 ' 가져오기 ' 단추가 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. 다음 가져오기 창에서 **파일 업로드** 를 선택 하 고 이전에 만든 컴퓨터의 컬렉션 파일로 이동 합니다. 열기를 선택합니다.
1. **가져오기** 단추를 선택 하 여 확인 합니다.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="컬렉션으로 가져올 파일과 가져오기 단추를 표시 하는 Postman의 ' 가져오기 ' 창 스크린샷":::

이제 새로 가져온 컬렉션은 기본 Postman 보기의 컬렉션 탭에서 볼 수 있습니다.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="주 Postman 창의 스크린샷 새로 가져온 컬렉션이 ' 컬렉션 ' 탭에서 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

다음으로, 권한 부여를 위해 컬렉션에 전달자 토큰을 추가 하 고 Azure Digital 쌍 인스턴스에 연결 하려면 다음 섹션을 계속 진행 합니다.

### <a name="configure-authorization"></a>권한 부여 구성

다음으로, 만든 컬렉션을 편집 하 여 일부 액세스 정보를 구성 합니다. 만든 컬렉션을 강조 표시 하 고 **추가 작업 보기** 아이콘을 선택 하 여 메뉴를 끌어옵니다. **편집** 을 선택합니다.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Postman의 스크린샷 가져온 컬렉션에 대 한 ' 추가 작업 보기 ' 아이콘이 강조 표시 되 고 옵션에서 ' 편집 '이 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

권한 부여를 위해 컬렉션에 전달자 토큰을 추가 하려면 다음 단계를 수행 합니다. 여기서는 컬렉션의 모든 API 요청에 사용 하기 위해 [전달자 토큰 가져오기](#get-bearer-token) 섹션에서 수집한 **토큰 값** 을 사용 합니다.

1. 컬렉션에 대 한 편집 대화 상자에서 **권한 부여** 탭에 있는지 확인 합니다. 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="' 권한 부여 ' 탭을 표시 하는 Postman에서 가져온 컬렉션의 편집 대화 상자 스크린샷" lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Type을 **OAuth 2.0** 로 설정 하 고 액세스 토큰 상자에 액세스 토큰을 붙여넣은 다음 **저장** 을 선택 합니다.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="' 권한 부여 ' 탭의 가져온 컬렉션에 대 한 Postman edit 대화 상자의 스크린샷 Type은 ' OAuth 2.0 '이 고 액세스 토큰 상자가 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>추가 구성

# <a name="data-plane"></a>[데이터 평면](#tab/data-plane)

[데이터 평면](how-to-use-apis-sdks.md#overview-data-plane-apis) 컬렉션을 만드는 경우 컬렉션과 함께 제공 되는 일부 **변수** 를 설정 하 여 컬렉션을 Azure Digital twins 리소스에 쉽게 연결할 수 있습니다. 컬렉션의 많은 요청에 동일한 값 (예: Azure Digital Twins 인스턴스의 호스트 이름)이 필요한 경우 컬렉션의 모든 요청에 적용 되는 변수에 값을 저장할 수 있습니다. Azure Digital Twins에 대해 다운로드 가능한 컬렉션은 모두 컬렉션 수준에서 설정할 수 있는 미리 생성 된 변수와 함께 제공 됩니다.

1. 컬렉션에 대 한 편집 대화 상자에서 **변수** 탭으로 이동 합니다.

1. [*필수 조건*](#prerequisites) 섹션에서 인스턴스의 **호스트 이름을** 사용 하 여 관련 변수의 현재 값 필드를 설정 합니다. **저장** 을 선택합니다.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="' 변수 ' 탭을 표시 하는 Postman에서 가져온 컬렉션의 편집 대화 상자 스크린샷 ' CURRENT VALUE ' 필드가 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. 컬렉션에 추가 변수가 있으면 해당 값을 입력 하 고 저장 합니다.

위의 단계를 완료 하면 컬렉션을 구성 하는 작업이 완료 됩니다. 원하는 경우 컬렉션의 편집 탭을 닫을 수 있습니다.

# <a name="control-plane"></a>[제어 평면](#tab/control-plane)

[컨트롤 평면](how-to-use-apis-sdks.md#overview-control-plane-apis) 컬렉션을 만드는 경우 컬렉션을 구성 하는 데 필요한 모든 작업을 수행 했습니다. 원하는 경우 컬렉션의 편집 탭을 닫고 다음 섹션으로 진행할 수 있습니다.

--- 

### <a name="explore-requests"></a>요청 살펴보기

다음으로, Azure Digital Twins API 컬렉션 내의 요청을 탐색 합니다. 컬렉션을 확장 하 여 미리 만든 요청 (작업 범주별로 정렬 됨)을 볼 수 있습니다. 

요청에 따라 인스턴스와 해당 데이터에 대 한 다른 정보가 필요 합니다. 특정 요청을 작성 하는 데 필요한 모든 정보를 보려면 [Azure Digital Twins REST API 참조 설명서](/rest/api/azure-digitaltwins/)에서 요청 정보를 조회 합니다.

다음 단계를 사용 하 여 Postman collection에서 요청 세부 정보를 편집할 수 있습니다.

1. 목록에서 선택 하 여 편집 가능한 세부 정보를 가져옵니다. 

1. **경로 변수** 아래의 **매개** 변수 탭에 나열 된 변수 값을 입력 합니다.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Postman의 스크린샷 컬렉션은 요청을 표시 하도록 확장 됩니다. ' Path Variables ' 섹션이 요청 정보에 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. 각 탭에서 필요한 **헤더** 또는 **본문** 정보를 제공 합니다.

필요한 모든 세부 정보가 제공 되 면 **보내기** 단추를 사용 하 여 요청을 실행할 수 있습니다.

아래의 [*개별 요청 추가*](#add-an-individual-request) 섹션에 설명 된 프로세스를 사용 하 여 컬렉션에 사용자 고유의 요청을 추가할 수도 있습니다.

## <a name="create-your-own-collection"></a>사용자 고유의 컬렉션 만들기

모든 Azure Digital Twins Api의 기존 컬렉션을 가져오는 대신 컬렉션을 처음부터 새로 만들 수도 있습니다. 그런 다음 [Azure Digital Twins REST API 참조 설명서](/rest/api/azure-digitaltwins/)를 사용 하 여 개별 요청으로 채울 수 있습니다.

### <a name="create-a-postman-collection"></a>Postman Collection 만들기

1. 컬렉션을 만들려면 기본 postman 창에서 **새로 만들기** 단추를 선택 합니다.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="주 Postman 창의 스크린샷 ' 새로 만들기 ' 단추가 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-new.png":::

    **컬렉션** 형식을 선택 합니다.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Postman의 ' 새로 만들기 ' 대화 상자 스크린샷 ' 컬렉션 ' 옵션이 강조 표시 됩니다.":::

1. 새 컬렉션의 세부 정보를 입력 하는 탭이 열립니다. 컬렉션의 기본 이름 (**새 컬렉션**) 옆에 있는 편집 아이콘을 선택 하 여 원하는 이름으로 바꿉니다. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Postman에서 새 컬렉션의 편집 대화 상자 스크린샷 ' 새 컬렉션 ' 이름 옆의 편집 아이콘이 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

다음으로 권한 부여를 위해 컬렉션에 전달자 토큰을 추가 하려면 다음 섹션을 계속 진행 합니다.

### <a name="configure-authorization"></a>권한 부여 구성

권한 부여를 위해 컬렉션에 전달자 토큰을 추가 하려면 다음 단계를 수행 합니다. 여기서는 컬렉션의 모든 API 요청에 사용 하기 위해 [전달자 토큰 가져오기](#get-bearer-token) 섹션에서 수집한 **토큰 값** 을 사용 합니다.

1. 새 컬렉션에 대 한 편집 대화 상자에서 **권한 부여** 탭으로 이동 합니다.

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="' 인증 ' 탭을 표시 하는 Postman의 새 컬렉션 편집 대화 상자 스크린샷" lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Type을 **OAuth 2.0** 로 설정 하 고 액세스 토큰 상자에 액세스 토큰을 붙여넣은 다음 **저장** 을 선택 합니다.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="' 권한 부여 ' 탭의 새 컬렉션에 대 한 Postman edit 대화 상자의 스크린샷 Type은 ' OAuth 2.0 '이 고 액세스 토큰 상자가 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

위의 단계를 완료 하면 컬렉션을 구성 하는 작업이 완료 됩니다. 원하는 경우 새 컬렉션에 대 한 편집 탭을 닫을 수 있습니다.

새 컬렉션은 기본 Postman 보기의 컬렉션 탭에서 볼 수 있습니다.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="주 Postman 창의 스크린샷 새로 만든 사용자 지정 컬렉션은 ' 컬렉션 ' 탭에서 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>개별 요청 추가

이제 컬렉션이 설정 되었으므로 Azure 디지털 쌍 Api에 자신의 요청을 추가할 수 있습니다.

1. 요청을 만들려면 **새로 만들기** 단추를 다시 사용 합니다.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="주 Postman 창의 스크린샷 ' 새로 만들기 ' 단추가 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-new.png":::

    **요청** 형식을 선택 합니다.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Postman의 ' 새로 만들기 ' 대화 상자 스크린샷 ' 요청 ' 옵션이 강조 표시 됩니다.":::

1. 이 작업을 수행 하면 요청 저장 창이 열립니다. 여기에서 요청에 대 한 이름을 입력 하 고, 설명 (선택 사항)을 지정 하 고, 포함 된 컬렉션을 선택할 수 있습니다. 세부 정보를 입력 하 고 이전에 만든 컬렉션에 요청을 저장 합니다.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="설명 된 필드를 표시 하는 Postman의 ' 요청 저장 ' 창 스크린샷 ' Azure 디지털 Twins 컬렉션에 저장 ' 단추가 강조 표시 됩니다.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

이제 컬렉션에서 요청을 확인 하 고 선택 하 여 편집할 수 있는 세부 정보를 가져올 수 있습니다.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Postman의 스크린샷 Azure Digital Twins 컬렉션이 확장 되어 요청의 세부 정보를 표시 합니다." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>요청 정보 설정

Azure Digital Twins Api 중 하나에 대 한 Postman 요청을 만들려면 API URL과 필요한 세부 정보에 대 한 정보를 제공 해야 합니다. [Azure Digital Twins REST API 참조 설명서](/rest/api/azure-digitaltwins/)에서이 정보를 찾을 수 있습니다.

예제 쿼리를 계속 하기 위해이 문서에서는 쿼리 API (및 해당 [참조 설명서](/rest/api/digital-twins/dataplane/query/querytwins))를 사용 하 여 인스턴스의 모든 디지털 쌍을 쿼리 합니다.

1. 참조 설명서에서 요청 URL과 형식을 가져옵니다. 쿼리 API의 경우 현재 *게시 `https://digitaltwins-hostname/query?api-version=2020-10-31`* 됩니다.
1. Postman에서 요청에 대 한 형식을 설정 하 고 요청 URL을 입력 하 고 필요에 따라 URL에 자리 표시자를 채웁니다. 여기서는 [*필수 구성 요소*](#prerequisites) 섹션에서 인스턴스의 **호스트 이름을** 사용 합니다.
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Postman에서 새 요청의 세부 정보에 대 한 스크린샷 참조 설명서의 쿼리 URL이 요청 URL 상자에 채워져 있습니다." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. **매개 변수 탭에서** 요청에 대해 표시 된 매개 변수가 참조 설명서에 설명 된 매개 변수와 일치 하는지 확인 합니다. Postman에서이 요청에 대해 `api-version` 이전 단계에서 요청 URL을 입력 하면 매개 변수가 자동으로 채워집니다. 쿼리 API의 경우이 매개 변수는 필수 매개 변수 이므로이 단계가 수행 됩니다.
1. **권한 부여** 탭에서 **부모 로부터 인증 상속** 으로 유형을 설정 합니다. 이는이 요청에서 전체 컬렉션에 대해 이전에 설정한 권한 부여를 사용 함을 나타냅니다.
1. **헤더** 탭에서 요청에 대해 표시 된 헤더가 참조 설명서에 설명 된 헤더와 일치 하는지 확인 합니다. 이 요청에 대해 여러 헤더가 자동으로 채워집니다. 쿼리 API의 경우 헤더 옵션이 필요 하지 않으므로이 단계가 수행 됩니다.
1. **본문** 탭에서 요청에 대해 표시 된 본문이 참조 설명서에 설명 된 요구 사항과 일치 하는지 확인 합니다. 쿼리 API의 경우 쿼리 텍스트를 제공 하려면 JSON 본문이 필요 합니다. 인스턴스의 모든 디지털 쌍을 쿼리 하는이 요청에 대 한 예제 본문은 다음과 같습니다.

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="본문 탭에서 Postman의 새 요청 세부 정보에 대 한 스크린샷 ' SELECT * FROM DIGITALTWINS ' 쿼리를 사용 하는 원시 JSON 본문이 포함 되어 있습니다." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Azure Digital Twins 쿼리를 작성 하는 방법에 대 한 자세한 내용은 [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)를 참조 하세요.

1. 요청 유형에 필요할 수 있는 다른 필드에 대 한 참조 설명서를 확인 합니다. 쿼리 API의 경우 Postman 요청에서 모든 요구 사항이 충족 되었으므로이 단계가 수행 됩니다.
1. **보내기** 단추를 사용 하 여 완료 된 요청을 보냅니다.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="새 요청에 대 한 세부 정보를 표시 하는 Postman의 스크린샷 보내기 단추가 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-request-send.png":::

요청을 보낸 후 응답 세부 정보는 요청 아래의 Postman 창에 표시 됩니다. 응답의 상태 코드 및 본문 텍스트를 볼 수 있습니다.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Postman에서 보낸 요청의 스크린샷 요청 세부 정보 아래에 응답이 표시 됩니다. 상태는 200이 고 본문은 쿼리 결과를 표시 합니다." lightbox="media/how-to-use-postman/postman-request-response.png":::

참조 설명서에 제공 된 예상 응답 데이터와 응답을 비교 하 여 결과를 확인 하거나 발생 한 오류에 대해 자세히 알아볼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

디지털 Twins Api에 대 한 자세한 내용은 [*방법: Azure Digital Twins api 및 Sdk 사용*](how-to-use-apis-sdks.md)또는 [REST api에 대 한 참조 설명서를 참조](/rest/api/azure-digitaltwins/)하세요.