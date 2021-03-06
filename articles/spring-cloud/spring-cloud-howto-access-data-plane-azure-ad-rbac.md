---
title: Azure AD, RBAC를 사용 하 여 데이터 평면 액세스
description: Azure Active Directory 역할 기반 액세스 제어를 사용 하 여 데이터 평면에 액세스 하는 방법입니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220135"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>Azure Active Directory 및 역할 기반을 사용 하 여 데이터 평면 액세스 Access Control

이 문서에서는 AAD (Azure Active Directory) RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure 스프링 클라우드 구성 서버 및 서비스 레지스트리 끝점에 액세스 하는 방법을 설명 합니다.

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>AAD 사용자/그룹, MSI 또는 서비스 주체에 역할 할당

AAD 및 RBAC를 사용 하려면 다음 절차에 따라 *Azure 스프링 클라우드 데이터 판독기* 역할을 사용자, 그룹 또는 서비스 주체에 할당 해야 합니다.

1. 서비스 인스턴스의 서비스 개요 페이지로 이동 합니다.

2. **Access Control (IAM)** 을 클릭 하 여 액세스 제어 블레이드를 엽니다.

3. **추가** 단추를 클릭 하 고 **역할 할당을 추가** 합니다 (권한 부여는 추가 해야 할 수 있음).

4. **역할** 에서 *Azure 스프링 클라우드 데이터 판독기* 를 찾아 선택 합니다.
5. 사용자 유형에 따라 또는에 대 한 액세스 권한을 할당 `User, group, or service principal` `User assigned managed identity` 합니다. 사용자를 검색 하 고 선택 합니다.  
6. `Save`을 클릭합니다.

   ![할당-역할](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>데이터 평면 액세스

AAD 사용자에 게 *Azure 스프링 클라우드 데이터 판독기* 역할이 할당 된 후 고객은 사용자, 서비스 주체 또는 관리 id를 사용 하 여 Azure CLI에 로그인 할 수 있습니다.  액세스 토큰을 가져오려면 [Azure CLI 인증](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) 을 참조 하세요.  다음 명령을 사용 합니다.

```azurecli
az login
az account get-access-token
```

현재 CLI는 구성 서버 및 서비스 레지스트리의 기본 끝점을 지원 합니다. 자세한 내용은 [프로덕션 준비 끝점](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)을 참조 하세요. 

또한 고객은 끝점에 액세스 하 여 구성 서버 및 서비스 레지스트리의 지원 되는 끝점에 대 한 전체 목록을 가져올 수 있습니다.
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

헤더의 액세스 토큰은 권한 부여를 제공 합니다. "GET" 메서드만 지원 됩니다.

예를 들어와 같은 끝점에 액세스 *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* 하 여 eureka의 상태를 확인 합니다.

다양 한 루트 끝점은 다양 한 클라우드 유형에 따라 아래에 표시 됩니다.

| 클라우드          | 루트 끝점              |
| -------------- | -------------------------- |
| 공용         | svc.azuremicroservices.io  |
| Mooncake/중국 | svc.microservices.azure.cn |

응답이 *401 인증* 되지 않은 경우 역할이 성공적으로 할당 되었는지 확인 하십시오.  역할이 적용 되는 데 몇 분 정도 걸리며 액세스 토큰이 만료 되지 않았는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
* [Azure CLI 인증](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [프로덕션 준비 끝점](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>참고 항목
* [역할 및 권한 만들기](spring-cloud-howto-permissions.md)