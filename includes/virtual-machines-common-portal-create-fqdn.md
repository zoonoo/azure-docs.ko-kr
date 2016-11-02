## 빠른 단계 

이 문서에서는 포털에서 구독에 로그인했으며 Resource Manager 배포 모델을 통해 사용 가능한 이미지로 가상 컴퓨터를 만들었다고 가정합니다. 가상 컴퓨터가 실행되기 시작되면 다음 단계를 수행합니다.

1.  포털에서 가상 컴퓨터 설정을 확인하고 공용 IP 주소를 클릭합니다.

    ![locate ip resource](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2.  공용 IP에 대한 DNS 이름은 비어 있습니다. 공용 IP 블레이드에 대한 **구성**을 클릭합니다.

    ![IP 설정](./media/virtual-machines-common-portal-create-fqdn/settingsIP.PNG)

3.  원하는 DNS 이름 레이블을 입력하고 이 구성을 **저장**합니다.

    ![enter dns name label](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)

    이제 공용 IP 리소스의 블레이드에 이 새 DNS 레이블이 표시됩니다.

4.  공용 IP 블레이드를 닫고 포털에서 가상 컴퓨터 블레이드로 돌아갑니다. DNS 이름/FQDN이 공용 IP 리소스에 대한 IP 주소 옆에 나타나는지 확인합니다.

    ![FQDN is created](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)

<!---HONumber=AcomDC_0831_2016-->