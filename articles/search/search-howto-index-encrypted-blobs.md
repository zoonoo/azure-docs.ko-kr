---
title: 암호화 된 Azure Blob storage 콘텐츠를 검색 합니다.
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search를 사용 하 여 Azure Blob Storage의 암호화 된 문서에서 텍스트를 인덱싱하고 추출 하는 방법에 대해 알아봅니다.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 6a4dcec2b50a13a256c82e4a5ec54c9b22aa973f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791990"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Azure Cognitive Search에서 blob 인덱서 및 기술력과를 사용 하 여 암호화 된 blob을 인덱싱하는 방법

이 문서에서는 [Azure Cognitive Search](search-what-is-azure-search.md) 를 사용 하 여 [Azure Key Vault](../key-vault/general/overview.md)를 사용 하 여 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) 내에서 이전에 암호화 된 문서를 인덱싱하는 방법을 보여 줍니다. 일반적으로 인덱서는 암호화 키에 대 한 액세스 권한이 없기 때문에 암호화 된 파일에서 콘텐츠를 추출할 수 없습니다. 그러나 [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) 사용자 지정 기술, [documentextractionskill](cognitive-search-skill-document-extraction.md)을 활용 하 여 키에 대 한 제어 된 액세스를 제공 하 여 파일의 암호를 해독 한 다음 해당 파일에서 콘텐츠를 추출할 수 있습니다. 이렇게 하면 암호화 되지 않은 상태로 저장 되는 데이터에 대해 걱정할 필요 없이 이러한 문서를 인덱싱하는 기능이 해제 됩니다.

이 가이드는 Postman 및 Search REST Api를 사용 하 여 다음 작업을 수행 합니다.

> [!div class="checklist"]
> * Azure Key Vault를 사용 하 여 암호화 된 Azure Blob storage에서 PDF, HTML, .DOCX 및 .PPTX와 같은 전체 문서 (구조화 되지 않은 텍스트)로 시작 합니다.
> * 문서를 해독 하 고 텍스트를 추출 하는 파이프라인을 정의 합니다.
> * 출력을 저장할 인덱스를 정의 합니다.
> * 파이프라인을 실행하여 인덱스를 만들고 로드합니다.
> * 전체 텍스트 검색과 풍부한 쿼리 구문을 사용하여 결과를 검색합니다.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 엽니다.

## <a name="prerequisites"></a>필수 구성 요소

