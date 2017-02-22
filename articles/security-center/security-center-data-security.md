---
title: "Azure 보안 센터 데이터 보안 | Microsoft Docs"
description: "이 문서에서는 Azure 보안 센터에서 데이터 관리하고 보호하는 방법을 설명합니다."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 9852981e530cd147c2d34ac2ede251b58a167a0a
ms.openlocfilehash: 5c030f463b21284c15752cf95aa1f9a75f17ffb0


---
# <a name="azure-security-center-data-security"></a>Azure 보안 센터 데이터 보안
고객이 위협을 방지, 감지 및 대응하는 데 도움을 주기 위해 Azure Security Center에서는 구성 정보, 메타데이터, 이벤트 로그, 크래시 덤프 파일 등을 포함한 보안 관련 데이터를 수집하고 처리합니다. Microsoft는 코딩부터 서비스에 이르기까지 엄격한 규정 준수 및 보안 지침을 따릅니다.

이 문서에서는 Azure 보안 센터에서 데이터 관리하고 보호하는 방법을 설명합니다.


## <a name="data-sources"></a>데이터 원본
Azure Security Center에서는 다음 소스의 데이터를 분석하여 보안 상태에 대한 가시성을 제공하고, 취약점을 식별하고, 완화 방법을 권장하고, 활성 위협을 감지합니다.

- Azure 서비스: 해당 서비스의 리소스 공급자와 통신하여 배포한 Azure 서비스의 구성에 대한 정보를 사용합니다.
- 네트워크 트래픽: 원본/대상 IP/포트, 패킷 크기 및 네트워크 프로토콜과 같은 Microsoft의 인프라에서 샘플링된 네트워크 트래픽 메타데이터를 사용합니다.
- 파트너 솔루션: 방화벽 및 맬웨어 방지 솔루션과 같은 통합된 파트너 솔루션의 보안 경고를 사용합니다.
- 가상 컴퓨터: Windows 이벤트 및 감사 로그, IIS 로그, syslog 메시지 및 가상 컴퓨터의 크래시 덤프 파일과 같은 보안 이벤트에 대한 구성 정보 및 정보를 사용합니다.


## <a name="data-protection"></a>데이터 보호
**데이터 분리:**데이터는 서비스 전체에서 각 구성 요소에 논리적으로 별도로 유지됩니다. 모든 데이터에는 조직별로 태그가 지정됩니다. 이 태그는 데이터 수명 주기 동안 유지되며 서비스의 각 계층에서 적용됩니다.

**데이터 액세스**: Microsoft 직원은 보안 권장 사항을 제공하고 잠재적 보안 위협을 조사하기 위해 Azure 서비스에서 수집 또는 분석한 정보에 액세스할 수 있습니다. 이러한 정보는 크래시 덤프, 프로세스 생성 이벤트, VM 디스크 스냅샷 및 아티팩트 등을 포함하며 고객 데이터 또는 가상 컴퓨터의 개인 데이터가 의도치 않게 포함될 수도 있습니다. Microsoft는 [Microsoft Online Services 약관 및 개인정보처리방침](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)을 준수합니다. 즉, Microsoft는 고객 데이터를 사용하거나 광고 또는 유사한 상업적 목적에서 정보를 파생하지 않습니다. 필요에 따라 고객 데이터를 사용하여 해당 서비스를 제공하도록 호환할 수 있는 목적으로 Azure 서비스를 제공합니다. 고객 데이터에 대한 모든 권한을 유지합니다.

**데이터 사용**: Microsoft는 여러 테넌트에 발생하는 패턴 및 위협 인텔리전스를 사용하여 방지 및 검색 기능을 향상시킵니다. [개인 정보 취급 방침](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)에 설명된 개인 정보 취급 방침 약정에 따라 수행합니다.

## <a name="data-location"></a>데이터 위치
**저장소 계정**: 가상 컴퓨터가 실행되는 각 영역에 저장소 계정을 지정합니다. 그러면 데이터를 수집하는 가상 컴퓨터와 동일한 지역에 데이터를 저장할 수 있습니다. 저장소 계정에 크래시 덤프 파일을 비롯한 이 데이터를 영구적으로 저장합니다. VM 디스크 스냅숏은 VM 디스크와 동일한 저장소 계정에 저장됩니다.

**Azure Security Center 저장소**: 보안 경고(파트너 경고 포함), 권장 사항 및 보안 상태에 대한 정보는 현재 미국에 있는 Azure Security Center 저장소에 중앙 집중식으로 저장됩니다. 이 정보는 필요한 가상 컴퓨터에서 수집된 관련된 구성 정보 및 보안 이벤트를 포함하여 보안 경고, 권장 사항 또는 보안 상태를 제공할 수 있습니다.

Azure 보안 센터는 크래시 덤프 파일의 임시 복사본을 수집하고 이용 시도 및 손상 성공의 증거를 찾기 위해 분석합니다. Azure Security Center는 작업 영역과 동일한 지역 내에서 이 분석을 수행하고 분석이 완료되면 임시 복사본을 삭제합니다.

컴퓨터 아티팩트는 VM과 동일한 지역에 중앙 집중식으로 저장됩니다.


## <a name="managing-data-collection-from-virtual-machines"></a>가상 컴퓨터에서 데이터 컬렉션 관리
Azure 보안 센터를 사용하도록 설정하는 경우 각 구독에 데이터 수집이 활성화됩니다. 또한 Azure Security Center의 보안 정책 섹션에서 데이터 수집을 설정할 수 있습니다. 데이터 수집이 활성화되면 Azure 보안 센터는 지원되는 모든 기존 가상 컴퓨터 및 새로 만든 가상 컴퓨터에 Azure 모니터링 에이전트를 프로비전합니다. Azure 보안 모니터링 확장은 다양한 보안 관련 구성 및 이벤트를 검사하여 ETW([Windows용 이벤트 추적](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx))로 보냅니다. 또한 운영 체제는 컴퓨터를 실행하는 동안 이벤트 로그 이벤트를 발생시킵니다. 이러한 데이터의 예: 운영 체제 유형 및 버전, 운영 체제 로그(Windows 이벤트 로그), 프로세스 실행, 컴퓨터 이름, IP 주소, 로그인된 사용자 및 테넌트 ID입니다. Azure 모니터링 에이전트는 이벤트 로그 항목을 읽으며 ETW는 이를 추적하고 분석을 위해 저장소 계정에 복사합니다.

언제든지 가상 컴퓨터에서 데이터 컬렉션을 비활성화할 수 있으며 이는 Azure 보안 센터에서 이전에 설치한 모니터링 에이전트를 제거합니다. VM 디스크 스냅숏 및 아티팩트 컬렉션은 데이터 수집이 사용하지 않도록 설정된 경우에도 여전히 사용하도록 설정됩니다.


## <a name="see-also"></a>참고 항목
이 문서에서는 Azure 보안 센터에서 데이터 관리하고 보호하는 방법을 알아봅니다. Azure 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터의 계획 및 운영 가이드](security-center-planning-and-operations-guide.md) — 디자인 고려 사항을 계획하고 이해하여 Azure 보안 센터를 채택하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md) — Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) — 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) — 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md) — 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) — Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.



<!--HONumber=Feb17_HO1-->


