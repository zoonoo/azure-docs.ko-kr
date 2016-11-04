---
title: 내 Cordova 프로젝트(Visual Studio 연결된 서비스)의 변경 내용 | Microsoft Docs
description: 'Visual Studio 연결된 서비스를 사용하여 Azure 모바일 서비스를 추가한 후 Azure Cordova 프로젝트의 변경 내용에 대해 설명합니다. '
services: mobile-services
documentationcenter: na
author: mlhoop
manager: douge
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 07/21/2016
ms.author: mlearned

---
# Visual Studio 연결된 서비스를 사용하여 Azure 모바일 서비스를 추가한 후 내 Azure Cordova 프로젝트의 변경 내용
## 참조 추가됨
모든 다중 장치 하이브리드 앱에 포함된 Azure 모바일 서비스 클라이언트 플러그 인이 사용하도록 설정되었습니다.

## 모바일 서비스에 대한 연결 문자열 값
`services\mobileServices\settings` 아래에 선택한 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 포함하는 새 JavaScript(.js) 파일이 **MobileServiceClient**를 사용하여 생성되었습니다. 이 파일에는 모바일 서비스 클라이언트 개체의 초기화가 포함되어 있는데, 이 코드는 다음과 유사합니다.

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

[모바일 서비스에 대한 자세한 정보](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0727_2016-->