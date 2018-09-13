---
title: Azure Stack (클라우드 운영자)에서 사용자 지정 마켓플레이스 항목 게시 | Microsoft Docs
description: Azure Stack 운영자로 Azure Stack에서 사용자 지정 마켓플레이스 항목을 게시 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: c16d8a282d489e7a2b5ee9908f52224aea6118d6
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713394"
---
# <a name="the-azure-stack-marketplace-overview"></a>Azure Stack Marketplace 개요

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Marketplace는 서비스, 응용 프로그램 및 Azure Stack에 대 한 사용자 지정 리소스는 컬렉션입니다. 리소스에는 네트워크, 가상 머신, 저장소 및 등이 포함 됩니다. 사용자가 새 리소스를 만들고 새 응용 프로그램을 배포 하려면 여기 제공 됩니다. 생각할 쇼핑 카탈로그 사용자가 탐색 하 고 사용 하려는 항목을 선택 합니다. 마켓플레이스 항목을 사용 하려면 사용자는 항목에 대 한 액세스를 부여 하는 제품을 구독 해야 합니다.

Azure Stack 운영자로 항목을 추가 하려는 marketplace (게시) 합니다. 데이터베이스, App Services 등과 같은 항목을 게시할 수 있습니다. 게시 되도록 모든 사용자에 게 표시 합니다. 사용자가 만든 사용자 지정 항목을 게시할 수 있습니다. 증가 하는 항목을 게시할 수도 있습니다 [Azure Marketplace 항목 목록을](azure-stack-marketplace-azure-items.md)합니다. Marketplace에 항목을 게시할 때 사용자가 5 분 내에 볼 수 있습니다.

> [!Caution]  
> 이미지 및 json 파일 이라고 하는 모든 갤러리 항목 아티팩트 Azure Stack marketplace에서 사용할 수 있도록 한 후 인증 없이 액세스할 수 있습니다. 자세한 게시 시 고려 사항 사용자 지정 마켓플레이스 항목을 참조 하세요 [만들기 및 마켓플레이스 항목 게시](azure-stack-create-and-publish-marketplace-item.md)합니다.

Marketplace를 열려면 관리자 콘솔에서 선택 **+ 리소스 만들기**합니다.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace 항목
Azure Stack Marketplace 항목 서비스, 응용 프로그램 또는 사용자가 다운로드 하 고 사용할 수 있는 리소스입니다. 모든 Azure Stack Marketplace 항목 계획 및 제품은 같은 관리 항목을 포함 하 여 모든 사용자에 게 표시 됩니다. 이러한 항목에는 사용자에 게 작동 하지 않는 되지만 보기에 대 한 구독이 필요 하지 않습니다.

모든 Marketplace 항목은 다음이 있습니다.

* 리소스 프로비전을 위한 Azure 리소스 관리자 템플릿
* 문자열, 아이콘 및 기타 마케팅 참고 자료와 같은 메타데이터
* 서식 지정 정보를 포털에서 항목을 표시 합니다.

Marketplace에 게시 된 모든 항목에는 Azure 갤러리 패키지 (.azpkg) 형식을 사용 합니다. 마켓플레이스 항목의 일부가 아니라 Azure Stack에 배포 또는 런타임 리소스 (예: 코드, 소프트웨어 또는 가상 머신 이미지를 사용 하 여 zip 파일)를 개별적으로 추가 합니다. 

버전 1803 이상에서 사용 하 여 Azure Stack에서 Azure 또는 사용자 지정 이미지를 업로드 하는 경우 다운로드할 때 스파스 파일에 이미지를 변환 합니다. 이 프로세스는 시간이 추가 이미지를 추가할 때 하지만 공간을 절약 하며 해당 이미지 배포 속도가 향상 됩니다. 변환이 새 이미지에만 적용 됩니다.  기존 이미지 변경 되지 않습니다. 

## <a name="next-steps"></a>다음 단계
[Marketplace 항목 다운로드](azure-stack-download-azure-marketplace-item.md)  
[Marketplace 항목 만들기 및 게시](azure-stack-create-and-publish-marketplace-item.md)

