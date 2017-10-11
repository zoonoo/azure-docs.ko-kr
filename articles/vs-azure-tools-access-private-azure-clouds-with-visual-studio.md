---
title: "Visual Studio로 사설 Azure 클라우드에 액세스 | Microsoft Docs"
description: "Visual Studio를 사용하여 사설 클라우드 리소스에 액세스하는 방법에 대해 알아봅니다."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/19/2017
ms.author: kraigb
ms.openlocfilehash: b2578c837732ab05d538e9b896ed3a3035075a70
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Visual Studio로 사설 Azure 클라우드에 액세스
기본적으로 Visual Studio는 공용 Azure 클라우드 REST 끝점을 지원합니다. 이 항목에서는 사설 클라우드 인증서를 사용하여 Visual Studio에서 사설 클라우드에 액세스하고 상호 작용하는 방법을 알아봅니다.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Visual Studio의 사설 클라우드에 액세스 하려면
1. 사설 클라우드를 위한 [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885) 에서 게시 설정 파일을 다운로드 하거나 게시 설정 파일에 대해 관리자에게 문의합니다. Azure의 공용 버전에서 이 설정의 다운로드에 대한 링크: [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). 다운로드한 파일은 확장명이 `.publishsettings`여야 합니다.

1. Visual Studio 열기

1. **서버 탐색기**의 상황에 맞는 메뉴에서 **Azure** 노드를 마우스 오른쪽 단추로 클릭하고 **구독 관리 및 필터**를 선택합니다.
   
    ![구독 명령 관리](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. **Microsoft Azure 구독 관리** 대화 상자에서 **인증서** 탭을 선택한 후 **가져오기**를 선택합니다.
   
    ![Azure 인증서 가져오기](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. **Microsoft Azure 구독 가져오기** 대화 상자에서 **찾아보기**를 선택합니다.

    ![Microsoft Azure 구독 가져오기 대화 상자의 찾아보기 단추](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. **열기** 대화 상자에서 게시 설정 파일을 저장한 디렉터리로 이동하여 파일을 선택한 다음 **열기**를 선택합니다.

    ![게시 설정 파일 선택](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. **Microsoft Azure 구독 가져오기** 대화 상자로 돌아가면 **찾아보기**를 선택합니다.

    ![게시 설정 파일 가져오기](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    게시 설정 파일에서 Visual Studio로 인증서를 가져왔으므로 이제 사설 클라우드 리소스와 상호 작용할 수 있습니다.
   
## <a name="next-steps"></a>다음 단계
- [Visual Studio에서 Azure 클라우드 서비스에 게시](https://msdn.microsoft.com/library/azure/ee460772.aspx)
- [방법: 게시 설정 및 구독 정보를 다운로드 및 가져오기](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)
