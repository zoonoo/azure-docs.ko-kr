---
title: 문제 해결 - QnAMaker
titleSuffix: Azure Cognitive Services
description: QnAMaker는 사용자의 Azure 계정에 호스트된 구성 요소로 구성됩니다. 디버깅 과정에서 사용자가 QnAMaker Azure 리소스를 조작하거나 QnAMaker 지원 팀에 설치에 대한 추가 정보를 제공해야 할 수도 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 94a3dfd9a3560321d7e2753ccd385fb1a5323107
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559916"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>QnA Maker 서비스 및 런타임을 지원하기 위한 문제 해결 팁
QnAMaker는 사용자의 Azure 계정에 호스트된 구성 요소로 구성됩니다. 디버깅 과정에서 사용자가 QnAMaker Azure 리소스를 조작하거나 QnAMaker 지원 팀에 설치에 대한 추가 정보를 제공해야 할 수도 있습니다.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>최신 QnAMaker 런타임 업데이트를 얻는 방법
QnAMaker 런타임은 Azure Portal에서 [QnAMaker 서비스를 만들 때](./set-up-qnamaker-service-azure.md) 배포한 Azure App Service의 일부입니다. 런타임은 주기적으로 업데이트됩니다. QnA Maker App Service 자동 업데이트 모드에서 4 월 2019 사이트 확장 릴리스 (버전 5 +)를 게시 합니다. 이는 업그레이드 하는 동안 가동 중지 시간이 0이 되도록 이미 설계 되었습니다. 에서 https://www.qnamaker.ai/UserSettings 현재 버전을 확인할 수 있습니다. 버전이 버전 5.x 보다 이전 버전인 경우 App Service를 다시 시작 하 여 최신 업데이트를 적용 하 여 QnAMaker 설치에 적용 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    ![QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. App Service를 클릭하고 [개요] 섹션 열기

     ![QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. App Service를 다시 시작합니다. 몇 초 이내에 완료될 것입니다. 다시 시작되는 시간에는 이 QnAMaker 서비스의 다운스트림 애플리케이션/봇 빌드를 최종 사용자가 사용할 수 없습니다.

    ![QnAMaker appservice 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>QnAMaker 서비스 호스트 이름을 가져오는 방법
QnAMaker 서비스 호스트 이름은 QnAMaker 고객 지원팀 또는 UserVoice에 디버깅에 대해 문의할 때 유용합니다. 호스트 이름은 https:// *{hostname}* .azurewebsites.net 형식의 URL입니다.
    
1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    ![Azure Portal의 QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. App Service 클릭

     ![QnAMaker App Service 선택](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. 호스트 이름 URL은 [개요] 섹션에서 사용 가능

    ![QnAMaker 호스트 이름](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [활성 학습을 사용하여 기술 자료 질문 개선](./improve-knowledge-base.md)
