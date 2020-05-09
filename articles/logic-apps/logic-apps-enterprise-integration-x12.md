---
title: B2B 용 X12 메시지 송신 및 수신
description: 엔터프라이즈 통합 팩와 함께 Azure Logic Apps를 사용 하 여 B2B 엔터프라이즈 통합 시나리오용 X12 메시지 교환
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 8ec20e03544ba54b83130ae41244dcdb186252d0
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613093"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 B2B 엔터프라이즈 통합용 X12 메시지 교환

Azure Logic Apps에서 X12 메시지로 작업 하려면 x12 통신을 관리 하기 위한 트리거와 작업을 제공 하는 X12 커넥터를 사용할 수 있습니다. EDIFACT 메시지에 대 한 자세한 내용은 [edifact 메시지 교환](logic-apps-enterprise-integration-edifact.md)을 참조 하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* X12 커넥터를 사용 하려는 논리 앱 및 논리 앱의 워크플로를 시작 하는 트리거 X12 커넥터는 트리거가 아니라 동작만 제공 합니다. 논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다.

* Azure 구독과 연결 되 고 X12 커넥터를 사용 하려는 논리 앱에 연결 된 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 입니다. 논리 앱과 통합 계정은 모두 동일한 위치 또는 Azure 지역에 있어야 합니다.

* X12 id 한정자를 사용 하 여 통합 계정에 이미 정의 된 두 개 이상의 [거래 업체](../logic-apps/logic-apps-enterprise-integration-partners.md)

