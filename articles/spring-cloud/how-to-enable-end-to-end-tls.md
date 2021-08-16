---
title: 엔드투엔드 전송 계층 보안 사용
titleSuffix: Azure Spring Cloud
description: 애플리케이션에 대한 엔드투엔드 전송 계층 보안을 사용하도록 설정하는 방법.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2fce1e619435a8f637619f0fa3ec3a619163c60b
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108135152"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>애플리케이션에 대한 엔드투엔드 TLS 사용

이 토픽에서는 엔드투엔드 SSL/TLS를 사용하도록 설정하여 수신 컨트롤러에서 HTTPS를 지원하는 애플리케이션으로 향하는 트래픽을 보호하는 방법을 보여줍니다. 엔드투엔드 TLS를 사용 설정하고 키 자격 증명 모음에서 인증서를 로드한 이후에는 Azure Spring Cloud 내의 모든 통신이 TLS의 보호를 받습니다.

   ![TLS의 보호를 받는 통신 그래프.](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>사전 요구 사항 

- 배포된 Azure Spring Cloud 인스턴스. [Azure CLI를 통해 배포에 대한 빠른 시작](./quickstart.md)을 수행하여 시작하세요.
- 엔드투엔드 TLS가 익숙하지 않은 경우 [엔드투엔드 TLS 샘플](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl)을 참조하세요.
- 필요한 인증서를 Spring Boot 앱에 안전하게 로드하기 위해 [KeyVault Spring Boot Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates)를 사용할 수 있습니다.


## <a name="enable-end-to-end-tls-on-an-existing-app"></a>기존 앱에서 엔드투엔드 TLS 사용

`az spring-cloud app update --enable-end-to-end-tls` 명령을 사용하여 앱에서 엔드투엔드 TLS를 사용하거나 사용하지 않도록 설정합니다.

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>사용자 지정 도메인을 바인딩할 때 엔드투엔드 TLS 사용

`az spring-cloud app custom-domain update --enable-end-to-end-tls` 또는 `az spring-cloud app custom-domain bind --enable-end-to-end-tls` 명령을 사용하여 앱에서 엔드투엔드 TLS를 사용하거나 사용하지 않도록 설정합니다.

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-using-azure-portal"></a>Azure Portal을 통해 엔드투엔드 TLS 사용
[Azure Portal](https://portal.azure.com/)에서 엔드투엔드 TLS를 사용하도록 설정하려면 먼저 앱을 만든 다음, 해당 기능을 사용하도록 설정합니다.

1. 평소처럼 포털에서 앱을 만듭니다. 포털에서 해당 앱으로 이동합니다.
2. 왼쪽 탐색 창에서 **설정** 그룹이 나올 때까지 아래로 스크롤합니다.
3. **엔드투엔드 TLS** 를 선택합니다.
4. **엔드투엔드 TLS** 를 *예* 로 전환합니다.

 ![포털에서 엔드투엔드 TLS 사용](./media/enable-end-to-end-tls/enable-tls.png)


## <a name="verify-end-to-end-tls-status"></a>엔드투엔드 TLS 상태 확인

`az spring-cloud app show` 명령을 사용하여 `enableEndToEndTls` 값을 확인합니다.
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>다음 단계
* [Config Server 및 서비스 레지스트리 액세스](how-to-access-data-plane-azure-ad-rbac.md)