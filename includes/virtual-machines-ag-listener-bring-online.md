1. 다시 장애 조치(Failover) 클러스터 관리자로 이동합니다.  **역할** 을 확장한 다음 가용성 그룹을 강조 표시합니다.  **리소스** 탭에서 수신기 이름을 마우스 오른쪽 단추로 클릭하고 속성을 클릭합니다.
2. **종속성** 탭을 클릭합니다. 여러 리소스가 나열되어 있으면 IP 주소에 AND가 아닌 OR 종속성이 있는지 확인합니다.  **확인**을 클릭합니다.
3. 수신기 이름을 마우스 오른쪽 단추로 클릭하고 **온라인 상태로 전환**을 클릭합니다.
4. 수신기가 온라인 상태로 전환되면 **리소스** 탭에서 가용성 그룹을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
   
    ![가용성 그룹 리소스 구성](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)
5. 수신기 이름 리소스(IP 주소 리소스 이름이 아님)에 대한 종속성을 만듭니다. **확인**을 클릭합니다.
   
    ![수신기 이름에 대한 종속성 추가](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)
6. **SQL Server Management Studio** 를 시작하고 주 복제본에 연결합니다.
7. **AlwaysOn 고가용성** | **가용성 그룹** | **<AvailabilityGroupName>** | **가용성 그룹 수신기**로 이동합니다. 
8. 이제 장애 조치(Failover) 클러스터 관리자에서 만든 수신기 이름이 표시됩니다. 수신기 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
9. **포트** 상자에서 이전에 사용한 $EndpointPort(이 자습서에서는 1433이 기본값임)를 사용하여 가용성 그룹 수신기에 대한 포트 번호를 지정한 다음 **확인**을 클릭합니다.



<!--HONumber=Nov16_HO3-->


