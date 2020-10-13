---
title: Azure IoT 모델 리포지토리의 개념 이해 | Microsoft Docs
description: 솔루션 개발자 또는 IT 전문가는 Azure IoT 모델 리포지토리의 기본 개념에 대해 알아봅니다.
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5d07257d1e23ee792aa996e31a2c28c17bc23d34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715062"
---
# <a name="azure-iot-model-repository"></a>Azure IoT 모델 리포지토리

Azure IoT 모델 리포지토리를 사용하면 디바이스 빌더가 IoT 플러그 앤 플레이 디바이스 모델을 관리하고 공유할 수 있습니다. 장치 모델은 [DTDL (디지털 Twins 모델링 언어)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)을 사용 하 여 정의 된 JSON LD 문서입니다. 모델 리포지토리 서비스에 저장 된 모델은 IoT 플러그 앤 플레이 클라우드 솔루션을 통합 하 고 개발 하는 인증을 요구 하지 않고도 액세스 제어 또는 공개적으로 솔루션 개발자와 공유할 수 있습니다.

> [!NOTE]
> 장치 빌더는 장치에서 직접 또는 모듈을 사용 하거나 IoT Edge 모듈에서 IoT 플러그 앤 플레이 장치 모델을 구현 하도록 선택할 수 있습니다.

다음을 사용 하 여 모델 리포지토리에 액세스할 수 있습니다.

