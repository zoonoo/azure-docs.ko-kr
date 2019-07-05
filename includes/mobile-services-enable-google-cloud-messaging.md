---
title: 포함 파일
description: 포함 파일
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a7d0a899b4a2ef86153e82a4e3f04e7ec430a1bc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182795"
---
1. [Google 클라우드 콘솔](https://console.developers.google.com/cloud-resource-manager)로 이동하고 Google 계정 자격 증명으로 로그인합니다. 
2. 도구 모음에서 **프로젝트 만들기**를 선택합니다. 
   
    ![새 프로젝트 만들기](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. **프로젝트 이름**에 프로젝트 이름을 입력하고 **만들기**를 클릭합니다.
4. 도구 모음에서 **경고** 단추를 선택하고 목록에서 프로젝트를 선택합니다. 프로젝트에 대한 대시보드가 표시됩니다. 다음 URL을 사용하여 대시보드를 직접 탐색할 수도 있습니다. `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

    ![경고에서 프로젝트 선택](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. 대시보드의 **프로젝트 정보** 타일에 **프로젝트 번호**를 기록해 둡니다. 

    ![프로젝트 ID](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. 대시보드의 **API** 타일에서 **API 개요로 이동**을 선택합니다. 

    ![API 개요 링크](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. **API** 페이지에서 **API 및 서비스 사용**을 선택합니다. 

    ![[API 및 서비스 사용] 단추](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. **Google Cloud Messaging**을 검색하여 선택합니다. 

    ![Google Cloud Messaging 검색 및 선택](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. 프로젝트에 대해 Google Cloud Messaging을 사용하려면 **사용**을 선택합니다.

    ![Google Cloud Messaging 사용](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. 도구 모음에서 **자격 증명 만들기**를 선택합니다. 

    ![[자격 증명 만들기] 단추](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. **자격 증명을 프로젝트에 추가** 페이지에서 **API 키** 링크를 선택합니다. 

    ![[자격 증명 만들기] 단추](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. **API 키** 페이지에서 **만들기/저장**을 선택합니다. 다음 예제에서는 **IP 주소** 옵션을 선택하고 허용된 IP 주소로 **0.0.0.0/0**을 입력합니다. API 키를 적절하게 제한해야 합니다. 

    ![API 키 - [만들기] 단추](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. **API 키**를 클립보드에 복사하고 어딘가에 저장합니다. 

    ![API 키 복사](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    이 API 키를 사용하여 Azure 에서 GCM에 인증하고 앱 대신 푸시 알림을 보낼 수 있게 합니다. 프로젝트 대시보드를 다시 탐색하려면 다음 URL을 사용합니다. `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

