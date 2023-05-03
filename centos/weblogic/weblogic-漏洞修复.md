```shell
\Middleware\Oracle_Home\oracle_common\modules\com.oracle.webservices.wls.bea-wls9-async-response_12.1.3.war
%DOMAIN_HOME%\servers\AdminServer\tmp\.internal\com.oracle.webservices.wls.bea-wls9-async-response_12.1.3.war
%DOMAIN_HOME%\servers\AdminServer\tmp\_WL_internal\com.oracle.webservices.wls.bea-wls9-async-response_12.1.3




/app/bea/bea121/oracle_common/modules


DOMAIN_HOME="/app/bea/bea121/user_projects/domains/base_domain"

================================================

kill  -9 `  ps -ef|grep weblogic.Server |grep -v grep |awk '/weblogic/ {print $2 " "$3}' `

 


#    mv /app/bea/bea121/oracle_common/modules/com.oracle.webservices.wls.bea-wls9-async-response_12.1.3.war   /app/bea.bak


cd /app/bea/bea121/user_projects/domains/base_domain/servers 
mv  AdminServer/tmp/ AdminServer/tmp.bak
mv TRANSPORT/tmp/ TRANSPORT/tmp.bak
mv WGZS_APP2_JDK17/tmp/ WGZS_APP2_JDK17/tmp.bak
mv WGZS_APP_JDK17/tmp/ WGZS_APP_JDK17/tmp.bak
mv WGZS_WS_JDK17/tmp/ WGZS_WS_JDK17/tmp.bak


/app/bea/bea121/user_projects/domains/base_domain/startWebLogic.sh



vim  /app/bea/bea121/user_projects/domains/base_domain/bin/startWebLogic.sh

JAVA_OPTIONS="${JAVA_OPTIONS} -Dweblogic.wsee.skip.async.response=true -Dweblogic.wsee.wstx.wsat.deployed=false"



10.50.169.41	7001	7090	7020	7010	7060	
10.50.169.42	7001	7020	7010	7050	9010	
10.50.169.43	7001	7090	7020	7010	7060	
10.50.169.44	7001	7020	7010	9010	7060	
10.50.169.45	7001	7090	7020	7010	7060	
10.50.169.46	7001	7020	7010	7050	9010	
10.50.169.47	7001	7090	7020	7010	7060	
10.50.169.48	7001	7020	7010	7050	9010	7060


ps -ef|grep weblogic.Server |grep -v grep |wc -l 





```

