<properties
   pageTitle="Operations Management Suite 보안 및 감사 솔루션 데이터 보안 | Microsoft Azure"
   description="이 문서에서는 데이터를 Operations Management Suite 보안 및 감사 솔루션에서 데이터를 관리하고 보호하는 방법을 설명합니다."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="yurid"/>


# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Operations Management Suite 보안 및 감사 솔루션 데이터 보안

고객이 위협을 방지, 감지하고 위협에 응답할 수 있도록 하려면 [OMS(Operations Management Suite) 보안 및 감사 솔루션](operations-management-suite-overview.md)에서는 다음을 포함하여 리소스에 대한 데이터를 수집하고 처리합니다.

- 보안 이벤트 로그
- Windows (ETW) 이벤트에 대한 이벤트 추적
- AppLocker 감사 이벤트
- Windows 방화벽 로그
- 고급 위협 분석 이벤트
- 기준 평가 결과
- 맬웨어 방지 평가 결과
- 업데이트/패치 평가 결과
- 에이전트에 명시적으로 설정되어 있는 Syslogs 스트림

이 데이터의 개인 정보 보호 및 보안을 보호하기 위해 노력하고 있습니다. Microsoft는 코딩부터 서비스에 이르기까지 엄격한 규정 준수 및 보안 지침을 따릅니다.
이 문서에서는 OMS 보안 및 감사 솔루션에서 데이터를 관리하고 보호하는 방법을 설명합니다.

## <a name="data-sources"></a>데이터 원본

OMS 보안 및 감사 솔루션에서는 OMS 에이전트가 설치되어 있는 Virtual Machines 및 물리적 컴퓨터에서 데이터를 분석합니다. OMS 보안 및 감사 솔루션에서는 Windows 이벤트, 감사 로그, IIS 로그 및 syslog 메시지와 같은 보안 이벤트에 대한 구성 정보를 수집할 수 있습니다. 이러한 데이터의 예: 운영 체제 유형 및 버전, 프로세스 실행, 컴퓨터 이름, IP 주소, 로그인된 사용자 및 테넌트 ID입니다.  

## <a name="data-protection"></a>데이터 보호

**데이터 분리:**데이터는 서비스 전체에서 각 구성 요소에 논리적으로 별도로 유지됩니다. 모든 데이터에는 조직별로 태그가 지정됩니다. 이 태그는 데이터 수명 주기 동안 유지되며 서비스의 각 계층에서 적용됩니다. 

**데이터 액세스**: Microsoft 직원은 보안 권장 사항을 제공하고 잠재적 보안 위협을 조사하기 위해 서비스에서 수집되고 분석된 정보에 액세스할 수 있습니다. [Microsoft Online Services 약관](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) 및 [개인 정보 취급 방침](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)을 준수합니다. 즉, Microsoft는 고객 데이터를 사용하거나 광고 또는 유사한 상업적 목적에서 정보를 파생하지 않습니다. Microsoft 직원은 보안 권장 사항을 제공하고 잠재적 보안 위협을 조사하기 위해 서비스에서 수집되고 분석된 정보에 액세스할 수 있습니다. 필요에 따라 고객 데이터를 사용하여 해당 서비스를 제공하도록 호환할 수 있는 목적으로 Azure 서비스를 제공합니다. 고객은 고유한 데이터에 대한 모든 권한을 유지합니다.

**데이터 사용**: Microsoft는 여러 테넌트에 발생하는 패턴 및 위협 인텔리전스를 사용하여 방지 및 검색 기능을 향상시킵니다. [개인 정보 취급 방침](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)에 설명된 개인 정보 취급 방침 약정에 따라 수행합니다.

> [AZURE.NOTE] 데이터 위치는 초기 OMS 보안 및 감사 구성 프로세스의 일부인 작업 영역 만들기 중에 OMS 작업 영역 수준에서 구성됩니다.

## <a name="see-also"></a>참고 항목

이 문서에서는 OMS에서 데이터를 관리하고 보호하는 방법을 알아봅니다. OMS 보안 및 감사 솔루션에 대한 자세한 내용은 다음을 참조하세요.

- [OMS(Operations Management Suite) 개요](operations-management-suite-overview.md)
- [Operations Management Suite 보안 및 감사 솔루션의 보안 경고 모니터링 및 응답](oms-security-responding-alerts.md)
- [Operations Management Suite 보안 및 감사 솔루션의 리소스 모니터링](oms-security-monitoring-resources.md)




<!--HONumber=Oct16_HO2-->