* 통합 계정에 이미 추가한 XML 유효성 검사에 사용할 [스키마](../logic-apps/logic-apps-enterprise-integration-schemas.md) 입니다. HIPAA (건강 보험 이식성 및 책임 Act) 스키마를 사용 하는 경우 [hipaa 스키마](#hipaa-schemas)를 참조 하세요.

* X12 커넥터를 사용 하려면 먼저 거래 업체 간에 X12 [규약](../logic-apps/logic-apps-enterprise-integration-agreements.md) 을 만들고 통합 계정에 해당 규약을 저장 해야 합니다. HIPAA (건강 보험 이식성 및 책임 Act) 스키마를 사용 하 여 작업 하는 경우 규약에 섹션 `schemaReferences` 을 추가 해야 합니다. 자세한 내용은 [HIPAA 스키마](#hipaa-schemas)를 참조 하세요.

<a name="receive-settings"></a>

## <a name="receive-settings"></a>수신 설정

규약 속성을 설정한 후이 규약에서이 규약을 통해 파트너 로부터 받은 인바운드 메시지를 식별 하 고 처리 하는 방법을 구성할 수 있습니다.

1. **추가** 아래에서 **수신 설정**을 선택합니다.

1. 사용자와 메시지를 교환하는 파트너와의 규약에 따라 이러한 속성을 구성합니다. **수신 설정은** 다음 섹션으로 구성 됩니다.

   * [식별자](#inbound-identifiers)
   * [Acknowledgement](#inbound-acknowledgement)
   * [스키마](#inbound-schemas)
   * [봉투](#inbound-envelopes)
   * [컨트롤 번호](#inbound-control-numbers)
   * [Validations](#inbound-validations)
   * [내부 설정](#inbound-internal-settings)

   속성 설명은 이 섹션에 있는 테이블을 참조하세요.

1. 완료 되 면 **확인**을 선택 하 여 설정을 저장 해야 합니다.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>수신 설정-식별자

![인바운드 메시지의 식별자 속성](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| 속성 | Description |
|----------|-------------|
| **ISA1(인증 한정자)** | 사용 하려는 권한 부여 한정자 값입니다. 기본값은 **00-인증 정보 없음**입니다. <p>**참고**: 다른 값을 선택 하는 경우 **ISA2** 속성의 값을 지정 합니다. |
| **ISA2** | **ISA1** 속성이 **00**이 아닌 경우 사용할 권한 부여 정보 값입니다. 이 속성 값의 최소 길이는 1 자에서 10 자 사이 여야 합니다. |
| **ISA3(보안 한정자)** | 사용 하려는 보안 한정자 값입니다. 기본값은 **00-보안 정보 없음**입니다. <p>**참고**: 다른 값을 선택 하는 경우 **ISA4** 속성의 값을 지정 합니다. |
| **ISA4** | **ISA3** 속성이 00이 아닌 경우 사용할 보안 정보 값입니다. **보안 정보가**없는 것입니다. 이 속성 값의 최소 길이는 1 자에서 10 자 사이 여야 합니다. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>수신 설정-승인

![인바운드 메시지에 대 한 승인](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| 속성 | Description |
|----------|-------------|
| **TA1 필요** | 교환 보낸 사람에게 기술 승인(TA1)을 반환합니다. |
| **FA 필요** | 교환 보낸 사람에게 기능 승인(FA)을 반환합니다. <p>**FA 버전** 속성의 경우 스키마 버전에 따라 997 또는 999 승인을 선택 합니다. <p>수락 된 트랜잭션 집합에 대 한 기능 승인에서 AK2 루프를 생성할 수 있도록 설정 하려면 **AK2/IK2 Loop 포함**을 선택 합니다. |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>수신 설정-스키마

![인바운드 메시지에 대 한 스키마](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

이 섹션에서는 각 트랜잭션 유형 (ST01) 및 발신자 응용 프로그램 (GS02)에 대 한 [통합 계정](../logic-apps/logic-apps-enterprise-integration-accounts.md) 에서 [스키마](../logic-apps/logic-apps-enterprise-integration-schemas.md) 를 선택 합니다. EDI 수신 파이프라인은이 섹션에 설정 된 값과 스키마를 들어오는 메시지의 ST01 및 GS02에 대 한 값 및 들어오는 메시지의 스키마와 일치 하는 방식으로 들어오는 메시지를 디스어셈블 합니다. 각 행을 완료 한 후에는 새 빈 행이 자동으로 표시 됩니다.

| 속성 | Description |
|----------|-------------|
| **버전** | 스키마의 X12 버전 |
| **트랜잭션 유형(ST01)** | 트랜잭션 유형 |
| **발신자 애플리케이션(GS02)** | 보낸 사람 응용 프로그램 |
| **스키마** | 사용 하려는 스키마 파일 |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>수신 설정-봉투

![인바운드 메시지의 트랜잭션 집합에서 사용할 구분 기호](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| 속성 | Description |
|----------|-------------|
| **ISA11 사용** | 트랜잭션 집합에 사용할 구분 기호입니다. <p>- **표준 식별자**: EDI 수신 파이프라인에서 들어오는 문서의 소수 표기법이 아닌 마침표 (.)를 소수 표기법으로 사용 합니다. <p>- **반복 구분 기호**: 단순 데이터 요소 또는 반복 되는 데이터 구조의 반복 발생에 대 한 구분 기호를 지정 합니다. 예를 들어 일반적으로 캐럿(^)을 반복 구분 기호로 사용합니다. HIPAA 스키마의 경우 캐럿만 사용할 수 있습니다. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>수신 설정-컨트롤 번호

![인바운드 메시지에 대 한 컨트롤 번호 중복 처리](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| 속성 | Description |
|----------|-------------|
| **교환 컨트롤 번호 중복 허용 안 함** | 중복 교환을 차단합니다. 받은 교환 컨트롤 번호의 교환 컨트롤 번호 (ISA13)를 확인 합니다. 일치가 검색 되 면 EDI 수신 파이프라인이 교환을 처리 하지 않습니다. <p><p>확인을 수행할 일 수를 지정 하려면 **중복 된 ISA13 확인 (일)** 속성에 대 한 값을 입력 합니다. |
| **그룹 컨트롤 번호 중복 허용 안 함** | 중복 그룹 컨트롤 번호가 있는 교환을 차단 합니다. |
| **트랜잭션 집합 컨트롤 번호 중복 허용 안 함** | 중복 트랜잭션 집합 컨트롤 번호가 있는 교환을 차단 합니다. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>수신 설정-유효성 검사

![인바운드 메시지의 유효성 검사](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

**기본** 행은 EDI 메시지 유형에 사용 되는 유효성 검사 규칙을 보여 줍니다. 다른 규칙을 정의 하려면 규칙을 **true**로 설정 하려는 각 상자를 선택 합니다. 각 행을 완료 한 후에는 새 빈 행이 자동으로 표시 됩니다.

| 속성 | Description |
|----------|-------------|
| **메시지 유형** | EDI 메시지 유형 |
| **EDI 유효성 검사** | 스키마의 EDI 속성, 길이 제한, 빈 데이터 요소 및 후행 구분 기호로 정의되는 데이터 형식에 대해 EDI 유효성 검사를 수행합니다. |
| **확장 유효성 검사** | 데이터 형식이 EDI가 아니면 데이터 요소 요구 사항에 따라 유효성 검사가 수행 되 고 반복, 열거 및 데이터 요소 길이 유효성 검사 (min 또는 max)가 허용 됩니다. |
| **선행/후행 0 허용** | 선행 또는 후행 0 및 공백 문자를 추가로 유지 합니다. 이러한 문자를 제거하지 마십시오. |
| **선행/후행 0 자르기** | 선행 또는 후행 0 및 공백 문자를 제거 합니다. |
| **후행 구분 기호 정책** | 후행 구분 기호를 생성합니다. <p>- **허용 되지 않음**: 인바운드 교환에서 후행 구분 기호 및 구분 기호를 금지 합니다. 이 경우 교환에 후행 구분 기호가 있으면 잘못된 것으로 선언됩니다. <p>- **선택 사항**: 후행 구분 기호 및 구분 기호를 포함 하거나 포함 하지 않는 교환을 허용 합니다. <p>- **필수**: 인바운드 교환에 후행 구분 기호와 구분 기호가 있어야 합니다. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>수신 설정-내부 설정

![인바운드 메시지에 대 한 내부 설정](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| 속성 | Description |
|----------|-------------|
| **10 진수 형식 Nn을 밑수 10 숫자 값으로 변환** | "Nn" 형식으로 지정 된 EDI 번호를 밑수 10 숫자 값으로 변환 합니다. |
| **후행 구분 기호가 허용되는 경우 빈 XML 태그 만들기** | 교환 발신자가 후행 구분 기호에 대해 빈 XML 태그를 포함 하도록 합니다. |
| **교환을 트랜잭션 집합으로 분할 - 오류 발생 시 트랜잭션 집합 일시 중단** | 트랜잭션 집합에 적절 한 봉투를 적용 하 여 교환의 각 트랜잭션 집합을 별도의 XML 문서로 구문 분석 합니다. 유효성 검사가 실패 하는 트랜잭션만 일시 중단 합니다. |
| **교환을 트랜잭션 집합으로 분할 - 오류 발생 시 교환 일시 중단** | 적절 한 봉투를 적용 하 여 교환의 각 트랜잭션 집합을 별도의 XML 문서로 구문 분석 합니다. 교환에 포함된 하나 이상의 트랜잭션 집합에 대한 유효성 검사가 실패하는 경우 전체 교환을 일시 중단합니다. |
| **교환 유지 - 오류 발생 시 트랜잭션 집합 일시 중단** | 교환을 그대로 유지 하 고 일괄 처리 된 전체 교환에 대해 XML 문서를 만듭니다. 유효성 검사에 실패 한 트랜잭션 집합만 일시 중단 하지만 다른 모든 트랜잭션 집합은 계속 처리 합니다. |
| **교환 유지 - 오류 발생 시 교환 일시 중단** |교환을 그대로 유지하고 배치로 처리된 교환 전체에 대한 XML 문서를 만듭니다. 교환에 포함된 하나 이상의 트랜잭션 집합에 대한 유효성 검사가 실패하는 경우 전체 교환을 일시 중단합니다. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>송신 설정

규약 속성을 설정한 후이 규약에서이 규약을 통해 파트너에 게 보내는 아웃 바운드 메시지를 식별 하 고 처리 하는 방법을 구성할 수 있습니다.

1. **추가** 아래에서 **송신 설정**을 선택합니다.

1. 사용자와 메시지를 교환하는 파트너와의 규약에 따라 이러한 속성을 구성합니다. 속성 설명은 이 섹션에 있는 테이블을 참조하세요.

   **송신 설정은** 다음과 같은 섹션으로 구성 됩니다.

   * [식별자](#outbound-identifiers)
   * [Acknowledgement](#outbound-acknowledgement)
   * [스키마](#outbound-schemas)
   * [봉투](#outbound-envelopes)
   * [컨트롤 버전 번호](#outbound-control-version-number)
   * [컨트롤 번호](#outbound-control-numbers)
   * [문자 집합 및 구분 기호](#outbound-character-sets-separators)
   * [유효성 검사](#outbound-validation)

1. 완료 되 면 **확인**을 선택 하 여 설정을 저장 해야 합니다.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>송신 설정-식별자

![아웃 바운드 메시지에 대 한 식별자 속성](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| 속성 | Description |
|----------|-------------|
| **ISA1(인증 한정자)** | 사용 하려는 권한 부여 한정자 값입니다. 기본값은 **00-인증 정보 없음**입니다. <p>**참고**: 다른 값을 선택 하는 경우 **ISA2** 속성의 값을 지정 합니다. |
| **ISA2** | **ISA1** 속성이 **00**이 아닌 경우 사용할 권한 부여 정보 값입니다. 이 속성 값의 최소 길이는 1 자에서 10 자 사이 여야 합니다. |
| **ISA3(보안 한정자)** | 사용 하려는 보안 한정자 값입니다. 기본값은 **00-보안 정보 없음**입니다. <p>**참고**: 다른 값을 선택 하는 경우 **ISA4** 속성의 값을 지정 합니다. |
| **ISA4** | **ISA3** 속성이 00이 아닌 경우 사용할 보안 정보 값입니다. **보안 정보가**없는 것입니다. 이 속성 값의 최소 길이는 1 자에서 10 자 사이 여야 합니다. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>송신 설정-승인

![아웃 바운드 메시지에 대 한 승인 속성](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| 속성 | Description |
|----------|-------------|
| **TA1 필요** | 교환 보낸 사람에게 기술 승인(TA1)을 반환합니다. <p>이 설정은 메시지를 보내는 호스트 파트너가 규약의 게스트 파트너 로부터 승인을 요청 하도록 지정 합니다. 이러한 승인은 규약의 수신 설정에 따라 호스트 파트너에서 예상 됩니다. |
| **FA 필요** | 교환 보낸 사람에게 기능 승인(FA)을 반환합니다. **FA 버전** 속성의 경우 스키마 버전에 따라 997 또는 999 승인을 선택 합니다. <p>이 설정은 메시지를 보내는 호스트 파트너가 규약의 게스트 파트너 로부터 승인을 요청 하도록 지정 합니다. 이러한 승인은 규약의 수신 설정에 따라 호스트 파트너에서 예상 됩니다. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>송신 설정-스키마

![아웃 바운드 메시지에 대 한 스키마](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

이 섹션에서는 각 트랜잭션 유형 (ST01)에 대 한 [통합 계정](../logic-apps/logic-apps-enterprise-integration-accounts.md) 에서 [스키마](../logic-apps/logic-apps-enterprise-integration-schemas.md) 를 선택 합니다. 각 행을 완료 한 후에는 새 빈 행이 자동으로 표시 됩니다.

| 속성 | Description |
|----------|-------------|
| **버전** | 스키마의 X12 버전 |
| **트랜잭션 유형(ST01)** | 스키마에 대 한 트랜잭션 유형입니다. |
| **스키마** | 사용 하려는 스키마 파일입니다. 먼저 스키마를 선택 하면 버전 및 트랜잭션 유형이 자동으로 설정 됩니다. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>송신 설정-봉투

![아웃 바운드 메시지에 사용할 트랜잭션 집합의 구분 기호](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| 속성 | Description |
|----------|-------------|
| **ISA11 사용** | 트랜잭션 집합에 사용할 구분 기호입니다. <p>- **표준 식별자**: EDI 송신 파이프라인에서 아웃 바운드 문서의 소수 표기법 대신 마침표 (.)를 사용 하 여 소수 표기법을 사용 합니다. <p>- **반복 구분 기호**: 단순 데이터 요소 또는 반복 되는 데이터 구조의 반복 발생에 대 한 구분 기호를 지정 합니다. 예를 들어 일반적으로 캐럿(^)을 반복 구분 기호로 사용합니다. HIPAA 스키마의 경우 캐럿만 사용할 수 있습니다. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>송신 설정-컨트롤 버전 번호

![아웃 바운드 메시지에 대 한 버전 번호 제어](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

이 섹션에서는 각 교환의 [통합 계정](../logic-apps/logic-apps-enterprise-integration-accounts.md) 에서 [스키마](../logic-apps/logic-apps-enterprise-integration-schemas.md) 를 선택 합니다. 각 행을 완료 한 후에는 새 빈 행이 자동으로 표시 됩니다.

| 속성 | Description |
|----------|-------------|
| **컨트롤 버전 번호(ISA12)** | X12 표준의 버전 |
| **사용 표시기(ISA15)** | **테스트** 데이터, **정보** 데이터 또는 **프로덕션** 데이터 인 교환의 컨텍스트입니다. |
| **스키마** | EDI 송신 파이프라인으로 전송 되는 X12 인코딩된 교환의 GS 및 ST 세그먼트를 생성 하는 데 사용할 스키마입니다. |
| **GS1** | 선택 사항으로, 기능 코드를 선택 합니다. |
| **GS2** | 선택 사항으로, 응용 프로그램 발신자를 지정 합니다. |
| **GS3** | 선택 사항으로, 응용 프로그램 수신기를 지정 합니다. |
| **GS4** | 선택 사항으로 **CCYYMMDD** 또는 **YYMMDD**를 선택 합니다. |
| **GS5** | 선택 사항으로 **HHMM**, **HHMMSS**또는 **HHMMSSdd**를 선택 합니다. |
| **GS7** | 선택 사항으로, 담당 에이전시의 값을 선택 합니다. |
| **GS8** | 스키마 문서 버전을 지정 합니다 (옵션). |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>설정 전송-컨트롤 번호

![아웃 바운드 메시지에 대 한 컨트롤 번호](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| 속성 | Description |
|----------|-------------|
| **교환 컨트롤 번호(ISA13)** | 교환 컨트롤 번호에 대 한 값의 범위입니다 .이 값의 최소값은 1이 고 최대값은 999999999입니다. |
| **그룹 컨트롤 번호(GS06)** | 최소값은 1이 고 최대값은 999999999 일 수 있는 그룹 컨트롤 번호의 값 범위입니다. |
| **트랜잭션 집합 컨트롤 번호(ST02)** | 트랜잭션 집합 컨트롤 번호의 값 범위 (최소값은 1이 고 최대값은 999999999 일 수 있음) <p>- **Prefix**: 선택 사항으로 영숫자 값입니다. <br>- **Suffix**: 선택 사항입니다. 영숫자 값입니다. |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>송신 설정-문자 집합 및 구분 기호

![아웃 바운드 메시지의 메시지 유형에 대 한 구분 기호](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

**기본** 행은 메시지 스키마에 대 한 구분 기호로 사용 되는 문자 집합을 표시 합니다. **기본** 문자 집합을 사용 하지 않으려는 경우 각 메시지 유형에 다른 구분 기호 집합을 입력할 수 있습니다. 각 행을 완료 한 후에는 새 빈 행이 자동으로 표시 됩니다.

> [!TIP]
> 특수 문자 값을 제공하려면 JSON으로 계약을 편집하고 특수 문자에 대한 ASCII 값을 제공합니다.

| 속성 | Description |
|----------|-------------|
| **사용할 문자 집합** | **기본**, **확장**또는 **UTF8**인 X12 문자 집합입니다. |
| **스키마** | 사용 하려는 스키마입니다. 스키마를 선택한 후 아래 구분 기호 설명에 따라 사용 하려는 문자 집합을 선택 합니다. |
| **입력 형식** | 문자 집합의 입력 형식입니다. |
| **Component Separator** | 복합 데이터 요소를 구분 하는 단일 문자 |
| **Data Element Separator** | 복합 데이터 내에서 단순 데이터 요소를 구분 하는 단일 문자 |
| **대체 문자 구분 기호** | 아웃 바운드 X12 메시지를 생성할 때 페이로드 데이터의 모든 구분 기호 문자를 대체 하는 대체 문자 |
| **Segment Terminator** | EDI 세그먼트의 끝을 나타내는 단일 문자입니다. |
| **접미어** | 세그먼트 식별자와 함께 사용할 문자입니다. 접미사를 지정 하면 세그먼트 마침 표시 데이터 요소를 비워 둘 수 있습니다. 세그먼트 마침 표시를 비워 두는 경우 접미사를 지정해야 합니다. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>송신 설정-유효성 검사

![아웃 바운드 메시지의 유효성 검사 속성](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

**기본** 행은 EDI 메시지 유형에 사용 되는 유효성 검사 규칙을 보여 줍니다. 다른 규칙을 정의 하려면 규칙을 **true**로 설정 하려는 각 상자를 선택 합니다. 각 행을 완료 한 후에는 새 빈 행이 자동으로 표시 됩니다.

| 속성 | Description |
|----------|-------------|
| **메시지 유형** | EDI 메시지 유형 |
| **EDI 유효성 검사** | 스키마의 EDI 속성, 길이 제한, 빈 데이터 요소 및 후행 구분 기호로 정의되는 데이터 형식에 대해 EDI 유효성 검사를 수행합니다. |
| **확장 유효성 검사** | 데이터 형식이 EDI가 아니면 데이터 요소 요구 사항에 따라 유효성 검사가 수행 되 고 반복, 열거 및 데이터 요소 길이 유효성 검사 (min 또는 max)가 허용 됩니다. |
| **선행/후행 0 허용** | 선행 또는 후행 0 및 공백 문자를 추가로 유지 합니다. 이러한 문자를 제거하지 마십시오. |
| **선행/후행 0 자르기** | 선행 또는 후행 0 및 공백 문자를 제거 합니다. |
| **후행 구분 기호 정책** | 후행 구분 기호를 생성합니다. <p>- **허용 되지 않음**: 아웃 바운드 교환에서 후행 구분 기호 및 구분 기호를 금지 합니다. 이 경우 교환에 후행 구분 기호가 있으면 잘못된 것으로 선언됩니다. <p>- **선택 사항**: 후행 구분 기호 및 구분 기호를 사용 하거나 사용 하지 않고 교환을 보냅니다. <p>- **필수**: 아웃 바운드 교환에 후행 구분 기호와 구분 기호가 있어야 합니다. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA 스키마 및 메시지 유형

HIPAA 스키마 및 277 또는 837 메시지 유형을 사용 하는 경우 몇 가지 추가 단계를 수행 해야 합니다. 이러한 메시지 유형에 대 한 [문서 버전 번호 (GS8)](#outbound-control-version-number) 는 9 자 (예: "005010X222A1")를 포함 합니다. 또한 일부 문서 버전 번호는 변형 메시지 유형에 매핑됩니다. 스키마 및 규약에서 올바른 메시지 유형을 참조 하지 않으면 다음 오류 메시지가 표시 됩니다.

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

이 테이블에는 영향 받는 메시지 유형, 모든 변형 및 해당 메시지 유형에 매핑되는 문서 버전 번호가 나열 됩니다.

| 메시지 유형 또는 변형 |  Description | 문서 버전 번호 (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | 의료 보험 정보 상태 알림 | 005010X212 |
| 837_I | 의료 보험 요구 | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | 의료 보험 요구 기관 | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | 의료 보험 요구 전문가 | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

이러한 문서 버전 번호를 사용 하는 경우에는 문자 길이가 잘못 되었다는 오류가 발생 하므로 EDI 유효성 검사도 사용 하지 않도록 설정 해야 합니다.

이러한 문서 버전 번호와 메시지 유형을 지정 하려면 다음 단계를 수행 합니다.

1. HIPAA 스키마에서 현재 메시지 유형을 사용 하려는 문서 버전 번호의 변형 메시지 유형으로 바꿉니다.

   예를 들어 `005010X222A1` `837` 메시지 유형으로 문서 버전 번호를 사용 하려는 경우를 가정해 보겠습니다. 스키마에서 각 `"X12_00501_837"` 값을 대신 `"X12_00501_837_P"` 값으로 바꿉니다.

   스키마를 업데이트 하려면 다음 단계를 수행 합니다.

   1. Azure Portal에서 통합 계정으로 이동 합니다. 스키마를 찾아 다운로드 합니다. 메시지 유형을 바꾸고 스키마 파일의 이름을 바꾸고 수정 된 스키마를 통합 계정에 업로드 합니다. 자세한 내용은 [Edit 스키마](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas)를 참조 하세요.

   1. 규약의 메시지 설정에서 수정 된 스키마를 선택 합니다.

1. 규약의 `schemaReferences` 개체에서 문서 버전 번호와 일치 하는 변형 메시지 유형을 지정 하는 다른 항목을 추가 합니다.

   예를 들어 `005010X222A1` `837` 메시지 유형에 문서 버전 번호를 사용 하려는 경우를 가정해 보겠습니다. 규약에는 `schemaReferences` 다음과 같은 속성 및 값이 포함 된 섹션이 있습니다.

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   이 `schemaReferences` 섹션에서는 다음 값을 포함 하는 다른 항목을 추가 합니다.

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   완료 되 면 `schemaReferences` 섹션은 다음과 같습니다.

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. 규약의 메시지 설정에서 각 메시지 유형에 대해 또는 **기본값** 을 사용 하는 경우 모든 메시지 유형에 대해 **edi 유효성 검사** 확인란의 선택을 취소 하 여 edi 유효성 검사를 사용 하지 않도록 설정 합니다.

   ![모든 메시지 유형 또는 각 메시지 유형에 대해 유효성 검사를 사용 하지 않도록 설정](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명 된 작업 및 제한과 같이이 커넥터에 대 한 자세한 기술 세부 정보는 [커넥터의 참조 페이지](https://docs.microsoft.com/connectors/x12/)를 참조 하세요.

> [!NOTE]
> [Ise (통합 서비스 환경](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md))의 논리 앱의 경우이 커넥터의 ise 레이블이 지정 된 버전은 [ise에 대 한 B2B 메시지 제한을](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)사용 합니다.

## <a name="next-steps"></a>다음 단계

* [Logic Apps에 대 한 다른 커넥터](../connectors/apis-list.md) 에 대해 알아보기