이 예에서는 Azure Blob Storage 파일을 이미 업로드 하 고 프로세스에서 암호화 한 것으로 가정 합니다. 처음에 파일을 업로드 하 고 암호화 하는 데 도움이 필요한 경우 [이 자습서](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) 를 통해이 작업을 수행 하는 방법을 확인 하세요.

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
+ [Azure 함수](https://azure.microsoft.com/services/functions/)
+ [Postman 데스크톱 앱](https://www.getpostman.com/)
+ [만들기](search-create-service-portal.md) 또는 [기존 검색 서비스 찾기](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 이 가이드에는 무료 서비스를 사용할 수 있습니다. 무료 검색 서비스는 세 개의 인덱스, 세 개의 인덱서, 3 개의 데이터 원본 및 3 개의 기술력과으로 제한 합니다. 이 가이드는 각각 하나씩 만듭니다. 시작하기 전에 새 리소스를 수용할 수 있는 공간이 서비스에 있는지 확인하세요.

## <a name="1---create-services-and-collect-credentials"></a>1-서비스 만들기 및 자격 증명 수집

### <a name="set-up-the-custom-skill"></a>사용자 지정 기술 설정

이 예제에서는 [Azure Search 파워 기술](https://github.com/Azure-Samples/azure-search-power-skills) GitHub 리포지토리의 샘플 [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) 프로젝트를 사용 합니다. 이 섹션에서는 기술에서 사용할 수 있도록 기술을 Azure 함수에 배포 합니다. 기본 제공 배포 스크립트는 **psdbf-함수-앱** 으로 시작 하는 이라는 Azure 함수 리소스를 만들고 해당 기술을 로드 합니다. 구독 및 리소스 그룹을 제공 하 라는 메시지가 표시 됩니다. Azure Key Vault 인스턴스가 상주 하는 것과 동일한 구독을 선택 해야 합니다.

운영상, DecryptBlobFile 기술은 각 blob에 대 한 URL 및 SAS 토큰을 입력으로 사용 하 고, Azure Cognitive Search에서 기대 하는 파일 참조 계약을 사용 하 여 다운로드 된 암호 해독 된 파일을 출력 합니다. 암호 해독을 수행 하려면 DecryptBlobFile에 암호화 키가 필요 합니다. 설정의 일부로 Azure Key Vault의 암호화 키에 대 한 DecryptBlobFile 함수 액세스 권한을 부여 하는 액세스 정책도 만듭니다.

1. [DecryptBlobFile 방문 페이지](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment)에 있는 **Azure에 배포** 단추를 클릭 하면 Azure Portal 내에서 제공 된 리소스 관리자 템플릿이 열립니다.

1. **Azure Key Vault 인스턴스가 존재** 하는 구독을 선택 하 고 (다른 구독을 선택 하는 경우이 가이드는 작동 하지 않음) 기존 리소스 그룹을 선택 하거나 새 리소스 그룹을 만듭니다. 새 리소스 그룹을 만드는 경우 배포할 지역을 선택 해야 합니다.

1. **검토 + 만들기** 를 선택 하 고 약관에 동의 했는지 확인 한 다음 **만들기** 를 선택 하 여 Azure 함수를 배포 합니다.

    ![포털의 ARM 템플릿](media/indexing-encrypted-blob-files/arm-template.jpg "포털의 ARM 템플릿")

1. 배포가 완료될 때까지 기다립니다.

1. 포털에서 Azure Key Vault 인스턴스로 이동 합니다. 사용자 지정 기술에 대 한 키 액세스 권한을 부여 하는 [액세스 정책을](../key-vault/general/assign-access-policy-portal.md) Azure Key Vault에 만듭니다.
 
    1. **설정** 에서 **액세스 정책** 을 선택 하 고 **액세스 정책 추가** 를 선택 합니다.
     
       ![Keyvault 액세스 정책 추가](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Keyvault 액세스 정책")

    1. **템플릿에서 구성** 아래에서 **Azure Data Lake Storage 또는 Azure Storage** 을 선택 합니다.

    1. 보안 주체에 대해 배포한 Azure Function 인스턴스를 선택 합니다. 2 단계에서 생성 하는 데 사용 된 리소스 접두사를 사용 하 여 검색할 수 있습니다. 여기에는 기본 접두사 값 **psdbf-app** 이 포함 됩니다.

    1. 권한 있는 응용 프로그램 옵션에는 아무것도 선택 하지 마십시오.
     
        ![Keyvault 액세스 정책 템플릿 추가](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Keyvault 액세스 정책 템플릿")

    1. 액세스 정책 페이지에서 **저장** 을 클릭 하 여 액세스 정책을 실제로 추가 하려면 먼저 액세스 정책 페이지에서 저장을 클릭 해야 합니다.
     
         ![키 자격 증명 모음 저장 액세스 정책](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Keyvault 액세스 정책 저장")

1. 포털에서 **psdbf** 함수로 이동 하 고 나중에이 가이드에서 필요에 따라 다음 속성을 기록해 둡니다.

    1. 함수 URL은 함수에 대 한 기본 페이지의 **Essentials** 아래에서 찾을 수 있습니다.
    
        ![함수 URL](media/indexing-encrypted-blob-files/function-uri.jpg "Azure 함수 URL을 찾을 수 있는 위치")

    1. 호스트 키 코드- **앱 키** 로 이동 하 고 클릭 하 여 **기본** 키를 표시 하 고 값을 복사 하 여 찾을 수 있습니다.
     
        ![함수 호스트 키 코드](media/indexing-encrypted-blob-files/function-host-key.jpg "Azure 함수 호스트 키 코드를 찾을 수 있는 위치")

### <a name="cognitive-services"></a>Cognitive Services

AI 보강 및 기술 실행은 자연어 및 이미지 처리를 위한 Text Analytics 및 Computer Vision를 포함 하 여 Cognitive Services에 의해 지원 됩니다. 실제 프로토타입 또는 프로젝트를 완료하는 것이 목표라면 이 시점에서 Cognitive Services를 인덱싱 작업에 연결할 수 있도록 Azure Cognitive Search와 동일한 지역에 프로비저닝합니다.

그러나 Azure Cognitive Search는 백그라운드에서 Cognitive Services에 연결하여 인덱서 실행당 20개의 체험 트랜잭션을 제공할 수 있으므로 이 연습에서는 리소스 프로비저닝을 건너뛸 수 있습니다. 20 개의 문서를 처리 한 후에는 Cognitive Services 키가 기술에 연결 되어 있지 않으면 인덱서가 실패 합니다. 대규모 프로젝트의 경우 종량제 S0 계층에서 Cognitive Services를 프로비저닝할 계획입니다. 자세한 내용은 [Cognitive Services 연결](cognitive-search-attach-cognitive-services.md)을 참조하세요. 선택한 인식 기술이 Cognitive Services에 연결 되어 있지 않은 경우에도 (예: 제공 된 기술에 기술이 추가 되지 않은 경우) Cognitive Services 키는 20 개 이상의 문서와 함께 기술를 실행 하는 데 필요 합니다.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

마지막 구성 요소는 Azure Cognitive Search 이며 [포털에서 만들](search-create-service-portal.md)수 있습니다. 무료 계층을 사용 하 여이 가이드를 완료할 수 있습니다. 

Azure 함수를 사용 하는 것 처럼 잠시 시간을 걸리고 관리 키를 수집 합니다. 또한 요청을 구조화하기 시작할 때 각 요청을 인증하는 데 사용되는 엔드포인트 및 관리 API 키를 제공해야 합니다.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search용 관리 API 키와 URL을 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 검색 서비스의 이름을 확인합니다. 엔드포인트 URL을 검토하여 서비스 이름을 확인할 수 있습니다. 엔드포인트 URL이 `https://mydemo.search.windows.net`인 경우 서비스 이름은 `mydemo`입니다.

2. **설정** > **키** 에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

   ![서비스 이름과 관리자 및 쿼리 키 확인](media/search-get-started-javascript/service-name-and-keys.png)

모든 요청에서 서비스에 보내는 각 요청의 헤더마다 API 키가 필요합니다. 유효한 키는 요청을 보내는 애플리케이션과 이 요청을 처리하는 서비스 간에 요청별로 신뢰를 설정합니다.

## <a name="2---set-up-postman"></a>2 - Postman 설정

Postman을 설치하고 설정합니다.

### <a name="download-and-install-postman"></a>Postman 다운로드 및 설치

1. [Postman 컬렉션 소스 코드](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json)를 다운로드합니다.
1. **파일** > **가져오기** 를 차례로 선택하여 소스 코드를 Postman으로 가져옵니다.
1. **컬렉션** 탭, **...** (줄임표) 단추를 차례로 선택합니다.
1. **편집** 을 선택합니다. 
   
   ![탐색 결과를 표시하는 Postman 앱](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Postman의 [편집] 메뉴로 이동")
1. **편집** 대화 상자에서 **변수** 탭을 선택합니다. 

**변수** 탭에서 이중 중괄호 안에 특정 변수가 있을 때마다 Postman에서 교환하는 값을 추가할 수 있습니다. 예를 들어 Postman에서 `{{admin-key}}` 기호를 `admin-key`에 설정한 현재 값으로 바꿉니다. Postman은 URL, 헤더, 요청 본문 등에서 이 대체를 수행합니다. 

에 대 한 값을 얻으려면 `admin-key` 앞에서 적어둔 Azure Cognitive Search admin api 키를 사용 합니다. `search-service-name`을 사용 하는 Azure Cognitive Search 서비스의 이름으로 설정 합니다. `storage-connection-string`저장소 계정의 **액세스 키** 탭에서 값을 사용 하 여 설정 하 고, `storage-container-name` 암호화 된 파일이 저장 되는 저장소 계정의 blob 컨테이너 이름으로 설정 합니다. `function-uri`이전에 적어둔 Azure 함수 URL로 설정 하 고, `function-code` 이전에 적어둔 azure 함수 호스트 키 코드로 설정 합니다. 다른 값에는 기본값을 그대로 둘 수 있습니다.

![Postman 앱 변수 탭](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Postman의 변수 창")


| 변수    | 가져올 수 있는 위치 |
|-------------|-----------------|
| `admin-key` | Azure Cognitive Search 서비스의 **키** 페이지.  |
| `search-service-name` | Azure Cognitive Search 서비스의 이름. URL은 `https://{{search-service-name}}.search.windows.net`입니다. | 
| `storage-connection-string` | 스토리지 계정의 **액세스 키** 탭에서 **key1** > **연결 문자열** 을 차례로 선택합니다. | 
| `storage-container-name` | 인덱싱할 암호화 된 파일을 포함 하는 blob 컨테이너의 이름입니다. | 
| `function-uri` |  기본 페이지의 **Essentials** 아래에 있는 Azure 함수 | 
| `function-code` | Azure 함수에서 **앱 키** 로 이동한 후 클릭 하 여 **기본** 키를 표시 하 고 값을 복사 합니다. | 
| `api-version` | **2020-06-30** 으로 둡니다. |
| `datasource-name` | **암호화 된-blob-ds** 로 둡니다. | 
| `index-name` | **암호화 된 blob-idx** 로 남겨 둡니다. | 
| `skillset-name` | **암호화 된 blob-ss** 로 둡니다. | 
| `indexer-name` | **Ixr** 로 남겨 둡니다. | 

### <a name="review-the-request-collection-in-postman"></a>Postman에서 요청 컬렉션 검토

이 가이드를 실행 하는 경우 4 개의 HTTP 요청을 발급 해야 합니다. 

- **인덱스를 만들기 위한 PUT 요청** : 이 인덱스는 Azure Cognitive Search에서 사용하고 반환하는 데이터를 포함합니다.
- **데이터 원본을 만들기 위한 POST 요청** :이 데이터 원본은 Azure Cognitive Search 서비스를 저장소 계정에 연결 하 고 따라서 암호화 된 blob 파일에 연결 합니다. 
- **기술을 만들기 위한 PUT 요청** : 기술은 blob 파일 데이터를 암호 해독 하는 Azure Function에 대 한 사용자 지정 기술 정의를 지정 하 고, 문서를 해독 한 후 각 문서에서 텍스트를 추출 하는 [Documentextractionskill](cognitive-search-skill-document-extraction.md) 지정 합니다.
- **인덱서를 만들기 위한 PUT 요청** : 인덱서를 실행하면 데이터를 읽고, 기술 세트를 적용하고, 결과를 저장합니다. 이 요청은 마지막으로 실행해야 합니다.

[소스 코드](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) 에는 네 개의 요청 뿐만 아니라 몇 가지 유용한 추가 요청을 포함 하는 postman 컬렉션이 포함 되어 있습니다. 요청을 실행 하려면 Postman에서 요청에 대 한 탭을 선택 하 고 각각에 대해 **보내기** 를 선택 합니다.

## <a name="3---monitor-indexing"></a>3-인덱싱 모니터링

인덱싱 및 보강은 인덱서 만들기 요청을 제출하는 즉시 시작됩니다. 저장소 계정에 있는 문서 수에 따라 인덱싱이 다소 시간이 걸릴 수 있습니다. 인덱서가 아직 실행 중인지 확인 하려면 Postman collection의 일부로 제공 되는 **인덱서 상태 가져오기** 요청을 사용 하 고 응답을 검토 하 여 인덱서가 실행 중인지 확인 하거나 오류 및 경고 정보를 확인 합니다.  

무료 계층을 사용 하는 경우 다음 메시지가 필요 합니다. `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` . 이 메시지는 무료 계층의 blob 인덱싱이 [문자 추출에 32k 제한이](search-limits-quotas-capacity.md#indexer-limits)있기 때문에 나타납니다. 더 높은 계층에서는 이 데이터 세트에 대한 이 메시지가 표시되지 않습니다. 

## <a name="4---search"></a>4-검색

인덱서 실행이 완료 되 면 일부 쿼리를 실행 하 여 데이터가 성공적으로 해독 되 고 인덱싱되는 지 확인할 수 있습니다. 포털에서 Azure Cognitive Search 서비스로 이동 하 고, [검색 탐색기](search-explorer.md) 를 사용 하 여 인덱싱된 데이터에 대해 쿼리를 실행 합니다.

## <a name="next-steps"></a>다음 단계

이제 암호화 된 파일을 성공적으로 인덱싱 했으므로 [인식 기술을 추가 하 여이 파이프라인에서 반복할](cognitive-search-defining-skillset.md)수 있습니다. 이를 통해 데이터에 대 한 추가 정보를 보강 하 고 얻을 수 있습니다.

이중 암호화 된 데이터로 작업 하는 경우 Azure Cognitive Search에서 사용할 수 있는 인덱스 암호화 기능을 조사할 수 있습니다. 인덱서는 인덱싱을 위해 해독 된 데이터를 요구 하지만 인덱스가 있으면 고객이 관리 하는 키를 사용 하 여 암호화할 수 있습니다. 이렇게 하면 휴지 상태에서 데이터가 항상 암호화 됩니다. 자세한 내용은 [Azure Cognitive Search에서 데이터 암호화에 대 한 고객 관리 키 구성](search-security-manage-encryption-keys.md)을 참조 하세요.