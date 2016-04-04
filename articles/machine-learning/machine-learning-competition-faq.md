<properties
	pageTitle="기계 학습 경쟁 FAQ | Microsoft Azure"
	description="Microsoft Cortana 분석 제품군 경쟁 관련 질문과 대답입니다."
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/11/2016"
	ms.author="haining;chlovel;garye"/>

# Microsoft Cortana 분석 제품군 경쟁 관련 FAQ

**데이터 과학에 대한 일반적인 질문을 어디서 할 수 있나요?**

[Microsoft Azure 기계 학습 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning)을 사용하세요.

**경쟁에 참가하려면 어떻게 해야 하나요?**

[Microsoft Azure 기계 학습](https://studio.azureml.net/?selectAccess=true&o=2%22%20\t%20%22_blank)에서 무료 또는 유료 계정을 만듭니다. 경쟁 자습서를 수행하고 간략한 비디오를 봅니다.

**참가하려면 데이터 과학자여야 하나요?**

아니요. 실제로 데이터 과학자가 아닌 데이터 고급 사용자 및 비 전문 데이터 과학자가 콘테스트에 참가하는 것이 좋습니다. 지원 문서는 모든 사용자가 경쟁할 수 있도록 디자인되었습니다.

**시간은 얼마나 걸리나요?**

경쟁에 참가하여 10-15분 내에 데이터 모델을 만들 수 있습니다. 예측 실험 구체화는 기계 학습에 대한 숙련도에 따라 15분 이상 걸릴 수 있습니다.

**내 점수는 어떻게 계산되나요? 경쟁이 실행되는 동안과 끝난 후에 다르게 계산되나요? 공용 및 개인 점수는 무엇인가요?**

1.  전체 데이터 집합은 학습(60%) 및 테스트(나머지 40%) 데이터로 임의로 층화되어 분할되었습니다. 임의 분할은 학습 및 테스트 데이터의 레이블 분포가 각 지역에 일관되도록 지역 + 세그먼트 + 하위 그룹으로 층화됩니다.  

2.  판독기 모듈 구성의 시작 실험의 일부로 학습 데이터가 업로드되어 제공되었습니다.

3.  동일한 층화(공용 테스트용 60% 및 개인 테스트용 40%)를 사용하여 테스트 데이터가 공용 및 개인 테스트 데이터로 추가로 분할되었습니다.

4.  공용 테스트 데이터가 점수 매기기의 초기 라운드에 사용되었습니다. 결과는 공용 점수라고도 하며 출품작을 제출했을 때 제출 내역에서 얻은 것입니다. 출품작을 제출할 때마다 수행되었습니다. 이 공용 점수는 공용 순위표에서 순위를 지정할 때 사용됩니다.

5.  개인 테스트 데이터는 경쟁이 끝난 후 점수 매기기의 최종 라운드에 사용되었습니다. 개인 점수라고도 합니다.

6.  각 참가자에 대해 공용 점수가 가장 높은 5개의 항목이 자동으로 선택되어 개인 점수를 계산합니다. 개인 점수가 가장 높은 항목이 선택되어 최종 우승자를 결정하는 최종 순위에 입력됩니다.

**요금을 지불해야 하나요?**

아니요, Microsoft Azure 기계 학습에서 무료 작업 영역을 설정하여 경쟁할 수 있습니다. 게스트 계정은 데이터를 저장할 수 없으므로 무료 8시간이 만료되기 전에 출품작을 제출해야 합니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**유료 구독으로 업그레이드하려면 어떻게 해야 하나요?**

Microsoft Azure 마켓플레이스의 이 페이지에서 구독을 구매 또는 업데이트할 수 있습니다. 가격 FAQ를 보려면 [이 페이지](https://azure.microsoft.com/pricing/details/machine-learning/)를 참조하세요.

**벨리즈 시도 단추란 무엇인가요?**

(웹 서비스 포털 지침 및 돌아가기 위한 단계별 스크린샷 포함)

**경쟁에서 우승하면 어떻게 되나요?**

Microsoft에서 개인 순위표의 결과를 확인한 다음 연락드립니다. 사용자 프로필의 메일 주소가 최신 상태인지 확인하세요.

**경쟁에서 우승하면 상금은 어떻게 받나요?**

경쟁 우승자인 경우 자격 상태, 라이선스 및 릴리스의 선언에 서명해야 합니다. 이 양식에서는 경쟁 규칙을 반복합니다. 우승자는 미국 세금 양식 W-9를 작성하거나 미국 납세자가 아닌 경우 양식 W-8BEN을 작성해야 합니다.

<!---HONumber=AcomDC_0323_2016-->