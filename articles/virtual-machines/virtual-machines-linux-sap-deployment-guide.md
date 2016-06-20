<properties
   pageTitle="Linux VM(가상 컴퓨터)에서 SAP NetWeaver – 배포 가이드 | Microsoft Azure"
   description="Linux VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드"
   services="virtual-machines-linux,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="05/17/2016"
   ms.author="sedusch"/>

# Azure VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드

[767598]: https://service.sap.com/sap/support/notes/767598
[773830]: https://service.sap.com/sap/support/notes/773830
[826037]: https://service.sap.com/sap/support/notes/826037
[965908]: https://service.sap.com/sap/support/notes/965908
[1139904]: https://service.sap.com/sap/support/notes/1139904
[1173395]: https://service.sap.com/sap/support/notes/1173395
[1245200]: https://service.sap.com/sap/support/notes/1245200
[1409604]: https://service.sap.com/sap/support/notes/1409604
[1558958]: https://service.sap.com/sap/support/notes/1558958
[1585981]: https://service.sap.com/sap/support/notes/1585981
[1588316]: https://service.sap.com/sap/support/notes/1588316
[1590719]: https://service.sap.com/sap/support/notes/1590719
[1605680]: https://service.sap.com/sap/support/notes/1605680
[1619720]: https://service.sap.com/sap/support/notes/1619720
[1619726]: https://service.sap.com/sap/support/notes/1619726
[1619967]: https://service.sap.com/sap/support/notes/1619967
[1750510]: https://service.sap.com/sap/support/notes/1750510
[1752266]: https://service.sap.com/sap/support/notes/1752266
[1757924]: https://service.sap.com/sap/support/notes/1757924
[1757928]: https://service.sap.com/sap/support/notes/1757928
[1758182]: https://service.sap.com/sap/support/notes/1758182
[1758496]: https://service.sap.com/sap/support/notes/1758496
[1772688]: https://service.sap.com/sap/support/notes/1772688
[1814258]: https://service.sap.com/sap/support/notes/1814258
[1882376]: https://service.sap.com/sap/support/notes/1882376
[1909114]: https://service.sap.com/sap/support/notes/1909114
[1922555]: https://service.sap.com/sap/support/notes/1922555
[1928533]: https://service.sap.com/sap/support/notes/1928533
[1941500]: https://service.sap.com/sap/support/notes/1941500
[1956005]: https://service.sap.com/sap/support/notes/1956005
[1973241]: https://service.sap.com/sap/support/notes/1973241
[1999351]: https://service.sap.com/sap/support/notes/1999351
[2015553]: https://service.sap.com/sap/support/notes/2015553
[2039619]: https://service.sap.com/sap/support/notes/2039619
[2121797]: https://service.sap.com/sap/support/notes/2121797
[2134316]: https://service.sap.com/sap/support/notes/2134316
[2178632]: https://service.sap.com/sap/support/notes/2178632
[2191498]: https://service.sap.com/sap/support/notes/2191498
[2243692]: https://service.sap.com/sap/support/notes/2243692
[2233094]: https://service.sap.com/sap/support/notes/2233094

[azure-portal]: https://portal.azure.com
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-ps]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

[planning-guide]: virtual-machines-linux-sap-planning-guide.md "Linux VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드"
[planning-guide-classic]: virtual-machines-windows-classic-sap-planning-guide.md
[deployment-guide]: virtual-machines-linux-sap-deployment-guide.md "Linux VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드"
[deployment-guide-classic]: virtual-machines-windows-classic-sap-deployment-guide.md
[dbms-guide]: virtual-machines-linux-sap-dbms-guide.md "Linux VM(가상 컴퓨터)에서 SAP NetWeaver - DBMS 배포 가이드"
[dbms-guide-classic]: virtual-machines-windows-classic-sap-dbms-guide.md
[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971
[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[getting-started]: virtual-machines-linux-sap-getting-started-arm.md
[getting-started-windows-classic]: virtual-machines-windows-classic-sap-getting-started.md

[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-getting-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-getting-started.md#f2a5e9d8-49e4-419e-9900-af783173481c
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-getting-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-getting-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-getting-started.md#4bb7512c-0fa0-4227-9853-4004281b1037

[getting-started-planning]: virtual-machines-linux-sap-getting-started-arm.md#3da0389e-708b-4e82-b2a2-e92f132df89c
[getting-started-deployment]: virtual-machines-linux-sap-getting-started-arm.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-dbms]: virtual-machines-linux-sap-getting-started-arm.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82

[deployment-guide-2.2]: virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "SAP 리소스"
[deployment-guide-3]: virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Microsoft Azure에서의 SAP용 VM 배포 시나리오"
[deployment-guide-3.1.2]: virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "사용자 지정 이미지를 사용하여 VM 배포"
[deployment-guide-3.2]: virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "시나리오 1: SAP용 Azure 마켓플레이스에서 VM 배포"
[deployment-guide-3.3]: virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포"
[deployment-guide-3.4]: virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "시나리오 3: SAP에서 일반화되지 않은 Azure VHD를 사용하여 온-프레미스에서 VM 이동"
[deployment-guide-4.1]: virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Azure PowerShell cmdlet 배포"
[deployment-guide-4.2]: virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "SAP 관련 PowerShell cmdlet 다운로드 및 가져오기"
[deployment-guide-4.3]: virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "온-프레미스 도메인에 VM 가입 - Windows에만 해당"
[deployment-guide-4.4]: virtual-machines-linux-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Azure VM 에이전트 다운로드, 설치 및 사용"
[deployment-guide-4.4.2]: virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.5]: virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "SAP용 Azure 고급 모니터링 확장 구성"
[deployment-guide-4.5.1]: virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "Azure CLI"
[deployment-guide-5.1]: virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "SAP용 Azure 고급 모니터링에 대한 준비 검사"
[deployment-guide-5.2]: virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Azure 모니터링 인프라 구성에 대한 상태 검사"
[deployment-guide-5.3]: virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "SAP용 Azure 모니터링 인프라의 추가 문제 해결"
[deployment-guide-install-vm-agent-windows]: virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-configure-proxy]: virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "프록시 구성"
[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "모니터링 구성"
[deployment-guide-troubleshooting-chapter]: virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Azure의 SAP용 종단 간 모니터링 설정 확인 및 문제 해결"
[deployment-guide-figure-100]: ./media/virtual-machines-linux-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-300]: ./media/virtual-machines-linux-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-linux-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-500]: ./media/virtual-machines-linux-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-600]: ./media/virtual-machines-linux-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-700]: ./media/virtual-machines-linux-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-linux-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-50]: ./media/virtual-machines-linux-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-900]: ./media/virtual-machines-linux-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-1000]: ./media/virtual-machines-linux-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-1100]: ./media/virtual-machines-linux-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-linux-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-linux-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-1400]: ./media/virtual-machines-linux-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-5]: virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-6]: virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-7]: virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-11]: virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-14]: virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-azure-cli-installed]: virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda

[planning-guide-1.2]: virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "리소스"
[planning-guide-2.1]: virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "클라우드 전용 - 온-프레미스 고객 네트워크에 의존하지 않고 Azure에 가상 컴퓨터 배포"
[planning-guide-2.2]: virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "프레미스 간 - 온-프레미스 네트워크에 요구 사항을 완전히 통합하고 Azure에 단일 또는 다중 SAP VM 배포"
[planning-guide-3.1]: virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Azure 지역"
[planning-guide-3.2]: virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "Microsoft Azure 가상 컴퓨터 개념"
[planning-guide-3.2.1]: virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "장애 도메인"
[planning-guide-3.2.2]: virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "업그레이드 도메인"
[planning-guide-3.2.3]: virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Azure 가용성 집합"
[planning-guide-3.3.2]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Azure 프리미엄 저장소"
[planning-guide-5.1.1]: virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동"
[planning-guide-5.1.2]: virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "고객별 이미지를 사용하여 VM 배포"
[planning-guide-5.2]: virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Azure용 SAP로 VM 준비"
[planning-guide-5.2.1]: virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로의 VM 이동 준비"
[planning-guide-5.2.2]: virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "SAP용 고객별 이미지를 사용하여 VM 배포 준비"
[planning-guide-5.3.1]: virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Azure 디스크와 Azure 이미지 간 차이점"
[planning-guide-5.3.2]: virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "온-프레미스에서 Azure로 VHD 업로드"
[planning-guide-5.4.2]: virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Azure 저장소 계정 간 디스크 복사"
[planning-guide-5.5.1]: virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "SAP 배포를 위한 VM/VHD 구조"
[planning-guide-5.5.3]: virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "연결된 디스크에 대한 자동 탑재 설정"
[planning-guide-7]: virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "SAP 인스턴스의 클라우드 전용 배포 개념"
[planning-guide-7.1]: virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "SAP NetWeaver 데모/학습 시나리오가 있는 단일 VM"
[planning-guide-9.1]: virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "SAP용 Azure 모니터링 솔루션"
[planning-guide-11.4.1]: virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "SAP 응용 프로그램 서버에 대한 고가용성"
[planning-guide-11.5]: virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "SAP 인스턴스에 대해 자동 시작 사용"
[planning-guide-microsoft-azure-networking]: virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Microsoft Azure 네트워킹"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "저장소: Microsoft Azure 저장소 및 데이터 디스크"
[planning-guide-azure-premium-storage]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Azure 프리미엄 저장소"
[planning-guide-figure-100]: ./media/virtual-machines-linux-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-200]: ./media/virtual-machines-linux-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-300]: ./media/virtual-machines-linux-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-400]: ./media/virtual-machines-linux-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]: ./media/virtual-machines-linux-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]: ./media/virtual-machines-linux-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]: ./media/virtual-machines-linux-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-figure-1300]: ./media/virtual-machines-linux-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]: ./media/virtual-machines-linux-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]: ./media/virtual-machines-linux-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]: ./media/virtual-machines-linux-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]: ./media/virtual-machines-linux-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-2100]: ./media/virtual-machines-linux-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]: ./media/virtual-machines-linux-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]: ./media/virtual-machines-linux-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]: ./media/virtual-machines-linux-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]: ./media/virtual-machines-linux-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]: ./media/virtual-machines-linux-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]: ./media/virtual-machines-linux-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]: ./media/virtual-machines-linux-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]: ./media/virtual-machines-linux-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-3000]: ./media/virtual-machines-linux-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]: ./media/virtual-machines-linux-sap-planning-guide/3200-sap-ha-with-sql.png

