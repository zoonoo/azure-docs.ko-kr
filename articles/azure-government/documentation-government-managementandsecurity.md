<properties
	pageTitle="Azure Government 설명서 | Microsoft Azure"
	description="Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다."
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="09/23/2016"
	ms.author="ryansoc"/>


#  Azure Government 관리 및 보안

##  키 자격 증명 모음

다음 정보는 Azure 주요 자격 증명 모음에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure 주요 자격 증명 모음으로 암호화된 모든 데이터는 규제된/제어된 데이터를 포함할 수 있습니다. | Azure 주요 자격 증명 모음 메타데이터는 내보내기 제어된 데이터를 포함하도록 허용되지 않습니다. 이 메타데이터는 주요 자격 증명 모음을 생성 및 유지 관리하는 경우 입력된 모든 구성 데이터를 포함합니다. 다음 필드에 규제된/제어된 데이터인 리소스 그룹 이름, 주요 자격 증명 모음 이름, 구독 이름을 입력하지 마세요. |

주요 자격 증명 모음은 일반적으로 Azure Government에서 사용할 수 있습니다. 공용에서 마찬가지로 확장명이 없으므로 PowerShell 및 CLI를 통해서만 주요 자격 증명 모음을 사용할 수 있습니다.

추가 정보는 [Azure 주요 자격 증명 모음 공개 설명서](/key-vault-get-started)를 참조하세요.

## Log Analytics

Log Analytics는 Azure Government에서 미리 보기로 제공됩니다.

### 공용 Azure와의 차이점

다음 Log Analytics 기능과 솔루션은 현재 Azure Government에서 사용할 수 없습니다. 이 목록은 기능/솔루션 상태가 변경될 때 업데이트됩니다.

+ 거의 실시간 메트릭
  - 시간 범위가 6시간 미만인 메트릭 그래프를 볼 때 그래프가 새 메트릭 값으로 자동 업데이트되지 않음
+ PowerBI로 데이터 내보내기
+ 네트워크 모니터링 솔루션
+ Office 365 솔루션
+ Windows 10 Analytics 업그레이드 솔루션
+ 응용 프로그램 종속성 모니터링
+ 업데이트 평가
+ Azure Portal 통합
  - 모니터링할 Azure Storage는 반드시 PowerShell 또는 Resource Manager 템플릿을 통해 선택해야 함
  - Log Analytics를 활성화할 가상 컴퓨터는 반드시 PowerShell 또는 Resource Manager 템플릿을 통해 선택해야 함
+ Linux 모니터링
+ OMS 모바일 응용 프로그램
+ Microsoft Monitoring Agent VM 확장
+ OMS Linux Agent VM 확장
+ 사용 데이터
+ Azure Automation을 사용하는 경고 전자 메일 및 업데이트 관리

다음 Log Analytics 기능은 Azure Government에서 다르게 작동합니다.

+ Azure Government의 Log Analytics 포털에서 Windows 에이전트를 다운로드해야 합니다.
+ 보안 및 감사 솔루션은 악의적인 IP 검색을 지원하지 않습니다.
+ 데이터 수집기 API를 사용하여 데이터를 업로드하려면 Azure Government URL을 사용해야 합니다.

### 질문과 대답

+ 공용 Azure의 Log Analytics에서 Azure Government로 데이터를 마이그레이션할 수 있습니까?
  - 아니요. 공용 Azure에서 Azure Government로 데이터 또는 작업 영역을 이동하는 것은 불가능합니다.
+ OMS Log Analytics 포털에서 공용 Azure와 Azure Government 작업 영역 간에 전환할 수 있습니까?
  - 아니요. 공용 Azure 포털과 Azure Government 포털은 각각 별도이며 정보를 공유하지 않습니다.

자세한 내용은 [Log Analytics 공개 문서](../log-analytics/log-analytics-overview.md)를 참조하세요.

## 다음 단계

추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.

<!---HONumber=AcomDC_0928_2016-->