<properties 
	pageTitle="Microsoft Azure 구독에 대한 청구 경고 설정" 
	description="갑작스러운 청구에 당황하지 않도록 Azure 청구에 대한 경고를 설정하는 방법을 설명합니다." 
	services="" 
	documentationCenter="" 
	authors="vikdesai" 
	manager="msmbaldwin" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="vikdesai"/>

# Microsoft Azure 구독에 대한 청구 경고 설정

Azure 구독에 매월 지출하는 금액이 걱정되시나요? Azure 구독의 계정 관리자인 경우 Azure 청구 경고 서비스를 사용하여 Azure 계정에 대한 청구 활동을 모니터링하고 관리하는 데 도움이 되는 사용자 지정된 청구 경고를 만들 수 있습니다.

이 서비스는 미리 보기 서비스이므로 먼저 등록해야 합니다. Azure 계정 관리 포털의 <a href="https://account.windowsazure.com/PreviewFeatures">미리 보기 기능 페이지</a>를 방문하세요.

## 경고 임계값 및 메일 받는 사람 설정

구독에 대해 청구 서비스가 설정되었다는 메일 확인을 받은 후에 계정 포털의 <a href="https://account.windowsazure.com/Subscriptions">구독 페이지</a>를 방문합니다. 모니터링할 구독을 클릭한 다음 **경고**를 클릭합니다.

![][Image1]

그런 다음 **경고 추가**를 클릭하여 첫 번째 경고를 만듭니다. 구독당 최대 총 5개의 청구 경고를 설정할 수 있으며, 각 경고에 대해 서로 다른 임계값을 설정하고, 각 경고에 대해 최대 두 명의 메일 받는 사람을 설정할 수 있습니다.

![][Image2]

경고를 추가할 때 고유 이름을 지정하고 지출 임계값을 선택하며 경고를 전송할 메일 주소를 선택합니다. 임계값을 설정할 때 **경고 대상** 목록에서 **청구 금액 합계** 또는 **금액 크레딧**을 선택할 수 있습니다. 청구 금액 합계의 경우 경고는 구독 지출이 임계값을 초과하면 전송됩니다. 금액 크레딧의 경우 경고는 금액 크레딧이 한도 아래로 떨어지면 전송됩니다. 금액 크레딧은 일반적으로 MSDN 계정과 연결된 무료 평가판 및 구독에 적용됩니다.

![][Image3]

Azure는 모든 메일 주소를 지원하지만 해당 메일 주소가 작동하는지 확인하지 않으므로 오타가 없는지 다시 한 번 확인하세요.

## 경고 확인

경고를 설정하고 나면 계정 센터에 해당 경고가 나열되고 더 설정할 수 있는 경고 횟수가 표시됩니다. 각 경고에 대해 전송된 날짜 및 시간, 청구 금액 합계에 대한 경고인지 금액 크레딧에 대한 경고인지 여부 및 설정한 한도가 표시됩니다. 날짜 및 시간 형식은 24시간 UTC(Universal Time Coordinate)이며 날짜는 yyyy-mm-dd 형식입니다. 경고를 편집하려면 목록에서 경고의 더하기 기호를 클릭하고, 경고를 삭제하려면 휴지통을 클릭합니다.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png

<!---HONumber=July15_HO3-->