[dbms-guide-2]: virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "RDBMS 배포의 구조"
[dbms-guide-2.1]: virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "VM 및 VHD용 캐싱"
[dbms-guide-2.2]: virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "소프트웨어 RAID"
[dbms-guide-2.3]: virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Microsoft Azure 저장소"
[dbms-guide-3]: virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Azure VM에서 고가용성 및 재해 복구"
[dbms-guide-5]: virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "SQL Server RDBMS에 대한 고유 정보"
[dbms-guide-5.5.1]: virtual-machines-linux-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 이상"
[dbms-guide-5.5.2]: virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 및 이전 버전"
[dbms-guide-5.6]: virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Microsoft Azure 마켓플레이스에서 SQL Server 이미지 사용"
[dbms-guide-5.8]: virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Azure의 SAP용 일반 SQL Server 요약"
[dbms-guide-8.4.1]: virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "저장소 구성"
[dbms-guide-8.4.2]: virtual-machines-linux-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "백업 및 복원"
[dbms-guide-8.4.3]: virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "백업 및 복원에 대한 성능 고려 사항"
[dbms-guide-8.4.4]: virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "기타"
[dbms-guide-figure-100]: ./media/virtual-machines-linux-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-linux-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-linux-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-linux-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-linux-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-linux-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-linux-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-linux-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-linux-sap-dbms-guide/900-sap-cache-server-on-premises.png

[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[Logo_Windows]: ./media/virtual-machines-linux-sap-shared/Windows.png
[Logo_Linux]: ./media/virtual-machines-linux-sap-shared/Linux.png

[vm-size-specs]: virtual-machines-size-specs.md
[azure-subscription-service-limits-subscription]: azure-subscription-service-limits.md#subscription
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]: vpn-gateway-cross-premises-options.md
[vpn-gateway-site-to-site-create]: vpn-gateway-site-to-site-create.md
[virtual-machines-deploy-rmtemplates-azure-cli]: virtual-machines-deploy-rmtemplates-azure-cli.md "Azure Resource Manager 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-deploy-rmtemplates-powershell.md "Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리"
[virtual-machines-linux-capture-image-resource-manager]: virtual-machines-linux-capture-image.md
[virtual-machines-manage-availability]: virtual-machines-manage-availability.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-how-to-attach-disk.md
[virtual-networks-reserved-private-ip]: virtual-networks-static-private-ip-arm-ps.md
[virtual-machines-sql-server-infrastructure-services]: virtual-machines-sql-server-infrastructure-services.md
[storage-redundancy]: storage-redundancy.md
[storage-scalability-targets]: storage-scalability-targets.md
[virtual-networks-manage-dns-in-vnet]: virtual-networks-manage-dns-in-vnet.md
[resource-groups-networking]: resource-groups-networking.md
[virtual-networks-static-private-ip-arm-pportal]: virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-multiple-nics]: virtual-networks-multiple-nics.md
[virtual-network-deploy-multinic-arm-template]: virtual-network-deploy-multinic-arm-template.md
[virtual-network-deploy-multinic-arm-ps]: virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-cli]: virtual-network-deploy-multinic-arm-cli.md
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-about-vpn-devices]: vpn-gateway-about-vpn-devices.md
[vpn-gateway-vpn-faq]: vpn-gateway-vpn-faq.md
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[powershell-install-configure]: powershell-install-configure.md
[xplat-cli]: xplat-cli.md
[virtual-machines-deploy-rmtemplates-azure-cli]: virtual-machines-deploy-rmtemplates-azure-cli.md
[xplat-cli-azure-resource-manager]: xplat-cli-azure-resource-manager.md
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-create-upload-vhd-suse.md
[storage-use-azcopy]: storage-use-azcopy.md
[virtual-machines-linux-capture-image-resource-manager-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[storage-azure-cli]: storage-azure-cli.md
[storage-powershell-guide-full-copy-vhd]: storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-azure-cli-copy-blobs]: storage-azure-cli.md#copy-blobs
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-size-specs]: virtual-machines-size-specs.md
[virtual-machines-sql-server-performance-best-practices]: virtual-machines-sql-server-performance-best-practices.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]: virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md
[virtual-machines-sql-server-alwayson-availability-groups-powershell]: virtual-machines-sql-server-alwayson-availability-groups-powershell.md
[virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener]: virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md
[virtual-networks-configure-vnet-to-vnet-connection]: virtual-networks-configure-vnet-to-vnet-connection.md
[azure-subscription-service-limits]: azure-subscription-service-limits.md
[virtual-machines-configuring-oracle-data-guard]: virtual-machines-configuring-oracle-data-guard.md
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-attach-disk-preview]: virtual-machines-attach-disk-preview.md
[virtual-machines-workload-template-sql-alwayson]: virtual-machines-workload-template-sql-alwayson.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-how-to-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux
[resource-group-authoring-templates]: resource-group-authoring-templates.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-linux-create-upload-vhd-step-1]: virtual-machines-linux-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[deploy-template-powershell]: resource-group-template-deploy.md#deploy-with-powershell
[deploy-template-cli]: resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: resource-group-template-deploy.md#deploy-with-the-preview-portal
[virtual-networks-udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[resource-group-overview]: ../resource-group-overview.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-networks-nsg]: virtual-networks-nsg.md
[storage-premium-storage-preview-portal]: storage-premium-storage-preview-portal.md
[storage-introduction]: storage-introduction.md
[virtual-machines-upload-image-windows-resource-manager]: virtual-machines-windows-upload-image.md
[virtual-machines-azure-resource-manager-architecture]: virtual-machines-azure-resource-manager-architecture.md
[virtual-machines-windows-tutorial]: virtual-machines-windows-hero-tutorial.md
[virtual-networks-create-vnet-arm-pportal]: virtual-networks-create-vnet-arm-pportal.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-ps-create.md
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[virtual-machines-azurerm-versus-azuresm]: virtual-machines-azurerm-versus-azuresm.md

[install-extension-cli]: https://github.com/Azure/azure-linux-extensions/blob/master/AzureEnhancedMonitor/README.md

