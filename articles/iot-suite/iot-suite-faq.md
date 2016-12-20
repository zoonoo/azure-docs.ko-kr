---
title: Azure IoT Suite FAQ | Microsoft Docs
description: "IoT Suite에 대한 질문과 대답"
services: 
suite: iot-suite
documentationcenter: 
author: aguilaaj
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2016
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5cbbebfcc41ef8c45366c04ddd9a5697f7b91b61


---
# <a name="frequently-asked-questions-for-iot-suite"></a>IoT Suite에 대한 질문과 대답
### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Azure 포털에서 리소스 그룹을 삭제하는 것과 azureiotsuite.com의 미리 구성된 솔루션에서 삭제를 클릭하는 것의 차이는 무엇인가요?
* [azureiotsuite.com][lnk-azureiotsuite]에서 미리 구성된 솔루션을 삭제하면, 미리 구성된 솔루션을 만들 때 프로비전된 모든 리소스가 삭제됩니다. 리소스 그룹에 리소스를 추가하면, 이들 역시 삭제됩니다. 
* [Azure Portal][lnk-azure-portal]에서 리소스 그룹을 삭제하면 해당 리소스 그룹에 포함된 리소스만 삭제됩니다. 따라서 [Azure 클래식 포털][lnk-classic-portal]의 미리 구성된 솔루션과 연결된 Azure Active Directory 응용 프로그램도 삭제해야 합니다.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>하나의 구독에 프로비전할 수 있는 IoT Hub 인스턴스는 몇 개인가요?
10개입니다. [Azure 지원 티켓][link-azuresupportticket]을 만들어서 이 한도를 높일 수 있지만, 기본적으로 [Azure 구독 제한][link-azuresublimits]에서 정한 대로 구독당 10개의 IoT Hub를 프로비전할 수 있습니다. 결과적으로 미리 구성된 모든 솔루션이 새 IoT Hub를 프로비전하기 때문에 지정된 구독에서 최대 10개의 미리 구성된 솔루션을 프로비전할 수 있습니다. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>하나의 구독에 프로비전할 수 있는 DocumentDB 인스턴스는 몇 개인가요?
50개입니다. [Azure 지원 티켓][link-azuresupportticket]을 만들어서 이 한도를 높일 수 있지만, 기본적으로 구독 당 DocumentDB 인스턴스를 50개만 프로비전할 수 있습니다. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>하나의 구독에 프로비전할 수 있는 무료 Bing 지도 API는 몇 개인가요?
2개입니다. 두 개의 Enterprise용 내부 트랜잭션 Level 1 Bing Maps 계획을 Azure 구독에서 만들 수 있습니다. 원격 모니터링 솔루션이 내부 트랜젝션 Level 1 계획을 사용하여 기본으로 프로비전됩니다. 결과적으로 구독에 원격 모니터링 솔루션을 가감 없이 2개까지만 프로비전할 수 있습니다.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>정적 맵이 있는 원격 모니터링 솔루션 배포에서는 대화형 Bing 맵을 어떻게 추가하나요?
1. [Azure Portal][lnk-azure-portal]에서 엔터프라이즈용 Bing 맵 API QueryKey를 가져옵니다. 
   
   1. [Azure Portal][lnk-azure-portal]에서 엔터프라이즈용 Bing 맵 API가 있는 리소스 그룹으로 이동합니다.
   2. 모든 설정을 클릭한 다음 키 관리를 클릭합니다. 
   3. MasterKey와 QueryKey 등, 두 개의 키가 보일 것입니다. QueryKey에 대한 값을 복사합니다.
      
      > [!NOTE]
      > 엔터프라이즈용 Bing 맵 API 계정이 없는 경우 [Azure Portal][lnk-azure-portal]에서 + 새로 만들기를 클릭하고 엔터프라이즈용 Bing 맵 API를 검색한 다음 지침에 따라 새로 만듭니다.
      > 
      > 
2. [Azure IoT 원격 모니터링][lnk-remote-monitoring-github]에서 최신 코드를 풀다운합니다.
3. 저장소의 /docs/ 폴더에서 명령줄 배포 지침에 따라 로컬 또는 클라우드 배포를 실행합니다. 
4. 로컬 또는 클라우드 배포를 실행한 후에는 루트 폴더에서 배포 중에 *.user.config 파일이 만들어졌는지 확인합니다. 텍스트 편집기에서 이 파일을 엽니다. 
5. QueryKey에서 복사한 값을 포함하도록 다음 줄을 변경합니다. 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>DreamSpark용 Microsoft Azure가 있는 경우 사전 구성된 솔루션을 만들 수 있나요?
현재는 [DreamSpark용 Microsoft Azure][lnk-dreamspark] 계정을 사용하여 사전 구성된 솔루션을 만들 수 없습니다. 하지만 몇 분 이내에 [Azure용 무료 평가판 계정][lnk-30daytrial]을 만들면 사전 구성된 솔루션을 만들 수 있습니다.

### <a name="how-do-i-delete-an-aad-tenant"></a>AAD 테넌트를 어떻게 삭제하나요?
Eric Golpe의 블로그 게시물 [Azure AD 테넌트 삭제 연습][lnk-delete-aad-tennant]을 참조하세요.

## <a name="next-steps"></a>다음 단계
미리 구성된 IoT Suite 솔루션의 몇 가지 다른 기능 및 기능을 탐색할 수 있습니다.

* [예측 정비 사전 구성 솔루션 개요][lnk-predictive-overview]
* [처음부터 IoT 보안을 고려][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx



<!--HONumber=Nov16_HO3-->


