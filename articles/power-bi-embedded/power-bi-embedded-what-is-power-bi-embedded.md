<properties
   pageTitle="Microsoft Power BI Embedded란"
   description=""
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
   ms.date="03/29/2016"
   ms.author="jocaplan"/>


# Microsoft Power BI Embedded란

**Microsoft Power BI Embedded**를 사용하면 Power BI 보고서를 웹 또는 모바일 응용 프로그램으로 통합할 수 있으므로 사용자에게 데이터를 시각화하기 위해 사용자 지정 솔루션을 빌드할 필요가 없습니다.

![](media\powerbi-embedded-whats-is\what-is.png)

**Microsoft Power BI Embedded**는 ISV(독립 소프트웨어 공급업체)가 응용 프로그램 내에서 Power BI 데이터 환경을 표시할 수 있도록 하는 Azure 서비스입니다. ISV가 응용 프로그램을 빌드했습니다. 이러한 응용 프로그램에는 고유한 사용자 및 고유한 기능 집합이 있습니다. 이러한 앱에는 이제 **Microsoft Power BI Embedded**를 통해 강력한 기능을 가질 수 있는 차트 및 보고서와 같은 일부 기본 제공 데이터 요소가 있을 수도 있습니다. 응용 프로그램의 사용자는 앱을 사용하기 위한 Power BI 계정이 필요 없습니다. 사용자는 이전처럼 응용 프로그램에 계속 로그인할 수 있으며 추가 라이선스를 요구하지 않고 Power BI 보고 및 타일 환경을 보고 조작할 수 있습니다.

## Microsoft Power BI Embedded 라이선스

**Microsoft Power BI Embedded** 사용 모델에서 Power BI 라이선스에 대한 책임이 최종 사용자에게 있지 않습니다. 대신 시각적 개체를 사용하는 앱의 개발자가 **렌더링**을 구매하고 해당 리소스를 소유하는 구독에 요금이 부과됩니다.

## Microsoft Power BI Embedded 개념적 모델

![](media\powerbi-embedded-whats-is\model.png)

