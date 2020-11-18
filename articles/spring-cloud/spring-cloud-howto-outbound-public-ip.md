---
title: Azure 스프링 클라우드에서 아웃 바운드 공용 IP 주소를 식별 하는 방법
description: 데이터베이스, 저장소, Key Vault 등의 외부 리소스와 통신 하기 위해 고정 아웃 바운드 공용 IP 주소를 확인 하는 방법입니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663777"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 아웃 바운드 공용 IP 주소를 확인 하는 방법

이 페이지에서는 Azure 스프링 클라우드 응용 프로그램의 고정 아웃 바운드 공용 IP 주소를 보는 방법을 설명 합니다.  공용 Ip는 데이터베이스, 저장소 및 키 자격 증명 모음과 같은 외부 리소스와 통신 하는 데 사용 됩니다.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 IP 주소가 작동 하는 방식

Azure 스프링 클라우드 서비스에는 하나 이상의 아웃 바운드 공용 IP 주소가 있습니다. 아웃 바운드 공용 IP 주소의 수는 계층 및 기타 요인에 따라 달라질 수 있습니다. 

아웃 바운드 공용 IP 주소는 일반적으로 일정 하지만 동일 하 게 유지 되지만 예외가 있습니다.

## <a name="when-outbound-ips-change"></a>아웃바운드 IP가 변경되는 경우

각 Azure 스프링 클라우드 인스턴스에는 지정 된 시간에 설정 된 아웃 바운드 공용 IP 주소 수가 있습니다. 백 엔드 데이터베이스와 같은 응용 프로그램의 모든 아웃 바운드 연결에서는 아웃 바운드 공용 IP 주소 중 하나를 원본 IP 주소로 사용 합니다. IP 주소는 런타임에 임의로 선택 되므로 백 엔드 서비스는 모든 아웃 바운드 IP 주소에 대 한 방화벽을 열어야 합니다.

다음 작업 중 하나를 수행 하는 경우 아웃 바운드 공용 Ip의 수가 변경 됩니다.

- 계층 간에 Azure 스프링 클라우드 인스턴스를 업그레이드 합니다.
- 비즈니스 요구에 대 한 더 많은 아웃 바운드 공용 Ip에 대 한 지원 티켓을 올립니다.

## <a name="find-outbound-ips"></a>아웃바운드 IP 찾기

Azure Portal에서 현재 서비스 인스턴스가 사용 하는 아웃 바운드 공용 IP 주소를 찾으려면 인스턴스의 왼쪽 탐색 창에서 **네트워킹** 을 클릭 합니다. 이러한 필드는 **아웃 바운드 IP 주소** 필드에 나열 됩니다.

에서 다음 명령을 실행 하 여 동일한 정보를 찾을 수 있습니다 Cloud Shell

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
* [Azure 리소스의 관리 ID에 대해 자세히 알아보기](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Azure 스프링 클라우드의 key vault에 대 한 자세한 정보](spring-cloud-tutorial-managed-identities-key-vault.md)
