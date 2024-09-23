# 리눅스 환경에서 Average Load 실습

**학습 목적 : 리눅스 시스템에서 Average Load에 대한 이해!**

<h2 style="font-size: 25px;"> TEAM 👨‍👨‍👧 <br>
</h2>

|<img src="https://avatars.githubusercontent.com/u/81280628?v=4" width="100" height="100"/>|<img src="https://avatars.githubusercontent.com/u/86951396?v=4" width="100" height="100"/>
|:-:|:-:|
|[@손대현](https://github.com/DaeHyeonSon)|[@이아영](https://github.com/ayleeee)|
---
<br>



### 개요 🚩

리눅스 환경에서 CPU 및 I/O 부하 실험을 통해 Average Load와 성능 모니터링에 대해 이해하며, 이를 통해 시스템 부하를 관리하는 방법을 학습하고자 한다. 

**추가적으로 CPU에 부하를 줄 수 있는 sh를 제작하여 Average Load 증가율을 살피는 실험을 진행하여 본다.**

<br>

### **avg Load 개념🎲**

`uptime` 명령어를 통해 시스템 평균 로드 확인 가능

<div align="center">
  <img src="https://github.com/user-attachments/assets/aa81540f-4df8-4f93-aaba-e9712a4c83b5" width="70%" />
</div>

`만약` 평균 로드가 0.22라고 하여 CPU 사용률이 22% 인가라고 했을 때 잘못된 이해라고 말할 수 있다. `정확한 정의`는 CPU 사용률이 아닌 `활성 프로세스`(Active Process)의 수를 나타낸다. 

※ 활성 프로세스 - **runnable** or **uninterruptible** 프로세스

<br>

### **시스템에 적합한 로드값이란? ⛏**

이상적인 평균 로드의 경우 시스템의 CPU 수와 동일한 값을 가진다.
<div align="center">
  <img src="https://github.com/user-attachments/assets/65c4100b-aaad-42ff-a90e-5f8e0c7fedf5" width="70%" />
  <img src="https://github.com/user-attachments/assets/36e61025-0088-4204-a9b1-69610bc96726" width="70%" />
</div>

※ 평균 로드가 CPU 수보다 크면 시스템이 과부하 상태에 있을 가능성이 크다.

<br><br>

**실제 사례** 

- **CPU가 1개인 시스템**에서 평균 로드가 2라면 프로세스들이 CPU를 기다리고 있다는 의미이다.
- **CPU가 4개인 시스템**에서 평균 로드가 2라면 50%의 CPU 용량이 여유 있는 상태이다.

<br>

### avg Load 작동 방식

- **평균 로드 값을 해석하는 방법 :** 1분, 5분, 15분 동안의 평균 로드를 함께 분석하여 시스템 상태의 추세를 확인

로드값 	해석
비슷한 값	시스템 상태가 안정적임을 의미함
1분 값이 낮은 경우	최근에 시스템 부하가 감소했음을 나타냄
1분 값이 높은 경우	최근 시스템 부하가 증가했음을 나타내며, 계속 증가할 가능성도 있으므로 주의

**주의사항 ❗**

평균 로드와 CPU 사용률에 대한 혼동이 자주 일어난다. 

`평균 로드`의 경우 CPU를 사용하는 프로세스 뿐만 아니라 **I/O 작업을 대기 중인 프로세스**도 포함한다. 
반면 `CPU 사용률`은 CPU가 **실제로 사용된 비율**만을 나타낸다.

따라서 `CPU 사용률`이 낮더라도 **I/O 대기 프로세스가 많으면** `평균 로드`가 **높을** 수 있습니다.


### **avg Load 분석 방법 💬**

툴을 사용하여 평균 로드를 분석 가능하다. 이러한 툴을 이용하여 **어떤 프로세스가 부하**를 일으키는지 추정할 수 있다.

`mpstat` : 각 CPU의 성능을 실시간으로 분석하는 툴

<div align="center">
  <img src="https://github.com/user-attachments/assets/4e3991b9-925e-4d71-bdb3-9ceb8c7c943b" width="70%" />
</div>

`pidstat` : 개별 프로세스의 CPU, 메모리, I/O 사용률을 모니터링하는 툴

<div align="center">
  <img src="https://github.com/user-attachments/assets/63fa1145-95f2-46f2-b5c4-b93b98c6e479" width="70%" />
</div>

### **시나리오 분석 📖**


- **CPU-intensive process**

**`strees --cpu 1`** 명령어를 통해 CPU 사용률을 100%로 올린 결과 **평균 로드**가 1로 증가하며, mpstat으로 확인하였을 때 CPU가 온전히 사용중인 것 확인

**`uptime`을 통한 평균 로드 확인 :**

<div align="center">
  <img src="https://github.com/user-attachments/assets/46aa15c8-3a0b-4abd-9366-dcf0647391c2" width="70%" />
</div>

**`pidstat`을 통한 stress확인 :**

<div align="center">
  <img src="https://github.com/user-attachments/assets/fefd57c1-30c2-427f-b96d-9dd64713c032" width="70%" />
</div>

**`stress --cpu 1 --timeout 600` 명령어 입력 전/후 pidstat을 통한 비교 :**

<div align="center">
  <img src="https://github.com/user-attachments/assets/4f0bb685-b8a0-41ad-a111-0250a28ac959" width="70%" />
</div>

<div align="center">
  <img src="https://github.com/user-attachments/assets/49b67f45-346d-4aee-b012-a7d4a0299797" width="70%" />
</div>

<br>

- **I/O-intensive process**

`stress -i 1 --timeout 600` 명령어를 통해  I/O 대기 시간을 증가시킨 결과 I/O 대기 시간이 증가하여 평균 로드가 상승하지만 CPU 사용률은 낮은 것을 확인

`mpstat` **확인 결과 :**

<div align="center">
  <img src="https://github.com/user-attachments/assets/d01e1272-24bd-44b7-b869-ff80e30f5cea" width="70%" />
</div>

`pidstat` **확인 결과 :**

<div align="center">
  <img src="https://github.com/user-attachments/assets/b43fd9b2-53a1-49e9-8629-eab78a678064" width="70%" />
</div>

`uptime 확인 결과`

<div align="center">
  <img src="https://github.com/user-attachments/assets/e95ba614-fb3b-4ab0-92f4-dc41c987e129" width="70%" />
</div>

<br>

- **scenario with a large number of processes**

`strees --cpu 8` **명령어를 통해 8개의 CPU를 실행 :**

<div align="center">
  <img src="https://github.com/user-attachments/assets/04d8f4d9-cd81-477e-9c8f-100dc2f43de7" width="70%" />
</div>

이를 통해 2개의 CPU만 있는 시스템에서 평균 로드가 8에 근접하여 CPU 과부하 상태 발생

**`pidstat` 을 통한 확인 :**

<div align="center">
  <img src="https://github.com/user-attachments/assets/7d7d0713-234c-46da-bea7-c21c90dd9b50" width="70%" />
</div>

### CPU 부하 TEST 🧨

다양한 암호화 알고리즘의 성능을 테스트하여 CPU에 부하를 주는 sh를 생성한 뒤 테스트 진행

```bash
#!/bin/bash
# 스크립트 실행 시 첫 번째 인자가 "test"인 지 확인.
if [ $1 = "test" ]
then
  for i in {1..2}
  do
    while [ 1 ]
    do
    # `openssl speed` 명령어를 실행하여 CPU 부하를 발생시킴.
    # 이 명령어는 다양한 암호화 알고리즘의 성능을 테스트하며, CPU에 상당한 부하를 줌.
    # `&> /dev/null`로 결과 출력을 버리고, `&`로 백그라운드에서 실행.
      openssl speed &> /dev/null &
    done
  done
    # 백그라운드에서 실행된 프로세스가 모두 완료될 때까지 대기.
  wait
fi
```
<div align="center">
  <img src="https://github.com/user-attachments/assets/0f6c378d-aafe-40b2-b945-300b8ebb72a5" width="70%" />
</div>

<div align="center">
  <img src="https://github.com/user-attachments/assets/9a44bc7d-a6b8-4dd5-904d-cdbd6ea77a24" width="70%" />
</div>

### 결론 🔔💪

위와 같은 실험을 통해 리눅스 시스템의 성능 분석을 위해 **평균 로드 값**이 어떻게 해석되는지, 그리고 다양한 상황에서 시스템 부하를 이해할 수 있었다.

이를 통해 시스템 성능 분석, 문제 해결 능력, 그리고 시스템 최적화에 대한 전문성을 강화할 수 있었다. 

**이를 통해 얻은 것**
1. 시스템 성능을 모니터링하고 평균 로드 및 성능 문제를 분석하여 신속히 해결할 수 있는 **분석 능력**.
2. CPU 및 I/O 부하를 테스트하고 최적화하여 시스템의 **안정성과 효율성**을 강화할 수 있는 경험
3. `stress`, `mpstat`, `pidstat`, `iostat`와 같은 성능 모니터링 도구를 활용하여 성능 병목 현상을 **정확히 파악**하고 해결할 수 있는 **문제 해결 능력**.

<hr>

## Reference 🧷
https://blog.devgenius.io/deep-understanding-of-average-load-in-linux-74822e1dbcb1

*본 실습은 위 자료를 참조하여 제작하였습니다.*
