<properties 
	pageTitle="엔터프라이즈 통합 팩 X12 메시지 디코딩 커넥터에 대해 알아보기 | Microsoft Azure 앱 서비스 | Microsoft Azure" 
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

# X12 메시지 디코딩 시작

EDI 및 파트너 관련 속성의 유효성을 검사하고 각 트랜잭션 집합의 XML 문서를 생성하며 처리된 트랜잭션에 대한 승인을 생성합니다.

## 연결 만들기

### 필수 조건

* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.

* 통합 계정은 X12 메시지 디코딩 커넥터를 사용해야 합니다. [통합 계정](./app-service-logic-enterprise-integration-create-integration-account.md), [파트너](./app-service-logic-enterprise-integration-partners.md) 및 [X12 규약](./app-service-logic-enterprise-integration-x12.md)을 만드는 방법에 대한 세부 사항을 확인합니다.

### 다음 단계를 사용하여 X12 메시지 디코딩에 연결합니다.

1. [논리 앱 만들기](./app-service-logic-create-a-logic-app.md)에서는 예제를 제공합니다.

2. 이 연결에는 트리거가 필요하지 않습니다. 다른 트리거를 사용하여 요청 트리거와 같은 논리 앱을 시작합니다. 논리 앱 디자이너에서 트리거를 추가하고 작업을 추가합니다. 드롭다운 목록에서 Microsoft 관리되는 API 표시를 선택한 다음 검색 상자에 "x12"를 입력합니다. X12 - X12 메시지 디코딩 선택

	![x12 검색](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png)

3. 이전에 통합 계정에 대한 연결을 만들지 않은 경우 연결 세부 정보를 지정하라는 메시지가 표시됩니다.

	![통합 계정 연결](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage4.png)

4. 통합 계정 세부 정보를 입력합니다. 별표가 있는 속성은 필수 사항입니다.

	| 속성 | 세부 정보 |
	| -------- | ------- |
	| 연결 이름 * | 연결의 이름을 입력합니다. |
	| 통합 계정 * | 통합 계정 이름을 입력합니다. 통합 계정 및 논리 앱은 동일한 Azure 위치에 있어야 합니다. |

	완료되면 연결 정보가 다음과 비슷하게 표시됩니다.
	
	![통합 계정 연결 생성](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage5.png)

5. **만들기**를 선택합니다.
	
6. 연결이 만들어졌는지 확인합니다.

	![통합 계정 연결 세부 사항](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage6.png)

7. 디코딩할 X12 플랫 파일 메시지 선택

	![필수 필드 제공](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage7.png)

## X12 디코딩은 다음을 수행합니다.

* 거래 업체 규약에 대한 봉투의 유효성 검사
* 각 트랜잭션 집합에 대한 XML 문서를 생성합니다.
* EDI 및 파트너 관련 속성의 유효성을 검사합니다.
	* EDI 구조 유효성 검사 및 확장된 스키마 유효성 검사
	* 교환 봉투 구조의 유효성 검사
	* 제어 스키마에 대한 봉투의 스키마 유효성 검사
	* 메시지 스키마에 대한 트랜잭션 집합 데이터 요소의 스키마 유효성 검사
	* 트랜잭션 집합 데이터 요소에서 수행한 EDI 유효성 검사
* 교환, 그룹 및 트랜잭션 집합 제어 번호가 중복되지 않았는지 확인합니다.
	* 이전에 받은 교환에 대한 교환 제어 번호를 확인합니다.
	* 교환에 있는 다른 그룹 제어 번호에 대한 그룹 제어 번호를 확인합니다.
	* 해당 그룹의 다른 트랜잭션 집합 제어 번호에 대한 트랜잭션 집합 제어 번호를 확인합니다.
* 전체 교환을 XML로 변환합니다.
	* 교환을 트랜잭션 집합으로 분할 - 오류 발생 시 트랜잭션 일시 중단: 교환의 각 트랜잭션 집합을 개별 XML 문서로 구문 분석합니다. 교환에 포함된 하나 이상의 트랜잭션 집합에 대한 유효성 검사가 실패하는 경우 X12 디코딩은 해당 트랜잭션 집합만 일시 중단합니다.
	* 교환을 트랜잭션 집합으로 분할 - 오류 발생 시 교환 일시 중단: 교환의 각 트랜잭션 집합을 개별 XML 문서로 구문 분석합니다. 교환에 포함된 하나 이상의 트랜잭션 집합에 대한 유효성 검사가 실패하는 경우 X12 디코딩은 전체 교환을 일시 중단합니다.
	* 교환 유지- 오류 발생 시 트랜잭션 집합 일시 중단: 전체 배치된 교환에 대한 XML 문서를 만듭니다. X12 디코딩은 다른 모든 트랜잭션 집합을 계속 처리하는 반면 유효성 검사가 실패한 해당 트랜잭션 집합만 일시 중단합니다.
	* 교환 유지- 오류 발생 시 교환 일시 중단: 전체 배치된 교환에 대한 XML 문서를 만듭니다. 교환에 포함된 하나 이상의 트랜잭션 집합에 대한 유효성 검사가 실패하는 경우 X12 디코딩은 전체 교환을 일시 중단합니다.
* 기술 및/또는 기능 승인을 생성합니다(구성된 경우).
	* 기술 승인은 헤더 유효성 검사의 결과로 생성됩니다. 기술 승인은 주소 수신기에 의한 교환 헤더 및 트레일러의 처리 상태를 보고합니다.
	* 기능 승인은 본문 유효성 검사의 결과로 생성됩니다. 기능 승인은 받은 문서를 처리하는 동안 발생한 오류를 각각 보고합니다.

## 다음 단계

[엔터프라이즈 통합 팩에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")

<!---HONumber=AcomDC_0824_2016-->