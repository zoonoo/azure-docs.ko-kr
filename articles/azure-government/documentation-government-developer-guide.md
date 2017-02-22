---
title: "Azure Government 개발자 가이드 | Microsoft Docs"
description: "이 문서에서는 Azure Government의 기능을 비교하고 응용 프로그램 개발에 대한 지침을 제공합니다."
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: zakramer
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: 0bab116e977a8490bf0c114c8b829aa1ce07e923
ms.openlocfilehash: 41af18426ce3f8124458a61c3af280c3dd17cfcd


---
# <a name="azure-government-developer-guide"></a>Azure Government 개발자 가이드
Azure Government 환경은 Microsoft 네트워크의 나머지 부분과 분리된 물리적 인스턴스입니다. 이 가이드에서는 응용 프로그램 개발자와 관리자가 Azure의 개별 지역과 상호 작용하고 작업하기 위해 이해해야 하는 차이점에 대해 설명합니다.

## <a name="overview"></a>개요
Azure Government는 별도의 Microsoft Azure 서비스 인스턴스입니다. 이는 미국 연방 기관, 주 및 지방 정부 및 해당 솔루션 공급자의 보안 및 규정 준수 요구 사항을 처리합니다. Azure Government는 미국 정부 이외의 배포와의 한 물리적 및 네트워크 격리, 그리고 선별된 미국 직원을 제공합니다.

Microsoft에서는 개발자가 글로벌 Microsoft Azure 서비스("글로벌 서비스") 및 Microsoft Azure Government 서비스에 클라우드 응용 프로그램을 만들고 배포하는 데 도움이 되는 다양한 도구를 제공합니다.

개발자는 글로벌 서비스와 달리 Azure Government 서비스에 응용 프로그램을 만들고 배포할 때 두 서비스 간의 주요 차이점을 알아야 합니다. 특히 프로그래밍 환경 설정 및 구성, 끝점 구성, 응용 프로그램 작성 및 Azure Government에 서비스로 응용 프로그램 배포 등에 대해 이해해야 합니다.

이 문서에서는 두 서비스 간의 차이점을 요약합니다. [Azure Government](http://www.azure.com/gov "Azure Government") 사이트 및 MSDN의 [Microsoft Azure 기술 라이브러리](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN")에서 제공하는 정보를 보완하고 있습니다. 또한 공식적인 정보는 [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/ "Microsoft Azure 보안 센터"), [Azure 설명서 센터](https://azure.microsoft.com/documentation/) 및 [Azure 블로그](https://azure.microsoft.com/blog/ "Azure 블로그") 등의 다른 위치에서도 제공할 수 있습니다.

이 내용은 Microsoft Azure Government에 배포하는 파트너 및 개발자를 위한 것입니다.

## <a name="guidance-for-developers"></a>개발자를 위한 지침
현재 제공되는 대부분의 기술 내용은 Azure Government가 아닌 글로벌 서비스를 위해 개발되고 있다고 가정하고 있습니다. 이러한 이유로 Azure Government에서 호스팅하기 위해 개발하는 응용 프로그램의 두 가지 주요 차이점을 알고 있어야 합니다.

* 글로벌 서비스의 특정 지역에 있는 특정 서비스와 기능이 Azure Government에서 제공되지 않을 수 있습니다.
* Azure Government의 기능 구성이 글로벌 서비스의 기능 구성과 다를 수 있습니다. 따라서 샘플 코드, 구성 및 단계를 검토하여 Azure Government Cloud Services 환경 내에서 빌드하고 실행하는지 확인해야 합니다.

현재 미국 아이오와 주 정부 및 미국 버지니아 주 정부는 Azure Government를 지원하는 데이터 센터입니다. 현재 데이터 센터 및 사용 가능한 서비스는 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services)을 참조하세요.


## <a name="endpoint-mapping"></a>끝점 매핑
공용 Azure 및 SQL Database 끝점을 Azure Government 특정 끝점에 매핑하는 방법에 대해 알아보려면 다음 표를 참조합니다.

| 이름 | Azure Government 끝점 |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId  | https://management.core.usgovcloudapi.net/ |
| GalleryUrl | https://gallery.usgovcloudapi.net/ |
| ManagementPortalUrl | https://manage.windowsazure.us |
| ServiceManagementUrl | https://management.core.usgovcloudapi.net/ |
| PublishSettingsFileUrl | https://manage.windowsazure.us/publishsettings/index |
| ResourceManagerUrl | https://management.usgovcloudapi.net/ |
| SqlDatabaseDnsSuffix | .database.usgovcloudapi.net |
| StorageEndpointSuffix | core.usgovcloudapi.net |
| ActiveDirectoryAuthority | https://login-us.microsoftonline.com/ |
| GraphUrl | https://graph.windows.net/ |
| GraphEndpointResourceId | https://graph.windows.net/ |
| TrafficManagerDnsSuffix | usgovtrafficmanager.net |
| AzureKeyVaultDnsSuffix | vault.usgovcloudapi.net |
| AzureKeyVaultServiceEndpointResourceId | https://vault.usgovcloudapi.net |



## <a name="next-steps"></a>다음 단계
Azure Government에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [평가판에 등록](https://azuregov.microsoft.com/trial/azuregovtrial)
* [Azure Government 구입 및 액세스](http://azure.com/gov)
* [Azure Government 개요](/azure-government-overview)
* [Azure Government 블로그](http://blogs.msdn.microsoft.com/azuregov/)
* [Azure 규정 준수](https://www.microsoft.com/trustcenter/compliance/complianceofferings)



<!--HONumber=Feb17_HO3-->


