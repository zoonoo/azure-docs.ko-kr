<properties
	pageTitle="배치 서비스 할당량 및 제한 | Microsoft Azure"
	description="Azure 배치 서비스 사용에 대한 할당량, 제한 및 제약 조건에 알아봅니다."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="danlep"/>



# Azure 배치 서비스에 대한 할당량 및 제한

이 문서에서는 Azure 배치 서비스에 사용할 수 있는 특정 리소스의 기본 및 최대 제한을 나열합니다. 이러한 제한의 대부분은 Azure가 구독 또는 배치 계정에 적용하는 할당량입니다.

프로덕션 배치 작업을 실행하려고 계획하는 경우 하나 이상의 할당량을 기본값 이상으로 늘려야 할 수도 있습니다. 할당량을 늘리려면 무료로 온라인 고객 지원 요청을 개설합니다.

>[AZURE.NOTE]할당량은 신용 한도액일 뿐이며 용량을 보장하는 것은 아닙니다. 대규모 용량이 필요한 경우 Azure 지원에 문의하세요.

## 구독 할당량
리소스|기본 제한|최대 제한
---|---|---
구독당 지역별 배치 계정|1|50

## 배치 계정 할당량
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## 기타 제한
리소스|최대 제한
---|---
컴퓨터 노드당 작업|4 x 노드 코어 수

## 배치 할당량 보기

[Azure 포털](https://portal.azure.com)에서 배치 계정 할당량을 봅니다.

1. 포털에서 **배치 계정**, 배치 계정 이름을 차례로 클릭합니다.

2. 계정 블레이드에서 **설정** > **속성**을 클릭합니다.

	![배치 계정 할당량][account_quotas]

3. **속성** 블레이드에서 현재 배치 계정에 적용되는 할당량을 검토합니다.

## 할당량 증가

다음 단계를 사용하여 Azure 포털에서 할당량 증가를 요청합니다([Azure 클래식 포털](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)에서도 증가 요청 가능).

1. Preview 포털의 대시보드에서 **도움말+지원**을 클릭합니다.

2. **Create support request(지원 요청 만들기)> 기본**을 클릭합니다.

3. **기본** 블레이드에서 다음을 수행합니다.

	a. **문제점 유형**에서 **할당량**을 선택합니다.

	b. 사용 중인 구독을 선택합니다.

	c. **서비스**에서 **배치 서비스**를 선택합니다.

	d. **지원 플랜**에서 **Azure 지원 플랜 - 개발자**를 선택합니다.

	**다음**을 클릭합니다.

4. **문제** 블레이드에서 다음을 수행합니다.

	a. **문제 유형**에서 **배치**를 선택합니다.

	b. **세부 정보**에서 특정 계정의 변경하려는 할당량과 원하는 새 제한을 나열합니다.

	**다음**을 클릭합니다.

5. **연락처 정보** 블레이드에서 연락처 세부 정보를 입력하고 **다음**을 클릭합니다.

6. **만들기**를 클릭하여 새 지원 요청을 제출합니다.

Azure 지원에서 사용자에게 연락합니다. 요청을 완료하려면 업무일 기준 최대 2일이 걸릴 수 있습니다.

## 관련된 항목

* [Azure 배치 계정 만들기 및 관리](batch-account-create-portal.md)

* [Azure 배치 기능 개요](batch-api-basics.md)

* [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=AcomDC_1210_2015-->