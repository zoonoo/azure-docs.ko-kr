---
title: Azure 개인 끝점을 사용 하 여 개인적으로 웹 앱에 연결
description: 이 문서에서는 Azure 개인 끝점을 사용 하 여 개인적으로 웹 앱에 연결 하는 방법을 설명 합니다.
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 10/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3e0f05240aba9b5c92689315e409aaabe793b3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91772841"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint"></a>Azure 개인 끝점을 사용 하 여 개인적으로 웹 앱에 연결

Azure 개인 끝점은 Azure 개인 링크에 대 한 기본 빌딩 블록입니다. 개인 끝점을 사용 하면 개인적으로 웹 앱에 연결할 수 있습니다. 이 문서에서는 개인 끝점을 사용 하 여 웹 앱을 배포한 다음 VM (가상 머신)에서 웹 앱에 연결 하는 방법을 알아봅니다.

자세한 내용은 [Azure 웹 앱에 전용 끝점 사용][privateendpointwebapp]을 참조 하세요.

> [!Note]
> 개인 끝점은 PremiumV2 계층, PremiumV3 Windows 웹 앱, Linux 웹 앱 및 Azure Functions 프리미엄 요금제 (탄력적 프리미엄 요금제 라고도 함)에 대 한 공용 지역에서 사용할 수 있습니다. 

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

