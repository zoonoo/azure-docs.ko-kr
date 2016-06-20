<properties
   pageTitle="FAQ"
   description="Power BI Embedded FAQ"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/01/2016"
   ms.author="derrickv"/>

# Power BI Embedded FAQ

1. **빌드 시 Power BI에 대해 무엇을 알렸나요?**

    Microsoft는 Microsoft Power BI Embedded 서비스 공개 미리 보기를 지금 사용할 수 있는지를 발표했습니다.

2.	**Microsoft Power BI Embedded란?**

    Microsoft Power BI Embedded는 사용자 컨트롤을 처음부터 빌드해야 하는 시간과 비용을 들이지 않고 응용 프로그램 개발자가 뛰어나고 완전한 대화형 보고서 및 시각화를 고객 쪽 앱에 포함할 수 있도록 하는 Azure 서비스입니다. 기본적으로 제공되는 다양한 데이터 시각화 중에서 선택하거나 응용 프로그램의 고유한 요구에 맞게 사용자 지정 시각화를 쉽게 작성할 수 있습니다. Power BI Embedded는 더 큰 응용 프로그램의 일부로 BI를 고객에게 제공하려는 ISV/응용 프로그램 개발자가 사용하기 위한 것입니다.

3.	**Microsoft Power BI Embedded를 누가 사용하고 사용하는 이유는 무엇인가요?**

    Microsoft Power BI Embedded는 사용자가 직접 빌드하지 않아도 모든 장치에 걸쳐 사용자에게 뛰어난 대화형 데이터 시각화 환경을 제공하려는 응용 프로그램 개발자를 위한 것입니다. Power BI Embedded로 개발자는 **직접 쿼리**를 사용하여 항상 최신 보기를 제공할 수 있습니다. 개발자는 또한 프로그래밍 방식으로 Azure ARM API 및 Power BI API를 사용하여 Power BI를 배포, 관리 및 자동화할 수도 있습니다. 모든 Power BI와 마찬가지로 포함된 서비스는 응용 프로그램의 사용량 및 수요에 맞게 자동으로 조정됩니다. Power BI Embedded 서비스는 종량제 사용량 기반 가격 책정 모델 기능을 갖추고 있습니다. 가격 책정에 대한 자세한 내용은 [이 서비스의 가격은 어떻게 책정되나요?](#price)를 참조하세요.

4.	**Power BI Embedded는 Power BI 서비스와 어떤 관련이 있나요?**

    Power BI Embedded와 Power BI 서비스는 별개의 서비스입니다. Power BI Embedded는 사용량 기반 요금 청구 모델 기능을 갖추고 있으며 Azure 포털을 통해 배포되고 ISV가 고객이 사용할 수 있도록 응용 프로그램의 데이터 시각화를 포함할 수 있도록 디자인되었습니다. Power BI 서비스는 O365 포털을 통해 청구 및 배포되고, 주 사용 대상이 엔터프라이즈 내부용인 독립 실행형 일반용 BI 서비스입니다. Power BI 서비스에 대한 자세한 내용을 보려면 [www.powerbi.com](www.powerbi.com)을 방문하세요.

5.	**Power BI Embedded를 통해 내 앱을 어떻게 향상시키나요?**

    응용 프로그램은 데이터 시각화를 활용하여 응용 프로그램에서 사용자 결정을 직접 제공하는 경우 훨씬 더 강력합니다. Power BI Embedded를 사용하면 대화형이고 항상 최신인 풍부한 데이터 시각화로 앱을 향상시키므로 앱의 유틸리티, 사용자 만족도 및 충성도를 향상시키고 상황별 분석을 모든 장치에서 쉽게 제공할 수 있습니다.

6.	**내 앱에서 Power BI Embedded를 사용하는 방법에 대한 규칙이나 제한 사항이 있나요?**

    (1) 응용 프로그램이 Power BI Embedded 서비스에 중요한 기본 기능을 추가하고 Power BI 서비스에 대한 주요 대체 서비스가 아닌 경우, (2) 응용 프로그램이 회사 외부 사용자 전용인 경우, 개발하는 응용 프로그램 내에서만 Power BI Embedded 서비스를 사용할 수 있습니다. 내부 비즈니스 응용 프로그램 내에서는 Power BI Embedded 서비스를 사용할 수 없습니다.

7.	**Power BI Embedded를 내부 응용 프로그램을 만드는 데 사용할 수 있나요?**

    아니요, Power BI Embedded는 외부 사용자가 사용할 목적으로만 제공되며 내부 비즈니스 응용 프로그램 내에서는 사용할 수 없습니다. 내부 비즈니스 응용 프로그램용으로 Power BI 콘텐츠를 포함하려면 Power BI 서비스를 사용해야 합니다. 해당 콘텐츠를 사용하는 모든 사용자는 유효한 Power BI Free 또는 Power BI Pro 사용자 구독 라이선스가 있어야 합니다. Power BI 서비스를 사용한 내부 포함에 대한 자세한 내용은 [https://dev.powerbi.com](https://dev.powerbi.com)에서 확인할 수 있습니다.

8.	**이 서비스는 전 세계적으로 사용할 수 있나요?**

    Power BI Embedded 서비스는 미국 중남부 데이터 센터의 BUILD 2016 알림 시점에 북아메리카에서 사용 가능합니다. 이 서비스는 Azure 데이터 센터의 다른 지역에도 곧 롤아웃될 계획입니다.

9. **어떤 SLA가 서비스에 사용 가능한가요?**

    Power BI Embedded는 현재 공식 SLA가 없는 미리 보기 Azure 서비스로 사용할 수 있습니다. SLA는 서비스가 미리 보기에서 일반 공급으로 전환될 때 제공됩니다.

<a name="price"/>
10.	**이 서비스의 가격은 어떻게 책정되나요?**

    Power BI Embedded는 현재 미리 보기로 제공되며 2016년 5월 1일까지 무료로 제공됩니다. 2016년 5월 1일부터는 서비스가 유료로 제공됩니다. 고객은 두 가지 기본 라이선스 방식인 MOSP(Microsoft Online Subscription Program) 또는 엔터프라이즈 VL 프로그램을 통해 서비스를 구입할 수 있습니다.

    (1) 응용 프로그램이 Microsoft 서비스에 중요한 기본 기능을 추가하고 Power BI 서비스에 대한 주요 대체 서비스가 아닌 경우, (2) 응용 프로그램이 외부 사용자 전용인 경우, 개발하는 응용 프로그램 내에서만 Power BI Embedded 서비스를 사용할 수 있습니다. 내부 비즈니스 응용 프로그램 내에서는 Power BI Embedded 서비스를 사용할 수 없습니다.


   ![](media\power-bi-embedded-faq\price.png)

11.	**렌더란 무엇이며 어떻게 청구되나요?**

    렌더링은 시각화가 서비스에 대한 쿼리가 필요한 최종 사용자에게 표시될 때 발생합니다. Power BI 서비스는 응용 프로그램에 요금이 부과되는 렌더링의 수를 줄이기 위해 가능하면 렌더링된 콘텐츠를 캐시하려고 시도합니다. 그러나 필터링과 같은 사용자 작업으로 인해 서비스에 쿼리가 발생할 수 있으며 이는 새 렌더링을 구성합니다.

    예를 들어 사용자가 네 개의 시각적 개체를 포함하는 보고서를 보는 경우 네 개의 렌더링이 발생할 수 있습니다. 사용자가 보고서를 새로 고치고 서비스에 더 많은 쿼리가 전송되면 렌더는 4개 이상이 됩니다. 서비스 소유자는 정적 데이터 시나리오에서 비용 표시를 제한하고 비용을 최소화하기 위해 최종 사용자가 유료 렌더가 되는 새 쿼리를 추진할 수 있는 범위를 제어할 수 있습니다.

    렌더는 1,000개 렌더당 청구됩니다. 1,000개 미만의 렌더는 비례하여 청구됩니다. 고객은 매월 1,000개의 무료 렌더를 받습니다. 볼륨 라이선싱 계약을 통해 구입하는 고객은 Microsoft 파트너 또는 판매자에게 가격 정보를 문의해야 합니다.

12.	**렌더링의 수를 예상할 수 있도록 도구 또는 참고 자료를 제공하나요? 완료된 렌더링의 수를 알 수 있는 방법은 무엇인가요?**

    Azure 포털에서는 구독에 대해 수행된 렌더링의 수에 대한 청구 세부 정보를 제공합니다.

13.	**Power BI Embedded로 응용 프로그램을 개발하려면 Power BI 구독이 필요한가요? 어떻게 시작하나요?**

    응용 프로그램 개발자에게 응용 프로그램에서 사용하려는 보고서 및 시각화를 만들기 위해 Power BI 구독이 필요하지 않습니다. Microsoft Azure 구독 및 무료 Power BI Desktop 응용 프로그램이 필요합니다.

    Power BI Embedded 서비스를 사용하는 방법에 대한 자세한 내용은 서비스 설명서를 참조하세요.

14.	**Azure 구독이 있습니다. 기존 구독을 사용하여 Power BI Embedded를 사용할 수 있나요?**

    예. 기존 Azure 구독을 사용하여 Microsoft Power BI Embedded 서비스를 프로비전하고 사용할 수 있습니다.

15.	**응용 프로그램 최종 사용자에게 Power BI 라이선스가 필요한가요?**

    아니요. 응용 프로그램의 최종 사용자는 앱 내 데이터 시각화에 액세스하기 위해 별도로 Power BI 구독을 구매할 필요가 없습니다. Power BI Embedded 모델에서는 응용 프로그램 공급자에게 Azure 소비 측정기를 통해 서비스에 대한 요금이 청구됩니다. [가격 책정 및 라이선스 페이지](http://go.microsoft.com/fwlink/?LinkId=760527)를 참조하세요.

16.	**사용자 인증이 Power BI Embedded와 작동하는 방법은 무엇인가요?**

    Power BI Embedded 서비스는 명시적인 최종 사용자 인증 대신 인증 및 권한 부여에 대한 앱 토큰을 사용합니다. 앱 토큰 모델에서 응용 프로그램이 최종 사용자에 대한 인증 및 권한 부여를 관리합니다. 그런 다음 필요한 경우 앱이 서비스에 요청된 보고서를 렌더링하라고 지시하는 앱 토큰을 만들어 보냅니다. 이 디자인에서는 앱이 사용자 인증 및 권한 부여에 Azure Active Directory를 사용할 수는 있지만 그럴 필요가 없습니다. 앱 토큰에 대한 자세한 내용은 [앱 토큰](https://azure.microsoft.com/ko-KR/documentation/articles/power-bi-embedded-get-started-sample/#key-flow) 설명서 페이지를 참조하세요.

17.	**Power BI Embedded로 현재 지원되는 데이터 원본은 무엇인가요?**

    서비스의 공개 미리 보기 동안 직접 쿼리를 통해 기본 자격 증명을 사용하는 클라우드 데이터 원본에 대한 액세스를 지원할 예정입니다. 즉, Azure SQL DB, HDInsight Spark 및 Azure SQL DW와 같은 원본을 현재 지원하고 있습니다. 몇 달 이내에 다른 데이터 원본 및 액세스 유형에 대한 지원도 추가됩니다. Power BI 개발자 사이트 [http://dev.powerbi.com](http://dev.powerbi.com/)에서 새로 지원되는 데이터 원본을 발표할 예정입니다.

18.	**Power BI Embedded에 대한 테넌트 모델은 어떻게 작동하나요?**

    Power BI Embedded 모델에는 Azure AD(Azure Active Directory) 테넌트의 고객이 있어야 한다는 요구 사항이 없습니다. 고객에 대한 Azure AD를 요구하도록 선택하거나 선택하지 않을 수 있습니다. 결과적으로 응용 프로그램의 아키텍처 및 인프라는 Power BI Embedded에 필요한 테넌트 모델을 결정하는 것입니다.

    응용 프로그램을 빌드하거나 작업하는 개발자 또는 직원에게는 Azure 포털을 통해 Azure 구독 및 작업 영역 컬렉션을 관리할 수 있도록 Azure AD 사용자 계정이 있어야 합니다. 개발자가 보고서를 가져오고, 연결 문자열을 수정하고, 포함된 URL을 가져오도록 하는 프로그래밍 API에서는 인증에 앱 토큰이 대신 사용되며 결과적으로 Azure AD가 필요하지 않습니다. API 및 Azure 포털을 사용하는 방법에 대한 자세한 내용은 [Azure.com의 Power BI Embedded 설명서 페이지](https://azure.microsoft.com/ko-KR/documentation/services/power-bi-embedded/)에서 확인할 수 있습니다.

19.	**자세한 내용을 알아보려면 어떤 정보를 참조해야 하나요?**

    [Power BI Embedded 설명서 페이지](http://go.microsoft.com/fwlink/?LinkId=760526)를 방문하여 참조하세요. [Power BI 개발자 블로그](http://blogs.msdn.com/powerbidev) 또는 dev.powerbi.com의 Power BI 개발자 센터를 방문하여 이 서비스에 대한 최신 정보를 언제든지 확인할 수 있습니다. [Stackoverflow](http://stackoverflow.com/questions/tagged/powerbi)에 질문할 수도 있습니다.

20.	**어떻게 시작하나요?**

    지금 무료로 시작할 수 있습니다. Azure 구독이 있는 경우 이제 Azure 포털에서 직접 Power BI Embedded를 프로비전할 수 있습니다. [무료 Azure 계정](https://azure.microsoft.com/free/)을 만들 수도 있습니다. Power BI Embedded 서비스를 프로비전하면 직접 Power BI REST API를 쉽게 사용하거나 [GitHub](http://go.microsoft.com/fwlink/?LinkID=746472)에서 사용할 수 있는 개발자 SDK를 사용할 수 있습니다. 개발자 SDK를 활용하는 방법에 대한 샘플을 제공합니다.

## 참고 항목

- [Microsoft Power BI Embedded란](power-bi-embedded-what-is-power-bi-embedded.md)
- [Microsoft Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0608_2016-->