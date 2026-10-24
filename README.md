# MNDW1 로딩창 만들기 
LCD16x02 첫 칸에 표시하는 아두이노 동작 상태 캐릭터 과제

# 프로젝트 소개 
이 프로젝트는 지렁이가 흙을 먹고 배설하며 땅을 다시 채우는 과정을 모티브로 하여 만든 순환형 로딩 애니메이션 입니다. 0에서 점점 채워지는 과정으로 로딩을 표현하는 보통의 틀에서 벗어나 꽉 채워진 화면에서 지렁이가 흙을 먹고 배설하는 과정으로 순환을 표현하여 로딩창을 나만의 방식으로 표현해봤어요.
지렁이의 형체를 세밀하게 그리지 않고 흙이 비워지고 다시 채워지는 변화에 초점을 맞춰 작은 LCD 화면에서 지렁이를 단순한 픽셀로 표현해 관람자가 움직임을 상상하도록 했고 그 결과 로딩의 핵심인 작업이 진행 중이라는 느낌이 더 돋보이도록 구성해보았습니다.

# 사용 부품
- Arduino [보드 모델 입력]
- 16×2 I2C LCD
- 점퍼선
- USB 케이블
# 회로도
<img width="1451" height="758" alt="스크린샷 2026-10-24 145030" src="https://github.com/user-attachments/assets/e3ca72ec-952b-4b39-a78a-c40669d3eebd" />
# 구현 방식

#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

byte frames[][8] = {
  {
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111
  },
  {
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B01011
  },
  {
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B01011,
    B00011
  },
  {
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B01011,
    B00011,
    B00011
  },
  {
    B11111,
    B11111,
    B11111,
    B11111,
    B01011,
    B00011,
    B00011,
    B00011
  },
  {
    B11111,
    B11111,
    B11111,
    B01011,
    B00011,
    B00011,
    B00011,
    B00011
  },
  {
    B11111,
    B11111,
    B01011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011
  },
  {
    B11111,
    B01011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011
  },
  {
    B01011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011
  },
  {
    B00011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011
  },
  {
    B00001,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011
  },
  {
    B00000,
    B00001,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011
  },
  {
    B00000,
    B00000,
    B00001,
    B00011,
    B00011,
    B00011,
    B00011,
    B00011
  },
  {
    B00000,
    B00000,
    B00000,
    B00001,
    B00011,
    B00011,
    B00011,
    B00011
  },
  {
    B00000,
    B00000,
    B00000,
    B00000,
    B00001,
    B00011,
    B00011,
    B00011
  },
  {
    B00000,
    B00000,
    B00000,
    B00000,
    B00000,
    B00001,
    B00011,
    B00011
  },
  {
    B00000,
    B00000,
    B00000,
    B00000,
    B00000,
    B00000,
    B00001,
    B00011
  },
  {
    B00000,
    B00000,
    B00000,
    B00000,
    B00000,
    B00000,
    B00000,
    B00001
  },
  {
    B00000,
    B00000,
    B00000,
    B00000,
    B00000,
    B00000,
    B00000,
    B00000
  }
};

const int frameCount = sizeof(frames) / sizeof(frames[0]);
const unsigned long frameDelay = 200;
const byte lcdColumn = 0;
const byte lcdRow = 0;

void showFrame(int index) {
  lcd.createChar(0, frames[index]);
  lcd.setCursor(lcdColumn, lcdRow);
  lcd.write((uint8_t)0);
  delay(frameDelay);
}

void setup() {
  lcd.init();
  lcd.backlight();
  lcd.clear();
}

void loop() {
  for (int i = 0; i < frameCount; i++) {
    showFrame(i);
  }

  for (int i = frameCount - 2; i >= 1; i--) {
    showFrame(i);
  }
}
# 주요 코드 설명 
줄은 픽셀 입력이고 
const int frameCount = sizeof(frames) / sizeof(frames[0]);
const unsigned long frameDelay = 200;
const byte lcdColumn = 0;
const byte lcdRow = 0;

frameCount: 배열에 저장된 프레임 개수를 자동 계산
frameDelay: 한 프레임을 200ms(0.2초) 동안 표시
lcdColumn, lcdRow: 표시 위치를 첫째 줄, 첫 번째 글자로 지정

void showFrame(int index) {
  lcd.createChar(0, frames[index]);
  lcd.setCursor(lcdColumn, lcdRow);
  lcd.write((uint8_t)0);
  delay(frameDelay);
}
딜레이 2초로 구현

void setup() {
  lcd.init();
  lcd.backlight();
  lcd.clear();
}
전원을 켜면 한 번 실행되어 LCD 초기화 → 백라이트 켜기 → 화면 지우기를 수행

void loop() {
  for (int i = 0; i < frameCount; i++) {
    showFrame(i);
  }

  for (int i = frameCount - 2; i >= 1; i--) {
    showFrame(i);
  }
}
첫 번째 반복문은 처음부터 끝까지, 두 번째는 역순으로 재생하여 양 끝 프레임이 중복 표시되지 않도록 범위를 조정
 흙이 비워졌다가 다시 채워지는 애니메이션을 계속 반복한다
 # 구현 영상 
https://www.youtube.com/shorts/5YdHjdV1fU0
 # 고찰
 이번 작업은 단순히 픽셀이 켜지고 꺼지는 동작으로 캐릭터의 움직임을 표현할 수 있다는 점에 중점을 뒀다 남들이 보이는 캐릭터를 사용할 때 반대로 캐릭터가 있지만 눈에는 잘보이지 않는다면 재미있을 것 같아 구현하였다
첫 줄 첫 글자라는 아주 작은 공간은 표현의 한계였지만 동시에 어떤 부분을 살리고 무얼 덜어낼지 깊이 고민하게 만든 조건이었다. 지렁이의 형태를 정밀하게 그리는 대신 흙이 움직이는 변화를 통해 활동을 상징적으로 표현하여 한 칸 안에서 반복되는 애니메이션을 완성했다.
단점으로는 설명 없이 처음 접하는 사람에게는 지렁이의 움직임이라기보다 그저 칸이 채워지고 비워지는 패턴으로 보일 수 있다. 그렇기에 동작이 반복된다는 직관적인 느낌과 그 동작이 지렁이라는 것을 알아채기 힘들어 보인다/
코드 측면에서 현재 사용한 delay() 방식은 구현이 직관적이지만 프레임이 대기하는 동안 다른 연산을 처리하기 힘들다. 나중에 센서값 측정이나 버튼 입력을 동시에 처리하려면 millis() 기반의 비차단 방식으로 바꿀 필요가 있다. 이때 핵심 연산이 돌아가는 중에도 애니메이션이 매끄럽게 갱신되도록 만드는 것이 중요해 보인다
움직임의 부드러움 또한 단순 프레임 수만으로 결정되지 않는다. 앞으로 프레임 간격을 조절하거나 변화가 큰 구간에 중간 프레임을 넣고 실제 LCD에서 잔상과 가독성을 직접 비교하며 최적의 속도를 찾으면 더 완성도가 높아질 것 같다.