- [Azure IoT 모델 리포지토리](https://aka.ms/iotmodelrepo) 포털
- [Azure IoT 모델 리포지토리 REST API](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [IoT 모델 리포지토리 명령 Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest&preserve-view=true)

## <a name="public-models"></a>공용 모델

모델 리포지토리에 저장 된 공용 디지털 쌍 모델은 모든 사용자가 인증 없이 응용 프로그램에서 사용 하 고 통합 하는 데 사용할 수 있습니다. 또한 공용 모델을 통해 장치 빌더 및 솔루션 개발자를 위한 열린 에코 시스템을 통해 IoT 플러그 앤 플레이 장치 모델을 공유 하 고 다시 사용할 수 있습니다.

모델 리포지토리에 모델을 게시 하 여 공용으로 설정 하는 방법에 대 한 지침은 **회사 모델** 의 [모델 게시](#publish-a-model) 섹션을 참조 하세요.

모델 리포지토리 포털을 사용 하 여 공용 모델을 보려면 다음을 수행 합니다.

1. [Azure IoT 모델 리포지토리 포털](https://aka.ms/iotmodelrepo)로 이동 합니다.

1. **공개 모델 보기**를 선택 합니다.

    ![공용 모델 보기](./media/concepts-model-repository/public-models.png)

REST API 사용 하 여 프로그래밍 방식으로 공용 모델을 보려면 [모델 REST API 가져오기](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) 설명서를 참조 하세요.

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

CLI를 사용 하 여 공용 모델을 보려면 Azure CLI [모델 가져오기](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) 명령을 참조 하세요.

## <a name="company-models"></a>회사 모델

회사 모델 리포지토리는 조직이 회사 또는 조직 내에서 사용자가 작성 한 디지털 쌍 모델을 만들고 관리할 수 있는 Azure IoT 모델 리포지토리의 테 넌 트입니다. 회사 모델은 회사 또는 조직의 인증 된 사용자만 사용할 수 있습니다. 모델 리포지토리 테 넌 트 관리자는 회사 또는 조직의 다른 사용자에 게 회사 모델 리포지토리의 모델에 대 한 사용 권한을 할당 하 고 액세스를 제어할 수 있습니다.

### <a name="set-up-your-company-model-repository"></a>회사 모델 리포지토리 설정

*회사 또는 학교 Azure Active Directory (AZURE AD) 계정을* 사용 하 여 모델 리포지토리에 액세스할 수 있습니다. 조직에 이미 Azure AD 테 넌 트가 있는 경우이 Azure AD 테 넌 트에서 사용자 계정 및 서비스 주체를 사용할 수 있습니다.

Azure AD 테 넌 트를 설정 하는 방법과 Azure AD 테 넌 트에서 사용자 또는 서비스 주체를 만드는 방법에 대 한 자세한 내용은 [추가 정보](#additional-information) 섹션을 참조 하세요.

- 조직의 첫 번째 사용자가 모델 리포지토리에 액세스 하거나 포털에 로그인 하는 경우 **테넌트 관리자** 역할이 부여 됩니다. 이 역할을 통해 조직의 저장소 테 넌 트에 있는 다른 사용자에 게 역할을 할당할 수 있습니다.

- **모델 읽기** 또는 **모델 만들기**와 같은 **테넌트 관리자** 다른 역할을 할당할 수 있습니다.

### <a name="understand-access-management"></a>액세스 관리 이해

다음 표에서는 회사 모델 리포지토리에서 지원 되는 기능과 관련 사용 권한을 요약 하 여 보여 줍니다.

| 기능  | 사용 권한| 설명|
|-------------|-----------|------------|
|모델 읽기|모델 읽기|기본적으로 회사 테 넌 트의 모든 사용자는 회사 모델을 볼 수 있습니다. 또한 사용자는 다른 회사에서 공유 하는 개인 모델만 볼 수 있습니다.|
|액세스 관리|액세스 관리|조직의 다른 사용자에 대 한 사용자 역할 할당 (추가 또는 제거)을 관리 합니다.|
|모델 만들기|모델 만들기|회사 모델 리포지토리에서 모델을 만듭니다.|
|모델 게시|모델 게시|모델을 게시 하 여 누구나 모델을 볼 수 있도록 합니다.|

다음 표에는 액세스 관리에 사용할 수 있는 모델 리포지토리에서 지원 되는 역할 및 해당 기능이 요약 되어 있습니다.

|역할|기능|
|----|----------|
|TenantAdministrator|액세스 관리, 모델 읽기|
|만든 이|모델 만들기, 모델 읽기|
|게시자|모델 게시, 모델 읽기|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>REST API를 사용 하 여 회사 모델에 액세스할 때 보안 토큰 전달

REST Api를 호출 하 여 개인 또는 공유 되는 회사 모델을 관리 하는 경우 JWT 형식으로 사용자 또는 서비스 주체에 대 한 권한 부여 토큰을 제공 해야 합니다. 사용자 또는 서비스 주체에 대 한 JWT 토큰을 가져오는 방법에 대 한 자세한 [내용은 추가 정보](#additional-information) 섹션을 참조 하세요.

회사 모델 또는 공유 모델을 대상으로 하는 경우 API의 인증 HTTP 헤더에 JWT 토큰을 전달 해야 합니다. 공용 모델을 대상으로 지정할 때 JWT 토큰이 필요 하지 않습니다.

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>회사 또는 공유 모델 보기

사용자는 저장소 테 넌 트의 *읽기 권한자* 역할의 멤버 이거나 모델을 읽기 위해 모델을 공유 해야 합니다. 사용자와 공유 된 게시 되지 않은 모델 목록과 사용자와 공유 된 게시 된 모델 목록을 볼 수 있습니다. 기본적으로 사용자는 회사의 모델, 다른 회사에서 공유한 모델 및 모든 공용 모델을 읽을 수 있습니다.

포털을 사용 하 여 회사 또는 공유 모델을 보려면 다음을 수행 합니다.

1. [Azure IoT 모델 리포지토리 포털](https://aka.ms/iotmodelrepo)에 로그인 합니다.

1. 왼쪽 창에서 **회사 모델** – **게시** 되지 않음을 확장 합니다.

    ![회사 모델 보기](./media/concepts-model-repository/view-company-models.png)

1. 왼쪽 창에서 **공유 모델 – 게시** 되지 않음을 확장 합니다.

    ![공유 모델 보기](./media/concepts-model-repository/view-shared-models.png)

REST API를 사용 하 여 회사 또는 공유 모델을 보려면 [모델 가져오기](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API 설명서를 참조 하세요. HTTP 요청에서 JWT 권한 부여 헤더를 전달 하는 방법에 대 한 자세한 내용은 [REST API를 사용 하 여 회사 모델에 액세스할 때 보안 토큰 전달](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) 을 참조 하세요.

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

CLI를 사용 하 여 회사 모델 또는 공유 모델을 보려면 Azure CLI [모델 가져오기](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) 명령을 참조 하세요.

### <a name="manage-roles"></a>역할 관리

테 넌 트 관리자는 저장소 테 넌 트의 사용자에 게 역할을 할당 하 여 회사 또는 조직에 전용 모델을 만들거나, 모델을 게시 하거나, 다른 사용자의 역할을 관리할 수 있습니다.

포털을 사용 하 여 모델 저장소 테 넌 트 역할에 사용자를 추가 하려면:

1. [Azure IoT 모델 리포지토리 포털](https://aka.ms/iotmodelrepo)에 로그인 합니다.

1. 왼쪽 창에서 **액세스 관리** 를 선택한 다음 **+ 추가**를 선택 합니다. **권한 추가** 창에서 역할에 추가 하려는 사용자의 작업 주소를 입력 합니다.

    ![회사 주소 추가](./media/concepts-model-repository/add-user.png)

1. **역할** 드롭다운에서 사용자를 추가 하려는 역할을 선택 합니다. 그런 다음, **저장**을 선택 합니다.

    ![역할 선택](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>모델 업로드

모델을 회사 모델 리포지토리에 업로드 하려면 저장소 테 넌 트의 **작성자** 역할의 멤버 여야 합니다.

이러한 모델은 게시 되지 않으며 기본적으로 조직 내의 사용자만 액세스할 수 있습니다. 하나 이상의 게시 되지 않은 모델을 외부 사용자와 공유할 수도 있습니다.

업로드 된 모델은 변경할 수 없습니다.

이러한 모델에 대 한 모델 Id는 모든 업로드 된 모델에 대 한 모든 저장소 테 넌 트에서 전역적으로 고유 해야 합니다.

포털을 사용 하 여 모델을 업로드 하려면:

1. [Azure IoT 모델 리포지토리 포털](https://aka.ms/iotmodelrepo)에 로그인 합니다.

1. 왼쪽 창에서 **회사 모델** 을 확장 하 고 **모델 만들기**를 선택 합니다. 그런 다음, **Json 가져오기**를 선택 합니다.

    ![모델 만들기](./media/concepts-model-repository/create-model.png)

1. 업로드 하려는 파일을 선택 합니다. 포털이 모델의 유효성을 성공적으로 검사 하는 경우 **저장**을 선택 합니다.

REST API를 사용 하 여 모델을 업로드 하려면 [모델 만들기](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) API를 참조 하세요. HTTP 요청에서 JWT 권한 부여 헤더를 전달 하는 방법에 대 한 자세한 내용은 [REST API를 사용 하 여 회사 모델에 액세스할 때 보안 토큰 전달](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) 을 참조 하세요.

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-05-01-preview", httpContent).ConfigureAwait(false);
```

CLI를 사용 하 여 모델을 업로드 하려면 모델 Azure CLI [만들기](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create&preserve-view=true) 명령을 참조 하세요.

### <a name="publish-a-model"></a>모델 게시

모델을 게시하려면 다음 요구 사항을 충족해야 합니다.

1. 모델을 게시하려면 조직이 [Microsoft 파트너 네트워크](https://docs.microsoft.com/partner-center/)의 멤버여야 합니다. 파트너 센터 계정을 만들려면 [파트너 센터 계정 만들기](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account)를 참조하세요. 계정이 승인되면 모델을 게시할 수 있습니다. 자세한 내용은 [파트너 센터 FAQ](https://support.microsoft.com/help/4340639/partner-center-account-faqs)를 참조하세요.

2. 사용자는 저장소 테 넌 트의 *게시자* 역할의 멤버 여야 합니다.

조직 내 사용자가 만들고 게시 한 모델은 *게시 된 모델로*표시 됩니다. 이러한 모델은 공용 이며 **공용 모델**의 모든 사용자가 찾을 수 있습니다.

포털을 사용하여 모델을 게시하려면:

1. [Azure IoT 모델 리포지토리 포털](https://aka.ms/iotmodelrepo)에 로그인 합니다.

2. 왼쪽 창에서 **회사 모델** 을 확장 하 고 게시 하려는 모델을 선택 합니다. 그런 다음 **게시**를 선택 합니다.

    ![모델 게시](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> Microsoft Partner (MPN) ID가 없다는 알림이 표시 되 면 알림의 등록 단계를 따릅니다. 자세한 내용은이 섹션의 시작 부분에 있는 요구 사항을 참조 하세요.

REST API를 사용 하 여 모델을 게시 하려면 [모델 게시](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) REST API 설명서를 참조 하세요. `update-metadata=true`REST API를 사용 하 여 모델을 게시 하려면 쿼리 문자열 매개 변수를 제공 합니다. HTTP 요청에서 JWT 권한 부여 헤더를 전달 하는 방법에 대 한 자세한 내용은 [REST API를 사용 하 여 회사 모델에 액세스할 때 보안 토큰 전달](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) 을 참조 하세요.

CLI를 사용 하 여 모델을 게시 하려면 Azure CLI [모델 게시](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish&preserve-view=true) 명령을 참조 하세요.

> [!NOTE]
> 모델은 인증 테스트를 실행 하기 전에 모델 리포지토리에 게시 해야 합니다. 자세히 알아보려면 [IoT 플러그 앤 플레이 장치를 인증 하는 방법](howto-certify-device.md)을 참조 하세요.

### <a name="share-a-model"></a>모델 공유

외부 조직의 사용자를 사용 하 여 만든 회사 모델을 공유할 수 있습니다. 이러한 방식으로 협력자가 개인 회사 모델을 사용 하 여 솔루션을 보고 개발할 수 있습니다.

예를 들어 장치 제조업체는 회사 또는 조직에 대 한 개인 모델을 유지 관리 하려고 할 수 있습니다. 장치 기능을 기밀로 유지 해야 하는 고객이 있을 수 있습니다.

회사 또는 조직 간에 모델을 공유 하면 공용이 아닌 모델에 안전 하 게 액세스할 수 있습니다.

포털을 사용 하 여 회사 모델을 공유 하려면:

- 모델을 만든 경우에는 **회사 모델** 섹션에서 모델을 볼 때 공유 및 **공유** **위치** 단추가 활성화 됩니다.

    ![모델 공유](./media/concepts-model-repository/share-model.png)

- 외부 사용자와 모델을 공유 하려면 **공유**를 선택 합니다. **모델 공유** 창에서 외부 사용자의 메일 주소를 입력 하 고 **저장**을 선택 합니다.

- 모델을 공유한 사용자를 보려면 **공유**대상을 선택 합니다.

- 특정 사용자와 모델 공유를 중지 하려면 **공유** 대상 창의 사용자 목록에서 사용자를 선택 합니다. 그런 다음 **제거** 를 선택 하 고 메시지가 표시 되 면 선택 사항을 확인 합니다.

    ![공유 중지](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>추가 정보

Azure AD로 작업할 때 도움이 되는 다음 항목을 찾을 수 있습니다.

- 새 Azure AD 테 넌 트를 만들려면 [AZURE ad에서 새 테 넌 트 만들기](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)를 참조 하세요. 대부분의 조직에는 이미 Azure AD 테 넌 트가 있습니다.

- Azure AD 테 넌 트에 사용자 또는 게스트 사용자를 추가 하려면 [AZURE ad를 사용 하 여 사용자 추가 또는 삭제](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)를 참조 하세요.

- Azure AD 테 넌 트에 서비스 주체를 추가 하려면 포털을 사용 하 여 [리소스에 액세스할 수 있는 AZURE ad 응용 프로그램 및 서비스 주체를 만드는 방법](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)을 참조 하세요.

- REST Api를 호출할 때 사용할 수 있도록 Azure AD에서 JWT 토큰을 가져오는 방법에 [대 한 자세한 내용은 클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE ad에서 토큰 획득](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

제안 된 다음 단계는 [IoT 플러그 앤 플레이 아키텍처](concepts-architecture.md)를 검토 하는 것입니다.
