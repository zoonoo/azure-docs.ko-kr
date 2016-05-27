<properties 
   pageTitle="Azure 앱 서비스에서 거래 업체 규약 만들기 | Microsoft Azure" 
   description="거래 업체 규약 만들기" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
	ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# 거래 업체 규약 만들기   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

거래 업체는 B2B(Business to Business) 통신과 관련된 엔터티입니다. 두 파트너가 관계를 설정하는 경우 이를 *규약*이라고 합니다. 정의된 규약은 두 파트너가 수행하려고 하는 통신을 기반으로 하며 특정 프로토콜 또는 전송입니다. Azure 앱 서비스가 지원하는 다양한 B2B 프로토콜 및 전송은 다음과 같습니다.

- AS2(Applicability Statement 2)
- EDIFACT(United Nations/Electronic Data Interchange For Administration, Commerce and Transport(UN/EDIFACT))
- X12(ASC X12)

### B2B 시나리오를 지원하는 BizTalk API 앱
다음 API 앱에서는 Azure 포털의 다양하고 직관적인 환경을 사용하여 이러한 기능을 사용할 수 있습니다.


## BizTalk TPM(거래 업체 관리)
- 파트너, 프로필 및 ID의 만들기 및 관리
- EDI 스키마의 저장 및 관리
- 인증서(AS2 프로토콜에서 사용됨)의 저장 및 관리
- AS2 규약의 생성 및 관리
- EDIFACT 규약(송신 측 일괄 처리 포함)의 생성 및 관리
- X12 규약(송신 측 일괄 처리 포함)의 생성 및 관리

![][1]


## AS2 커넥터
- 관련된 TPM API 앱 인스턴스에 정의된 대로 AS2 규약을 실행합니다.
- 문제 해결을 위한 AS2 처리/추적 정보를 표시합니다.


## BizTalk EDIFACT
- 관련된 TPM API 앱 인스턴스에 정의된 EDIFACT 규약을 실행합니다.
- 문제 해결을 위한 EDIFACT 처리/추적 정보를 표시합니다.
- 관련 TPM API 앱 인스턴스의 EDIFACT 규약에 정의된 일괄 처리(시작 및 중지)의 상태 관리를 제공합니다.


## BizTalk X12
- 관련 TPM API 앱 인스턴스에 정의된 X12 규약을 실행합니다. 
- 문제 해결을 위한 X12 처리/추적 정보를 표시합니다.
- 관련 TPM API 앱 인스턴스의 X12 규약에 정의된 일괄 처리(시작 및 중지)의 상태 관리를 제공합니다.

앞서 설명한 대로, AS2, X12 및 EDIFACT API 앱을 사용하려면 TPM API 앱이 예상대로 작동해야 합니다.


## 시작
거래 업체 규약을 만들려면:

1. **BizTalk 거래 업체 관리** 커넥터의 인스턴스를 만듭니다. 그렇게 하려면 빈 SQL 데이터베이스가 기능해야 합니다. 시작하기 전에 사용 가능한 빈 데이터베이스가 있으며 바로 사용할 수 있는지 확인하세요.
2. 규약에서 요구하는 대로 스키마 및 인증서를 업로드합니다. 생성된 TPM 인스턴스로 이동하고 'Schemas' 및/또는 'Certificates' 파트를 한 단계씩 실행하여 수행합니다.
3. 생성된 TPM 인스턴스로 이동하여 **Partners** 파트를 한 단계씩 코드 실행합니다.
4. 필요에 따라 파트너를 만듭니다. 또한 프로필을 적절히 편집하고 필요한 ID를 추가합니다.
5. 이제 **Agreements** 파트를 사용하여 규약을 만듭니다. 규약을 만들 때 사용되는 프로토콜을 선택해야 합니다. 나머지 구성 옵션은 선택한 프로토콜에 기반합니다.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 

<!------HONumber=AcomDC_0420_2016-->