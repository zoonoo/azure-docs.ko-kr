---
title: Azure Mobile Engagement 문제 해결 가이드 - 서비스
description: Azure Mobile Engagement에 대한 문제 해결 가이드
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8b4275da-c0b4-4690-824a-48e9d7a1fc6e
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 64bd4849e3bb9a37fb32940548d5ecf01fffe0ca
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-service-issues"></a>서비스 문제에 대한 문제 해결 가이드
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

다음은 Azure Mobile Engagement 실행 방법과 관련해서 발생할 수 있는 문제입니다.

## <a name="service-outages"></a>서비스 중단
### <a name="issue"></a>문제
* Azure Mobile Engagement 서비스 중단으로 인해 발생한 것처럼 보이는 문제입니다.

### <a name="causes"></a>원인
* Azure Mobile Engagement 서비스 중단으로 인해 발생한 것으로 나타나는 문제에는 여러 가지 원인이 있을 수 있습니다.
  * 원래 모든 Azure Mobile Engagement에서 전체적으로 발생하는 것으로 확인된 문제
  * 서버 중단으로 인한 알려진 문제(서버 상태에 항상 표시되지는 않음):
  * 예약 지연, 대상 지정 오류, 배지 업데이트 문제, 통계 수집 중지, 푸시 작동 중지, API 작동 중지, 새 앱이나 사용자 만들기 불가, DNS 오류, 장치의 UI/API/앱 시간 초과 오류
  * 클라우드 종속성 작동 중단 [Azure 서비스 상태](http://status.azure.com/)
  * PNS(푸시 알림 서비스) 종속성 작동 중단
  * 앱 스토어 작동 중단

1) 문제가 시스템 전체의 문제인지 테스트하려는 경우 다른 항목에서 같은 기능을 테스트할 수 있습니다.

* Azure Mobile Engagement 통합 응용 프로그램
* 테스트 장치
* 테스트 장치 OS 버전
* 캠페인
* 관리자 사용자 계정
* 브라우저(IE, Firefox, Chrome 등)
* Computer

2) 문제가 UI 또는 API에만 영향을 주는지 테스트하려면 다음을 수행합니다.

* Azure Mobile Engagement UI와 Azure Mobile Engagement API 둘 다에서 동일한 기능을 테스트합니다.

3) 휴대폰 네트워크 관련 문제인지를 테스트하려면 다음을 수행합니다.

* WIFI를 통해 인터넷에 연결되어 있는 동안과 3G 휴대폰 네트워크를 통해 인터넷에 연결되어 있는 동안 테스트합니다.
* 방화벽이 Azure Mobile Engagement IP 주소 또는 포트 차단하고 있지 않은지 확인합니다.

4) 장치 관련 문제인지를 테스트하려면 다음을 수행합니다.

* 장치에서 다른 Azure Mobile Engagement 통합 응용 프로그램을 사용하여 Azure Mobile Engagement에 연결할 수 있는지 테스트합니다.
* Azure Mobile Engagement UI에 표시된 휴대폰에서 이벤트, 작업 및 크래시를 생성할 수 있는지 테스트합니다. 
* 해당 장치 ID에 따라 Azure Mobile Engagement UI에서 장치로 푸시 알림을 보낼 수 있는지 테스트합니다. 

5) 앱 관련 문제인지를 테스트하려면 다음을 수행합니다.

* 실제 장치 대신 에뮬레이터에서 응용 프로그램을 설치하고 테스트합니다.

6) 최종 사용자 장치의 OS 업그레이드 관련 문제인지를 테스트하려면 다음을 수행합니다(문제를 해결하려면 SDK를 업그레이드해야 함).

* OS 버전이 서로 다른 여러 장치에서 응용 프로그램을 테스트합니다.
* 최신 버전의 SDK를 사용하고 있는지 확인합니다.

## <a name="connectivity-and-incorrect-information-issues"></a>연결 및 잘못된 정보 문제
### <a name="issue"></a>문제
* Azure Mobile Engagement UI에 로그인하는 데 문제가 있습니다.
* Azure Mobile Engagement API에서 연결 문제가 발생합니다.
* 장치 API를 통해 앱 정보 태그를 업로드하는 데 문제가 있습니다.
* Azure Mobile Engagement에서 로그나 내보낸 데이터를 다운로드하는 데 문제가 있습니다.
* Azure Mobile Engagement UI에 잘못된 정보가 표시됩니다.
* Azure Mobile Engagement 로그에 잘못된 정보가 표시됩니다.

### <a name="causes"></a>원인
* 사용자 계정에 작업을 수행할 수 있는 권한이 있는지 확인합니다.
* 문제가 단일 컴퓨터나 로컬 네트워크에서만 발생하지 않는지 확인합니다.
* Azure Mobile Engagement 서비스에서 작동 중단이 보고되지 않았는지 확인합니다.
* 앱 정보 태그 파일이 다음 규칙을 모두 따르는지 확인합니다.
  * UTF8 문자 집합만 사용합니다. ANSI 문자 집합은 지원되지 않습니다.
  * 쉼표(",")를 구분 기호 문자로 사용합니다. .csv 구문 기호 문자를 쉼표(",")에서 세미콜론(";") 등의 다른 문자로 변경하도록 요청하는 서비스 요청을 개설할 수 있습니다.
  * 부울 값 "true"와 "false"에 소문자만 사용합니다.
  * 최대 파일 크기인 35MB보다 작은 파일을 사용합니다.

