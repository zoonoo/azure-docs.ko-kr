---
title: Virtual network의 Azure 스프링 클라우드 액세스 앱
description: Virtual network의 Azure 스프링 클라우드에서 앱에 액세스 합니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878711"
---
# <a name="access-your-application-in-a-private-network"></a>개인 네트워크에서 애플리케이션에 액세스

이 문서에서는 개인 네트워크에서 응용 프로그램에 대 한 끝점에 액세스 하는 방법을 설명 합니다.  액세스 권한을 얻으려면 개인 FQDN (정규화 된 도메인 이름)을 IP 주소로 변환/확인 하기 위해 구독에서 **Azure 사설 DNS 영역** 을 만들어야 합니다.

Azure 스프링 클라우드 서비스 인스턴스의 응용 프로그램에 대 한 **끝점 할당** 을 가상 네트워크에 배포 하는 경우 끝점은 개인 FQDN입니다. 도메인은 개인 네트워크 에서만 액세스할 수 있습니다. 앱 및 서비스는 응용 프로그램 끝점을 사용 합니다. 여기에는 [앱 및 배포 보기](spring-cloud-howto-staging-environment.md#view-apps-and-deployments)에 설명 된 **테스트 끝점이** 포함 됩니다. [실시간으로 Azure 스프링 클라우드 앱 로그 스트림](spring-cloud-howto-log-streaming.md)에 설명 된 **로그 스트리밍이** 개인 네트워크 내 에서만 작동 합니다.

## <a name="create-a-private-dns-zone"></a>프라이빗 DNS 영역 만들기

다음 절차에서는 개인 네트워크의 응용 프로그램에 대 한 개인 DNS 영역을 만듭니다.

1. Azure Portal을 엽니다. 위쪽 검색 상자에서 **사설 DNS 영역** 을 검색 하 고 결과에서 **사설 DNS 영역** 을 선택 합니다.

2. **사설 DNS 영역** 페이지에서 **+ 추가** 를 선택 합니다.

3. **사설 DNS 영역 만들기** 페이지에서 양식을 작성 합니다. 영역 **이름** 으로 **<span>private.azuremicroservices.io</span>** 을 입력 합니다.

4. **검토 + 만들기** 를 선택합니다.

5. **만들기** 를 선택합니다.

영역을 만드는 데 몇 분이 걸릴 수 있습니다.

## <a name="link-the-virtual-network"></a>가상 네트워크 연결

가상 네트워크에 개인 DNS 영역을 연결 하려면 가상 네트워크 링크를 만들어야 합니다.

1. 위에서 만든 개인 DNS 영역 리소스를 선택 합니다. **<span>private.azuremicroservices.io</span>** 

2. 왼쪽 창에서 **가상 네트워크 링크** 를 선택합니다.

3. **추가** 를 선택합니다.

4. **링크 이름** 에 대해 **azure-스프링-dns 링크** 를 입력 합니다.

5. **Virtual network** 의 경우 [azure Virtual Network에 Azure 스프링 클라우드 배포 (VNet 주입)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)에서 설명한 대로 만든 가상 네트워크를 선택 합니다.

    ![가상 네트워크 링크 추가](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. **확인** 을 클릭합니다.

## <a name="create-dns-record"></a>DNS 레코드 만들기

DNS를 변환/확인 하는 데 개인 DNS 영역을 사용 하려면 영역에 "A" 형식 레코드를 만들어야 합니다.

1. [Azure virtual network (VNet 주입)에서 Azure 스프링 클라우드 배포](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)에 설명 된 대로 만든 가상 네트워크 리소스를 선택 합니다.

2. **연결 된 장치** 검색 상자에 *kubernetes* 를 입력 합니다.

3. 필터링 된 결과에서 서비스 인스턴스의 서비스 런타임 **서브넷** 에 연결 된 **장치** 를 찾아 해당 **IP 주소** 를 복사 합니다. 이 샘플에서 IP 주소는 *10.1.0.7* 입니다.

    [![DNS 레코드 ](media/spring-cloud-access-app-vnet/create-dns-record.png) 만들기](media/spring-cloud-access-app-vnet/create-dns-record.png)

또는 다음 az CLI 명령을 사용 하 여 IP를 페치할 수 있습니다.

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. 위에서 만든 개인 DNS 영역 리소스를 선택 합니다. **<span>private.azuremicroservices.io</span>**.

5. **+ 레코드 집합** 을 선택합니다.

6. **레코드 집합 추가** 에서 다음 정보를 입력 하거나 선택 합니다.

    |설정     |값                                                                      |
    |------------|---------------------------------------------------------------------------|
    |속성        |*\** 를 입력합니다.                                                                 |
    |Type        |선택                                                                |
    |TTL         |*1* 을 입력 합니다.                                                                  |
    |TTL 단위    |**시간** 선택                                                           |
    |IP 주소  |3 단계에서 복사한 IP 주소를 입력 합니다. 샘플에서 *10.1.0.7* 을 입력 합니다.    |

    그런 다음 **확인** 을 선택합니다.

    ![개인 DNS 영역 레코드 추가](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>응용 프로그램에 대 한 개인 FQDN 할당

[마이크로 서비스 응용 프로그램 빌드 및 배포](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)의 절차를 수행 하면 응용 프로그램에 대 한 개인 FQDN을 할당할 수 있습니다.

1. 가상 네트워크에 배포 된 Azure 스프링 클라우드 서비스 인스턴스를 선택 하 고 왼쪽 메뉴에서 **앱** 탭을 엽니다.

2. 응용 프로그램을 선택 하 여 **개요** 페이지를 표시 합니다.

3. **끝점 할당** 을 선택 하 여 전용 FQDN을 응용 프로그램에 할당 합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

    ![개인 끝점 할당](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. 이제 할당 된 개인 FQDN (레이블이 지정 된 **URL**)을 사용할 수 있습니다. 이 파일은 개인 네트워크 내 에서만 액세스할 수 있으며 인터넷에서는 액세스할 수 없습니다.

## <a name="access-application-private-fqdn"></a>응용 프로그램 전용 FQDN 액세스

할당 후 개인 네트워크에서 응용 프로그램의 개인 FQDN에 액세스할 수 있습니다. 예를 들어 동일한 가상 네트워크 또는 피어 링 가상 네트워크에서 jumpbox 컴퓨터를 만들고 해당 jumpbox 컴퓨터에 개인 FQDN에 액세스할 수 있습니다.

![Vnet에서 개인 끝점 액세스](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>다음 단계

- [Application Gateway 및 Azure 방화벽을 사용 하 여 인터넷에 응용 프로그램 노출](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>참고 항목

- [VNET에서 Azure Spring Cloud 문제 해결](spring-cloud-troubleshooting-vnet.md)
- [VNET에서 Azure Spring Cloud를 실행하는 고객의 책임](spring-cloud-vnet-customer-responsibilities.md)