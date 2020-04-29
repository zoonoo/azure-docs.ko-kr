---
title: Azure 개인 끝점을 사용 하 여 개인적으로 웹 앱에 연결
description: Azure 개인 끝점을 사용 하 여 개인적으로 웹 앱에 연결
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80287818"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Azure 개인 끝점 (미리 보기)을 사용 하 여 개인적으로 웹 앱에 연결

Azure 개인 끝점은 Azure의 개인 링크에 대 한 기본 빌딩 블록입니다. 웹 앱에 개별적으로 연결할 수 있습니다.
이 빠른 시작에서는 개인 끝점을 사용 하 여 웹 앱을 배포 하 고 가상 머신에서이 웹 앱에 연결 하는 방법을 알아봅니다.

자세한 내용은 [Azure 웹 앱에 대 한 개인 끝점 사용][privatenedpointwebapp]을 참조 하세요.

> [!Note]
>미리 보기는 모든 PremiumV2 Windows 및 Linux Web Apps의 미국 동부 및 미국 서 부 2 지역에서 사용할 수 있습니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="virtual-network-and-virtual-machine"></a>가상 네트워크 및 가상 컴퓨터

이 섹션에서는 개인 끝점을 통해 웹 앱에 액세스 하는 데 사용 되는 VM을 호스트 하는 가상 네트워크 및 서브넷을 만듭니다.

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기

이 섹션에서는 가상 네트워크 및 서브넷을 만듭니다.

1. 화면의 왼쪽 위에서 **리소스** > 만들기**네트워킹** > **가상 네트워크** 를 선택 하거나 검색 상자에서 **virtual network** 를 검색 합니다.

1. **가상 네트워크 만들기**에서 기본 사항 탭에 다음 정보를 입력 하거나 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![Virtual Network 만들기][1]

1. **"다음: IP 주소 >"** 을 클릭 하 고 다음 정보를 입력 하거나 선택 합니다.

   > [!div class="mx-imgBorder"]
   >![IP 주소 구성][2]

1. 서브넷 섹션에서 **"+ 서브넷 추가"** 를 클릭 하 고 다음 정보를 입력 하 고 **"추가"** 를 클릭 합니다.

   > [!div class="mx-imgBorder"]
   >![서브넷 추가][3]

1. **"검토 + 만들기"** 를 클릭 합니다.

1. 유효성 검사를 통과 한 후 **"만들기"** 를 클릭 합니다.

### <a name="create-virtual-machine"></a>가상 머신 만들기

1. Azure Portal 화면의 왼쪽 위에서 **리소스** > 만들기**계산** > **가상 머신** 을 선택 합니다.

1. 가상 머신 만들기 - 기본 사항에서 다음 정보를 입력하거나 선택합니다.

   > [!div class="mx-imgBorder"]
   >![가상 컴퓨터 기본][4]

1. **"다음: 디스크"를** 선택 합니다.

   기본 설정을 유지 합니다.

1. **"다음: 네트워킹"** 을 선택 하 고 다음 정보를 선택 합니다.

   > [!div class="mx-imgBorder"]
   >![네트워킹][5]

1. **"검토 + 만들기"** 를 클릭 합니다.

1. 유효성 검사에 통과 한 메시지에서 **"만들기"** 를 클릭 합니다.

## <a name="create-your-web-app-and-private-endpoint"></a>웹 앱 및 개인 끝점 만들기

이 섹션에서는 개인 끝점을 사용 하 여 개인 웹 앱을 만듭니다.

> [!Note]
>개인 끝점 기능은 Premium V2 SKU 에서만 사용할 수 있습니다.

### <a name="web-app"></a>웹앱

1. Azure Portal 화면의 왼쪽 위에서 **리소스** > 만들기**웹** > **웹 앱** 을 선택 합니다.

1. 웹 앱 만들기-기본에서 다음 정보를 입력 하거나 선택 합니다.

   > [!div class="mx-imgBorder"]
   >![웹 앱 기본][6]

1. **"검토 + 만들기"** 를 선택 합니다.

1. 유효성 검사에 통과 한 메시지에서 **"만들기"** 를 클릭 합니다.

### <a name="create-the-private-endpoint"></a>개인 끝점 만들기

1. 웹 앱 속성에서 **설정** > **네트워킹** 을 선택 하 고 **"개인 끝점 연결 구성"** 을 클릭 합니다.

   > [!div class="mx-imgBorder"]
   >![웹 앱 네트워킹][7]

