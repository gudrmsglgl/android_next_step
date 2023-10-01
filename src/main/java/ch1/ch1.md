


## System Apps

- 선탑재 기본 앱(홈, 카메라, 전화, 브라우저 등), 다운로드해서 설치하는 일반 앱 > 애플리케이션 스택
- API Framework 위에서 동작
- 단말 제조사들은 기본 앱을 커스터마이징, 제조사 단말에 특화된 앱도 추가로 제공
- 우리가 만드는 앱과 동일한 레벨
- 차이점
    - 선탑재된 기본 앱
        - 시스템 권한을 사용할 수 있음
        - 프로세스 우선순위를 높일 수 있음 > 단말에 메모리가 부족한 상황에서 시스템 강제로 종료 시키는 프로세스를 정하는 기준
        - 예를 들어 메모리가 부족해도 전화 앱이 중지되면 안 됨 > 전화 앱은 우선순위가 높게 설정

## Java API Framework

- 안드로이드 OS 위에서 애플리케이션의 기반이 되는 기본 구조.

  > 예시)
  >
  > - 액티비티를 시작할 떄 생성자로 ‘직접’ 액티비티 인스턴스 생성 및 생명주기 메소드 호출 X
  > - 환경 설정에서 언어가 변경될 때 > 해당 언어의 리소스를 ‘직접’ 찾아서 가져오지 않음
- ‘직접’하지 않아도 되는 것은 애플리케이션 프레임워크 알아서 하기 때문
- 규칙에 따라서 만들면 나머지는 프레임워크의 몫
- Manager
    - Activity Manager > 액티비티 생성 및 생명주기 메서드를 호출 역할
    - Resource Manager > 리소스를 찾아주는 역할

## 네이티브 C/C++ 코드 사용
- 하드웨어 제어나 빠른 속도가 필요한 것들은 내부적 JNI 연결해서 > Native C/C++ 사용
- Telephone, Location Manager 등은 하드웨어를 제어하기 위해서, Resource Manager 등은 리소스 테이블을 유지하고 접근할 때 빠른속도를 위해서 사용

## 씬 클라이언트와 서버

### MediaServer / SystemServer
![service](https://github.com/gudrmsglgl/android_next_step/assets/16537977/4966f92a-76de-456e-95ba-4be5ea9a70d6)

### MediaServer
![4](https://github.com/gudrmsglgl/android_next_step/assets/16537977/0563dd6d-e8cd-402f-acac-18fa67169fdd)

### SystemServer
![system](https://github.com/gudrmsglgl/android_next_step/assets/16537977/5e6bb2ea-3a2f-4fc8-a125-84233fed6934)

### Context Manager
- 서비스 관련 요청을 수행하기 위해서는 IPC 데이터를 수신 대기하는 상태에 들어 있어야 함
- 다른 서비스 서버 / 서비스 클라이언트 이전에 미리 실행되어야 함
- init.rc 내용을 보면 서비스 서버(MediaServer, SystemServer) 실행되기 이전에 실행(serviceManager)됨
  ```bash
  service servicemanager /system/bin/servicemanager
     user system
     critical
     onrestart restart zygote
     onrestart restart media
  ```

- 서버 기능은 별도 프로세스 > system_server에서 실행
  > 여러 앱을 통햅해서 관리하는 '통합 문의 채널'
  > 
  > startActivity() 를 실행하면 먼저 system_server에서  액티비티 찾음
  > 
  > - 로컬 앱 프로세스 > system_server에서 액티비티 찾음
  > 
  > - 갤러리, 카메라 화면 다른 앱의 액티비티를 띄우는 경우 > 해당 액티비티를 가진 앱 프로세스 없다면 > system_server에서 프로세스 띄움
  > 
  > - system_server는 액티비티 스택에도 택티비티 내용 반영 > 마지막 액티비티를 가진 앱 프로세스에 액티비티 띄우라는 메세지 보냄
  >  
  > 📌 모든 안드로이드 컴포넌트 > system_server 거쳐 관리 > 앱 프로세스에 다시 메세지 보내는 방식으로 동작

- 앱 프로세스는 thin client
  > 앱 프로세스
  > - 컴포넌트 탐색, 액티비티 스택 관리, 서비스 목록 유지, ANR 처리등을 직접하지 않음 > system_server 모두 위임
  > - 컴포넌트 실행 등 최소한의 역할만을 한다.
  > 