[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[sap-templates-2-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[templates-101-simple-windows-vm]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[template-201-vm-from-specialized-vhd]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd

[sap-pam]: https://support.sap.com/pam "SAP 제품 가용성 매트릭스"

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

회사에서는 Microsoft Azure를 통해 긴 조달 주기를 거치지 않고도 최단 시간에 계산 및 저장소 리소스를 가져올 수 있습니다. 회사에서는 Azure 가상 컴퓨터를 사용하여 SAP NetWeaver 기반 응용 프로그램과 같은 클래식 응용 프로그램을 Azure에 배포하고 온-프레미스에서 사용할 수 있는 추가 리소스를 확보하지 않고도 안정성과 가용성을 확장할 수 있습니다. 또한 Microsoft Azure는 프레미스 간 연결을 지원하여 회사에서 온-프레미스 도메인, 사설 클라우드 및 SAP 시스템 배경으로 적극적으로 Azure 가상 컴퓨터를 통합할 수 있도록 합니다.

이 백서는 SAP NetWeaver 기반 응용 프로그램의 배포를 위해 Azure 가상 컴퓨터를 준비하는 방법을 단계별로 설명합니다. [계획 및 구현 가이드][planning-guide]에 포함된 정보를 알고 있다고 가정합니다. 그렇지 않은 경우 해당 문서를 먼저 읽어야 합니다.

이 문서는 지정된 플랫폼에서 SAP 소프트웨어 설치 및 배포에 대한 기본 리소스를 나타내는 SAP 설치 설명서 및 SAP 정보를 보완합니다.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## 소개
전 세계의 많은 회사가 SAP NetWeaver 기반 응용 프로그램(특히 SAP Business Suite)을 사용하여 업무상 중요한 비즈니스 프로세스를 실행합니다. 따라서 시스템 상태가 중요한 자산이며 성능 문제를 포함하여 오작동 발생 시 엔터프라이즈 지원을 제공하는 기능이 필수 요구 사항이 되고 있습니다. Microsoft Azure는 업무상 중요한 모든 응용 프로그램의 지원 가능성 요구 사항에 맞게 뛰어난 플랫폼 계측을 제공합니다. 이 가이드를 통해 SAP 소프트웨어 배포의 대상이 되는 Microsoft Azure 가상 컴퓨터를 구성할 수 있습니다. 따라서 가상 컴퓨터가 만들어지는 방식에 관계없이 엔터프라이즈 지원을 Azure 마켓플레이스에서 제공하거나 고객별 이미지를 사용하여 제공할 수 있습니다. 다음에서 필요한 모든 설치 단계를 자세히 설명합니다.

## 필수 조건 및 리소스
### 필수 조건
시작하기 전에 다음 챕터에서 설명하는 필수 조건을 충족하는지 확인하세요.

#### 로컬 개인용 컴퓨터
SAP 소프트웨어 배포를 위한 Azure 가상 컴퓨터 설치는 여러 단계로 구성됩니다. Windows VM 또는 Linux VM을 관리하려면 Microsoft Azure 포털 및 PowerShell 스크립트를 사용해야 합니다. 그러려면 Windows 7 이상이 실행되는 로컬 개인용 컴퓨터가 필요합니다. Linux VM만 관리하고 이 작업에 Linux 컴퓨터를 사용하려면 Azure CLI(Azure 명령줄 인터페이스)를 사용할 수도 있습니다.

#### 인터넷 연결
필요한 도구 및 스크립트를 다운로드하고 실행하려면 인터넷 연결이 필요합니다. 또한 Azure 고급 모니터링 확장을 실행하는 Microsoft Azure 가상 컴퓨터는 인터넷에 액세스해야 합니다. Azure VM이 Azure 가상 네트워크 또는 온-프레미스 도메인의 일부인 경우 관련 프록시 설정이 이 문서의 [프록시 구성][deployment-guide-configure-proxy] 챕터에 설명된 대로 설정되어 있는지 확인합니다.

#### Microsoft Azure 구독
Azure 계정이 이미 있으며 해당 로그온 자격 증명은 알려져 있습니다.

#### 토폴로지 고려 사항 및 네트워킹
Azure에서 SAP 배포의 아키텍처 및 토폴로지를 정의해야 합니다. 다음에 대한 아키텍처입니다.

* 사용할 Microsoft Azure 저장소 계정
* SAP 시스템을 배포할 가상 네트워크
* SAP 시스템을 배포할 리소스 그룹
* SAP 시스템을 배포할 Azure 지역
* SAP 구성(2계층 또는 3계층)
* VM 크기 및 VM에 탑재할 추가 VHD 수
* SAP 전송 및 수정 시스템 구성

따라서 Azure 저장소 계정 또는 Azure 가상 네트워크를 이미 만들고 구성했어야 합니다. Azure 저장소 계정 또는 Azure 가상 네트워크를 만들고 구성하는 방법은 [계획 및 구현 가이드][planning-guide]에서 설명합니다.

#### SAP 크기 조정
* 예를 들어 SAP Quicksizer를 사용하여 예상되는 SAP 워크로드가 결정되었고 해당 SAPS 숫자는 알려져 있습니다. 
* SAP 시스템의 필요한 CPU 리소스 및 메모리 소비를 알고 있어야 합니다.
* 초당 필요한 I/O 작업을 알고 있어야 합니다.
* Azure에서 서로 다른 VM 간의 결과적 통신에 필요한 네트워크 대역폭은 알려져 있습니다.
* 온-프레미스 자산과 Azure가 배포된 SAP 시스템 간에 필요한 네트워크 대역폭은 알려져 있습니다.

#### 리소스 그룹
리소스 그룹은 동일한 수명 주기(예: 동시에 만들어지고 삭제됨)를 갖는 모든 리소스를 포함하는 새로운 개념입니다. 리소스 그룹에 대한 자세한 내용은 [이 문서][resource-group-overview]를 참조하세요.

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP 리소스
구성 작업 중 다음 리소스가 필요합니다.

* SAP Note [1928533]
	* SAP 소프트웨어 배포에 지원되는 Azure 가상 컴퓨터 크기 목록 
	* Azure 가상 컴퓨터 크기별 중요한 용량 정보
	* 지원되는 SAP 소프트웨어 및 OS와 DB 조합
* SAP Note [2015553] Microsoft Azure에 SAP 소프트웨어를 배포할 때 SAP에서 지원하는 필수 조건이 나열되어 있습니다.
* SAP Note [1999351] SAP용 고급 Azure 모니터링을 위한 추가 문제 해결 정보가 포함되어 있습니다.
* SAP Note [2178632] Microsoft Azure에서 SAP에 대한 모든 사용 가능한 모니터링 메트릭의 세부 정보가 포함되어 있습니다. 
* SAP Note [1409604] 새 Azure Resource Manager에 배포할 때 Microsoft Azure에 필요한 Windows용 SAP 호스트 에이전트 버전이 포함되어 있습니다.
* SAP Note [2191498] 새 Azure Resource Manager에 배포할 때 Microsoft Azure에 필요한 Linux용 SAP 호스트 에이전트 버전이 포함되어 있습니다.
* SAP Note [2243692] Azure에서 Linux의 SAP용 라이선스에 대한 정보가 포함되어 있습니다.
* 필요한 모든 Linux용 SAP Note를 포함하는 [SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)
* [Azure PowerShell][azure-ps]에 포함되는 SAP 관련 PowerShell cmdlet
* [Azure CLI][azure-cli]에 포함되는 SAP 관련 Azure CLI
* [Microsoft Azure 포털][azure-portal]

[설명]: <> (MSSedusch TODO SAP Note 1409604에서 SAP 호스트 에이전트용 ARM 패치 수준 추가)
 
다음 가이드에서 Microsoft Azure에서 SAP 항목도 설명합니다.

* [Azure VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드][planning-guide]
* [Azure VM(가상 컴퓨터)에서 SAP NetWeaver – 배포 가이드(이 문서)][deployment-guide]
* [Azure VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Microsoft Azure에서의 SAP용 VM 배포 시나리오
이 챕터에서는 다양한 배포 방법 및 각 배포 유형에 대한 단계별 내용을 알아봅니다.

### SAP용 VM 배포
Microsoft Azure는 VM 및 관련 디스크를 배포하기 위한 여러 가지 방법을 제공합니다. 따라서 배포 방법에 따라 VM 준비가 달라질 수 있으므로 차이점을 이해해야 합니다. 일반적으로 다음 시나리오를 살펴봅니다.

#### Azure 마켓플레이스에서 VM 배포
Azure 마켓플레이스에서 VM을 배포하는 데 Microsoft 또는 타사에서 제공한 이미지를 사용할 수 있습니다. Microsoft Azure에서 VM을 배포한 후 온-프레미스 환경에서와 동일한 지침 및 도구를 사용하여 VM 내부에 SAP 소프트웨어를 설치합니다. Azure VM에 SAP 소프트웨어를 설치할 때는 SAP 설치 미디어를 Azure VHD에 업로드 및 저장하거나 필요한 SAP 설치 미디어를 모두 포함하는 '파일 서버'로 사용할 Azure VM을 만드는 것이 좋습니다.

[설명]: <> (MSSedusch TODO 파일 관리(예: 파일 서버 또는 VHD)를 권장해야 하는 이유가 무엇인가요? 온-프레미스에서와 차이가 있나요?)

자세한 내용은 [시나리오 1: SAP용 Azure 마켓플레이스에서 VM 배포][deployment-guide-3.2] 챕터를 참조하세요.

#### <a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>사용자 지정 이미지를 사용하여 VM 배포
OS 또는 DBMS 버전에 대한 특정 패치 요구 사항으로 인해 Azure 마켓플레이스에서 제공된 이미지가 용도에 맞지 않을 수 있습니다. 따라서 '개인' OS/DB VM 이미지를 사용하는 VM을 만들어 이후 여러 번 배포할 수 있습니다. 개인 이미지를 만드는 단계는 Windows 이미지 및 Linux 이미지에 따라 다릅니다.

___

> ![Windows][Logo_Windows] Windows
>
> 여러 가상 컴퓨터를 배포하는 데 사용할 수 있는 Windows 이미지를 준비하려면 Windows 설정(예: Windows SID 및 호스트 이름)을 온-프레미스 VM에서 추상화/일반화해야 합니다. <https://technet.microsoft.com/library/cc721940.aspx>에 설명된 대로 sysprep을 사용하여 수행할 수 있습니다.
>
> ![Linux][Logo_Linux] Linux
>
> 여러 가상 컴퓨터를 배포하는 데 사용할 수 있는 Linux 이미지를 준비하려면 일부 Linux 설정을 온-프레미스 VM에서 추상화/일반화해야 합니다. [이 문서][virtual-machines-linux-capture-image] 또는 [이 문서][virtual-machines-linux-agent-user-guide-command-line-options]에 설명된 대로 waagent -deprovision을 사용하여 수행할 수 있습니다.

___

SAP 소프트웨어 프로비전 관리자를 통해 새 SAP 시스템을 설치하거나 가상 컴퓨터에 연결된 VHD에서 데이터베이스 백업을 복원하거나 DBMS에서 지원하는 경우 Azure 저장소에서 직접 데이터베이스 백업을 복원하여 데이터베이스 내용을 설정할 수 있습니다([DBMS 배포 가이드][dbms-guide] 참조). 온-프레미스 VM(특히 2계층 시스템)에 SAP 시스템을 이미 설치한 경우 Azure VM 배포 후 SAP Software Provisioning Manager에서 지원하는 시스템 이름 변경 절차를 통해 SAP 시스템 설정을 조정할 수 있습니다(SAP Note [1619720]). 그렇지 않은 경우 Azure VM 배포 후 나중에 SAP 소프트웨어를 설치할 수 있습니다.

자세한 내용은 [시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포][deployment-guide-3.3] 챕터를 참조하세요.

#### 일반화되지 않은 디스크를 사용하여 온-프레미스에서 Microsoft Azure로 VM 이동
온-프레미스에서 Microsoft Azure로 특정 SAP 시스템을 이동하려고 합니다. 이 작업은 OS, SAP 이진 및 결과적 DBMS 이진을 포함하는 VHD와 DBMS의 데이터와 로그 파일이 있는 VHD를 Microsoft Azure에 업로드하여 수행할 수 있습니다. [사용자 지정 이미지를 사용하여 VM 배포][deployment-guide-3.1.2] 챕터에서 설명한 시나리오와는 달리 Azure VM의 호스트 이름, SAP SID 및 SAP 사용자 계정을 온-프레미스 환경에서 구성된 대로 유지합니다. 따라서 운영 체제를 일반화할 필요는 없습니다. 이 경우는 SAP 배경의 일부가 온-프레미스에서 실행되고 일부는 Microsoft Azure에서 실행되는 프레미스 간 시나리오에 주로 적용됩니다.

자세한 내용은 [시나리오 3: SAP에서 일반화되지 않은 Azure VHD를 사용하여 온-프레미스에서 VM 이동][deployment-guide-3.4] 챕터를 참조하세요.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>시나리오 1: SAP용 Azure 마켓플레이스에서 VM 배포
Microsoft Azure를 사용하여 Windows Server 및 다른 Linux 배포의 표준 OS 이미지 일부를 제공하는 Azure 마켓플레이스에서 VM 인스턴스를 배포할 수 있습니다. DBMS SKU(예: SQL Server)를 포함하는 이미지를 배포할 수도 있습니다. DBMS SKU를 포함하는 이미지를 사용하는 방법에 대한 자세한 내용은 [DBMS 배포 가이드][dbms-guide]를 참조하세요.

Azure 마켓플레이스에서 VM을 배포하는 단계의 SAP 관련 시퀀스는 다음과 같습니다.

![Azure 마켓플레이스에서 VM 이미지를 사용하여 SAP 시스템용 VM 배포 순서도][deployment-guide-figure-100]

순서도에 따라 다음 단계를 실행해야 합니다.

#### Azure 포털을 사용하여 가상 컴퓨터 만들기
Azure 마켓플레이스에서 이미지를 사용하여 새 가상 컴퓨터를 만드는 가장 쉬운 방법은 Azure 포털을 사용하는 것입니다. <https://portal.azure.com/#create>로 이동합니다. 배포하려는 운영 체제 유형(예: Windows 또는 SUSE)을 입력하고 버전을 선택합니다. 배포 모델 "Azure Resource Manager"를 선택한 다음 만들기를 클릭합니다.

마법사의 필수 매개 변수를 통해 저장소 계정 또는 네트워크 인터페이스와 같은 필요한 모든 리소스와 함께 가상 컴퓨터를 만들 수 있습니다. 다음은 일부 매개 변수입니다.

1. 기본 사항
    1. 이름: 리소스의 이름, 즉 가상 컴퓨터 이름입니다.
    1. 사용자 이름 및 암호/SSH 공개 키: 사용자 이름과 프로비전 중에 만든 사용자 암호를 입력합니다. Linux 가상 컴퓨터의 경우 SSH를 사용하여 컴퓨터에 로그인하는 데 사용할 공용 SSH 키를 입력할 수도 있습니다.
    1. 구독: 새 가상 컴퓨터를 프로비전하는 데 사용할 구독을 선택합니다.
    1. 리소스 그룹: 리소스 그룹의 이름입니다. 새 리소스 그룹 또는 기존의 리소스 그룹의 이름을 삽입할 수 있습니다.
    1. 위치: 새 가상 컴퓨터를 배포해야 할 위치를 선택합니다. 온-프레미스 네트워크에 가상 컴퓨터를 연결하려는 경우 온-프레미스 네트워크에 Azure를 연결하는 가상 네트워크의 위치를 선택해야 합니다. 자세한 내용은 [계획 가이드][planning-guide]의 [Microsoft Azure 네트워킹][planning-guide-microsoft-azure-networking] 챕터를 참조하세요.
1. 크기: 지원되는 VM 유형 목록은 SAP Note [1928533]을 참조하세요. 또한 프리미엄 저장소를 사용하려면 올바른 유형을 선택해야 합니다. 모든 VM 유형이 프리미엄 저장소를 지원하지는 않습니다. 자세한 내용은 [계획 가이드][planning-guide]의 [저장소: Microsoft Azure 저장소 및 데이터 디스크][planning-guide-storage-microsoft-azure-storage-and-data-disks] 및 [Azure 프리미엄 저장소][planning-guide-azure-premium-storage] 챕터를 참조하세요.
1. 설정
    1. 저장소 계정: 기존 저장소 계정을 선택하거나 새 저장소 계정을 만들 수 있습니다. 기타 저장소 유형에 대한 자세한 내용은 [DBMS 가이드][dbms-guide]의 [Microsoft Azure 저장소][dbms-guide-2.3] 챕터를 참조하세요. 모든 저장소 유형이 SAP 응용 프로그램 실행을 위해 지원되지는 않습니다.
    1. 가상 네트워크 및 서브넷: 인트라넷에 가상 컴퓨터를 통합하려는 경우 온-프레미스 네트워크에 연결된 가상 네트워크를 선택합니다.
    1. 공용 IP 주소: 사용하려는 공용 IP 주소를 선택하거나 매개 변수를 입력하여 새 공용 IP 주소를 만듭니다. 인터넷에서 가상 컴퓨터에 액세스하는 공용 IP 주소를 사용할 수 있습니다. 또한 가상 컴퓨터에 대한 액세스를 필터링할 네트워크 보안 그룹을 만들어야 합니다.
    1. 네트워크 보안 그룹: 자세한 내용은 [NSG(네트워크 보안 그룹)란?][virtual-networks-nsg]을 참조하세요.
    1. 모니터링: 진단 설정을 해제할 수 있습니다. 이는 [모니터링 구성][deployment-guide-configure-monitoring-scenario-1] 챕터에서 설명한 대로 Azure 고급 모니터링을 활성화하는 명령을 실행할 때 자동으로 사용 설정됩니다.
    1. 가용성: 가용성 집합을 선택하거나 매개 변수를 입력하여 새 가용성 집합을 만듭니다. 자세한 내용은 [Azure 가용성 집합][planning-guide-3.2.3] 챕터를 참조하세요.
1. 요약: 요약 페이지에 제공된 정보의 유효성을 검사하고 확인을 클릭합니다.

마법사를 마치면 선택한 리소스 그룹에서 가상 컴퓨터가 배포됩니다.

#### 템플릿을 사용하여 가상 컴퓨터 만들기
[azure-quickstart-templates GitHub 리포지토리][azure-quickstart-templates-github]에서 게시된 SAP 템플릿 중 하나를 사용하여 배포를 만들 수도 있습니다. 또는 [Azure 포털][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] 또는 [Azure CLI][virtual-machines-linux-tutorial]를 사용하여 수동으로 가상 컴퓨터를 만들 수 있습니다.

* [2계층 구성(단일 가상 컴퓨터) 템플릿][sap-templates-2-tier-marketplace-image] 한 대의 가상 컴퓨터를 사용하여 2계층 시스템을 만들려는 경우 이 템플릿을 사용합니다.
* [3계층 구성(복수 가상 컴퓨터) 템플릿][sap-templates-3-tier-marketplace-image] 여러 대의 가상 컴퓨터를 사용하여 3계층 시스템을 만들려는 경우 이 템플릿을 사용합니다.

위의 템플릿 중 하나를 열면 Azure 포털이 매개 변수 편집 패널을 탐색합니다. 다음 정보를 입력합니다.

* **sapSystemId**: SAP 시스템 ID
* **osType**: 배포하려는 운영 체제(예: Windows Server 2012 R2 또는 SLES 12)
    * 목록에는 Microsoft Azure의 SAP에서 지원하는 버전만 포함됩니다.
* **sapSystemSize**: SAP 시스템의 크기
    * 새 시스템에서 제공하는 SAP의 양입니다. 시스템에 필요한 SAP의 양을 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
* **systemAvailability**: (3계층 템플릿에만 해당) 시스템 가용성 
    * HA 설치에 적합한 구성에 대한 HA를 선택합니다. ASCS용 2개의 데이터베이스 서버 및 2개의 서버가 생성됩니다.
* storageType: (2계층 템플릿에만 해당) 사용해야 하는 저장소 유형 
    * 더 큰 시스템의 경우 프리미엄 저장소를 사용하는 것이 좋습니다. 기타 저장소 유형에 대한 자세한 내용은 다음을 참조하세요. 
        * [DBMS 가이드][dbms-guide]의 [Microsoft Azure 저장소][dbms-guide-2.3]
        * [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소][storage-premium-storage-preview-portal]
        * [Microsoft Azure 저장소 소개][storage-introduction]
* **adminUsername** 및 **adminPassword**: 사용자 이름 및 암호
    * 컴퓨터에 로그온하는 데 사용할 수 있게 만들어진 새 사용자입니다.
* **newOrExistingSubnet**: 새 가상 네트워크 및 서브넷을 만들지 또는 기존 서브넷을 사용할지를 결정합니다. 온-프레미스 네트워크에 연결되어 있는 가상 네트워크가 이미 있는 경우 기존 항목을 선택합니다.
* **subnetId**: 가상 컴퓨터를 연결해야 하는 서브넷의 ID입니다. 온-프레미스 네트워크에 가상 컴퓨터를 연결할 Express 경로 가상 네트워크의 서브넷 또는 VPN을 선택합니다. ID는 일반적으로 /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`> 형태입니다.

모든 매개 변수를 입력한 후에 구독 및 사용하려는 리소스 그룹을 선택합니다. 기존 리소스 그룹을 선택하거나 드롭다운 메뉴에서 "+ 새로 만들기"를 선택하여 새 리소스 그룹을 만들 수 있습니다. 새 리소스 그룹을 만드는 경우 리소스 그룹 및 가상 컴퓨터가 생성될 지역을 선택할 수도 있습니다.

약관을 검토하여 동의하고 만들기를 클릭합니다.

Azure 마켓플레이스에서 이미지를 사용하는 경우 Azure VM 에이전트가 기본적으로 배포됩니다.

#### 프록시 설정 구성
온-프레미스 네트워크 구성에 따라, VPN 또는 Express 경로를 통해 온-프레미스 네트워크에 연결되어 있는 경우 가상 컴퓨터에서 프록시를 구성해야 할 수 있습니다. 그러지 않으면 가상 컴퓨터가 인터넷에 액세스하지 못할 수 있으며 따라서 필요한 확장을 다운로드할 수 없거나 모니터링 데이터를 수집할 수 없습니다. 이 문서의 [프록시 구성][deployment-guide-configure-proxy] 챕터를 참조하세요.

#### 도메인 가입(Windows에만 해당)
Azure에서 배포가 Azure 사이트 간 또는 Express 경로를 통해 온-프레미스 AD/DNS에 연결된 경우([계획 및 구현 가이드][planning-guide]에서 프레미스 간으로도 참조됨), VM이 온-프레미스 도메인에 가입됩니다. 이 단계의 고려 사항은 이 문서의 [온-프레미스 도메인에 VM 가입(Windows에만 해당)][deployment-guide-4.3] 챕터에서 설명합니다.

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>모니터링 구성
이 문서의 [SAP용 Azure 고급 모니터링 확장 구성][deployment-guide-4.5] 챕터에서 설명된 대로 SAP용 Azure 고급 모니터링 확장을 구성합니다.

이 문서의 [SAP 리소스][deployment-guide-2.2] 챕터에 나열된 리소스에서 필요한 SAP 커널 및 SAP 호스트 에이전트의 최소 버전에 대한 SAP 모니터링의 필수 조건을 확인합니다.

#### 모니터링 확인
[Azure의 SAP용 종단 간 모니터링 설정 확인 및 문제 해결][deployment-guide-troubleshooting-chapter] 챕터에 설명된 대로 모니터링이 작동 중인지 확인합니다.

#### 배포 후 단계
VM이 만들어져 배포되면 VM에 모든 필수 소프트웨어 구성 요소를 설치할 수 있습니다. 따라서 이러한 유형의 VM을 배포하려면 일부 다른 VM의 Microsoft Azure에서 이미 사용할 수 있게 설치된 소프트웨어 또는 연결할 수 있는 디스크가 필요합니다. 또는 온-프레미스 자산에 연결된(공유 설치) 프레미스 간 시나리오를 살펴봅니다.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포
[계획 및 구현 가이드][planning-guide]의 자세한 단계를 통해 이미 설명한 대로 사용자 지정 이미지를 준비하고 만들어 여러 VM을 새로 만드는 데 사용하는 방법이 있습니다. 순서도의 단계 시퀀스는 다음과 같습니다.
 
![개인 마켓플레이스에서 VM 이미지를 사용하여 SAP 시스템용 VM 배포 순서도][deployment-guide-figure-300]

순서도에 따라 다음 단계를 실행해야 합니다.

#### 가상 컴퓨터 만들기
Azure 포털을 통해 개인 OS 이미지를 사용하여 배포를 만들려면 [azure-quickstart-templates GitHub 리포지토리][azure-quickstart-templates-github]에 게시된 SAP 템플릿 중 하나를 사용합니다. [PowerShell][virtual-machines-upload-image-windows-resource-manager]을 사용하여 수동으로 가상 컴퓨터를 만들 수도 있습니다.

* [2계층 구성(단일 가상 컴퓨터) 템플릿][sap-templates-2-tier-user-image] 한 대의 가상 컴퓨터 및 고유의 OS 이미지를 사용하여 2계층 시스템을 만들려는 경우 이 템플릿을 사용합니다.
* [3계층 구성(복수 가상 컴퓨터) 템플릿][sap-templates-3-tier-user-image] 여러 대의 가상 컴퓨터 및 고유의 OS 이미지를 사용하여 3계층 시스템을 만들려는 경우 이 템플릿을 사용합니다.

위의 템플릿 중 하나를 열면 Azure 포털이 매개 변수 편집 패널을 탐색합니다. 다음 정보를 입력합니다.

* **sapSystemId**: SAP 시스템 ID
* **osType**: 배포하려는 운영 체제 유형, Windows 또는 Linux
* **sapSystemSize**: SAP 시스템의 크기
    * 새 시스템에서 제공하는 SAP의 양입니다. 시스템에 필요한 SAP의 양을 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
* **systemAvailability**: (3계층 템플릿에만 해당) 시스템 가용성 
    * HA 설치에 적합한 구성에 대한 HA를 선택합니다. ASCS용 2개의 데이터베이스 서버 및 2개의 서버가 생성됩니다.
* **storageType**: (2계층 템플릿에만 해당) 사용해야 하는 저장소 유형 
    * 더 큰 시스템의 경우 프리미엄 저장소를 사용하는 것이 좋습니다. 기타 저장소 유형에 대한 자세한 내용은 다음을 참조하세요. 
        * [DBMS 가이드][dbms-guide]의 [Microsoft Azure 저장소][dbms-guide-2.3]
        * [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소][storage-premium-storage-preview-portal]
        * [Microsoft Azure 저장소 소개][storage-introduction]
* **adminUsername** 및 **adminPassword**: 사용자 이름 및 암호
    * 컴퓨터에 로그온하는 데 사용할 수 있게 만들어진 새 사용자입니다.
* **userImageVhdUri**: 개인 OS 이미지 VHD의 URI(예: https://`< accountname'>.blob.core.windows.net/vhds/userimage.vhd)
* **userImageStorageAccount**: 개인 OS 이미지가 저장된 저장소 계정의 이름(예: 위의 URI 예에서 `<accountname`>)
* **newOrExistingSubnet**: 새 가상 네트워크 및 서브넷을 만들지 또는 기존 서브넷을 사용할지를 결정합니다. 온-프레미스 네트워크에 연결되어 있는 가상 네트워크가 이미 있는 경우 기존 항목을 선택합니다.
* **subnetId**: 가상 컴퓨터를 연결해야 하는 서브넷의 ID입니다. 온-프레미스 네트워크에 가상 컴퓨터를 연결할 Express 경로 가상 네트워크의 서브넷 또는 VPN을 선택합니다. ID는 일반적으로 /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`> 형태입니다.

모든 매개 변수를 입력한 후에 구독 및 사용하려는 리소스 그룹을 선택합니다. 기존 리소스 그룹을 선택하거나 드롭다운 메뉴에서 "+ 새로 만들기"를 선택하여 새 리소스 그룹을 만들 수 있습니다. 새 리소스 그룹을 만드는 경우 리소스 그룹 및 가상 컴퓨터가 생성될 지역을 선택할 수도 있습니다.

약관을 검토하여 동의하고 만들기를 클릭합니다.

#### VM 에이전트(Linux에만 해당) 설치
위의 템플릿을 사용하려는 경우 사용자 이미지에 Linux 에이전트가 이미 설치되어 있어야 합니다. 그렇지 않으면 배포에 실패합니다. 이 문서의 [Azure VM 에이전트 다운로드, 설치 및 사용][deployment-guide-4.4] 챕터에 설명된 대로 사용자 이미지에 VM 에이전트를 다운로드하고 설치합니다. 위 템플릿을 사용하지 않는 경우 나중에 VM 에이전트를 설치할 수도 있습니다.

#### 도메인 가입(Windows에만 해당)
Azure에서 배포가 Azure 사이트 간 또는 Express 경로를 통해 온-프레미스 AD/DNS에 연결된 경우([계획 및 구현 가이드][planning-guide]에서 프레미스 간으로도 참조됨), VM이 온-프레미스 도메인에 가입됩니다. 이 단계의 고려 사항은 이 문서의 [온-프레미스 도메인에 VM 가입(Windows에만 해당)][deployment-guide-4.3] 챕터에서 설명합니다.

#### 프록시 설정 구성
온-프레미스 네트워크 구성에 따라, VPN 또는 Express 경로를 통해 온-프레미스 네트워크에 연결되어 있는 경우 가상 컴퓨터에서 프록시를 구성해야 할 수 있습니다. 그러지 않으면 가상 컴퓨터가 인터넷에 액세스하지 못할 수 있으며 따라서 필요한 확장을 다운로드할 수 없거나 모니터링 데이터를 수집할 수 없습니다. 이 문서의 [프록시 구성][deployment-guide-configure-proxy] 챕터를 참조하세요.

#### 모니터링 구성
이 문서의 [SAP용 Azure 고급 모니터링 확장 구성][deployment-guide-4.5] 챕터에서 설명된 대로 SAP용 Azure 모니터링 확장을 구성합니다. 이 문서의 [SAP 리소스][deployment-guide-2.2] 챕터에 나열된 리소스에서 필요한 SAP 커널 및 SAP 호스트 에이전트의 최소 버전에 대한 SAP 모니터링의 필수 조건을 확인합니다.

#### 모니터링 확인
[Azure의 SAP용 종단 간 모니터링 설정 확인 및 문제 해결][deployment-guide-troubleshooting-chapter] 챕터에 설명된 대로 모니터링이 작동 중인지 확인합니다.

### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>시나리오 3: SAP에서 일반화되지 않은 Azure VHD를 사용하여 온-프레미스에서 VM 이동
이 시나리오는 온-프레미스에서 Azure로 현재 형태와 모양으로 단순히 이동하는 SAP 시스템 사례의 주소를 지정합니다. 즉, Windows 또는 Linux 호스트 이름 및 SAP SID 등의 이름 변경은 발생하지 않습니다. 이 사례에서 VHD는 배포하는 동안 이미지로 참조되지 않지만 OS 디스크로 직접 사용됩니다. 배포와 관련하여 이 사례는 VM 에이전트를 배포 중 자동으로 설치할 수 없다는 점에서 이전의 두 사례와는 다릅니다. 따라서 Azure VM 에이전트를 Microsoft에서 다운로드하고 나중에 수동으로 VM 내에서 설치하고 활성화해야 합니다. 작업에 성공하면 계속해서 SAP 호스트 모니터링 Azure 확장 및 해당 구성을 시작할 수 있습니다. Azure VM 에이전트의 함수에 대한 자세한 내용은 이 문서를 확인하세요.

[설명]: <> (MSSedusch TODO 아래 Windows 링크 업데이트)

___

> ![Windows][Logo_Windows] Windows
>
> <http://blogs.msdn.com/b/wats/archive/2014/02/17/bginfo-guest-agent-extension-for-azure-vms.aspx>
>
> ![Linux][Logo_Linux] Linux
>
> <https://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/>

___

여러 단계의 워크플로는 다음과 같습니다.
 
![VM 디스크를 사용하여 SAP 시스템용 VM 배포 순서도][deployment-guide-figure-400]

디스크가 이미 업로드되고 Azure에서 정의되었다고 가정하고([계획 및 구현 가이드][planning-guide] 참조) 다음 단계를 수행합니다.

#### 가상 컴퓨터 만들기
Azure 포털을 통해 개인 OS 디스크를 사용하여 배포를 만들려면 [azure-quickstart-templates GitHub 리포지토리][azure-quickstart-templates-github]에 게시된 SAP 템플릿을 사용합니다. PowerShell 또는 Azure CLI를 사용하여 수동으로 가상 컴퓨터를 만들 수도 있습니다.

* [2계층 구성(단일 가상 컴퓨터) 템플릿][sap-templates-2-tier-os-disk]
    * 한 대의 가상 컴퓨터를 사용하여 2계층 시스템을 만들려는 경우 이 템플릿을 사용합니다.

위의 템플릿을 열면 Azure 포털이 매개 변수 편집 패널을 탐색합니다. 다음 정보를 입력합니다.

* **sapSystemId**: SAP 시스템 ID
* **osType**: 배포하려는 운영 체제 유형, Windows 또는 Linux
* **sapSystemSize**: SAP 시스템의 크기
    * 새 시스템에서 제공하는 SAP의 양입니다. 시스템에 필요한 SAP의 양을 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
* **storageType**: (2계층 템플릿에만 해당) 사용해야 하는 저장소 유형 
    * 더 큰 시스템의 경우 프리미엄 저장소를 사용하는 것이 좋습니다. 기타 저장소 유형에 대한 자세한 내용은 다음을 참조하세요. 
        * [DBMS 가이드][dbms-guide]의 [Microsoft Azure 저장소][dbms-guide-2.3]
        * [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소][storage-premium-storage-preview-portal]
        * [Microsoft Azure 저장소 소개][storage-introduction]
* **osDiskVhdUri**: 개인 OS 디스크의 URI(예: https://`<accountname'>.blob.core.windows.net/vhds/osdisk.vhd)
* **newOrExistingSubnet**: 새 가상 네트워크 및 서브넷을 만들지 또는 기존 서브넷을 사용할지를 결정합니다. 온-프레미스 네트워크에 연결되어 있는 가상 네트워크가 이미 있는 경우 기존 항목을 선택합니다.
* **subnetId**: 가상 컴퓨터를 연결해야 하는 서브넷의 ID입니다. 온-프레미스 네트워크에 가상 컴퓨터를 연결할 Express 경로 가상 네트워크의 서브넷 또는 VPN을 선택합니다. ID는 일반적으로 /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`> 형태입니다.

모든 매개 변수를 입력한 후에 구독 및 사용하려는 리소스 그룹을 선택합니다. 기존 리소스 그룹을 선택하거나 드롭다운 메뉴에서 "+ 새로 만들기"를 선택하여 새 리소스 그룹을 만들 수 있습니다. 새 리소스 그룹을 만드는 경우 리소스 그룹 및 가상 컴퓨터가 생성될 지역을 선택할 수도 있습니다.

약관을 검토하여 동의하고 만들기를 클릭합니다.

#### VM 에이전트 설치
위의 템플릿을 사용하려는 경우 OS 디스크에 Linux 에이전트가 이미 설치되어 있어야 합니다. 그렇지 않으면 배포에 실패합니다. 이 문서의 [Azure VM 에이전트 다운로드, 설치 및 사용][deployment-guide-4.4] 챕터에 설명된 대로 VM에 VM 에이전트를 다운로드하고 설치합니다.

위 템플릿을 사용하지 않는 경우 나중에 VM 에이전트를 설치할 수도 있습니다.

#### 도메인 가입(Windows에만 해당)
Azure에서 배포가 Azure 사이트 간 또는 Express 경로를 통해 온-프레미스 AD/DNS에 연결된 경우([계획 및 구현 가이드][planning-guide]에서 프레미스 간으로도 참조됨), VM이 온-프레미스 도메인에 가입됩니다. 이 단계의 고려 사항은 이 문서의 [온-프레미스 도메인에 VM 가입(Windows에만 해당)][deployment-guide-4.3] 챕터에서 설명합니다.

#### 프록시 설정 구성
온-프레미스 네트워크 구성에 따라, VPN 또는 Express 경로를 통해 온-프레미스 네트워크에 연결되어 있는 경우 가상 컴퓨터에서 프록시를 구성해야 할 수 있습니다. 그러지 않으면 가상 컴퓨터가 인터넷에 액세스하지 못할 수 있으며 따라서 필요한 확장을 다운로드할 수 없거나 모니터링 데이터를 수집할 수 없습니다. 이 문서의 [프록시 구성][deployment-guide-configure-proxy] 챕터를 참조하세요.

#### 모니터링 구성
이 문서의 [SAP용 Azure 고급 모니터링 확장 구성][deployment-guide-4.5] 챕터에서 설명된 대로 SAP용 Azure 고급 모니터링 확장을 구성합니다.

이 문서의 [SAP 리소스][deployment-guide-2.2] 챕터에 나열된 리소스에서 필요한 SAP 커널 및 SAP 호스트 에이전트의 최소 버전에 대한 SAP 모니터링의 필수 조건을 확인합니다.

#### 모니터링 확인
[Azure의 SAP용 종단 간 모니터링 설정 확인 및 문제 해결][deployment-guide-troubleshooting-chapter] 챕터에 설명된 대로 모니터링이 작동 중인지 확인합니다.

### 시나리오 4: SAP용 모니터링 구성 업데이트
모니터링 구성을 업데이트해야 하는 경우가 있습니다.

* 공동 MS/SAP 팀에서는 모니터링 기능을 확장하고 더 많은 카운터를 추가하거나 일부 카운터를 삭제하기로 했습니다. 
* Microsoft는 모니터링 데이터를 제공하는 새 버전의 기본 Azure 인프라를 도입했으며 SAP용 Azure 고급 모니터링 확장이 해당 변경 내용으로 개선되었습니다.
* Azure VM에 탑재된 VHD를 추가하거나 특정 VHD를 제거합니다. 이 경우 저장소 관련 데이터의 컬렉션을 업데이트해야 합니다. 끝점을 추가 또는 삭제하거나 VM에 IP 주소를 할당하여 구성을 변경하는 경우 모니터링 구성에 영향을 주지 않습니다.
* Azure VM의 크기를 변경합니다(예: A5에서 다른 크기의 VM).
* Azure VM에 새 네트워크 인터페이스를 추가합니다.

모니터링 구성을 업데이트하려면 다음을 수행합니다.

* 이 문서의 [SAP용 Azure 고급 모니터링 확장 구성][deployment-guide-4.5] 챕터에 설명된 단계에 따라 모니터링 인프라를 업데이트합니다. 이 챕터에 설명된 스크립트를 다시 실행하면 모니터링 구성이 배포되었는지 검색되며 모니터링 구성에 필요한 변경 사항이 수행됩니다. 

___

> ![Windows][Logo_Windows] Windows
>
> Azure VM 에이전트를 업데이트하는 경우 사용자가 개입할 필요가 없습니다. VM 에이전트가 자체적으로 자동 업데이트하며 VM을 다시 부팅하지 않아도 됩니다.
>
> ![Linux][Logo_Linux] Linux
>
> [이 문서][virtual-machines-linux-update-agent]의 단계에 따라 Azure Linux 에이전트를 업데이트하세요.

___

## 자세한 단일 배포 단계

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell cmdlet 배포
* <https://azure.microsoft.com/downloads/>으로 이동합니다.
* '명령줄 도구' 섹션에 'Windows PowerShell'이라는 섹션이 있습니다. '설치' 링크를 따릅니다.
* Microsoft 다운로드 관리자가 .exe로 끝나는 줄 항목과 함께 팝업됩니다. '실행' 옵션을 선택합니다.
* Microsoft 웹 플랫폼 설치 관리자를 실행할 것인지를 묻는 팝업이 나타납니다. 예를 누릅니다.
* 다음과 같은 화면이 나타납니다.
 
![Azure PowerShell cmdlet의 설치 화면][deployment-guide-figure-500] <a name="figure-5"></a>

* 설치를 누르고 EULA에 동의합니다.

PowerShell cmdlet의 업데이트 여부를 자주 확인합니다. 일반적으로 매월 업데이트됩니다. [이][deployment-guide-figure-5] 그림에 표시된 설치 화면이 나올 때까지 위에서 설명한 대로 설치 단계를 수행하는 것이 이 작업을 수행하는 가장 쉬운 방법입니다. 이 화면에서 cmdlet의 릴리스 날짜와 실제 릴리스 번호가 표시됩니다. SAP Note [1928533] 또는 [2015553]에서 달리 언급하지 않는 한 최신 버전의 Azure PowerShell cmdlet을 사용하는 것이 좋습니다.

데스크톱/랩톱에 현재 설치된 Azure cmdlet의 버전은 다음 PS 명령으로 확인할 수 있습니다.

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

아래 [이][deployment-guide-figure-6] 그림과 같이 결과가 나타납니다.

![Azure PS cmdlet 버전 검사의 결과][deployment-guide-figure-600] <a name="figure-6"></a>

데스크톱/랩톱에 설치된 Azure cmdlet 버전이 최신 버전인 경우 Microsoft 웹 플랫폼 설치 관리자를 시작한 후 첫 번째 화면은 [이][deployment-guide-figure-5] 그림에서 보여지는 것과 약간 다르게 보입니다.

아래 [그림][deployment-guide-figure-7]에서 빨간색 원을 주목하세요.
 
![Azure PS cmdlet의 최신 릴리스가 설치되어 있음을 나타내는 Azure PowerShell cmdlet의 설치 화면][deployment-guide-figure-700] <a name="figure-7"></a>

화면이 [위][deployment-guide-figure-7]와 같이 보이면 최신 Azure cmdlet 버전이 이미 설치되어 있음을 나타내므로 설치를 진행할 필요가 없습니다. 이 경우 이 단계에서 설치를 '종료'할 수 있습니다.

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Azure CLI 배포
* <https://azure.microsoft.com/downloads/>으로 이동합니다.
* '명령줄 도구' 섹션에 'Azure 명령줄 인터페이스'라는 섹션이 있습니다. 운영 체제용 설치 링크를 따릅니다.

Azure CLI 업데이트 여부를 자주 확인합니다. 일반적으로 매월 업데이트됩니다. 위에서 설명한 대로 설치 단계를 수행하는 것이 이 작업을 수행하는 가장 쉬운 방법입니다.

데스크톱/랩톱에 현재 설치된 Azure CLI 버전은 다음 명령을 사용하여 확인할 수 있습니다.

```
azure --version
```

아래 [이][deployment-guide-figure-azure-cli-version] 그림과 같이 결과가 나타납니다.

![Azure CLI 버전 검사의 결과][deployment-guide-figure-760] <a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>온-프레미스 도메인에 VM 가입(Windows에만 해당)
온-프레미스 AD 및 DNS가 Azure로 확장되는 프레미스 간 시나리오에 SAP VM을 배포하는 경우 VM은 온-프레미스 도메인에 가입됩니다. 온-프레미스 도메인에 VM을 가입하는 세부 단계 및 온-프레미스 도메인의 멤버가 되기 위해 필요한 추가 소프트웨어는 고객마다 다릅니다. 일반적으로 온-프레미스 도메인에 VM을 가입하는 것은 맬웨어 방지 소프트웨어와 같은 추가 소프트웨어 또는 백업이나 모니터링 소프트웨어의 다양한 에이전트를 설치하는 것을 의미합니다.

또한 도메인 가입 시 인터넷 프록시 설정이 필수인 경우 게스트 VM의 Windows 로컬 시스템 계정(S-1-5-18) 또한 이러한 설정을 지정해야 합니다. 가장 쉬운 방법은 도메인 내 시스템에 적용하는 도메인 그룹 정책을 사용하여 프록시를 강제 설정하는 것입니다.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Azure VM 에이전트 다운로드, 설치 및 사용 설정
다음 단계는 SAP용 VM이 일반화되지 않은, 즉 Windows용 sysprep을 실행하지 않은 OS 이미지에서 배포되는 경우에 필요합니다. Azure 마켓플레이스에서 배포된 가상 컴퓨터에 대한 에이전트를 설치할 필요는 없습니다. 이러한 이미지는 이미 Azure 에이전트를 포함합니다.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

* Azure VM 에이전트 다운로드:
	* <https://go.microsoft.com/fwlink/?LinkId=394789>에서 Azure VM 에이전트 설치 관리자 패키지를 다운로드 합니다.
	* 랩톱 또는 서버에 VM 에이전트 MSI 패키지를 로컬로 저장합니다.
* Azure VM 에이전트 설치:
	* 터미널 서비스(RDP)를 사용하여 배포된 Azure VM에 연결합니다.
	* VM에서 Windows 탐색기 창을 열고 VM 에이전트의 MSI 파일에 대한 대상 디렉터리를 엽니다.
	* 로컬 랩톱/서버에서 VM의 VM 에이전트 대상 디렉터리로 Azure VM 에이전트 설치 관리자 MSI 파일을 끌어서 놓습니다.
	* VM에서 MSI 파일을 두 번 클릭합니다.
	* 온-프레미스 도메인에 가입된 VM의 경우 최종 인터넷 프록시 설정을 [프록시 구성][deployment-guide-configure-proxy] 챕터에서 설명한 대로 VM의 Windows 로컬 시스템 계정(S-1-5-18)에도 적용해야 합니다. VM 에이전트를 이 컨텍스트에서 실행하고 Azure에 연결할 수 있어야 합니다.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
다음 명령을 사용하여 Linux용 VM 에이전트를 설치하세요.

```
sudo zypper install WALinuxAgent
```

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>프록시 구성
프록시 구성 단계는 Windows 및 Linux에 따라 다릅니다.

#### Windows
이러한 설정은 인터넷에 액세스할 LocalSystem 계정에 대해서도 유효해야 합니다. 그룹 정책으로 프록시 설정을 지정하지 않은 경우 다음 단계에 따라 LocalSystem 계정에 대해 구성할 수 있습니다.

1.	gpedit.msc를 엽니다.
1.	컴퓨터 구성 -> 관리 템플릿 -> Windows 구성 요소 -> Internet Explorer로 이동하여 "사용자 단위보다는 컴퓨터 단위로 프록시 설정 만들기"를 사용하도록 설정합니다.
1.	제어판을 열고 네트워크 및 인터넷 -> 인터넷 옵션으로 이동합니다.
1.	연결 탭을 열고 LAN 설정을 클릭합니다.
1.	"자동으로 설정 검색"을 사용하지 않도록 설정합니다.
1.	"LAN에 프록시 서버 사용"을 사용하도록 설정하고 프록시 호스트 및 포트를 입력합니다.

#### Linux
/etc/waagent.conf에 있는 Microsoft Azure 게스트 에이전트의 구성 파일에서 올바른 프록시를 구성합니다. 다음 매개 변수를 설정해야 합니다.

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

다음으로 해당 구성을 변경한 후 에이전트를 다시 시작합니다.

```
sudo service waagent restart
```

필요한 VM 확장에 대해서도 /etc/waagent.conf의 프록시 설정이 적용됩니다. Azure 리포지토리를 사용하려는 경우 이러한 리포지토리에 대한 트래픽이 온-프레미스 인트라넷을 통해 전달되지 않는지 확인합니다. 강제 터널링을 사용하도록 사용자 정의 경로를 만든 경우 사이트 간 연결을 통해서가 아니라 인터넷에 직접 104.45.17.148로 트래픽을 라우트하는 경로를 추가해야 합니다. /etc/regionserverclnt.cfg에 나열된 IP 주소에 대한 경로도 추가해야 합니다. 아래 스크린샷에 예제가 나와 있습니다. 사용자 정의 경로에 대한 자세한 내용은 [이 문서][virtual-networks-udr-overview]를 참조하세요.

![강제 터널링][deployment-guide-figure-50]

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>SAP용 Azure 고급 모니터링 확장 구성
[Microsoft Azure에서의 SAP용 VM 배포 시나리오][deployment-guide-3] 챕터에 설명된 대로 VM이 준비되면 Azure VM 에이전트가 컴퓨터에 설치됩니다. 다음으로 중요한 단계는 Microsoft Azure의 글로벌 데이터 센터에 있는 Azure 확장 리포지토리에서 사용할 수 있는 SAP용 Azure 고급 모니터링 확장을 배포하는 것입니다. 자세한 내용은 [계획 및 구현 가이드][planning-guide-9.1]를 확인하세요.

Azure PowerShell 또는 Azure CLI를 사용하여 SAP용 Azure 고급 모니터링 확장을 사용할 수 있습니다. Windows 컴퓨터를 사용하여 Windows 또는 Linux VM에 확장을 설치하려는 경우 [Azure PowerShell][deployment-guide-4.5.1] 챕터를 참조하세요. Linux 데스크톱을 사용하여 Linux VM에 확장을 설치하려면 [Azure CLI][deployment-guide-4.5.2] 챕터를 참조하세요.

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Linux 및 Windows VM용 Azure PowerShell
SAP용 Azure 고급 모니터링 확장 설치 작업을 위해 다음 단계를 수행합니다.

* 최신 버전의 Microsoft Azure PowerShell cmdlet을 설치했는지 확인합니다. 이 문서의 [Azure PowerShell cmdlet 배포][deployment-guide-4.1] 챕터를 참조하세요.  
* 다음 PowerShell cmdlet을 실행합니다. 사용 가능한 환경 목록에서 Get-AzureRmEnvironment commandlet을 실행합니다. 공용 Azure를 사용하려는 경우 환경은 AzureCloud입니다. 중국의 Azure인 경우 AzureChinaCloud를 선택합니다.

```powershell
	$env = Get-AzureRmEnvironment -Name <name of the environment>
	Login-AzureRmAccount -Environment $env
	Set-AzureRmContext -SubscriptionName <subscription name>
    
    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

Azure 가상 컴퓨터 및 계정 데이터를 제공한 후 스크립트가 필수 확장을 배포하고 필요한 기능을 사용하도록 설정합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다. Set-AzureRmVMAEMExtension에 대한 자세한 내용은 [이 MSDN 문서][msdn-set-azurermvmaemextension]를 참조하세요.
  
![SAP 관련 Azure cmdlet Set-AzureRmVMAEMExtension의 성공적인 실행 결과 화면][deployment-guide-figure-900]

Set-AzureRmVMAEMExtension 실행을 성공적으로 마치면 SAP용 호스트 모니터링 기능을 구성하는 데 필요한 모든 단계가 수행됩니다.

스크립트 출력이 다음과 같이 제공됩니다.

* 기본 VHD(OS 포함)와 VM에 탑재된 모든 추가 VHD에 대한 모니터링 구성이 완료되었다는 확인이 표시됩니다.
* 다음 두 개의 메시지는 특정 저장소 계정에 대한 저장소 메트릭의 구성을 확인합니다. 
* 출력 한 줄에는 모니터링 구성의 실제 업데이트 상태가 표시됩니다.
* 또 다른 줄은 구성이 배포되거나 업데이트되었음을 확인해 줍니다.
* 출력의 마지막 줄은 모니터링 구성을 테스트할 수 있다는 정보를 제공합니다.
* Azure 고급 모니터링의 모든 단계가 성공적으로 실행되고 Azure 인프라가 필요한 데이터를 제공하는지 확인하려면 이 문서의 [SAP용 Azure 고급 모니터링에 대한 준비 검사][deployment-guide-5.1] 챕터에 설명된 대로 SAP용 Azure 고급 모니터링 확장에 대한 준비 검사를 진행합니다. 
* 이 작업을 계속하려면 Azure 진단이 관련 데이터를 수집할 때까지 15-30분 기다립니다.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Linux VM용 Azure CLI
[이 문서][install-extension-cli]의 단계에 따라 Linux 랩톱/데스크톱에서 Linux VM에 SAP용 Azure 고급 모니터링 확장을 설치합니다.

 [설명]: <> (MSSedusch TODO 연결이 여전히 유효한 경우 검사)

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Azure에서 SAP용 종단 간 모니터링 설정 확인 및 문제 해결
Azure VM을 배포하고 관련 Azure 모니터링 인프라를 설정한 후 Azure 고급 모니터링의 모든 구성 요소가 적절한 방식으로 작동하는지 확인합니다.

따라서 [SAP용 Azure 고급 모니터링에 대한 준비 검사][deployment-guide-5.1] 챕터에 설명된 대로 SAP용 Azure 고급 모니터링 확장에 대한 준비 검사를 실행합니다. 이 검사의 결과가 긍정적이고 관련된 모든 성능 카운터를 가져올 경우 Azure 모니터링이 성공적으로 설치되었습니다. 이 경우, 이 문서의 [SAP 리소스][deployment-guide-2.2] 챕터에 나열된 SAP Note에 설명된 대로 계속해서 SAP 호스트 에이전트가 설치됩니다. 준비 검사의 결과가 누락된 카운터를 나타내는 경우 [Azure 모니터링 인프라 구성에 대한 상태 검사][deployment-guide-5.2] 챕터에 설명된 대로 Azure 모니터링 인프라에 대한 상태 검사 실행이 진행됩니다. Azure 모니터링 구성에 문제가 있는 경우 문제 해결에 대한 자세한 도움말을 보려면 [SAP용 Azure 모니터링 인프라의 추가 문제 해결][deployment-guide-5.3] 챕터를 확인하세요.

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>SAP용 Azure 고급 모니터링에 대한 준비 검사
이 검사를 수행하여 SAP 응용 프로그램 내에 표시되는 메트릭이 기본 Azure 모니터링 인프라에서 완전히 제공되는지 확인합니다.

#### Windows VM에서 준비 검사 실행
준비 검사를 실행하려면 Azure 가상 컴퓨터에 로그온하여(관리자 계정 불필요) 다음 단계를 실행합니다.

* Windows 명령 프롬프트를 열고 SAP용 Azure 모니터링 확장의 설치 폴더를 C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\`<version`>\\drop으로 변경합니다.

위의 모니터링 확장에 대한 경로에 제공된 버전 부분은 달라질 수 있습니다. 설치 폴더에 모니터링 확장 버전의 여러 폴더가 표시되면 Windows 서비스 'AzureEnhancedMonitoring'의 구성을 확인하고 '실행 파일 경로'로 표시된 폴더로 전환합니다.
 
![SAP용 Azure 고급 모니터링 확장을 실행하는 서비스 속성][deployment-guide-figure-1000]

* 매개 변수 없이 명령 창에서 azperflib.exe를 실행합니다.

> [AZURE.NOTE] azperflib.exe는 루프에서 실행되고 60초마다 수집한 카운터를 업데이트합니다. 루프를 마치려면 명령 창을 닫습니다.

Azure 고급 모니터링 확장을 설치되어 있지 않거나 'AzureEnhancedMonitoring' 서비스가 실행되고 있지 않으면 확장이 올바르게 구성되지 않은 것입니다. 이 경우 확장을 다시 배포하는 방법에 대한 자세한 지침은 [SAP용 Azure 모니터링 인프라의 추가 문제 해결][deployment-guide-5.3] 챕터를 참조하세요.

##### azperflib.exe의 출력 확인
azperflib.exe의 출력은 SAP용 Azure 성능 카운터가 모두 채워진 상태로 표시됩니다. 수집된 카운터 목록의 맨 아래에서 Azure 모니터링의 상태를 나타내는 요약 및 상태 표시기를 찾을 수 있습니다.
 
![문제가 없음을 나타내는 azperflib.exe를 실행하여 표시된 상태 검사 출력][deployment-guide-figure-1100] <a name="figure-11"></a>

[위][deployment-guide-figure-11] 그림에서 보듯이 empty로 보고되는 'Counters total' 양의 출력과 'Health check'에 대해 반환되는 결과를 확인합니다.

결과 값을 다음과 같이 해석할 수 있습니다.

| Azperflib.exe 결과 값 | Azure 모니터링 준비 상태 |
| ------------------------------|----------------------------------- |
| **Counters total: empty** | 다음 2개의 Azure 저장소 카운터가 비어 있을 수 있습니다. <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>다른 모든 카운터는 값이 포함되어야 합니다. |
| **상태 검사** | 반환 상태가 OK를 표시하는 경우에만 OK입니다. |

azperflib.exe의 두 반환 값이 모든 카운터가 채워져 올바르게 반환되었음을 표시하지 않는 경우 아래 [Azure 모니터링 인프라 구성에 대한 상태 검사][deployment-guide-5.2] 챕터에 설명된 대로 Azure 모니터링 인프라 구성에 대한 상태 검사 지침을 따릅니다.

#### Linux VM에서 준비 검사 실행
준비 검사를 실행하기 위해 SSH를 사용하여 Azure 가상 컴퓨터에 연결하고 다음 단계를 실행합니다.

* Azure 고급 모니터링 확장의 출력을 확인합니다.
    * more /var/lib/AzureEnhancedMonitor/PerfCounters
        * 성능 카운터의 목록을 제공합니다. 파일은 비어 있으면 안 됩니다.
    * cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error
        * 오류가 없는 한 줄을 반환합니다. 예를 들어 3;config;Error;;0;0;**none**;0;1456416792;tst-servercs;
    * more /var/lib/AzureEnhancedMonitor/LatestErrorRecord
        * 비어 있거나 또는 존재하지 않아야 합니다.
* 위의 첫 번째 검사가 성공하지 못한 경우 다음 추가 테스트를 수행합니다.
    * waagent가 설치 및 시작되었는지 확인합니다.
        * sudo ls -al /var/lib/waagent/
            * waagent 디렉터리의 내용을 나열합니다.
        * ps -ax | grep waagent
            * 'python /usr/sbin/waagent -daemon'과 비슷한 하나의 항목을 표시합니다.
    * Linux 진단 확장이 설치 및 시작되었는지 확인합니다.
        * sudo ls -al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-*
            * Linux 진단 확장 디렉터리의 내용을 나열합니다.
        * ps -ax | grep diagnostic
            * 'python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py -daemon'과 비슷한 하나의 항목을 표시합니다.
    * Azure 고급 모니터링 확장이 설치 및 시작되었는지 확인합니다.
        * sudo ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/
    * Azure 고급 모니터링 확장 디렉터리의 내용을 나열합니다.
        * ps -ax | grep AzureEnhanced
            * 'python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon'과 비슷한 하나의 항목을 표시합니다.
* SAP Note 1031096에 설명된 대로 SAP 호스트 에이전트를 설치하고 saposcol의 출력을 확인합니다.
    * /usr/sap/hostctrl/exe/saposcol -d 실행
    * dump ccm 실행
    * 메트릭 "Virtualization\_Configuration\\Enhanced Monitoring Access"가 true인지 확인
* SAP NetWeaver ABAP 응용 프로그램 서버가 이미 설치된 경우 트랜잭션 ST06을 열고 고급 모니터링이 사용하도록 설정되어 있는지 확인합니다.

위 검사가 실패하는 경우 확장을 다시 배포하는 방법에 대한 자세한 지침은 [SAP용 Azure 모니터링 인프라의 추가 문제 해결][deployment-guide-5.3] 챕터를 참조하세요.

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Azure 모니터링 인프라 구성에 대한 상태 검사
모니터링 데이터의 일부가 [SAP용 Azure 고급 모니터링에 대한 준비 검사][deployment-guide-5.1] 챕터에서 설명한 테스트에 표시된 대로 올바르게 전달되지 않는 경우 Test-AzureRmVMAEMExtension cmdlet을 실행하여 Azure 모니터링 인프라 및 SAP용 모니터링 확장의 현재 구성이 올바른지 테스트합니다.

모니터링 구성을 테스트하려면 다음 시퀀스를 실행하세요.

* 이 문서의 [Azure PowerShell cmdlet 배포][deployment-guide-4.1] 챕터에 설명된 대로 최신 버전의 Microsoft Azure PowerShell cmdlet을 설치했는지 확인합니다.
* 다음 PowerShell cmdlet을 실행합니다. 사용 가능한 환경 목록에서 Get-AzureRmEnvironment commandlet을 실행합니다. 공용 Azure를 사용하려는 경우 환경은 AzureCloud입니다. 중국의 Azure인 경우 AzureChinaCloud를 선택합니다.

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* Azure 가상 컴퓨터 및 계정 데이터를 제공한 후 스크립트는 선택한 가상 컴퓨터의 구성을 테스트합니다.

 
![SAP 관련 Azure cmdlet Test-VMConfigForSAP\_GUI의 입력 화면][deployment-guide-figure-1200]

계정 및 Azure 가상 컴퓨터에 대한 정보를 입력한 후 스크립트는 선택한 가상 컴퓨터의 구성을 테스트합니다.
 
![SAP용 Azure 모니터링 인프라의 성공적인 테스트 출력][deployment-guide-figure-1300]

모든 검사가 OK로 표시되는지 확인합니다. 일부 검사가 OK가 아닌 경우 이 문서의 [SAP용 Azure 고급 모니터링 확장 구성][deployment-guide-4.5] 챕터에 설명된 대로 update cmdlet을 실행합니다. 15분 후 [SAP용 Azure 고급 모니터링에 대한 준비 검사][deployment-guide-5.1] 및 [Azure 모니터링 인프라 구성에 대한 상태 검사][deployment-guide-5.2] 챕터에 설명된 검사를 다시 수행합니다. 검사에서 여전히 일부 또는 모든 카운터에 문제가 있다고 표시하는 경우 [SAP용 Azure 모니터링 인프라의 추가 문제 해결][deployment-guide-5.3] 챕터로 진행하세요.

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>SAP용 Azure 모니터링 인프라의 추가 문제 해결

#### ![Windows][Logo_Windows] Azure 성능 카운터가 전혀 표시되지 않습니다.
Azure에서 성능 메트릭 컬렉션이 Windows 서비스 'Azureenhancedmonitoring'에 의해 수행됩니다. 서비스가 올바르게 설치되지 않은 경우 또는 VM에서 실행되지 않는 경우 성능 메트릭을 전혀 수집할 수 없습니다.

##### Azure 고급 모니터링 확장의 설치 디렉터리가 비어 있습니다. 

###### 문제
설치 디렉터리 C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\`<version`>\\drop이 비어 있습니다.

###### 해결 방법
확장이 설치되지 않았습니다. 이전에 설명한 대로 프록시 문제인지 확인하세요. 컴퓨터를 다시 부팅 및/또는 구성 스크립트 Set-AzureRmVMAEMExtension을 다시 실행해야 할 수 있습니다.

##### Azure 고급 모니터링 서비스가 없습니다. 

###### 문제
Windows 서비스 'AzureEnhancedMonitoring'이 존재하지 않습니다. Azperflib.exe: azperlib.exe 출력 시 [아래 그림][deployment-guide-figure-14]과 같이 오류가 발생합니다.
 
![SAP용 Azure 고급 모니터링 확장의 서비스가 실행되고 있지 않음을 나타내는 Azperflib.exe의 실행][deployment-guide-figure-1400] <a name="figure-14"></a>

###### 해결 방법
[위의 그림][deployment-guide-figure-14]과 같이 서비스가 없는 경우 SAP용 Azure 모니터링 확장이 제대로 설치되지 않은 것입니다. [Microsoft Azure에서의 SAP용 VM 배포 시나리오][deployment-guide-3] 챕터에서 배포 시나리오에 대해 설명된 단계에 따라 확장을 다시 배포합니다.

확장을 배포하고 1시간 후 Azure VM 내에서 Azure 성능 카운터가 제공되는지 여부를 다시 확인합니다.

##### Azure 고급 모니터링에 대한 서비스가 있지만 시작하지 못합니다. 

###### 문제
Windows 서비스 'AzureEnhancedMonitoring'이 있고 활성화되었지만 시작하지 못합니다. 자세한 내용은 응용 프로그램 이벤트 로그를 확인합니다.

###### 해결 방법
잘못된 구성입니다. [SAP용 Azure 고급 모니터링 확장 구성][deployment-guide-4.5] 챕터에 설명된 대로 VM에 대한 모니터링 확장을 다시 사용하도록 설정합니다.

#### ![Windows][Logo_Windows] 일부 Azure 성능 카운터가 누락됩니다.
Azure에서 성능 메트릭 컬렉션이 여러 원본에서 데이터를 가져오는 Windows 서비스 'AzureEnhancedMonitoring'에 의해 수행됩니다. 일부 구성 데이터가 로컬로 수집되고 Azure 진단에서 성능 메트릭을 읽고 저장소 카운터가 저장소 구독 수준에 대한 로그온에서 사용됩니다.

SAP Note [1999351]을 사용한 문제 해결이 도움이 되지 않는 경우 구성 스크립트 Set-AzureRmVMAEMExtension을 다시 실행합니다. 사용하도록 설정한 후 바로 저장소 분석 또는 진단 카운터가 생성되지 않을 수 있으므로 1시간 동안 기다려야 할 수 있습니다. 여전히 문제가 발생할 경우 구성 요소 BC-OP-NT-AZR의 SAP 고객 지원 메시지를 엽니다.

#### ![Linux][Logo_Linux] Azure 성능 카운터가 전혀 표시되지 않습니다.

Azure에서 성능 메트릭 컬렉션이 데몬에 의해 수행됩니다. 데몬이 실행되지 않는 경우 성능 메트릭은 전혀 수집할 수 없습니다.

##### Azure 고급 모니터링 확장의 설치 디렉터리가 비어 있습니다. 

###### 문제
디렉터리 /var/lib/waagent/에 Azure 고급 모니터링 확장에 대한 하위 디렉터리가 포함되지 않습니다.

###### 해결 방법
확장이 설치되지 않았습니다. 이전에 설명한 대로 프록시 문제인지 확인하세요. 컴퓨터를 다시 부팅 및/또는 구성 스크립트 Set-AzureRmVMAEMExtension을 다시 실행해야 할 수 있습니다.

#### ![Linux][Logo_Linux] 일부 Azure 성능 카운터가 누락됩니다.

Azure에서 성능 메트릭 컬렉션이 여러 원본에서 데이터를 가져오는 데몬에 의해 수행됩니다. 일부 구성 데이터가 로컬로 수집되고 Azure 진단에서 성능 메트릭을 읽고 저장소 카운터가 저장소 구독 수준에 대한 로그온에서 사용됩니다.

알려진 문제의 전체 최신 목록은 SAP용 고급 Azure 모니터링에 대한 추가 문제 해결 정보를 포함하는 SAP Note [1999351]을 참조하세요.

SAP Note [1999351]을 사용한 문제 해결이 도움이 되지 않는 경우 [SAP용 Azure 고급 모니터링 확장 구성][deployment-guide-4.5] 챕터에 설명된 대로 구성 스크립트 Set-AzureRmVMAEMExtension을 다시 실행하세요. 사용하도록 설정한 후 바로 저장소 분석 또는 진단 카운터가 생성되지 않을 수 있으므로 1시간 동안 기다려야 할 수 있습니다. 여전히 문제가 발생할 경우 Windows용 BC-OP-NT-AZR 또는 Linux 가상 컴퓨터용 BC-OP-LNX-AZR 구성 요소의 SAP 고객 지원 메시지를 엽니다.

<!---HONumber=AcomDC_0608_2016-->