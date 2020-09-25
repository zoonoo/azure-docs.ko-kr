---
title: 자습서 - Azure Spring Cloud에 기존 사용자 지정 도메인 매핑
description: 기존 사용자 지정 DNS(Distributed Name Service) 이름을 Azure Spring Cloud에 매핑하는 방법
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cd10421ddcf752625b8040e1afa4e7b15f142ce2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885678"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Azure Spring Cloud에 기존 사용자 지정 도메인 매핑

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

DNS(Domain Name Service)는 네트워크를 통해 네트워크 노드 이름을 저장하는 기술입니다. 이 자습서에서는 CNAME 레코드를 사용하여 www.contoso.com 같은 도메인을 매핑합니다. 이 기술은 인증서를 사용하여 사용자 지정 도메인을 보호하며 SSL(Secure Sockets Layer)이라고도 하는 TLS(전송 계층 보안)를 적용하는 방법을 보여줍니다. 

인증서는 웹 트래픽을 암호화합니다. 이러한 TLS/SSL 인증서는 Azure Key Vault에 저장할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
* Azure Spring Cloud에 배포된 애플리케이션. [빠른 시작: Azure Portal을 사용하여 기존 Azure Spring Cloud 애플리케이션 시작](spring-cloud-quickstart.md)을 참조하거나 기존 앱을 사용하세요.
* GoDaddy 같은 도메인 공급자의 DNS 레지스트리에 대한 액세스 권한이 있는 도메인
* 타사 공급자의 프라이빗 인증서(즉, 자체 서명된 인증서) 인증서는 도메인과 일치해야 합니다.
* 배포된 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 인스턴스

## <a name="import-certificate"></a>인증서 가져오기 
인증서를 가져오는 절차를 수행하려면 PEM 또는 PFX로 인코딩된 파일이 디스크에 있어야 하며 프라이빗 키가 필요합니다. 

인증서를 주요 자격 증명 모음에 업로드하려면 다음을 수행합니다.
1. 키 자격 증명 모음 인스턴스로 이동합니다.
1. 왼쪽 탐색 창에서 **인증서**를 클릭합니다.
1. 위쪽 메뉴에서 **생성/가져오기**를 클릭합니다.
1. **인증서 만들기** 대화 상자의 **인증서를 만드는 방법**에서 `Import`를 선택합니다.
1. **인증서 파일 업로드**에서 인증서 위치로 이동하여 선택합니다.
1. **암호**에 인증서의 프라이빗 키를 입력합니다.
1. **만들기**를 클릭합니다.

    ![인증서 가져오기 1](./media/custom-dns-tutorial/import-certificate-a.png)

인증서를 가져오기 전에 키 자격 증명 모음에 Azure Spring Cloud 액세스 권한을 부여하려면 다음을 수행합니다.
1. 키 자격 증명 모음 인스턴스로 이동합니다.
1. 왼쪽 탐색 창에서 **액세스 정책**을 클릭합니다.
1. 위쪽 메뉴에서 **액세스 정책 추가**를 클릭합니다.
1. 정보를 입력하고, **추가** 단추를 클릭한 다음, 액세스 정책을 **저장**합니다.

| 비밀 권한 | 인증서 권한 | 보안 주체 선택 |
|--|--|--|
| 가져오기, 목록 | 가져오기, 목록 | Azure Spring Cloud 도메인-관리 |

![인증서 가져오기 2](./media/custom-dns-tutorial/import-certificate-b.png)

또는 Azure CLI를 사용하여 Azure Spring Cloud 액세스를 키 자격 증명 모음에 부여할 수 있습니다.

다음 명령을 통해 개체 ID를 가져옵니다.
```
az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId
```

