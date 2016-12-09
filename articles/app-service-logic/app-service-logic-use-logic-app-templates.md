---
title: "논리 앱 템플릿 | Microsoft Docs"
description: "보다 쉽게 시작하려면 미리 만들어진 논리 앱 템플릿을 사용하는 방법에 대해 알아봅니다."
author: kevinlam1
manager: dwrede
editor: 
services: app-service\logic
documentationcenter: 
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.service: app-service-logic
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4e38ed7cb28b22679081868fc68852c134517990


---
# <a name="logic-app-templates"></a>논리 앱 템플릿
## <a name="what-are-logic-app-templates"></a>논리 앱 템플릿이란 무엇인가요?
논리 앱 템플릿은 미리 만들어진 논리 앱으로서 사용자 고유의 워크플로를 빠르게 만들기 시작하는 데 사용할 수 있습니다. 

이러한 템플릿을 통해 논리 앱을 사용하여 빌드할 수 있는 다양한 패턴을 발견할 수 있습니다. 템플릿을 그대로 사용하거나 시나리오에 맞도록 수정할 수 있습니다.

## <a name="overview-of-available-templates"></a>사용 가능한 템플릿 개요
현재 사용 가능한 여러 템플릿이 논리 앱 플랫폼에 게시되어 있습니다. 일부 예제 범주 및 이에 사용되는 커넥터 유형은 아래에 나열됩니다.

### <a name="enterprise-cloud-templates"></a>엔터프라이즈 클라우드 템플릿
Dynamics CRM, Salesforce, Box, Azure Blob 및 엔터프라이즈 클라우드 요구 사항에 대한 다른 커넥터를 통합하는 템플릿 이러한 템플릿을 사용하여 수행할 수 있는 몇 가지 예에는 잠재 고객을 구성하고 회사 파일 데이터를 백업하는 작업이 포함됩니다.

### <a name="enterprise-integration-pack-templates"></a>엔터프라이즈 통합 팩 템플릿
VETER(유효성 검사, 추출, 변환, 보강, 라우팅) 파이프라인의 구성, AS2를 통한 X12 EDI 문서 수신과 XML로 변환 및 X12와 AS2 메시지 처리가 가능합니다.

### <a name="protocol-pattern-templates"></a>프로토콜 패턴 템플릿
이러한 템플릿은 FTP 및 SFTP에 걸친 통합뿐만 아니라 HTTP를 통해 요청-응답과 같은 프로토콜 패턴을 포함하는 논리 앱으로 구성됩니다. 존재하는 대로 또는 더 복잡한 프로토콜 패턴을 만드는 기준으로 이러한 템플릿을 사용합니다.  

### <a name="personal-productivity-templates"></a>개인 생산성 템플릿
개인 생산성을 향상시킬 수 있는 패턴에는 매일 미리 알림을 설정하고 할 일 목록에 중요한 작업 항목을 설정하고 단일 사용자 승인 단계까지 시간이 걸리는 작업을 자동화하는 템플릿이 포함됩니다.

### <a name="consumer-cloud-templates"></a>소비자 클라우드 템플릿
Twitter, Slack 및 전자 메일과 같은 소셜 미디어 서비스와 통합하는 간단한 템플릿은 궁극적으로 소셜 마케팅 미디어 이니셔티브를 강화할 수 있습니다. 알림에는 흐린 복사 등의 템플릿을 포함하며 일반적으로 반복 작업에 소요된 시간을 저장하여 생산성을 높일 수 있습니다. 

## <a name="how-to-create-a-logic-app-using-a-template"></a>템플릿을 사용하여 논리 앱을 만드는 방법
논리 앱 템플릿을 사용하여 시작하려면 논리 앱 디자이너로 이동합니다. 기존 논리 앱을 열어 디자이너를 입력하는 경우 논리 앱은 디자이너 보기로 자동으로 로드됩니다. 그러나 새 논리 앱을 만드는 경우 아래의 화면이 표시됩니다.  
 ![](../../includes/media/app-service-logic-templates/template7.png)  

이 화면에서 빈 논리 앱 또는 미리 작성된 템플릿을 시작하도록 선택할 수 있습니다. 템플릿 중 하나를 선택하는 경우 추가 정보가 제공됩니다. 이 예제에서는 *새 파일이 Dropbox에서 만들어지면 OneDrive에 복사합니다.* 템플릿을 사용합니다.  
 ![](../../includes/media/app-service-logic-templates/template2.png)  

템플릿을 사용하려면 *이 템플릿 사용* 단추를 선택합니다. 템플릿이 사용하는 커넥터에 따라 계정에 로그인하라는 메시지가 표시됩니다. 또는 이러한 커넥터와 연결을 이전에 설정한 경우 아래와 같이 계속하도록 선택할 수 있습니다.  
 ![](../../includes/media/app-service-logic-templates/template3.png)  

연결을 설정하고 *계속*하도록 선택한 후에 논리 앱은 디자이너 보기로 열립니다.  
 ![](../../includes/media/app-service-logic-templates/template4.png)  

위의 예제에서 많은 템플릿의 경우와 마찬가지로 일부 필수 속성 필드는 커넥터 내에서 채워질 수 있습니다. 그러나 어떤 경우에는 논리 앱을 제대로 배포하기 전에 값이 필요할 수 있습니다. 누락된 필드 중 일부를 입력하지 않고 배포하려는 경우 오류 메시지와 함께 알림이 표시됩니다.

템플릿 뷰어로 돌아가려는 경우 위쪽 탐색 모음에서 *템플릿* 단추를 선택합니다. 템플릿 뷰어로 다시 전환하면 저장하지 않은 진행 상황이 손실됩니다. 템플릿 뷰어로 다시 전환하기 전에 이를 알리는 경고 메시지가 표시됩니다.  
 ![](../../includes/media/app-service-logic-templates/template5.png)  

## <a name="how-to-deploy-a-logic-app-created-from-a-template"></a>템플릿에서 만든 논리 앱을 배포하는 방법
템플릿을 로드하고 원하는 대로 변경하면 왼쪽 위 모퉁이에서 저장 단추를 선택합니다. 이렇게 하면 논리 앱을 저장하고 게시합니다.  
 ![](../../includes/media/app-service-logic-templates/template6.png)  

기존 논리 앱 템플릿에 다른 단계를 추가하거나 전체적으로 편집하는 방법에 대한 자세한 내용을 찾고 있다면 [논리 앱 만들기](app-service-logic-create-a-logic-app.md)에서 자세히 알아보세요.




<!--HONumber=Nov16_HO3-->


