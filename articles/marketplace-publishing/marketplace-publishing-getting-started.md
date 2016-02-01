<properties
   pageTitle="제품을 만들고 마켓플레이스에 배포하는 방법 개요 | Microsoft Azure"
   description="승인된 Microsoft 개발자가 되고, 가상 컴퓨터 이미지, 템플릿, 데이터 서비스 또는 개발자 서비스를 만들어서 Azure 마켓플레이스에 배포하는 데 필요한 단계를 이해합니다."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/07/2016"
   ms.author="hascipio" />

# Azure 마켓플레이스에 제품을 게시하는 방법
이 문서는 개발자가 솔루션을 만들어서 다른 Azure 고객 및 파트너가 구매하여 활용하도록 Azure 마켓플레이스에 배포하는 데 도움을 주기 위해 제공됩니다.

게시자로서 처음 수행하려고 할 작업은 자신의 회사에서 제공할 솔루션의 종류를 정의하는 것입니다. Azure 마켓플레이스는 여러 가지 솔루션을 지원하며, 각 솔루션을 마켓플레이스에 성공적으로 게시하는 데 필요한 일련의 작업은 서로 조금씩 다릅니다.

솔루션의 유형:

- 가상 컴퓨터 이미지
- 개발자 서비스
- 데이터 서비스
- 솔루션 템플릿

일부 단계는 다양한 유형의 솔루션 간에 공유됩니다. 여기에서는 모든 유형의 솔루션에 대해 완료해야 하는 단계에 대한 간략한 개요를 제공합니다.

> [AZURE.NOTE]Azure 마켓플레이스에서 작업을 시작하기 전에 사전 승인을 받아야 합니다. 데이터 서비스 게시자에는 해당하지 않습니다.

||가상 컴퓨터 이미지 |개발자 서비스 | 데이터 서비스 | 솔루션 템플릿 |
|----|----|----|----|----|
| **사전 승인 받기** | [Microsoft Azure 인증][link-certification] | [Microsoft Azure 인증][link-certification] | 해당 없음 | [Microsoft Azure 인증][link-certification] |
| **1단계: develo9per 계정 등록** | [Microsoft 개발자 계정: 생성 및 등록][link-accts] | [Microsoft 개발자 계정: 생성 및 등록][link-accts] | [Microsoft 개발자 계정: 생성 및 등록][link-accts] | [Microsoft 개발자 계정: 생성 및 등록][link-accts] |
|**2단계: 제품 만들기**| [일반 비기술적인 필수 요건](marketplace-publishing-pre-requisites.md)| [일반 비기술적인 필수 요건](marketplace-publishing-pre-requisites.md)| [일반 비기술적인 필수 요건](marketplace-publishing-pre-requisites.md)| [일반 비기술적인 필수 요건](marketplace-publishing-pre-requisites.md)|
|| [VM 기술적인 필수 조건][link-single-vm-prereq] | 개발자 서비스 기술적인 필수 조건 | [데이터 서비스 기술적인 필수 조건](marketplace-publishing-data-service-creation-prerequisites.md) | [솔루션 템플릿 기술적인 필수 조건](marketplace-publishing-solution-template-creation-prerequisites.md) |
||[VM 이미지 게시 가이드][link-single-vm] | 개발자 서비스 게시 가이드 | [데이터 서비스 게시 가이드](marketplace-publishing-data-service-creation.md) | [솔루션 템플릿 게시 가이드](marketplace-publishing-solution-template-creation.md) |
|| [Azure 마켓플레이스 마케팅 콘텐츠 가이드][link-pushstaging] | [Azure 마켓플레이스 마케팅 콘텐츠 가이드][link-pushstaging] | [Azure 마켓플레이스 마케팅 콘텐츠 가이드][link-pushstaging] | [Azure 마켓플레이스 마케팅 콘텐츠 가이드][link-pushstaging] |
| **3단계: 제품을 스테이징으로 푸시** | [스테이징에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md) | 스테이징에서 개발자 서비스 제품 테스트 | [스테이징에서 데이터 서비스 제품 테스트](marketplace-publishing-data-service-test-in-staging.md) | [스테이징에서 솔루션 템플릿 테스트](marketplace-publishing-solution-template-test-in-staging.md) |
| **4단계: 마켓플레이스로 제품 배포** | [마켓플레이스로 제품 배포][link-pushprod] | [마켓플레이스로 제품 배포][link-pushprod] | [마켓플레이스로 제품 배포][link-pushprod] | [마켓플레이스로 제품 배포][link-pushprod] |

## 지원
- [게시자로 지원 받기][suppt-general]
- [판매자 통찰력 보고 이해][suppt-rpt-insights]
- [지급 보고 이해][suppt-rpt-payouts]
- [마켓플레이스에서 일반적인 게시 문제 해결][suppt-common]

## 추가 리소스
- 사용한 포털에 대한 자세한 내용은 [필요한 포털](marketplace-publishing-portals.md)을 참조하세요.

**가상 컴퓨터**

- [Azure PowerShell 설정](marketplace-publishing-powershell-setup.md)
- [온-프레미스로 VM 이미지 만들기](marketplace-publishing-vm-image-creation-on-premise.md)
- [Azure Preview 포털에서 Windows를 실행하는 가상 컴퓨터 만들기](../virtual-machines-windows-tutorial/)

**데이터 서비스**

- [데이터 서비스 OData 매핑](marketplace-publishing-data-service-creation-odata-mapping.md)
- [데이터 서비스 OData 매핑 노드](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)
- [데이터 서비스 OData 매핑 예제](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

[suppt-general]: marketplace-publishing-get-publisher-support.md
[suppt-rpt-insights]: marketplace-publishing-report-seller-insights.md
[suppt-rpt-payouts]: marketplace-publishing-report-payout.md
[suppt-common]: marketplace-publishing-support-common-issues.md
[link-certification]: marketplace-publishing-azure-certification.md
[link-accts]: marketplace-publishing-accounts-creation-registration.md
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-pushprod]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_0121_2016-->