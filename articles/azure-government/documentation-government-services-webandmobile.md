---
title: "Azure Government 웹, 모바일 및 API | Microsoft Docs"
description: "Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다."
services: azure-government
cloud: gov
documentationcenter: 
author: brendalee
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/5/2016
ms.author: brendal
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: f0ba3e50c4281a0e8c365be075ae45ab72f6e82d

---
# <a name="azure-government-web--mobile"></a>Azure Government 웹 + 모바일
## <a name="app-services"></a>앱 서비스
### <a name="variations"></a>변형
Azure 앱 서비스는 일반적으로 Azure Government에서 사용할 수 있습니다.

Azure Government에서 만든 Azure 앱 서비스 앱에 대한 주소는 공용 클라우드에서 생성된 것과 다릅니다.

| 서비스 유형 | Azure 공용 | Azure Government |
| --- | --- | --- |
| App Service |*.azurewebsites.net |*.azurewebsites.us|

공용 클라우드에서 사용할 수 있는 일부 앱 서비스 기능은 Azure Government에서는 아직 사용할 수 없습니다.

- 앱 서비스 환경
- 앱 배포
    - 연속 배달(미리 보기)
- 설정
    - VNet 통합 및 하이브리드 연결
    - 보안 검색
- 개발 도구
    - 성능 테스트
    - 리소스 탐색기
    - PHP 디버깅
- 모니터링
    - Application Insights
    - 인스턴스당 메트릭
    - 라이브 HTTP 트래픽
    - 응용 프로그램 이벤트
    - FREB 로그
- 지원 & 문제 해결
    - 앱 서비스 관리자
    - 오류 기록
    - 진단 서비스
    - 완화


### <a name="considerations"></a>고려 사항
다음 정보는 앱 서비스에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
| --- | --- |
| Azure 앱 서비스에서 입력, 저장 및 처리된 데이터는 내보내기 제어된 데이터를 포함할 수 있습니다. Azure 앱 서비스에서 실행되는 이진 파일. Azure 플랫폼 구성 요소에 대한 액세스를 위한 암호 및 스마트 카드 PIN과 같은 정적 인증자. Azure 플랫폼 구성 요소를 관리하는 데 사용되는 인증서의 개인 키. SQL 연결 문자열. Azure 서비스에 저장된 인증서, 암호화 키, 마스터 키 및 저장소 키와 같은 기타 보안 정보/암호. |메타데이터는 내보내기 제어된 데이터를 포함하도록 허용되지 않습니다. 이 메타데이터는 Azure 앱 서비스를 생성 및 유지 관리하는 경우 입력된 모든 구성 데이터를 포함합니다. 다음 필드에 규제된/제어된 데이터인 리소스 그룹, 리소스 이름, 리소스 태그를 입력하지 마세요.|

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트는 [Microsoft Azure Government 블로그](https://blogs.msdn.microsoft.com/azuregov/)를 구독하세요.




<!--HONumber=Dec16_HO2-->


