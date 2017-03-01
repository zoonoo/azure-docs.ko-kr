---
title: "Linux의 웹앱에 있는 Node.js용 PM2 구성 사용 | Microsoft Docs"
description: "Linux의 웹앱에 있는 Node.js용 PM2 구성 사용"
keywords: "azure app service, 웹앱, nodejs, pm2, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: fb420f32-6d74-49c7-992f-0ed5616e66e7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: c4af07e79ae066f916c15aa239cb5dfdd3fef2a8
ms.lasthandoff: 02/17/2017


---
# <a name="use-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Linux의 웹앱에 있는 Node.js용 PM2 구성 사용
Linux의 Web Apps용 Node.js에 대해 응용 프로그램 스택을 설정한 경우 다음 이미지와 같이 Node.js 시작 파일을 설정하는 옵션이 제공됩니다.

![Node.js 시작 파일 설정][1]

이 옵션을 사용하여 다음 작업 중 하나를 수행할 수 있습니다.

* Node.js 앱용 시작 스크립트 지정(예: /bin/server.js)
* Node.js 앱에 사용할 PM2 구성 파일 지정(예: /foo/process.json)
  
  > [!NOTE]
  > 특정 파일이 수정될 때 Node.js 프로세스를 자동으로 다시 시작하려면 PM2 구성을 사용합니다. 그러지 않으면 응용 프로그램은 변경 알림의 받을 때(예: 응용 프로그램 코드가 변경될 때) 다시 시작되지 않습니다.
  > 
  > 

전체 옵션은 [프로세스 파일 설명서](http://pm2.keymetrics.io/docs/usage/application-declaration/)에서 확인할 수 있으며, 다음에 나오는 내용은 process.json 파일로 사용할 수 있는 샘플입니다.

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

이 구성에서 유의할 사항:

* "Script" 속성은 응용 프로그램의 시작 스크립트를 지정합니다.
* "instances" 속성은 시작할 노드 프로세스의 인스턴스 수를 지정합니다. 여러 코어가 있는 더 큰 VM에서 응용 프로그램을 실행하는 경우 여기에서 더 높은 값을 설정하여 리소스를 최대화하는 것이 좋습니다.
* "watch" 배열은 변경 시 노드 프로세스를 다시 시작하려는 모든 파일을 지정합니다.
* "watch_options"의 경우 응용 프로그램 콘텐츠가 탑재되는 방식 때문에 "usePolling"을 true로 지정해야 합니다.

## <a name="next-steps"></a>다음 단계
* [Linux의 App Service란?](app-service-linux-intro.md)
* [Linux의 Azure App Service Web Apps에 대한 FAQ](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png