1. 마법사에서 **"+ 추가"** 를 클릭 합니다.

   > [!div class="mx-imgBorder"]
   >![웹 앱 개인 끝점][8]

1. 구독, VNet 및 서브넷 정보를 입력 하 고 **"확인"** 을 클릭 합니다.

   > [!div class="mx-imgBorder"]
   >![웹 앱 네트워킹][9]

1. 개인 끝점 만들기를 검토 합니다.

   > [!div class="mx-imgBorder"]
   >![개인][10]
   >![끝점의 최종 보기 검토][11]

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

1. 포털의 검색 창에서 **Myvm** 을 입력 합니다.
1. **연결 단추**를 선택 합니다. 연결 단추를 선택한 후 가상 컴퓨터에 연결을 열고 **RDP** 를 선택 합니다.

   > [!div class="mx-imgBorder"]
   >![RDP 단추][12]

1. Azure에서 원격 데스크톱 프로토콜 (.rdp) 파일을 만들고 **rdp 파일 다운로드** 를 클릭 한 후 컴퓨터에 다운로드 합니다.

   > [!div class="mx-imgBorder"]
   >![RDP 파일 다운로드][13]

1. downloaded.rdp 파일을 엽니다.

- 메시지가 표시되면 연결을 선택합니다.
- VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

> [!Note]
> VM을 만들 때 입력 한 자격 증명을 지정 하려면 다른 계정을 사용 하 > 다른 옵션을 선택 해야 할 수도 있습니다.

- 확인을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 예 또는 계속을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.

## <a name="access-web-app-privately-from-the-vm"></a>VM에서 개인적으로 웹 앱 액세스

이 섹션에서는 개인 끝점을 사용 하 여 개인적으로 웹 앱에 연결 합니다.

1. 개인 끝점의 개인 IP를 가져오고, 검색 표시줄에 **개인 링크**를 입력 하 고, 개인 링크를 선택 합니다.

   > [!div class="mx-imgBorder"]
   >![Private Link][14]

1. 개인 링크 센터에서 개인 **끝점** 을 선택 하 여 모든 개인 끝점을 나열 합니다.

   > [!div class="mx-imgBorder"]
   >![개인 링크 센터][15]

1. 웹 앱 및 서브넷에 대 한 개인 끝점 링크를 선택 합니다.

   > [!div class="mx-imgBorder"]
   >![개인 끝점 속성][16]

1. 개인 끝점의 개인 IP 및 웹 앱의 FQDN (이 경우 webappdemope.azurewebsites.net 10.10.2.4)을 복사 합니다.

1. MyVM에서 공용 IP를 통해 웹 앱에 액세스할 수 없는지 확인 합니다. 브라우저를 열고 웹 앱 이름을 붙여 넣습니다. 403 금지 된 오류 페이지가 있어야 합니다.

   > [!div class="mx-imgBorder"]
   >![것][17]

> [!Important]
> 이 기능이 미리 보기 상태 이므로 DNS 항목을 수동으로 관리 해야 합니다.

1. 호스트 항목을 만들고 파일 탐색기를 연 다음 호스트 파일을 찾습니다.

   > [!div class="mx-imgBorder"]
   >![호스트 파일][18]

1. 메모장에서 호스트 파일을 편집 하 여 개인 IP 주소 및 웹 앱의 공개 이름이 포함 된 항목을 추가 합니다.

   > [!div class="mx-imgBorder"]
   >![콘텐츠 호스팅][19]

1. 파일 저장

1. 브라우저를 열고 웹 앱의 url을 입력 합니다.

   > [!div class="mx-imgBorder"]
   >![PE를 사용 하는 웹 사이트][20]

1. 개인 끝점을 통해 웹 앱에 액세스 하 고 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

개인 끝점, 웹 앱 및 VM을 사용 하 여 작업을 완료 하면 리소스 그룹 및 해당 리소스 그룹에 포함 된 모든 리소스를 삭제 합니다.

1. 포털 맨 위에 있는 검색 상자에 준비-rg를 입력 하 고 검색 결과에서 준비-rg를 선택 합니다.
1. 리소스 그룹 삭제를 선택합니다.
1. 리소스 그룹 이름에 대해 준비-rg를 입력 하 고 삭제를 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 가상 네트워크, 웹 앱 및 개인 끝점에서 VM을 만들었습니다. 인터넷에서 VM에 연결 하 고 개인 링크를 사용 하 여 웹 앱에 안전 하 게 전달 했습니다. 개인 끝점에 대 한 자세한 내용은 [Azure 개인 끝점 이란?][privateendpoint]을 참조 하세요.

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png

<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
