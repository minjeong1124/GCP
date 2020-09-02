## [Maven] mvnw, mvnw.cmd란?


원래 Maven을 사용하기 위해서는 Maven 파일을 설치하고, 환경 변수 설정을 해줘야 사용할 수 있지만, 
Spring Stater Project - Maven으로 Project를 생성하면 
Maven을 별도로 설치하지 않아도바로 사용이 가능 

그 이유는 **mvnw, mvnw.cmd** 라는 파일들 덕분

■ mvnw, mvnw.cmd
- mvnw : Maven을 설치하지 않아도 Maven을 실행시킬 수 있도록 해주는 유닉스 셸 스크립트
- mvnw.cmd : 위 스크립트의 배치 버전
- mvnw = maven wrapper의 약자

■ Maven Wrapper란?
- Maven이 설치되어 있지 않은 환경에서도 Maven을 사용할 수 있도록 해주는 도구
- Maven의 버전 정보를 명시만 해주면 알아서 해당 버전의 Maven을 사용할 수 있게 해줌
- 만약 버전 정보를 명시해주지 않으면, 최신 버전으로 설정해줍니다. 