키 자격 증명 모음에 Azure Spring Cloud 읽기 액세스 권한을 부여하고, 다음 명령에서 개체 ID를 바꿉니다.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list --secret-permissions get list
``` 

Azure Spring Cloud로 인증서를 가져오려면 다음을 수행합니다.
1. 서비스 인스턴스로 이동합니다. 
1. 앱의 왼쪽 탐색 창에서 **TLS/SSL 설정**을 선택합니다.
1. **Key Vault 인증서 가져오기**를 클릭합니다.

    ![인증서 가져오기](./media/custom-dns-tutorial/import-certificate.png)

또는 Azure CLI를 사용하여 인증서를 가져올 수 있습니다.

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> 이전 인증서 가져오기 명령을 실행하기 전에 키 자격 증명 모음에 Azure Spring Cloud 액세스 권한을 부여해야 합니다. 그렇지 않은 경우 다음 명령을 실행하여 액세스 권한을 부여할 수 있습니다.

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

인증서를 성공적으로 가져왔다면 **프라이빗 키 인증서** 목록에 표시됩니다.

![프라이빗 키 인증서](./media/custom-dns-tutorial/key-certificates.png)

또는 Azure CLI를 사용하여 인증서 목록을 표시할 수 있습니다.

```
az spring-cloud certificate list
```

> [!IMPORTANT] 
> 이 인증서로 사용자 지정 도메인을 보호하려면 여전히 인증서를 특정 도메인에 바인딩해야 합니다. 이 문서의 **SSL 바인딩** 섹션에 설명된 단계를 따릅니다.

## <a name="add-custom-domain"></a>사용자 지정 도메인 추가
CNAME 레코드를 사용하여 사용자 지정 DNS 이름을 Azure Spring Cloud에 매핑할 수 있습니다. 

> [!NOTE] 
> A 레코드는 지원되지 않습니다. 

### <a name="create-the-cname-record"></a>CNAME 레코드 만들기
DNS 공급자로 이동한 후 CNAME 레코드를 추가하여 도메인을 <service_name>.azuremicroservices.io에 매핑합니다. 여기서 <service_name>은 Azure Spring Cloud 인스턴스의 이름입니다. 와일드카드 도메인 및 하위 도메인을 지원합니다. CNAME을 추가한 후 DNS 레코드 페이지는 다음 예제와 비슷합니다. 

![DNS 레코드 페이지](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Azure Spring Cloud 앱에 사용자 지정 도메인 매핑
Azure Spring Cloud에 아직 애플리케이션이 없으면 [빠른 시작: Azure Portal을 사용하여 기존 Azure Spring Cloud 애플리케이션 시작](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master)의 지침을 따릅니다.

애플리케이션 페이지로 이동합니다.

1. **사용자 지정 도메인**을 선택합니다.
2. **사용자 지정 도메인 추가**를 선택합니다. 

    ![사용자 지정 도메인](./media/custom-dns-tutorial/custom-domain.png)

3. CNAME 레코드를 추가한 정규화된 도메인 이름을 입력합니다(예: www.contoso.com). 호스트 이름 레코드 형식이 CNAME으로 설정되었는지 확인합니다(<service_name>.azuremicroservices.io).
4. **유효성 검사**를 클릭하여 **추가** 단추를 사용하도록 설정합니다.
5. **추가**를 클릭합니다.

    ![사용자 지정 도메인 추가](./media/custom-dns-tutorial/add-custom-domain.png)

또는 Azure CLI를 사용하여 사용자 지정 도메인을 추가할 수 있습니다.
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> 
```

하나의 앱이 여러 도메인을 포함할 수 있지만, 하나의 도메인은 하나의 앱에만 매핑할 수 있습니다. 사용자 지정 도메인을 앱에 성공적으로 매핑했다면 사용자 지정 도메인 테이블에 표시됩니다.

![사용자 지정 도메인 테이블](./media/custom-dns-tutorial/custom-domain-table.png)

또는 Azure CLI를 사용하여 사용자 지정 도메인의 목록을 표시할 수 있습니다.
```
az spring-cloud app custom-domain list --app <app name> 
```

> [!NOTE]
> 사용자 지정 도메인에 **안전하지 않음**이라는 레이블이 있으면 아직 SSL 인증서에 바인딩되지 않았다는 뜻입니다. 브라우저에서 사용자 지정 도메인으로 전송되는 HTTPS 요청은 오류 또는 경고를 수신합니다.

## <a name="add-ssl-binding"></a>SSL 바인딩 추가
위의 그림처럼 사용자 지정 도메인 테이블에서 **SSL 바인딩 추가**를 선택합니다.  
1. **인증서**를 선택하거나 가져옵니다.
1. **저장**을 클릭합니다.

    ![SSL 바인딩 추가 1](./media/custom-dns-tutorial/add-ssl-binding.png)

또는 Azure CLI를 사용하여 **ssl 바인딩을 추가**할 수 있습니다.
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

SSL 바인딩을 성공적으로 추가한 후에는 도메인 상태가 안전함을 나타내는 **정상**으로 표시됩니다. 

![SSL 바인딩 추가 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS 적용
기본적으로 여전히 누구나 HTTP를 사용하여 앱에 액세스할 수 있지만, 모든 HTTP 요청을 HTTPS 포트로 리디렉션할 수 있습니다.

앱 페이지의 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 선택합니다. **HTTPS만 사용**을 *True*로 설정합니다.

![SSL 바인딩 추가 3](./media/custom-dns-tutorial/enforce-http.png)

또는 Azure CLI를 사용하여 HTTPS를 적용할 수 있습니다.
```
az spring-cloud app update -name <app-name> --https-only <true|false> -g <resource group> --service <service-name>
```

작업이 완료되면 앱을 가리키는 HTTPS URL 중 하나로 이동합니다. HTTP URL은 작동하지 않습니다.

## <a name="see-also"></a>참고 항목
* [Azure Key Vault란?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [인증서 가져오기](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Azure CLI를 사용하여 Spring Cloud 앱 시작](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

