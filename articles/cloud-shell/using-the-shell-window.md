---
title: Azure Cloud Shell 창 사용 | Microsoft Docs
description: Azure Cloud Shell 창 사용 방법 개요.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "70860313"
---
# <a name="using-the-azure-cloud-shell-window"></a>Azure Cloud Shell 창 사용

이 문서에는 Cloud Shell 창을 사용하는 방법에 대해 설명합니다.

## <a name="swap-between-bash-and-powershell-environments"></a>Bash와 PowerShell 환경 간의 교환

Azure Cloud Shell 도구 모음에서 환경 선택기를 사용하여 Bash 및 PowerShell 환경 간에 교환합니다.  
![환경 선택](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Cloud Shell 다시 시작
컴퓨터 상태를 다시 설정하려면 Azure Cloud Shell 도구 모음에서 다시 시작 아이콘을 클릭합니다.  
![Cloud Shell 다시 시작](media/using-the-shell-window/restart.png)
> [!WARNING]
> Cloud Shell을 다시 시작하면 컴퓨터 상태가 다시 설정되고 Azure 파일 공유에서 유지하지 않는 파일은 모두 손실됩니다.

## <a name="change-the-text-size"></a>텍스트 크기 변경
창의 왼쪽 상단에서 설정 아이콘을 클릭한 다음 "텍스트 크기" 옵션 위로 마우스를 가져가 원하는 텍스트 크기를 선택합니다. 선택 사항이 세션 전반에서 유지됩니다.
![텍스트 크기](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>글꼴 변경
창의 왼쪽 위에 있는 설정 아이콘을 클릭 하 고 "글꼴" 옵션을 마우스로 가리킨 다음 원하는 글꼴을 선택 합니다.  선택 사항이 세션 전반에서 유지됩니다.
![글꼴](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>파일 업로드 및 다운로드
창의 왼쪽 위에 있는 파일 업로드/다운로드 아이콘을 클릭 한 다음 업로드 또는 다운로드를 선택 합니다.  
![파일 업로드/다운로드](media/using-the-shell-window/uploaddownload.png)
* 파일을 업로드 하려면 팝업을 사용 하 여 로컬 컴퓨터의 파일을 찾아 원하는 파일을 선택 하 고 "열기" 단추를 클릭 합니다.  파일이 디렉터리에 업로드 됩니다 `/home/user` .
* 파일을 다운로드 하려면 팝업 창에 정규화 된 파일 경로를 입력 하 고 (기본적 `/home/user` 으로 표시 되는 디렉터리 아래의 경로) "다운로드" 단추를 선택 합니다.  
> [!NOTE] 
> Cloud Shell에서 파일 및 파일 경로는 대/소문자를 구분 합니다. 파일 경로에서 대/소문자를 다시 확인 합니다.

## <a name="open-another-cloud-shell-window"></a>다른 Cloud Shell 창을 엽니다.
Cloud Shell은 각 세션이 별도의 프로세스로 존재 하도록 허용 하 여 브라우저 탭에서 여러 동시 세션을 사용 하도록 설정 합니다.
세션을 종료하는 경우 동일한 컴퓨터에서 실행하더라도 각 프로세스가 독립적으로 실행되므로 각 세션 창을 개별적으로 종료해야 합니다.  
창의 왼쪽 위에서 새 세션 열기 아이콘을 클릭 합니다. 기존 컨테이너에 연결 된 다른 세션이 있는 새 탭이 열립니다.
![새 세션 열기](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell 편집기
* [Azure Cloud Shell 편집기 사용](using-cloud-shell-editor.md) 페이지를 참조 하세요.

## <a name="web-preview"></a>웹 미리 보기
창의 왼쪽 위에서 웹 미리 보기 아이콘을 클릭 하 고, "구성"을 선택 하 고, 원하는 포트를 지정 하 여 엽니다.  "포트 열기"를 선택 하 여 포트를 열거나 "열기 및 찾아보기"를 선택 하 여 포트를 열고 새 탭에서 포트를 미리 봅니다.  
![웹 미리 보기](media/using-the-shell-window/preview.png)  
<br>
![포트 구성](media/using-the-shell-window/preview-configure.png)  
창의 왼쪽 위에서 웹 미리 보기 아이콘을 클릭 하 고 "미리 보기 포트 ..."를 선택 합니다. 새 탭에서 열린 포트를 미리 볼 수 있습니다. 창의 왼쪽 위에서 웹 미리 보기 아이콘을 클릭 하 고 "포트 닫기 ..."를 선택 합니다. 열려 있는 포트를 닫습니다.  
![미리 보기/닫기 포트](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Cloud Shell 창 최소화 및 최대화
창을 숨기려면 창의 오른쪽 위에 있는 최소화 아이콘을 클릭합니다. 숨기기를 취소하려면 Cloud Shell 아이콘을 다시 클릭합니다.
창을 최대 높이로 설정하려면 최대화 아이콘을 클릭합니다. 창을 이전 크기로 복원하려면 복원 아이콘을 클릭합니다.  
![창 최소화 또는 최대화](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>복사 및 붙여넣기
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Cloud Shell 창 크기 조정
도구 모음의 위쪽 가장자리를 클릭한 채 위/아래로 끌어 Cloud Shell 창의 크기를 조정합니다.

## <a name="scrolling-text-display"></a>텍스트 표시 스크롤
마우스 또는 터치 패드로 터미널 텍스트까지 스크롤합니다.

## <a name="exit-command"></a>종료 명령
`exit`를 실행하면 활성 세션이 종료됩니다. 이 동작은 기본적으로 상호 작용 없이 20분 후에 발생합니다.

## <a name="next-steps"></a>다음 단계

[Cloud Shell 빠른 시작의 Bash](quickstart.md) <br>
[Cloud Shell 빠른 시작의 PowerShell](quickstart-powershell.md)
