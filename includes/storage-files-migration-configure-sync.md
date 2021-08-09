---
title: 포함 파일
description: 포함 파일
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: f4baa89757d4ae93af8c1067cf6ef4617ce95e5a
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109645132"
---
이 단계에서는 이전 단계에서 Windows Server 인스턴스에 설정한 모든 리소스 및 폴더를 서로 연결합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 스토리지 동기화 서비스 리소스를 찾습니다.
1. 각 Azure 파일 공유에 대한 스토리지 동기화 서비스 리소스 내에 새 '동기화 그룹'을 만듭니다. Azure 파일 동기화 용어로, Azure 파일 공유는 동기화 토폴로지에서 동기화 그룹 만들기로 설명하는 '클라우드 엔드포인트'가 됩니다. 동기화 그룹을 만들 때 여기에 동기화되는 파일 집합을 인식할 수 있도록 친숙한 이름을 지정합니다. 일치하는 이름의 Azure 파일 공유를 참조하는지 확인합니다.
1. 동기화 그룹을 만든 후에는 해당 그룹에 대한 행이 동기화 그룹 목록에 표시됩니다. 이름(링크)을 선택하여 동기화 그룹의 내용을 표시합니다. **클라우드 엔드포인트** 아래에서 Azure 파일 공유를 볼 수 있습니다.
1. **서버 엔드포인트 추가** 단추를 찾습니다. 프로비전한 로컬 서버의 폴더가 이 서버 엔드포인트의 경로가 됩니다.
