---
title: Azure Spring Cloud의 앱 및 배포
description: 이 토픽에서는 Azure Spring Cloud에서의 애플리케이션과 배포의 차이점에 대해 설명합니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8a543d3f6a875c41b3657ccc947c6f697bbcfd09
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108129120"
---
# <a name="app-and-deployment-in-azure-spring-cloud"></a>Azure Spring Cloud의 앱 및 배포

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

**앱** 과 **배포** 는 Azure Spring Cloud 리소스 모델의 두 가지 주요 개념입니다. Azure Spring Cloud에서 *앱* 은 하나의 비즈니스 앱 또는 하나의 마이크로 서비스를 추상화한 것입니다.  *앱* 으로 배포된 하나의 코드 또는 이진의 버전이 *배포* 에서 실행됩니다.  앱은 *Azure Spring Cloud 서비스 인스턴스*, 또는 다음 그림처럼 단순히 *서비스 인스턴스* 에서 실행됩니다.

 ![앱 및 배포](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

단일 Azure 구독 내에 여러 서비스 인스턴스를 포함할 수 있지만, Azure Spring Cloud 서비스는 비즈니스 앱 또는 마이크로 서비스를 구성하는 모든 앱이 단일 서비스 인스턴스 내에 있을 때 사용하기가 가장 쉽습니다.

Azure Spring Cloud 표준 계층을 사용하면 하나의 앱이 하나의 프로덕션 배포와 하나의 스테이징 배포를 포함할 수 있으므로, 파란색/녹색 배포를 쉽게 수행할 수 있습니다.

## <a name="app"></a>앱
다음 기능/속성은 앱 수준에서 정의됩니다.

| 기능 | Description |
|:--|:----------------|
| 공용</br>엔드포인트 | 엡에 액세스할 수 있는 URL |
| 사용자 지정</br>도메인 | 사용자 지정 도메인을 보호하는 CNAME 레코드 |
| 서비스</br>바인딩 | 다른 Azure 서비스와의 기본 연결 |
| 관리</br>ID | Azure Active Directory의 관리 ID를 사용하면 앱에서 Azure AD로 보호되는 다른 리소스(예: Azure Key Vault)에 쉽게 액세스할 수 있습니다. |
| 영구적</br>Storage | 데이터가 앱 다시 시작 후에도 유지되도록 하는 설정 |

## <a name="deployment"></a>배포

다음 기능/속성은 배포 수준에서 정의되며 프로덕션/스테이징 배포를 교체할 때 교환됩니다.

| 기능 | 설명 |
|:--|:----------------|
| CPU | 앱 인스턴스당 vcore 수 |
| 메모리 | 앱 인스턴스당 메모리(Gb)|
| 인스턴스</br>개수 | 수동 또는 자동으로 설정되는 앱 인스턴스 수 |
| 자동 크기 조정 | 미리 정의된 규칙 및 일정에 따라 인스턴스 수 자동 조정 |
| JVM</br>옵션 | JVM 옵션 설정  |
| Environment</br>변수 | 환경 변수 설정 |
| 런타임</br>버전 | Java 8/Java 11|

## <a name="restrictions"></a>제한

* **앱에 하나의 프로덕션 배포는 포함돼야 합니다**: 프로덕션 배포의 삭제는 API에 의해 차단됩니다. 삭제하기 전에 스테이징으로 바꾸어야 합니다.
* **앱에는 최대 두 개의 배포가 포함될 수 있습니다**: 세 개 이상의 배포를 만드는 작업은 API에 의해 차단됩니다. 기존 프로덕션 또는 스테이징 배포에 새 이진을 배포합니다.
* **배포 관리는 기본 계층에서 사용할 수 없습니다**: 파란색-녹색 배포 기능에는 표준 계층을 사용합니다.

## <a name="see-also"></a>추가 정보
* [Azure Spring Cloud에서 스테이징 환경 설정](./how-to-staging-environment.md)