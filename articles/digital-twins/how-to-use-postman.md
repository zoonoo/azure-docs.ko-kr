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
ms.openlocfilehash: 5dbe161af172b65919328fca0b272f3b658f1b32
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600492"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Postman을 사용 하 여 Azure Digital Twins Api에 요청을 보내는 방법

[Postman](https://www.getpostman.com/) 은 데스크톱 및 플러그 인 기반 GUI에서 핵심 HTTP 요청 기능을 제공 하는 REST 테스트 도구입니다. 이를 사용 하 여 HTTP 요청을 만들고 [Azure Digital Twins REST api](how-to-use-apis-sdks.md)에 제출할 수 있습니다.

이 문서에서는 다음 단계를 통해 Azure Digital Twins Api와 상호 작용 하도록 [Postman REST 클라이언트](https://www.getpostman.com/) 를 구성 하는 방법을 설명 합니다.

1. [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 를 사용 하 여 postman에서 API 요청을 수행 하는 데 사용할 전달자 토큰을 가져옵니다.
1. Postman collection을 설정 하 고 전달자 토큰을 사용 하 여 인증을 위해 Postman REST 클라이언트를 구성 합니다.
1. 구성 된 Postman을 사용 하 여 Azure Digital Twins Api에 요청을 만들고 보냅니다.

## <a name="prerequisites"></a>전제 조건

Postman을 사용 하 여 Azure Digital Twins Api에 액세스 하는 작업을 계속 하려면 Azure Digital Twins 인스턴스를 설정 하 고 Postman을 다운로드 해야 합니다. 이 섹션의 나머지 부분에서는 이러한 단계를 안내합니다.

### <a name="set-up-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 설정

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Postman 다운로드

다음으로, Postman 클라이언트의 데스크톱 버전을 다운로드 합니다. [*Www.getpostman.com/apps*](https://www.getpostman.com/apps) 으로 이동 하 여 메시지를 따라 앱을 다운로드 합니다.

## <a name="get-bearer-token"></a>전달자 토큰 가져오기

이제 Postman과 Azure Digital Twins 인스턴스를 설정 했으므로 Postman 요청이 Azure Digital Twins Api에 대 한 권한을 부여 하는 데 사용할 수 있는 전달자 토큰을 가져와야 합니다.

이 토큰을 얻을 수 있는 몇 가지 방법이 있습니다. 이 문서에서는 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 를 사용 하 여 Azure 계정에 로그인 하 고 해당 방식으로 토큰을 가져옵니다.

Azure CLI를 [로컬로 설치한](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)경우 컴퓨터에서 명령 프롬프트를 시작 하 여 다음 명령을 실행할 수 있습니다.
그렇지 않으면 브라우저에서 [Azure Cloud Shell](https://shell.azure.com) 창을 열고 여기에서 명령을 실행할 수 있습니다.

1. 먼저, 다음 명령을 실행 하 여 적절 한 자격 증명을 사용 하 여 Azure에 로그인 했는지 확인 합니다.

    ```azurecli-interactive
    az login
    ```

1. 다음으로 [az account get-access-token](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_get_access_token) 명령을 사용 하 여 Azure Digital twins 서비스에 대 한 액세스 권한이 있는 전달자 토큰을 가져옵니다.

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. 결과에서 값을 복사 `accessToken` 하 고 다음 섹션에서 사용할 수 있도록 저장 합니다. 요청을 인증 하기 위해 Postman에 제공 하는 **토큰 값** 입니다.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Az account get-token 명령의 결과를 보여 주는 로컬 콘솔 창의 뷰입니다. 결과의 필드 중 하나를 accessToken 라고 하며 e로 시작 하는 샘플 값이 강조 표시 됩니다.":::

>[!TIP]
>이 토큰은 5 분 이상, 최대 60 분 동안 유효 합니다. 현재 토큰에 대해 할당 된 시간을 초과 하는 경우이 섹션의 단계를 반복 하 여 새 토큰을 가져올 수 있습니다.

## <a name="set-up-postman-collection-and-authorization"></a>Postman collection 및 authorization 설정

다음에는 Postman을 설정 하 여 API 요청을 만듭니다.
이러한 단계는 로컬 Postman 응용 프로그램에서 발생 하므로 계속 진행 하 여 컴퓨터에서 Postman 응용 프로그램을 엽니다.

### <a name="create-a-postman-collection"></a>Postman Collection 만들기

Postman의 요청은 **컬렉션** (요청 그룹)에 저장 됩니다. 요청을 그룹화 하는 컬렉션을 만드는 경우 한 번에 여러 요청에 일반 설정을 적용할 수 있습니다. 이렇게 하면 전체 컬렉션에 대해 인증을 한 번만 구성 하면 되므로 Azure Digital Twins Api에 대해 둘 이상의 요청을 만들려는 경우 권한 부여가 상당히 간단해 집니다.

1. 컬렉션을 만들려면 *+ 새 컬렉션* 단추를 누릅니다.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="새로 열린 Postman 창의 뷰입니다. ' 새 컬렉션 ' 단추가 강조 표시 됩니다.":::

1. 뒤에 나오는 *새 컬렉션 만들기* 창에서 컬렉션의 **이름과** **설명** (선택 사항)을 제공 합니다.

다음으로 권한 부여를 위해 컬렉션에 전달자 토큰을 추가 하려면 다음 섹션을 계속 진행 합니다.

### <a name="add-authorization-token-and-finish-collection"></a>권한 부여 토큰 추가 및 컬렉션 마침

1. *새 컬렉션 만들기* 대화 상자에서 *권한 부여* 탭으로 이동 합니다. 여기서는 컬렉션의 모든 API 요청에 사용 하기 위해 [전달자 토큰 가져오기](#get-bearer-token) 섹션에서 수집한 **토큰 값** 을 입력 합니다.

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="' 권한 부여 ' 탭을 표시 하는 ' 새 컬렉션 만들기 ' Postman 창":::

1. *Type* 을 _**OAuth 2.0**_ 로 설정 하 *고 액세스 토큰 상자에* 액세스 토큰을 붙여넣습니다.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="' 권한 부여 ' 탭을 표시 하는 ' 새 컬렉션 만들기 ' Postman 창 ' OAuth 2.0 ' 형식을 선택 하 고 액세스 토큰 값을 붙여 넣을 수 있는 액세스 토큰 상자를 강조 표시 합니다.":::

1. 전달자 토큰에 붙여넣은 후 *만들기* 를 눌러 컬렉션 만들기를 완료 합니다.

이제 *컬렉션* 의 기본 postman 보기에서 새 컬렉션을 볼 수 있습니다.

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="주 Postman 창의 뷰입니다. 새로 만든 컬렉션은 ' 컬렉션 ' 탭에서 강조 표시 됩니다.":::

## <a name="create-a-request"></a>요청 만들기

이전 단계를 완료 한 후에는 Azure 디지털 쌍 Api에 대 한 요청을 만들 수 있습니다.

1. 요청을 만들려면 *+ 새로 만들기* 단추를 누릅니다.

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="주 Postman 창의 뷰입니다. ' 새로 만들기 ' 단추가 강조 표시 됩니다.":::

1. *요청* 을 선택 합니다.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="새 항목을 만들기 위해 선택할 수 있는 옵션을 표시 합니다. ' 요청 ' 옵션이 강조 표시 됩니다.":::

1. 이 작업을 수행 하면 요청 *저장* 창이 열립니다. 여기에서 요청에 대 한 이름을 입력 하 고, 설명 (선택 사항)을 지정 하 고, 포함 된 컬렉션을 선택할 수 있습니다. 세부 정보를 입력 하 고 이전에 만든 컬렉션에 요청을 저장 합니다.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="설명 된 필드를 채울 수 있는 ' 요청 저장 ' 창의 뷰입니다. ' Azure 디지털 Twins 컬렉션에 저장 ' 단추가 강조 표시 됩니다.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

이제 컬렉션에서 요청을 확인 하 고 선택 하 여 편집할 수 있는 세부 정보를 가져올 수 있습니다.

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="주 Postman 창의 뷰입니다. Azure Digital Twins 컬렉션이 확장 되 고 ' Query twins ' 요청이 강조 표시 됩니다. 요청에 대 한 세부 정보는 페이지의 가운데에 표시 됩니다." lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>요청 정보 설정

Azure Digital Twins Api 중 하나에 대 한 Postman 요청을 만들려면 API URL과 필요한 세부 정보에 대 한 정보를 제공 해야 합니다. [Azure Digital Twins REST API 참조 설명서](/rest/api/azure-digitaltwins/)에서이 정보를 찾을 수 있습니다.

예제 쿼리를 계속 하기 위해이 문서에서는 쿼리 API (및 해당 [참조 설명서](/rest/api/digital-twins/dataplane/query/querytwins))를 사용 하 여 인스턴스의 모든 디지털 쌍을 쿼리 합니다.

1. 참조 설명서에서 요청 URL과 형식을 가져옵니다. 쿼리 API의 경우 *POST `https://digitaltwins-name.digitaltwins.azure.net/query?api-version=2020-10-31`* 입니다.
1. Postman에서 요청에 대 한 형식을 설정 하 고 요청 URL을 입력 하 고 필요에 따라 URL에 자리 표시자를 채웁니다. 여기서는 [*필수 구성 요소*](#prerequisites) 섹션에서 인스턴스의 **호스트 이름을** 사용 합니다.
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="새 요청의 세부 정보에서 참조 설명서의 쿼리 URL은 요청 URL 상자에 채워집니다." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. *매개 변수 탭에서* 요청에 대해 표시 된 매개 변수가 참조 설명서에 설명 된 매개 변수와 일치 하는지 확인 합니다. Postman에서이 요청에 대해 `api-version` 이전 단계에서 요청 URL을 입력 하면 매개 변수가 자동으로 채워집니다. 쿼리 API의 경우이 매개 변수는 필수 매개 변수 이므로이 단계가 수행 됩니다.
1. *권한 부여* 탭에서 *부모 로부터 인증 상속* 으로 *유형을* 설정 합니다. 이는이 요청에서 전체 컬렉션에 대해 이전에 설정한 인증을 사용 함을 나타냅니다.
1. *헤더* 탭에서 요청에 대해 표시 된 헤더가 참조 설명서에 설명 된 헤더와 일치 하는지 확인 합니다. 이 요청에 대해 여러 헤더가 자동으로 채워집니다. 쿼리 API의 경우 헤더 옵션이 필요 하지 않으므로이 단계가 수행 됩니다.
1. *본문* 탭에서 요청에 대해 표시 된 본문이 참조 설명서에 설명 된 요구 사항과 일치 하는지 확인 합니다. 쿼리 API의 경우 쿼리 텍스트를 제공 하려면 JSON 본문이 필요 합니다. 인스턴스의 모든 디지털 쌍을 쿼리 하는이 요청에 대 한 예제 본문은 다음과 같습니다.

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="새 요청의 세부 정보에는 본문 탭이 표시 됩니다. ' SELECT * FROM DIGITALTWINS ' 쿼리를 사용 하는 원시 JSON 본문이 포함 되어 있습니다."lightbox="media/how-to-use-postman/postman-request-body.png":::

   Azure Digital Twins 쿼리를 작성 하는 방법에 대 한 자세한 내용은 [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)를 참조 하세요.

1. 요청 유형에 필요할 수 있는 다른 필드에 대 한 참조 설명서를 확인 합니다. 쿼리 API의 경우 Postman 요청에서 모든 요구 사항이 충족 되었으므로이 단계가 수행 됩니다.
1. *보내기* 단추를 사용 하 여 완료 된 요청을 보냅니다.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="새 요청의 세부 정보 근처에서 보내기 단추가 강조 표시 됩니다." lightbox="media/how-to-use-postman/postman-request-send.png":::

요청을 보낸 후 응답 세부 정보는 요청 아래의 Postman 창에 표시 됩니다. 응답의 상태 코드 및 본문 텍스트를 볼 수 있습니다.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="전송 된 요청의 세부 정보 아래에는 응답의 세부 정보가 강조 표시 됩니다. 쿼리에서 반환 된 디지털 쌍을 설명 하는 200 확인 및 본문 텍스트 상태가 있습니다." lightbox="media/how-to-use-postman/postman-request-response.png":::

참조 설명서에 제공 된 예상 응답 데이터와 응답을 비교 하 여 결과를 확인 하거나 발생 한 오류에 대해 자세히 알아볼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

디지털 Twins Api에 대 한 자세한 내용은 [*방법: Azure Digital Twins api 및 Sdk 사용*](how-to-use-apis-sdks.md)또는 [REST api에 대 한 참조 설명서를 참조](/rest/api/azure-digitaltwins/)하세요.