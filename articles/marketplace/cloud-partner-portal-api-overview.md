---
title: Cloud 파트너 포털 API 참조 - Microsoft 상업용 Marketplace
description: 사용할 필수 조건에 대한 설명과 Marketplace API 작업 목록입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 5813b08a14a95a8b7bbb51b3d6593fe374a83ba6
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282046"
---
# <a name="cloud-partner-portal-api-reference"></a>Cloud 파트너 포털 API 참조

> [!NOTE]
> Cloud 파트너 포털 API는 파트너 센터와 통합되었으며 파트너 센터에서 계속 작동합니다. 전환 시 사소한 변경 내용이 적용됩니다. 파트너 센터로 전환하고 나면 이 문서에 나열된 [CPP API에 대한 변경 내용](#changes-to-cpp-apis-after-the-migration-to-partner-center) 검토하여 코드가 계속 작동하는지 확인합니다. CPP API는 파트너 센터로 전환하기 전에 이미 통합된 기존 제품에만 사용해야 합니다. 새 제품은 파트너 센터 제출 API를 사용해야 합니다.

Cloud 파트너 포털 REST API를 사용하면 프로그래밍 방식으로 워크로드, 제안 및 게시자 프로필을 검색 및 조작할 수 있습니다. 이 API는 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 처리 시에 올바른 권한을 적용합니다.

이 참조는 Cloud 파트너 포털 REST API에 대한 기술 세부 정보를 제공합니다. 이 문서의 페이로드 샘플은 참조용일 뿐이며 새 기능이 추가되면 변경될 수 있습니다.

## <a name="prerequisites-and-considerations"></a>필수 구성 요소 및 고려 사항

이 API를 사용하기 전에 다음을 검토해야 합니다.

- 계정에 서비스 주체를 추가하고 인증을 위한 Azure AD(Azure Active Directory) 액세스 토큰을 가져오는 방법을 알아보기 위한 [필수 구성 요소](./cloud-partner-portal-api-prerequisites.md) 문서
- API 호출에 사용할 수 있는 [동시성 제어](./cloud-partner-portal-api-concurrency-control.md) 전략 2개
- 버전 관리 및 오류 처리와 같은 추가 API [고려 사항](./cloud-partner-portal-api-considerations.md)

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>파트너 센터로 마이그레이션한 이후 CPP API의 변경 내용

| **API** | **변경 내용 설명** | **영향** |
| ------- | ---------------------- | ---------- |
| POST 게시, GoLive, 취소 | 마이그레이션된 제품의 경우 응답 헤더의 형식은 서로 다르지만 작업 상태를 검색하기 위한 상대 경로를 나타내는 것과 동일한 방식으로 계속 작동합니다. | 제품에 대한 해당 POST 요청을 전송하는 경우 위치 헤더는 제품의 마이그레이션 상태에 따라 다음 두 가지 형식 중 하나가 됩니다.<ul><li>마이그레이션되지 않은 제품<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>마이그레이션된 제품<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET 작업 | 응답의 ‘알림-이메일’ 필드가 이전에 지원되었던 제품 유형의 경우 이 필드는 더 이상 사용되지 않으며 마이그레이션된 제품에 대해 더 이상 반환되지 않습니다. | 마이그레이션된 제품의 경우 요청에 지정된 이메일 목록에 더 이상 알림을 보내지 않습니다. 대신, API 서비스는 이메일을 보내기 위해 파트너 센터의 알림 이메일 프로세스와 정렬됩니다. 특히 알림은 파트너 센터에서 계정 설정의 판매자 연락처 정보 섹션에 설정된 이메일 주소에 전송되어 작업 진행률을 알립니다.<br><br>파트너 센터의 [계정 설정](https://go.microsoft.com/fwlink/?linkid=2165291)에서 판매자 연락처 정보 섹션에 있는 이메일 주소 세트를 검토하여 알림을 보낼 올바른 이메일이 제공되는지 확인하세요. |

## <a name="common-tasks"></a>일반 작업

이 참조는 다음과 같은 일반적인 작업을 수행하기 위한 API를 자세히 설명합니다.

### <a name="offers"></a>제품

- [모든 제안 검색](./cloud-partner-portal-api-retrieve-offers.md)
- [특정 제안 검색](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [제안 상태 검색](./cloud-partner-portal-api-retrieve-offer-status.md)
- [제품 만들기](./cloud-partner-portal-api-creating-offer.md)
- [제안 게시](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>작업

- [작업 검색](./cloud-partner-portal-api-retrieve-operations.md)
- [작업 취소](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>앱 게시

- [라이브로 전환](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>기타 작업

- [가상 머신 제안에 대한 가격 책정 설정](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>문제 해결

- [인증 오류 문제 해결](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
