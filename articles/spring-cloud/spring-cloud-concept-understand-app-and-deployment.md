---
title: Azure 스프링 클라우드의 앱 및 배포 이해
description: Azure 스프링 클라우드의 응용 프로그램과 배포의 차이점
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: c053a7830e02eb7c460bd030ca3c6a10c00ea78a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323640"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Azure 스프링 클라우드의 앱 및 배포 이해

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

**앱** 및 **배포** 는 Azure 스프링 클라우드의 리소스 모델에 있는 두 가지 주요 개념입니다. Azure 스프링 클라우드에서 *앱* 은 하나의 비즈니스 앱 또는 하나의 마이크로 서비스를 추상화 한 것입니다.  *응용 프로그램이* *배포*에서 실행 될 때 하나의 코드 또는 이진 버전이 배포 됩니다.  앱은 다음에 표시 된 것 처럼 *Azure 스프링 클라우드 서비스 인스턴스*또는 간단히 *서비스 인스턴스로*실행 됩니다.

 ![앱 및 배포](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

단일 Azure 구독 내에 여러 서비스 인스턴스를 포함할 수 있지만, Azure 스프링 클라우드 서비스는 비즈니스 앱 또는 마이크로 서비스를 구성 하는 모든 앱이 단일 서비스 인스턴스 내에 있는 경우 사용 하기 쉽습니다.

Azure 스프링 클라우드 표준 계층을 사용 하면 한 앱에서 하나의 프로덕션 배포와 하나의 스테이징 배포를 수행할 수 있으므로,이를 통해 파란색/녹색 배포를 쉽게 수행할 수 있습니다.

## <a name="app"></a>앱
다음 기능/속성은 앱 수준에 정의 됩니다.

| 기능 | 설명 |
|:--|:----------------|
| 공용</br>엔드포인트 | 앱에 액세스 하기 위한 URL입니다. |
| 사용자 지정</br>도메인 | 사용자 지정 도메인을 보호 하는 CNAME 레코드 |
| 서비스</br>바인딩 | 다른 Azure 서비스와의 기본 연결 |
| 관리 대상</br>ID | Azure Active Directory로 관리 되는 id를 사용 하면 앱에서 Azure Key Vault 같은 다른 Azure AD 보호 리소스에 쉽게 액세스할 수 있습니다. |
| 영구적</br>스토리지 | 앱을 다시 시작 하지 않아도 데이터를 유지할 수 있도록 설정 |

## <a name="deployment"></a>배포

다음 기능/속성은 배포 수준에서 정의 되며 프로덕션/스테이징 배포를 교체할 때 교환 됩니다.

| 기능 | 설명 |
|:--|:----------------|
| CPU | 앱 인스턴스당 vcore 수 |
| 메모리 | 앱 인스턴스당 Gb 메모리|
| 인스턴스</br>개수 | 앱 인스턴스 수, 수동 또는 자동으로 설정 |
| 자동 크기 조정 | 미리 정의 된 규칙 및 일정에 따라 자동으로 인스턴스 개수 조정 |
| JVM</br>옵션 | JVM 옵션 설정  |
| Environment</br>variables | 환경 변수 설정 |
| 런타임</br>버전 | Java 8/Java 11|

## <a name="restrictions"></a>제한

* **앱은 프로덕션 배포를 하나 포함 해야**합니다. 프로덕션 배포 삭제는 API에 의해 차단 됩니다. 삭제 하기 전에 준비로 바꾸어야 합니다.
* **앱에는 최대 두 개의 배포가 있을 수 있습니다**. 두 개 이상의 배포를 만드는 작업은 API에 의해 차단 됩니다. 기존 프로덕션 또는 스테이징 배포에 새 이진 파일을 배포 합니다.
* **배포 관리는 기본 계층에서 사용할 수 없습니다**. Blue-Green 배포 기능을 사용 하려면 표준 계층을 사용 하세요.

## <a name="see-also"></a>참조
* [Azure 스프링 클라우드에서 스테이징 환경 설정](spring-cloud-howto-staging-environment.md)
