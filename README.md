
2018/9/7 

2개월만에 만나서 다시 꺼낸 대학 졸업작품 아두이노..

오랫만에 하니까 기억도 안나는데, 문서화 작업도 제대로 안해놔서 이번참에 하려고 함.

---

## Arduino IDE

### Bluetooth 통신

```c
#include <SoftwareSerial.h>

// 블루투스모듈(HM-10) 의 TX가 아두이노의 D2
// 블루투스모듈(HM-10) 의 RX가 아두이노의 D3
SoftwareSerial BTSerial(2,3); 

// 전처리
void setup() {

  // 블루투스와 전송속도 맞춰줘야 함
  Serial.begin(9600);
  BTSerial.begin(9600);

  }

// 무한루프
void loop() {
  
  if (BTSerial.available()) {
    Serial.write(BTSerial.read());
  }

  if (Serial.available()) {
    BTSerial.write(Serial.read());
  }
    
}
```

![](./assets/hm-10.png)

시리얼 모니터로 AT 입력 시, OK 뜨면 **성공**

&nbsp;
&nbsp;

### Main Code

**Blynk - 라즈베리파이 - 아두이노** 로 통신하기 위한 코드

사실 아두이노에서 하는 것은

블루투스 신호로 데이터를 받는 코드와 

데이터에 따른 function만 정의하면 끝임

&nbsp;

라즈베리파이에서 아두이노 MAC address를 이용하여 신호를 보냄.

```c
#include <Servo.h>
#include <SoftwareSerial.h>

// 블루투스모듈(HM-10) 의 TX가 아두이노의 D2
// 블루투스모듈(HM-10) 의 RX가 아두이노의 D3
SoftwareSerial BTSerial(2,3);
 
Servo myservo;
int pos = 0; // servo 위치


// 전처리
void setup() { 

  // 블루투스와 전송속도 맞춰줘야 함
  Serial.begin(9600);
  BTSerial.begin(9600);

  // 아두이노의 D5를 이용하여 통신
  myservo.attach(5);
}
 

// 무한루프
void loop() { 

  if(BTSerial.available()) {
  
    delay(300);
    char c = BTSerial.read();
    int a = 1;
    int b = 0x23;
    Serial.write(c);
   
    if(c == '1') {
      for(pos = 0; pos < 150; pos += 2) {
        myservo.write(pos);              // 'pos'변수의 위치로 서보를 이동시킵니다.
        delay(15);                       // 서보 명령 간에 20ms를 기다립니다.
      } 
    }
   
    if(c == '2') {
      for(pos = 80; pos>=1; pos -= 2) {                                
        myservo.write(pos);              // 서보를 반대방향으로 이동합니다.
        delay(15);                       // 서보 명령 간에 20ms를 기다립니다.
      }
    }

  }


   if (Serial.available()) {
    BTSerial.write(Serial.read());
  }
}
```

&nbsp;
&nbsp;

### 블루투스 모듈 초기화

일반적으로 초기화하려면 `AT+RENEW`, `AT+RESET` 하면 된다.

아래는 라즈베리파이와 블루투스 연결이 되지 않을 때, 

블루투스 모듈(HM-10)을 초기화 해보는 방법임.

&nbsp;

**Arduino IDE - 시리얼 모니터**

```python
AT
AT+RENEW # 공장 초기화
AT+RESET # 모듈 재시작
AT+IMME1 # Auto-Connect 하지 않겠다. 명령(AT+START) 줄 때까지 기다려!
AT+ROLE0 # 제 모듈은 Slave 입니다. (AT+ROLE1 : 제모듈은 Master 입니다.)
AT+START # Connect 명령
```

[AT 커맨드 정리](http://blog.naver.com/PostView.nhn?blogId=xisaturn&logNo=220712028679) 에서 도움 받음.

&nbsp;
&nbsp;

---

## 라즈베리 파이

vnc viewer 이용

```bash
cd ~/Capstone/ble

# 대기중인 블루투스 MAC Address 확인
python blecomm.py

# 블루투스로 message 전송
python blecomm.py + MAC Address

```