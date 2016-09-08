<properties 
	pageTitle="엔터프라이즈 통합 팩 X12 메시지 인코딩 커넥터에 대해 알아보기 | Microsoft Azure 앱 서비스 | Microsoft Azure" 
	description="엔터프라이즈 통합 팩 및 논리 앱에서 파트너를 사용하는 방법 알아보기" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="padmavc" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="padmavc"/>

# X12 메시지 인코딩 시작

EDI 및 파트너 관련 속성의 유효성을 검사하고 XML로 인코딩된 메시지를 교환에서 EDI 트랜잭션 집합으로 변환하며 기술 및/또는 기능 승인을 요청합니다.

## 연결 만들기

### 필수 조건

* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.

* 통합 계정은 x12 메시지 인코딩 커넥터를 사용해야 합니다. [통합 계정](./app-service-logic-enterprise-integration-create-integration-account.md), [파트너](./app-service-logic-enterprise-integration-partners.md) 및 [X12 규약](./app-service-logic-enterprise-integration-x12.md)을 만드는 방법에 대한 세부 사항을 확인합니다.

### 다음 단계를 사용하여 X12 메시지 인코딩에 연결합니다.

1. [논리 앱 만들기](./app-service-logic-create-a-logic-app.md)에서는 예제를 제공합니다.

2. 이 연결에는 트리거가 필요하지 않습니다. 다른 트리거를 사용하여 요청 트리거와 같은 논리 앱을 시작합니다. 논리 앱 디자이너에서 트리거를 추가하고 작업을 추가합니다. 드롭다운 목록에서 Microsoft 관리되는 API 표시를 선택한 다음 검색 상자에 "x12"를 입력합니다. X12 - 규약 이름으로 X12 메시지 인코딩 또는 X12 - ID로 X12 메시지 인코딩 중 하나를 선택합니다.

	![x12 검색](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png)

3. 이전에 통합 계정에 대한 연결을 만들지 않은 경우 연결 세부 정보를 지정하라는 메시지가 표시됩니다.

	![통합 계정 연결](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage1.png)


4. 통합 계정 세부 정보를 입력합니다. 별표가 있는 속성은 필수 사항입니다.

	| 속성 | 세부 정보 |
	| -------- | ------- |
	| 연결 이름 * | 연결의 이름을 입력합니다. |
	| 통합 계정 * | 통합 계정 이름을 입력합니다. 통합 계정 및 논리 앱은 동일한 Azure 위치에 있어야 합니다. |

	완료되면 연결 정보가 다음과 비슷하게 표시됩니다.

	![통합 계정 연결 생성](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage2.png)


5. **만들기**를 선택합니다.

6. 연결이 만들어졌는지 확인합니다.

	![통합 계정 연결 세부 사항](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage3.png)

#### X12 - 규약 이름으로 X12 메시지 인코딩

7. 드롭다운 목록에서 X12 규약 및 인코딩할 xml 메시지를 선택합니다.

	![필수 필드 제공](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage4.png)

#### X12 - ID로 X12 메시지 인코딩

7.	보낸 사람 식별자, 보낸 사람 한정자, 받는 사람 식별자 및 받는 사람 한정자를 X12 규약에 구성된 대로 제공합니다. 인코딩할 xml 메시지 선택

	![필수 필드 제공](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage5.png)

## X12 인코딩은 다음을 수행합니다.

* 보낸 사람 및 받는 사람 컨텍스트 속성을 일치시켜 규약을 확인합니다.
* EDI 교환을 직렬화하며 XML로 인코딩된 메시지를 교환에서 EDI 트랜잭션 집합으로 변환합니다.
* 트랜잭션 집합 헤더 및 트레일러 세그먼트를 적용합니다.
* 교환 제어 번호, 그룹 제어 번호 및 나가는 교환 각각에 대한 트랜잭션 집합 제어 번호를 생성합니다.
* 페이로드 데이터에서 구분 기호를 바꿉니다.
* EDI 및 파트너 관련 속성의 유효성을 검사합니다.
	* 메시지 스키마에 대한 트랜잭션 집합 데이터 요소의 스키마 유효성 검사
	* 트랜잭션 집합 데이터 요소에서 수행한 EDI 유효성 검사
	* 트랜잭션 집합 데이터 요소에서 수행한 확장된 유효성 검사
* 기술 및/또는 기능 승인을 요청합니다(구성된 경우).
	* 기술 승인은 헤더 유효성 검사의 결과로 생성됩니다. 기술 승인은 주소 수신기에 의한 교환 헤더 및 트레일러의 처리 상태를 보고합니다.
	* 기능 승인은 본문 유효성 검사의 결과로 생성됩니다. 기능 승인은 받은 문서를 처리하는 동안 발생한 오류를 각각 보고합니다.

## 다음 단계

[엔터프라이즈 통합 팩에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")

<!---HONumber=AcomDC_0824_2016-->