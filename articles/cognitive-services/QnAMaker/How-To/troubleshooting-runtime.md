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
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b58a08c4cfa97356be4064456b2e9ec5cceccfbc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876335"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>QnA Maker 서비스 및 런타임을 지원하기 위한 문제 해결 팁

QnAMaker는 사용자의 Azure 구독에서 호스팅되는 리소스를 구성 합니다. 디버깅을 수행 하려면 사용자가 Azure QnAMaker 리소스를 조작 하거나 해당 설치에 대 한 추가 정보를 QnAMaker 지원 팀에 제공 해야 할 수 있습니다.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>최신 QnAMaker 런타임 업데이트를 얻는 방법

QnAMaker 런타임은 Azure Portal에서 [QnAMaker 서비스를 만들](./set-up-qnamaker-service-azure.md) 때 배포 되는 Azure App Service의 일부입니다. 런타임은 주기적으로 업데이트됩니다. QnA Maker App Service는 4 월 2019 사이트 확장 릴리스 (버전 5 이상) 이후 자동 업데이트 모드에 있습니다. 이는 업그레이드 하는 동안 가동 중지 시간이 0이 되도록 이미 설계 되었습니다. 

에서 https://www.qnamaker.ai/UserSettings 현재 버전을 확인할 수 있습니다. 버전이 버전 5.x 보다 이전 버전인 경우 App Service를 다시 시작 하 여 최신 업데이트를 적용 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    ![QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. App Service를 클릭하고 [개요] 섹션 열기

     ![QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. App Service를 다시 시작합니다. 몇 초 이내에 완료될 것입니다. 이 QnAMaker 서비스를 사용 하는 모든 종속 응용 프로그램 또는 봇은이 재시작 기간 동안 최종 사용자가 사용할 수 없습니다.

    ![QnAMaker appservice 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>QnAMaker 서비스 호스트 이름을 가져오는 방법
QnAMaker 서비스 호스트 이름은 QnAMaker 고객 지원팀 또는 UserVoice에 디버깅에 대해 문의할 때 유용합니다. 호스트 이름은 https:// *{hostname}* . AZUREWEBSITES.NET 형식의 URL입니다.
    
1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    ![Azure Portal의 QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. QnA Maker 리소스와 연결 된 App Service를 선택 합니다. 일반적으로 이름은 동일 합니다.

     ![QnAMaker App Service 선택](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 호스트 이름 URL은 [개요] 섹션에서 사용 가능

    ![QnAMaker 호스트 이름](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [활성 학습을 사용하여 기술 자료 질문 개선](./improve-knowledge-base.md)
