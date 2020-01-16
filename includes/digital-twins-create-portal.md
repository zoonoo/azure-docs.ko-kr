---
title: 포함 파일
description: 포함 파일
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/07/2020
ms.custom: include file
ms.openlocfilehash: 9ccdc7a438d1dade534d39dc97a39a3bdae37dc4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895510"
---
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 홈 사이드바를 선택한 다음, **+리소스 만들기**를 선택합니다. 

   [![홈 사이드바를 확장한 후 +리소스 만들기 선택](./media/create-digital-twins-portal/azure-portal-create-a-resource.png)](./media/create-digital-twins-portal/azure-portal-create-a-resource.png#lightbox)

1. **Digital Twins**를 검색하고 **Digital Twins**를 선택합니다. 

   [![새 Digital Twins 인스턴스를 만들기 위한 선택 항목](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png)](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png#lightbox)

   또는 **사물 인터넷**을 선택하고 **Digital Twins(미리 보기)** 를 선택합니다.

1. **만들기**를 선택하여 배포 프로세스를 시작합니다.

   [![리소스 배포 만들기 및 확인](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png)](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png#lightbox)

1. **Digital Twins** 창에서 다음 정보를 입력합니다.
   * **리소스 이름**: Digital Twins 인스턴스의 고유한 이름을 만듭니다.
   * **구독**: 이 Digital Twins 인스턴스를 만드는 데 사용할 구독을 선택합니다. 
   * **리소스 그룹**: Digital Twins 인스턴스에 대한 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)을 선택하거나 만듭니다.
   * **위치**: 디바이스에 가장 가까운 위치를 선택합니다.

     [![정보가 입력된 Digital Twins 창](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Digital Twins 정보를 검토한 다음, **만들기**를 선택합니다. Digital Twins 인스턴스가 만들어질 때까지 몇 분 정도 걸릴 수 있습니다. **알림** 창에서 진행 상황을 모니터링할 수 있습니다.

1. Digital Twins 인스턴스의 **개요** 창을 엽니다. **관리 API** 아래의 링크를 기록해 둡니다. **관리 API** URL은 다음과 같은 형식입니다. 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   이 URL은 인스턴스에 적용되는 Azure Digital Twins REST API 설명서로 이동합니다. [Azure Digital Twins Swagger 사용 방법](../articles/digital-twins/how-to-use-swagger.md)을 읽고 이 API 설명서를 읽고 사용하는 방법을 알아봅니다. **관리 API** URL을 복사한 후 다음 형식으로 수정합니다. 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   애플리케이션은 수정된 URL을 기본 URL로 사용하여 인스턴스 액세스합니다. 수정된 이 URL을 임시 파일에 복사합니다. 다음 섹션에서 필요합니다.

   [![관리 API 개요](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)