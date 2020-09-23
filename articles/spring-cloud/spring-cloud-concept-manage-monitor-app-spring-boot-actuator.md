---
title: Azure Spring Boot Actuator를 사용하여 앱 관리 및 모니터링
description: 스프링 부팅 발동기를 사용 하 여 앱을 관리 하 고 모니터링 하는 방법을 알아봅니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 93fd286aa76a0409a515abbf8c9dabd88a9a65c4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904284"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Azure Spring Boot Actuator를 사용하여 앱 관리 및 모니터링

**이 문서는 다음에 적용 됩니다.** ✔️ Java

앱에 새 이진을 배포한 후 기능을 확인 하 고 실행 중인 응용 프로그램에 대 한 정보를 볼 수 있습니다. 이 문서에서는 Azure 스프링 클라우드에서 제공 하는 테스트 끝점에서 API에 액세스 하 여 앱에 대 한 프로덕션 준비 기능을 제공 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서에서는 Azure 스프링 클라우드 서비스에서 성공적으로 배포 하 고 부팅할 수 있는 스프링 부팅 2.x 응용 프로그램이 있다고 가정 합니다.  [빠른 시작: Azure Portal을 사용 하 여 기존 Azure 스프링 클라우드 응용 프로그램 시작을](spring-cloud-quickstart.md) 참조 하세요.

## <a name="verify-app-through-test-endpoint"></a>테스트 끝점을 통해 앱 확인
1. **응용 프로그램 대시보드** 로 이동 하 고 앱을 클릭 하 여 앱 개요 페이지를 입력 합니다.

1. **개요** 창에 **테스트 끝점이**표시 됩니다.  명령줄 또는 브라우저에서이 끝점에 액세스 하 고 API 응답을 관찰 합니다.

1. 들어오는 섹션에서 사용할 **테스트 끝점** URI를 확인 합니다.

>[!TIP]
> * 앱이 프런트 엔드 페이지를 반환 하 고 상대 경로를 통해 다른 파일을 참조 하는 경우 테스트 끝점이 슬래시 (/)로 끝나는지 확인 합니다. 이렇게 하면 CSS 파일이 올바르게 로드 됩니다.
> * 브라우저에서 API를 확인 하 고 브라우저에서 로그인 자격 증명을 입력 하 여 페이지를 보려면 [URL 디코드](https://www.urldecoder.org/) 를 사용 하 여 테스트 끝점을 디코딩합니다. Url 디코드는 url을 "https:// \<username> : test.azureapps.io/" 형식으로 반환 \<password> @ \<cluster-name> \<app-name> / \<deployment-name> 합니다.  이 형식을 사용 하 여 끝점에 액세스 합니다.

## <a name="add-actuator-dependency"></a>발동기 종속성 추가

Maven 기반 프로젝트에 작동기를 추가 하려면 ' 스타터 ' 종속성을 추가 합니다.

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

새 이진 파일을 컴파일하여 앱에 배포 합니다.

## <a name="enable-production-ready-features"></a>프로덕션 준비 기능 사용
발동기 끝점을 사용 하면 응용 프로그램을 모니터링 하 고 상호 작용할 수 있습니다. 기본적으로, 스프링 부팅 응용 프로그램은 `health` 및 끝점을 노출 `info` 하 여 임의의 응용 프로그램 정보 및 상태 정보를 표시 합니다.

구성 및 구성 가능한 환경을 관찰 하려면 및 끝점도 사용 하도록 설정 해야 `env` `configgrops` 합니다.

1. 앱 **개요** 창으로 이동 하 여 설정 메뉴에서 **구성** 을 클릭 하 고 **환경 변수** 구성 페이지로 이동 합니다.
1. "키: 값" 폼에 다음 속성을 추가 합니다. 이 환경에서는 스프링 발동기 엔드포인트 "env", "health", "info"가 열립니다.

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. **저장** 단추를 클릭 하면 응용 프로그램이 자동으로 다시 시작 되 고 새 환경 변수가 로드 됩니다.

이제 앱 개요 창으로 돌아가 프로 비전 상태가 "성공"으로 변경 될 때까지 기다릴 수 있습니다.  실행 중인 인스턴스가 두 개 이상 있습니다.

> [!Note] 
> 앱을 공개에 노출 하면 이러한 발동기 끝점도 공개에 노출 됩니다. 환경 변수를 삭제 하 `management.endpoints.web.exposure.include` 고 다음을 설정 하 여 모든 끝점을 숨길 수 있습니다. `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>응용 프로그램 정보를 보려면 발동기 끝점 보기
1. 이제 url에 액세스 하 여 `"<test-endpoint>/actuator/"` 스프링 부팅 발동기에서 노출 하는 모든 끝점을 볼 수 있습니다.
1. 액세스 url `"<test-endpoint>/actuator/env"` , 앱에서 사용 하는 활성 프로필 및 로드 된 모든 환경 변수를 볼 수 있습니다.
1. 특정 환경을 검색 하려는 경우 url에 액세스 하 여 볼 수 있습니다  `"<test-endpoint>/actuator/env/{toMatch}"` .

기본 제공 되는 모든 끝점을 보려면 [끝점 노출](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 스프링 클라우드의 메트릭 이해](spring-cloud-concept-metrics.md)
* [Azure Spring Cloud의 앱 상태 이해](spring-cloud-concept-app-status.md)

