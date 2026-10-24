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
 
