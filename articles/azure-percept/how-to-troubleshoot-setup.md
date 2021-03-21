---
title: Azure Percept에 대 한 온 보 딩 환경에서 문제 해결
description: 온 보 딩 환경에서 발견 된 몇 가지 일반적인 문제에 대 한 문제 해결 팁을 확인 하세요.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662912"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Azure Percept 진한 온 보 딩 경험 문제 해결 가이드

Azure Percept 진한 온 보 딩 환경에서 발생할 수 있는 몇 가지 문제는 다음과 같습니다. 이 가이드의 단계를 사용한 후에도 문제가 계속 됩니다. Azure 고객 지원에 문의 하세요.

|문제|이유|해결 방법|
|:-----|:------|:----------|
|Azure 계정 등록 페이지 또는 Azure Portal에 연결 하는 경우 캐시 된 계정으로 자동으로 로그인 할 수 있습니다. 사용 하려는 계정이 아닌 경우 설명서와 일치 하지 않는 환경이 발생할 수 있습니다.|이는 일반적으로 브라우저의 설정 때문에 이전에 사용한 계정이 "기억나지 않습니다" 하기 때문입니다.|Azure 페이지에서 계정 이름이 표시 되는 페이지의 오른쪽 위 모서리를 클릭 한 다음 "로그 아웃"을 클릭 합니다. 그러면 올바른 계정으로 로그인 할 수 있습니다.|
|Azure Percept 진한 access point (scz) 네트워크가 사용 가능한 Wi-Fi 네트워크 목록에 나타나지 않습니다.|이 문제는 일반적으로 약간의 시간을 사용 하 여 자체적으로 해결 하는 일시적인 문제입니다.|네트워크가 표시 될 때까지 기다립니다. 15 분이 지난 후에도 장치를 다시 부팅 합니다.|
|Azure Percept 진한 액세스 지점에 대 한 연결은 종종 연결을 끊습니다.|장치와 호스트 컴퓨터가 연결 되지 않았기 때문일 수 있습니다. 호스트 컴퓨터에서 다른 Wi-Fi 연결의 간섭으로 인해 발생할 수도 있습니다.|안테나가 dev kit에 제대로 연결 되어 있는지 확인 합니다. 개발자 키트가 호스트 컴퓨터에서 멀리 떨어져 있는 경우 더 가까운 곳으로 이동 해 보세요. 호스트 컴퓨터에서 실행 중인 경우에는 LTE/5G와 같은 기타 인터넷 연결을 해제 합니다.|
|호스트 컴퓨터에는 Azure Percept 진한 액세스 지점에 대 한 연결에 대 한 보안 경고가 표시 됩니다.|이 문제는 알려진 문제 이며 이후 업데이트에서 수정 될 예정입니다.|Devkit Wi-Fi 액세스 지점에 대 한 온 보 딩 환경을 진행 하는 것이 안전 합니다.|
|Azure Percept 진한 access point (scz) 네트워크가 네트워크 목록에 나타나지만 연결에 실패 합니다.|이는 devkit Wi-Fi 액세스 지점의 일시적인 손상 때문일 수 있습니다.|Devkit를 다시 부팅 한 후 다시 시도 하십시오.|
|설치 환경에서 Wi-Fi 네트워크에 연결할 수 없습니다.|Wi-Fi 네트워크는 현재 인터넷에 연결 되어 있어야 Azure와 통신할 수 있습니다. EAP [PEAP/MSCHAP], 조임 포털 및 엔터프라이즈 EAP-TLS 연결은 현재 지원 되지 않습니다.|연결 중인 네트워크 유형 Wi-Fi 지원 되 고 인터넷에 연결 되어 있는지 확인 합니다.|