Azure의 다른 서비스와 같이 **Microsoft Power BI Embedded**의 리소스는 [Azure ARM API](https://msdn.microsoft.com/library/mt712306.aspx)를 통해 프로비전됩니다. 이 경우에 프로비전하는 리소스는 **Power BI 작업 영역 컬렉션**입니다.

## 작업 영역 컬렉션

**작업 영역 컬렉션**은 0개 이상의 **작업 영역**을 포함하는 리소스의 최상위 Azure 컨테이너입니다. **작업 영역** **컬렉션**에는 모든 표준 Azure 속성과 다음 항목이 있습니다.

-	**액세스 키** - Power BI API(다음 섹션에서 설명)를 안전하게 호출할 때 사용하는 키입니다.
-	**사용자** - Azure 포털 또는 ARM API를 통해 Power BI 작업 영역 컬렉션을 관리하는 관리자 권한이 있는 AAD(Azure Active Directory) 사용자입니다.
-	**지역** – **작업 영역 컬렉션** 프로비전의 일부이며 프로비전할 지역을 선택할 수 있습니다. 자세한 내용은 [Azure 영역](https://azure.microsoft.com/regions/)을 참조하세요.

## 작업 영역

**작업 영역**은 데이터 집합, 보고서 및 대시보드를 포함할 수 있는 Power BI 콘텐츠의 컨테이너입니다. **작업 영역**은 처음 만들 때 비어 있습니다. 미리 보기 중에 Power BI Desktop을 사용하여 모든 콘텐츠를 작성하고 [Power BI REST API](http://docs.powerbi.apiary.io/reference)를 사용하여 작업 영역 중 하나에 업로드합니다.

## 작업 영역 컬렉션 및 작업 영역 사용
**작업 영역 컬렉션** 및 **작업 영역**은 빌드할 응용 프로그램의 디자인에 가장 적합한 방법으로 사용되거나 구성되는 콘텐츠의 컨테이너입니다. 컨테이너 내에서 콘텐츠를 정렬할 수 있는 다양한 방법이 있습니다. 하나의 작업 영역 내에 모든 콘텐츠를 배치한 다음 나중에 앱 토큰을 사용하여 고객 간 콘텐츠를 더 세분화하는 방법을 선택할 수 있습니다. 또한 고객이 어느 정도 구분이 되도록 모든 고객을 분할된 작업 영역에 배치하는 방법을 선택할 수 있습니다. 또는 고객이 아닌 지역으로 사용자를 구성하는 방법을 선택할 수 있습니다. 이 유연한 디자인을 사용하면 콘텐츠를 구성할 가장 좋은 방법을 선택할 수 있습니다.
## 미리 보기의 데이터 원본

다음과 같이 미리 보기용으로 제한된 데이터 원본 집합을 사용할 수 있도록 지원할 예정입니다.

### 직접 쿼리

미리 보기용 클라우드 소스에 대한 직접 쿼리 연결을 지원합니다. 즉, 해당 데이터 원본에 연결하여 최신 데이터를 표시할 수 있습니다. 이러한 데이터 원본은 클라우드에서 연결할 수 있어야 하며 기본 인증을 사용해야 합니다. 이에 대한 몇 가지 이상적인 후보는 다음과 같습니다.

-	SQL Azure
-	SQL Azure DW
-	HD Insight Spark

## 캐시된 데이터 집합

캐시된 데이터 집합은 미리 보기에서 사용할 수 있습니다. 그러나 캐시된 데이터가 **Microsoft Power BI Embedded**에 로드되면 새로 고칠 수 없습니다.

## 앱 토큰으로 인증 및 권한 부여

**Microsoft Power BI Embedded**는 응용 프로그램에 따라 필요한 모든 사용자 인증 및 권한 부여를 수행합니다. 최종 사용자가 Azure AD(Azure Active Directory)의 고객이어야 한다는 명시적인 요구 사항은 없습니다. 대신, 응용 프로그램은 **응용 프로그램 인증 토큰(앱 토큰)**을 통해 Power BI 보고서를 **Microsoft Power BI Embedded**로 렌더링하는 권한 부여를 나타냅니다. 이러한 **앱 토큰**은 앱에서 보고서를 렌더링하려고 할 때 필요한 경우에 만들어집니다. [앱 토큰](power-bi-embedded-get-started-sample.md#key-flow)을 참조하세요.

![](media\powerbi-embedded-whats-is\app-tokens.png)

### 응용 프로그램 인증 토큰

**응용 프로그램 인증 토큰(앱 토큰)**은 **Microsoft Power BI Embedded**에 대한 인증에 사용됩니다. 세 가지 유형의 **앱 토큰**이 있습니다.

1.	프로비저닝 토큰 - 새 **작업 영역**을 **작업 영역 컬렉션**에 프로비전할 때 사용
2.	개발 토큰 - **Power BI REST API**를 직접 호출할 때 사용
3.	포함 토큰 - 포함된 iframe에서 보고서를 렌더링하도록 호출할 때 사용

이러한 토큰은 **Microsoft Power BI Embedded**와 상호 작용하는 여러 단계에 사용됩니다. 앱에서 Power BI로 사용 권한을 위임할 수 있도록 토큰을 디자인합니다.

### 앱 토큰 생성

미리 보기로 제공되는 SDK를 사용하여 토큰을 생성할 수 있습니다. 먼저 Create\_\_\_Token() 메서드 중 하나를 호출합니다. 그런 다음 **작업 영역 컬렉션**에서 검색한 액세스 키로 Generate() 메서드를 호출합니다. 토큰에 대한 기본 Create 메서드는 Microsoft.PowerBI.Security.PowerBIToken 클래스에 정의되어 있으며 다음과 같습니다.

-	[CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx)
-	[CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx)
-	[CreateReportEmbedToken](https://msdn.microsoft.com/library/mt710366.aspx)

[CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx) 및 [CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx)을 사용하는 방법에 대한 예는 [Microsoft Power BI Embedded 샘플 코드 시작](power-bi-embedded-get-started-sample.md)을 참조하세요.


## 참고 항목
- [일반적인 Microsoft Power BI Embedded 시나리오](power-bi-embedded-scenarios.md)
- [Microsoft Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)
- [앱 토큰](power-bi-embedded-get-started-sample.md#key-flow)
- [Power BI REST API](http://docs.powerbi.apiary.io/reference)
- [Azure 지역](https://azure.microsoft.com/regions/)

<!---HONumber=AcomDC_0406_2016-->