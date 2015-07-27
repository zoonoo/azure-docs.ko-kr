<properties 
	pageTitle="Azure 앱 서비스 및 기존 Azure 서비스에 미치는 영향" 
	description="새로운 Azure 앱 서비스와 해당 기능이 Azure의 기존 서비스에 미치는 영향을 설명합니다." 
	authors="yochayk" 
	editor="yochayk" 
	manager="nirma" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="yochayk"/>


# Azure 앱 서비스 및 기존 Azure 서비스

이 문서에서는 여러 Azure 서비스를 새로운 통합 제품인 [Azure 앱 서비스](http://azure.microsoft.com/services/app-service/)로 결합하는 변경의 일부로 적용된 기존 Azure 서비스의 변경 내용을 설명합니다.

## 개요 

[Azure 앱 서비스](http://azure.microsoft.com/services/app-service/)는 개발자가 모든 플랫폼 및 장치용 웹앱과 모바일 앱을 만들 수 있게 해주는 고유한 새 클라우드 서비스입니다. 앱 서비스는 반복 코딩 기능을 간소화하고 엔터프라이즈 및 SaaS 시스템과 통합되며 보안, 안정성 및 확장성 요구를 충족하는 동시에 비즈니스 프로세스를 자동화하도록 설계된 통합 솔루션입니다.

앱 서비스는 강력한 새 기능을 추가하는 동시에 [웹 사이트](http://azure.microsoft.com/services/websites/), [모바일 서비스](http://azure.microsoft.com/services/mobile-services/) 및 [Biztalk 서비스](http://azure.microsoft.com/services/biztalk-services/)와 같은 기존 Azure 서비스를 하나의 통합 서비스로 결합합니다. 앱 서비스를 통해 다음과 같은 앱 유형을 호스트할 수 있습니다.

-   웹앱
-   모바일 앱
-   API 앱
-   논리 앱

다음 표에서는 기존 Azure 서비스와 앱 서비스 간의 매핑 및 앱 서비스 내에서 사용할 수 있는 앱 유형을 설명합니다.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">기존 Azure 서비스</th>
<th align="left", style="width:10%">Azure 앱 서비스</th>
<th align="left", style="width:80%">변경 내용</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure 웹 사이트</td>
<td align="left">웹앱</td>
<td align="left"><li>Azure 웹 사이트의 경우 앱 서비스가 웹 사이트에서 웹앱으로의 이름 변경만으로 엄격하게 제한됩니다.
<p><li>모든 기존 웹 사이트 인스턴스는 이제 앱 서비스에서 웹앱이 됩니다.</p>
<p><li><em>웹앱</em> 아래에 기존 사이트가 모두 나열되어 있는 <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Azure 포털</a>을 통해 기존 웹 사이트에 액세스할 수 있습니다.</p>
<p><li><em>웹 호스팅 계획</em>은 이제 <em>앱 서비스 계획</em>이 됩니다. <em>앱 서비스 계획</em>은 웹앱, 모바일 앱, 논리 앱 또는 API 앱과 같은 앱 서비스의 모든 앱 유형을 호스트할 수 있습니다.</p>
<p><li>Azure 앱 서비스 웹앱은 일반 공급됩니다.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">웹앱에 대해 자세히 알아봅니다</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Azure 모바일 서비스</td>
<td align="left">모바일 앱</td>
<td align="left"><p><li>모바일 서비스는 독립 실행형 서비스로 계속 사용할 수 있으며 완전히 지원됩니다.</p>
<p><li>모바일 앱은 모바일 서비스의 모든 기능과 추가 기능을 통합하는 앱 서비스의 새로운 앱 유형입니다. 모바일 앱은 공개 미리 보기입니다.</p>
<p><li>쉽게 <a href="http://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview/">모바일 서비스에서 모바일 앱으로 마이그레이션</a>할 수 있습니다. 모바일 앱은 미리 보기 상태이므로 아직 프로덕션 앱을 실행하는 데 사용하지 않는 것이 좋습니다.</p>
<p><li>앱 서비스의 일부로 모바일 앱에는 온-프레미스 및 SaaS 시스템과 통합, 스테이징 슬롯, WebJobs, 향상된 크기 조정 옵션 등 모바일 서비스의 범위를 초월한 새 기능이 제공됩니다.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">모바일 앱에 대해 자세히 알아봅니다</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API 앱</td>
<td align="left">
<p><li>API 앱은 클라우드에서 API를 쉽게 빌드하고 사용할 수 있게 해주는 앱 서비스의 새로운 앱 유형입니다.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">API 앱에 대해 자세히 알아봅니다</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">논리 앱</td>
<td align="left">
<p><li>논리 앱은 비즈니스 프로세스를 쉽게 자동화할 수 있게 해주는 앱 서비스의 새로운 앱 유형입니다.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">논리 앱에 대해 자세히 알아봅니다</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Azure BizTalk 서비스</td>
<td align="left">BizTalk API 앱</td>
<td align="left">
<li><p>BizTalk 서비스는 독립 실행형 서비스로 계속 사용할 수 있으며 완전히 지원됩니다.</p>
<li><p>BizTalk 서비스의 모든 기능은 앱 서비스에 API 앱으로 통합되어 사용자가 앱 서비스의 모든 앱 유형에서 엔터프라이즈 응용 프로그램 통합 및 B2B 통합 시나리오를 수행할 수 있게 해줍니다.</p>
<li><p>이제 논리 앱을 사용하여 시각적 디자인 환경을 통해 워크플로를 만들어 비즈니스 프로세스를 자동화할 수 있습니다.</p></td>
</tr>
</tbody>
</table>

자세한 내용은 [앱 서비스 설명서](http://azure.microsoft.com/documentation/services/app-service/)를 참조하세요.
 

<!---HONumber=July15_HO3-->