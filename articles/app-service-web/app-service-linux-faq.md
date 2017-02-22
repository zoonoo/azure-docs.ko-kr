---
title: "Linux의 Azure App Service Web Apps에 대한 FAQ | Microsoft Docs"
description: "Linux의 Azure App Service Web Apps에 대한 FAQ입니다."
keywords: "Azure App Service, 웹앱, faq, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: d4a5260dde1994bbf4ef4467eddd2fb80a2199b0
ms.openlocfilehash: 22c6c22ba3123555d279fac087a6be2f99bde8d7


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Linux의 Azure App Service Web Apps에 대한 FAQ #

Linux에 Azure App Service가 출시되면서(현재 미리 보기), 우리는 현재 플랫폼에 기능을 추가하고 플랫폼을 더욱 개선하기 위한 작업을 진행하고 있습니다. 다음은 최근 몇 달 동안 고객이 가장 많이 물어본 질문입니다.
질문이 있으면 문서에 남겨주세요. 최대한 신속하게 답변을 드리겠습니다.

## <a name="built-in-images"></a>기본 제공 이미지 ##

**Q:** 플랫폼에서 제공하는 기본 제공 Docker 컨테이너를 분기하려고 합니다. 이러한 파일은 어디서 찾을 수 있나요?

**A:** 모든 Docker 파일은 https://github.com/azure-app-service에서 찾을 수 있습니다. 모든 Docker 컨테이너는 https://hub.docker.com/u/appsvc/에서 찾을 수 있습니다.

## <a name="management"></a>관리 ##

**Q:** 포털에서 다시 시작 단추를 눌러도 웹앱이 다시 시작되지 않는 이유는 무엇인가요?

**A:** 조만간 다시 시작 단추를 제공하기 위해 현재 작업하는 중입니다. 지금은 두 가지 방법이 있습니다.
1. 더미 응용 프로그램 설정을 추가 또는 변경합니다. 그러면 웹앱이 강제로 다시 시작됩니다. 
2. 웹앱을 중지했다가 시작합니다.

**Q:** VM에 SSH를 사용할 수 있나요?

**A:** 아니요, 조만간 앱 컨테이너에 SSH하는 방법을 제공할 예정입니다.

## <a name="continous-integration--deployment"></a>지속적인 통합/배포 ##

**Q:** DockerHub에서 이미지를 업데이트한 후에도 웹앱에서 여전히 기존 Docker 컨테이너 이미지를 사용합니다. 사용자 지정 컨테이너의 지속적인 통합/배포를 지원하나요?

**A:** 웹앱을 중지했다가 시작하거나 더미 응용 프로그램 설정을 변경/추가하여 컨테이너를 강제로 새로 고칠 수 있으며, 조만간 사용자 지정 컨테이너를 위한 CI/CD 기능이 제공될 것입니다.

## <a name="language-support"></a>언어 지원 ##

**Q:** 컴파일되지 않은 .net core 앱을 지원하나요?

**A:** 아니요, 모든 종속성과 함께 컴파일된 .net core 앱을 배포해야 하며, 조만간 완전한 배포 및 빌드 환경을 제공할 예정입니다.

## <a name="custom-containers"></a>사용자 지정 컨테이너 ##

**Q:** 나만의 사용자 지정 컨테이너를 사용하고 있습니다. 앱이 \home\ 디렉터리에 상주하는데 SCM 사이트 또는 ftp 클라이언트를 사용하여 콘텐츠를 찾아볼 때 파일을 찾을 수 없습니다. 내 파일은 어디에 있나요?

**A:** SMB 공유를 \home\ 디렉터리에 마운트하기 때문에 그 안에 있는 모든 콘텐츠가 재정의됩니다.

**Q:** 사용자 지정 컨테이너 이미지에 포트를 두 개 이상 표시하려 합니다. 가능할까요?

**A:** 현재는 지원되지 않습니다.

**Q:** 사용자 고유의 저장소를 가져올 수 있나요?

**A:** 현재는 지원되지 않지만 조만간 지원할 계획입니다.

**Q:** 내 사용자 지정 컨테이너의 파일 시스템 또는 실행 중인 프로세스를 SCM 사이트에서 찾을 수 없습니다. 그 이유는 무엇입니까?

**A:** SCM 사이트는 별도의 컨테이너에서 실행되기 때문에 사용자가 앱 컨테이너의 파일 시스템 또는 실행 중인 프로세스를 확인할 수 없습니다.

## <a name="pricing-and-sla"></a>가격 및 SLA ##

**Q:** 공개 미리 보기인 동안 가격은 얼마입니까?

**A:** 일반 Azure App Service 가격 책정을 사용하면 앱 실행 시간의 절반에 대해서만 요금이 청구됩니다. 즉, 일반 Azure App Service 가격에서 50%가 할인됩니다.

## <a name="other"></a>기타 ##

**Q:** 응용 프로그램 설정 이름에 지원되는 문자는 무엇입니까?

**A:** 응용 프로그램 설정에는 A-Z, a-z, 0-9 및 밑줄만 사용할 수 있습니다.

**Q:** 어디서 새 기능을 요청할 수 있나요?

**A:** https://aka.ms/webapps-uservoice에서 아이디어를 제출할 수 있습니다. 아이디어 제목에 [Linux]를 붙여 주세요.

## <a name="next-steps"></a>다음 단계
* [Linux의 App Service 소개](./app-service-linux-intro.md) 
* [Linux의 App Service란?](app-service-linux-intro.md)
* [Linux의 App Service에서 웹앱 만들기](./app-service-linux-how-to-create-a-web-app.md)



<!--HONumber=Feb17_HO3-->


