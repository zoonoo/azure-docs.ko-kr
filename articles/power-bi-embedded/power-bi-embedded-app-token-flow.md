<properties
   pageTitle="Power BI Embedded의 앱 토큰 흐름 정보"
   description="인증 및 권한 부여를 위한 앱 토큰에 대한 Power BI Embedded"
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
   ms.author="derrickv"/>

# Power BI Embedded의 앱 토큰 흐름 정보

**Power BI Embedded** 서비스는 명시적인 최종 사용자 인증 대신 인증 및 권한 부여에 대한 **앱 토큰**을 사용합니다. **앱 토큰** 모델에서 응용 프로그램이 최종 사용자에 대한 인증 및 권한 부여를 관리합니다. 필요한 경우 앱이 서비스에 요청된 보고서를 렌더링하라고 지시하는 **앱 토큰**을 만들어 보냅니다. 이 디자인에서는 앱이 사용자 인증 및 권한 부여에 **Azure Active Directory**를 사용할 수는 있지만 그럴 필요가 없습니다.

**다음은 앱 토큰 키의 흐름 방식입니다.**

1. 응용 프로그램에 API 키 복사 **Azure 포털**에서 키를 가져올 수 있습니다.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. 토큰이 클레임을 어설션하며 만료 시간이 있습니다.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. 토큰이 API 액세스 키로 서명됩니다.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. 사용자가 보고서 보기를 요청합니다.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.	API 액세스 키로 토큰의 유효성이 검사됩니다.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.	Power BI Embedded에서 사용자에게 보고서를 보냅니다.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

**Power BI Embedded**에서 사용자에게 보고서를 보낸 후 사용자는 사용자 지정 앱에서 보고서를 볼 수 있습니다. 예를 들어 [판매 데이터 PBIX 분석 샘플](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)을 가져온 경우 샘플 웹앱이 다음과 같이 보입니다.

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## 참고 항목
- [Microsoft Power BI Embedded 샘플 시작](power-bi-embedded-get-started-sample.md)
- [Microsoft Power BI Embedded란](power-bi-embedded-what-is-power-bi-embedded.md)
- [일반적인 Microsoft Power BI Embedded 미리 보기 시나리오](power-bi-embedded-scenarios.md)
- [Microsoft Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0420_2016-->