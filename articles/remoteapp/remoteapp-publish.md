---
title: "Azure RemoteApp에서 앱 게시 | Microsoft 문서"
description: "Azure RemoteApp에 응용 프로그램 및 리소스를 게시하는 방법에 대해 알아봅니다."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: c7e1a2cd-8e1f-4a33-bd43-8032ec9ac952
ms.service: remoteapp
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0b81ef8425e44a9813fc0220f0ab314a3110f265


---
# <a name="how-to-publish-an-app-in-remoteapp"></a>RemoteApp에 앱을 게시하는 방법
> [!IMPORTANT]
> Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

RemoteApp 컬렉션을 만든 후 사용자가 사용할 수 있게 하려는 앱이나 리소스를 게시해야 합니다. 구독과 함께 제공된 템플릿 이미지에는 기본적으로 몇 개의 앱만 게시되어 있습니다. 다른 앱을 공유하려면 게시해야 합니다.

> [!NOTE]
> 응용 프로그램을 업데이트해야 합니까? 먼저 [이미지를 업데이트할](remoteapp-update.md) 해야 합니다.
> 
> 

포털의 **게시** 탭에서 **게시**를 클릭합니다. 템플릿 이미지의 **시작** 메뉴에서 앱을 추가하거나 템플릿 이미지에서 앱이 설치되어 있는 경로를 제공합니다. **시작** 메뉴에서 추가하도록 선택하는 경우 목록에서 게시할 앱을 선택합니다. 앱의 경로를 제공하도록 선택한 경우 앱의 이름과 경로를 입력합니다. 경로에서 변수를 사용합니다(예: "c:\" 대신 "%systemdrive%" 사용).

> [!NOTE]
> **시작** 메뉴에서 앱을 추가하려는 경우 템플릿 이미지에서 **시작* 메뉴에 해당 앱을 추가**해야 합니다.* 그렇지 않으면 **시작** 메뉴에 *있는* 내용만 RemoteApp에 표시되어 혼란스러울 것입니다. 
> 
> 앱이 **시작** 메뉴에 있는지 확인하려면 %systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs 폴더 안에 바로 가기 파일(**.lnk**)을 배치합니다.
> 
> 템플릿을 만들 때 **시작** 메뉴에 앱 추가하는 것을 잊어버린 경우 앱에 경로를 추가하도록 선택합니다. (또는 템플릿 이미지를 다시 만듭니다. 하지만 작업이 훨씬 더 복잡 합니다.)
> 
> 




<!--HONumber=Nov16_HO3-->


