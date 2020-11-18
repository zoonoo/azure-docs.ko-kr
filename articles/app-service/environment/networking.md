---
title: App Service Environment 네트워킹
description: App Service Environment 네트워킹 세부 정보
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 61059c3e0f9737df6ace338f4252a338ea1f200c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663844"
---
# <a name="app-service-environment-networking"></a>App Service Environment 네트워킹

> [!NOTE]
> 이 문서는 App Service Environment v3 (미리 보기)에 대 한 것입니다.
> 

ASE (App Service Environment)는 웹 앱, api 앱 및 함수 앱을 호스트 하는 Azure App Service의 단일 테 넌 트 배포입니다. ASE를 설치할 때 배포 하려는 Azure Virtual Network (VNet)를 선택 합니다. 모든 인바운드 및 아웃 바운드 트래픽 응용 프로그램은 사용자가 지정 하는 VNet 내부에 있습니다.  

ASEv3는 두 개의 서브넷을 사용 합니다.  하나의 서브넷은 인바운드 트래픽을 처리 하는 개인 끝점에 사용 됩니다. 기존 서브넷 또는 새 서브넷 일 수 있습니다.  인바운드 서브넷은 개인 끝점 이외의 다른 용도로 사용할 수 있습니다. 아웃 바운드 서브넷은 ASE의 아웃 바운드 트래픽에만 사용할 수 있습니다. ASE 아웃 바운드 서브넷으로 이동할 수 있는 다른 항목이 없습니다.

## <a name="addresses"></a>주소 
ASE에는 만들 때 다음 주소가 있습니다.

| 주소 유형 | description |
|--------------|-------------|
| 인바운드 주소 | 인바운드 주소는 ASE에서 사용 하는 개인 끝점 주소입니다. |
| 아웃 바운드 서브넷 | 아웃 바운드 서브넷은 ASE 서브넷 이기도 합니다. 미리 보기 중이 서브넷은 아웃 바운드 트래픽에만 사용 됩니다. |
| Windows 아웃 바운드 주소 | 이 ASE의 Windows 앱은 인터넷에 대 한 아웃 바운드 호출을 수행할 때 기본적으로이 주소를 사용 합니다. |
| Linux 아웃 바운드 주소 | 이 ASE의 Linux 앱은 인터넷에 대 한 아웃 바운드 호출을 수행할 때 기본적으로이 주소를 사용 합니다. |

ASE에서 사용 하는 개인 끝점을 삭제 하면 ASE의 앱에 연결할 수 없습니다. ASE와 연결 된 Azure DNS 개인 영역을 삭제 하지 마세요.  

ASE는 아웃 바운드 서브넷의 주소를 사용 하 여 ASE에서 사용 하는 인프라를 지원 합니다. ASE에서 App Service 계획의 크기를 조정할 때 더 많은 주소를 사용 합니다. ASE의 앱은 아웃 바운드 서브넷에 전용 주소를 포함 하지 않습니다. 앱에서 아웃 바운드 서브넷의 앱에 사용 되는 주소는 시간이 지남에 따라 변경 됩니다.

## <a name="ports"></a>포트

ASE는 포트 80 및 443에서 응용 프로그램 트래픽을 수신 합니다.  ASE에 대 한 인바운드 또는 아웃 바운드 포트 요구 사항은 없습니다. 

## <a name="extra-configurations"></a>추가 구성

Asev2)와 달리 ASEv3에서는 NSGs (네트워크 보안 그룹) 및 UDRs (Route Tables)를 제한 없이 사용할 수 있도록 설정할 수 있습니다. ASE의 모든 아웃 바운드 트래픽을 NVA 장치로 강제로 터널링 하려는 경우. ASE에 대 한 모든 인바운드 트래픽 앞에 WAF 장치를 배치할 수 있습니다. 

## <a name="dns"></a>DNS

ASE의 앱은 VNet이 구성 된 DNS를 사용 합니다. 일부 앱에서 다른 DNS 서버를 사용 하려는 경우 앱 설정 WEBSITE_DNS_SERVER 및 WEBSITE_DNS_ALT_SERVER를 사용 하 여 앱 단위로 수동으로 설정할 수 있습니다. 앱 설정 WEBSITE_DNS_ALT_SERVER는 보조 DNS 서버를 구성 합니다. 보조 DNS 서버는 주 DNS 서버에서 응답이 없는 경우에만 사용 됩니다. 

## <a name="preview-limitation"></a>미리 보기 제한 사항

ASEv3에서 사용할 수 없는 몇 가지 네트워킹 기능이 있습니다.  ASEv3에서 사용할 수 없는 항목은 다음과 같습니다.

• FTP • 원격 디버그 • 외부 부하 분산 장치 배포 • 컨테이너 배포를 위한 개인 컨테이너 레지스트리에 액세스할 수 있는 기능 • 글로벌 피어 링 Vnet에 대 한 호출을 수행할 수 있는 기능 • 서비스 엔드포인트 또는 개인 끝점 보안 저장소 계정으로 백업/복원 하는 기능 • 앱 설정을 포함 하는 기능 서비스 끝점 또는 개인 끝점 보안 된 keyvault 계정에 BYOS를 사용 하는 기능 • 서비스 끝점 또는 개인 끝점 보안 저장소 계정에 대해 BYOS를 사용 하는 기능 • 아웃 바운드 트래픽에 Network Watcher 또는 NSG 흐름 사용
    
    