시작 하기 전에 [Azure Portal](https://portal.azure.com)에 로그인 합니다.

## <a name="create-a-virtual-network-and-virtual-machine"></a>가상 네트워크 및 가상 머신 만들기

이 섹션에서는 개인 끝점을 통해 웹 앱에 액세스 하는 데 사용할 VM을 호스트 하는 가상 네트워크 및 서브넷을 만듭니다.

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기

가상 네트워크 및 서브넷을 만들려면 다음을 수행 합니다.

1. 왼쪽 창에서 **리소스 만들기**  >  **네트워킹**  >  **가상 네트워크**를 선택 합니다.

1. **가상 네트워크 만들기** 창에서 **기본** 탭을 선택 하 고 여기에 표시 된 정보를 입력 합니다.

   > [!div class="mx-imgBorder"]
   > ![Azure Portal에서 "Virtual Network 만들기" 창의 스크린샷][1]

1. **IP 주소** 탭을 선택 하 고 여기에 표시 된 정보를 입력 합니다.

   > [!div class="mx-imgBorder"]
   > ![가상 네트워크 만들기 창에 있는 "IP 주소" 탭의 스크린샷][2]

1. **서브넷** 섹션에서 **서브넷 추가**를 선택 하 고 여기에 표시 된 정보를 입력 한 다음 **추가**를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > !["서브넷 추가" 창의 스크린샷][3]

1. **검토 + 만들기**를 선택합니다.

1. 유효성 검사를 완료 한 후 **만들기**를 선택 합니다.

### <a name="create-the-virtual-machine"></a>가상 머신 만들기

가상 컴퓨터를 만들려면 다음을 수행 합니다.

1. Azure Portal의 왼쪽 창에서 **리소스 만들기**  >  **계산**  >  **가상 머신**를 선택 합니다.

1. **가상 컴퓨터 만들기-기본 사항** 창에서 여기에 표시 된 정보를 입력 합니다.

   > [!div class="mx-imgBorder"]
   > !["가상 머신 만들기" 창의 스크린샷][4]

1. 완료되면 **다음: 디스크**를 선택합니다.

1. **디스크** 창에서 기본 설정을 유지 하 고 **다음: 네트워킹**을 선택 합니다.

1. **네트워킹** 창에서 여기에 표시 된 정보를 입력 합니다.

   > [!div class="mx-imgBorder"]
   > !["가상 컴퓨터 만들기" 창에서 "네트워킹" 탭의 스크린샷][5]

1. **검토 + 만들기**를 선택합니다.

1. 유효성 검사를 완료 한 후 **만들기**를 선택 합니다.

## <a name="create-a-web-app-and-a-private-endpoint"></a>웹 앱 및 개인 끝점 만들기

이 섹션에서는 개인 끝점을 사용 하는 개인 웹 앱을 만듭니다.

> [!Note]
> 개인 끝점 기능은 PremiumV2 및 PremiumV3 계층에 대해서만 사용할 수 있습니다.

### <a name="create-the-web-app"></a>웹앱 만들기

1. Azure Portal의 왼쪽 창에서 **리소스 만들기**  >  **웹**  >  **웹 앱**을 선택 합니다.

1. **웹 앱** 창에서 **기본** 탭을 선택 하 고 여기에 표시 된 정보를 입력 합니다.

   > [!div class="mx-imgBorder"]
   > !["웹 앱" 창에 있는 "기본 사항" 탭의 스크린샷][6]

1. **검토 + 만들기**를 선택합니다.

1. 유효성 검사를 완료 한 후 **만들기**를 선택 합니다.

### <a name="create-the-private-endpoint"></a>개인 끝점 만들기

1. 웹 앱 속성의 **설정**에서 **네트워킹**을 선택 하 고 * * 개인 끝점 연결 * *에서 **개인 끝점 연결 구성**을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![웹 앱 네트워킹 창에서 "개인 끝점 연결 구성" 링크의 스크린샷][7]

1. **개인 끝점 연결** 마법사에서 **추가**를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > !["개인 끝점 연결" 마법사의 추가 단추 스크린샷][8]

1. **구독**, **가상 네트워크**및 **서브넷** 드롭다운 목록에서 올바른 정보를 선택 하 고 **확인**을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > !["개인 끝점 추가" 창의 스크린샷][9]

1. 개인 끝점 생성의 진행률을 모니터링 합니다.

   > [!div class="mx-imgBorder"]
   > ![개인 끝점 ][10]
   >  ![ 추가 진행률의 스크린샷 새로 만든 개인 끝점의 스크린샷][11]

## <a name="connect-to-the-vm-from-the-internet"></a>인터넷에서 VM에 연결

1. Azure Portal **검색** 상자에 **myvm**을 입력 합니다.
1. **연결**을 선택한 다음 **RDP**를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > !["MyVM" 창에 있는 "RDP" 단추의 스크린샷][12]

1. **Rdp를 사용 하 여 연결** 창에서 **rdp 파일 다운로드**를 선택 합니다.  

   > [!div class="mx-imgBorder"]
   > !["RDP를 사용 하 여 연결" 창의 "RDP 파일 다운로드" 단추 스크린샷][13]

   Azure는 RDP (원격 데스크톱 프로토콜) 파일을 만들고 컴퓨터에 다운로드 합니다.   

1. 다운로드한 RDP 파일을 엽니다.

   a. 프롬프트에서 **연결**을 선택 합니다.  
   b. VM을 만들 때 지정한 사용자 이름 및 암호를 입력합니다.

     > [!Note]
     > 이러한 자격 증명을 사용 하려면 **More choices**  >  **다른 계정 사용**옵션을 선택 해야 할 수도 있습니다.

1. **확인**을 선택합니다.

   > [!Note]
   > 로그인 프로세스 중에 인증서 경고가 표시 되 면 **예** 또는 **계속**을 선택 합니다.

1. VM 바탕 화면 창이 표시 되 면이를 최소화 하 여 로컬 데스크톱으로 다시 이동 합니다.

## <a name="access-the-web-app-privately-from-the-vm"></a>VM에서 개인적으로 웹 앱에 액세스

이 섹션에서는 개인 끝점을 사용 하 여 개인적으로 웹 앱에 연결 합니다.

1. 개인 끝점의 개인 IP를 가져오려면 **검색** 상자에 **개인 링크** 를 입력 하 고 결과 목록에서 **개인 링크**를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![검색 결과 목록에 있는 "개인 링크" 링크의 스크린샷][14]

1. 개인 링크 센터의 왼쪽 창에서 **개인 끝점을 선택 하** 여 전용 끝점을 표시 합니다.

   > [!div class="mx-imgBorder"]
   > ![개인 링크 센터의 개인 끝점 목록 스크린샷][15]

1. 웹 앱 및 서브넷에 연결 되는 개인 끝점을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![개인 끝점에 대 한 속성 창의 스크린샷][16]

1. 개인 끝점의 개인 IP와 웹 앱의 FQDN (정규화 된 도메인 이름)을 복사 합니다. 위의 예제에서 개인 ID는 *`webappdemope.azurewebsites.net 10.10.2.4`* 입니다.

1. **Myvm** 창에서 공용 IP를 통해 웹 앱에 액세스할 수 없는지 확인 합니다. 이렇게 하려면 브라우저를 열고 웹 앱 이름을 붙여넣습니다. 페이지에 "오류 403-사용할 수 없음" 메시지가 표시 됩니다.

   > [!div class="mx-imgBorder"]
   > !["오류 403-사용할 수 없음" 오류 페이지의 스크린샷][17]

   DNS의 경우 다음 중 하나를 수행 합니다.
 
   - Azure DNS 개인 영역 서비스를 사용 합니다.  

     a. 이라는 DNS 개인 영역을 만든 *`privatelink.azurewebsites.net`* 다음 가상 네트워크에 연결 합니다.  
     b. 개인 끝점의 IP 주소를 사용 하 여 두 개의 A 레코드 (즉, 앱 이름 및 서비스 제어 관리자 [SCM] 이름)를 만듭니다.  
     > [!div class="mx-imgBorder"]
     > ![DNS 개인 영역 레코드의 스크린샷][21]  

   - VM의 *호스트* 파일을 사용 합니다.  

     a. 호스트 항목을 만들고 파일 탐색기를 연 다음 *호스트* 파일을 찾습니다.  
     > [!div class="mx-imgBorder"]
     > ![파일 탐색기의 호스트 파일을 보여 주는 스크린샷][18]  
     b. 텍스트 편집기에서 *호스트* 파일을 편집 하 여 웹 앱의 개인 IP 주소 및 공개 이름이 포함 된 항목을 추가 합니다.  
     > [!div class="mx-imgBorder"]
     > ![Hosts 파일의 텍스트 스크린샷][19]  
     다. 파일을 저장합니다.

1. 브라우저에서 웹 앱의 URL을 입력 합니다.

   > [!div class="mx-imgBorder"]
   > ![웹 앱을 표시 하는 브라우저의 스크린샷][20]

이제 개인 끝점을 통해 웹 앱에 액세스 합니다.

## <a name="clean-up-resources"></a>리소스 정리

개인 끝점, 웹 앱 및 VM을 사용 하 여 작업을 완료 하면 리소스 그룹 및 여기에 포함 된 모든 리소스를 삭제 합니다.

1. Azure Portal의 **검색** 상자에 **준비-rg**를 입력 한 다음 결과 목록에서 **준비-rg** 를 선택 합니다.

1. **리소스 그룹 삭제**를 선택합니다.

1. **리소스 그룹 이름 입력**아래에서 **준비-rg**를 입력 하 고 **삭제**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 네트워크, 웹 앱 및 개인 끝점에서 VM을 만들었습니다. 인터넷에서 VM에 연결 하 고 개인 링크를 사용 하 여 웹 앱에 안전 하 게 전달 했습니다. 

개인 끝점에 대 한 자세한 내용은 [Azure 개인 끝점 이란?][privateendpoint]을 참조 하세요.

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
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
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
