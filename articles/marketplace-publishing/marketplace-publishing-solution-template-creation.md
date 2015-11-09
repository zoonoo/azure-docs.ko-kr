<properties
   pageTitle="마켓플레이스용 솔루션 템플릿 만들기 가이드 | Microsoft Azure"
   description="Azure 마켓플레이스에서 구입하기 위한 여러 VM 이미지 솔루션 템플릿을 만들고 인증하고 배포하는 방법에 대한 자세한 지침입니다."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace-publishing"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="10/28/2015"
      ms.author="hascipio; v-divte" />

# Azure 마켓플레이스용 솔루션 템플릿 만들기에 대한 가이드
1단계 [계정 만들기 및 등록][link-acct-creation]을 완료한 후 [솔루션 템플릿을 만들기 위한 기술 필수 조건](marketplace-publishing-solution-template-creation-prerequisites.md) 아래에서 Azure 호환 솔루션 템플릿 만들기에 대해 안내했습니다. 이제 Azure 마켓플레이스의 [게시 포털][link-pubportal]에서 여러 VM 솔루션 템플릿을 만드는 단계를 안내합니다.

## 게시 포털에서 솔루션 템플릿 제품 만들기
[https://publish.windowsazure.com](http://publish.windowsazure.com)으로 이동하세요. **처음으로 [게시 포털](https://publish.windowsazure.com/)에 로그인할 때는 회사의 판매자 프로필이 등록된 계정을 동일하게 사용하세요.** 나중에 게시 포털에서 회사의 직원을 공동 관리자로 추가할 수 있습니다.

### 1\. '솔루션 템플릿' 선택

  ![그리기][img-pubportal-menu-sol-templ]

### 2\. 새 솔루션 템플릿 만들기

  ![그리기][img-pubportal-sol-templ-new]

### 3\. 토폴로지로 시작
솔루션 템플릿은 해당하는 모든 토폴로지의 '부모'입니다. 하나의 제품/솔루션 템플릿에서 여러 토폴로지를 정의할 수 있습니다. 제품이 스테이징으로 푸시될 때 해당 토폴로지도 모두 함께 푸시됩니다. 제품을 정의하는 단계는 아래와 같습니다. - 토폴로지 만들기 - **"토폴로지 식별자"**는 일반적으로 솔루션 템플릿의 토폴로지 이름입니다. 토폴로지 식별자는 아래와 같은 URL에서 사용됩니다.

  Azure 마켓플레이스: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Azure Preview 포털: https://ms.portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}

- 새 버전 추가  

### 4\. 토폴로지 버전 인증받기
해당하는 특정 토폴로지 버전을 프로비전하는 데 필요한 모든 파일이 포함된 zip 파일을 업로드합니다. 이 zip 파일에는 다음이 포함되어 있어야 합니다. - 루트에 *mainTemplate.json* 및 *createUiDefinition.json* 파일 - 연결된 모든 템플릿 및 모든 필수 스크립트

Zip 파일을 업로드한 후 **인증 요청**을 클릭합니다. Microsoft 인증 팀이 파일을 검토하고 토폴로지를 인증합니다.

> [AZURE.TIP]개발자가 솔루션 템플릿 토폴로지를 만들고 인증을 받는 동안 회사의 비즈니스/마케팅/법무 부서는 마케팅 및 법률 콘텐츠를 작업할 수 있습니다.

## 다음 단계
이제 솔루션 템플릿을 만들고 인증에 필요한 파일이 포함된 zip 파일을 제출했으므로 스테이징에서 제품을 테스트할 준비를 하기 전에 계속해서 [마켓플레이스 마케팅 콘텐츠 가이드](marketplace-publishing-push-to-staging.md)의 지침에 따라 작업을 수행할 수 있습니다.

## 참고 항목
- [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

**VM 이미지** - [Azure의 가상 컴퓨터 이미지 정보](https://msdn.microsoft.com/library/azure/dn790290.aspx)

**VM 확장** - [VM 에이전트 및 VM 확장 개요](https://msdn.microsoft.com/library/azure/dn832621.aspx) - [Azure VM 확장 및 기능](https://msdn.microsoft.com/library/azure/dn606311.aspx)

**ARM** - [Azure ARM 템플릿 작성](../resource-group-authoring-templates/) - [간단한 ARM 템플릿 예제](https://github.com/rjmax/ArmExamples)

**저장소 계정 제한** - [저장소 계정 제한을 모니터링하는 방법](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) - [프리미엄 저장소](../storage/storage-premium-storage-preview-portal/#scalability-and-performance-targets-whko-KRing-premium-storage)

[img-pubportal-menu-sol-templ]: media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]: media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]: marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=Nov15_HO1-->