# MNDW1 로딩창 만들기 
LCD16x02 첫 칸에 표시하는 아두이노 동작 상태 캐릭터 과제

# 프로젝트 소개 
이 프로젝트는 Arduino와 16×2 I2C LCD를 이용해 지렁이의 움직임을 픽셀 애니메이션으로 표현보았다. 지렁이가 땅속의 흙을 먹고 토양 속을 한 바퀴 이동한 다음 배설하는 장면을 여러 개의 5×8 픽셀 패턴으로 나누어 나타내고 프레임이 약 100ms 간격으로 바뀌며, 픽셀 패턴의 전환으로 지렁이와 흙의 움직임을 표현했다. 포인트로는 지렁이가 땅속에서 흙을 먹고 토양을 한 바퀴 돈 뒤 배설하는 과정을 통해 순환을 표현하여 로딩 되는 과정을 강조하며 재밌게 로딩창 임을 어필하였다.

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
    B10111
  },
  {
    B11111,
    B11111,
    B01011,
    B00011,
    B00011,
    B00011,
    B10111,
    B11111
  },
  {
    B11111,
    B01011,
    B00011,
    B00011,
    B00011,
    B10111,
    B11111,
    B11111
  },
  {
    B01011,
    B00011,
    B00011,
    B00011,
    B10111,
    B11111,
    B11111,
    B11111
  },
  {
    B00011,
    B00011,
    B00011,
    B10111,
    B11111,
    B11111,
    B11111,
    B11111
  },
  {
    B00011,
    B00011,
    B10111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111
  },
  {
    B00011,
    B10111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111
  },
  {
    B10111,
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
    B11111
  },
  {
    B11010,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111
  },
  {
    B11000,
    B11010,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111
  },
  {
    B11000,
    B11000,
    B11010,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111
  },
  {
    B11000,
    B11000,
    B11000,
    B11010,
    B11111,
    B11111,
    B11111,
    B11111
  },
  {
    B11101,
    B11000,
    B11000,
    B11000,
    B11010,
    B11111,
    B11111,
    B11111
  },
  {
    B11111,
    B11101,
    B11000,
    B11000,
    B11000,
    B11010,
    B11111,
    B11111
  },
  {
    B11111,
    B11111,
    B11101,
    B11000,
    B11000,
    B11000,
    B11010,
    B11111
  },
  {
    B11111,
    B11111,
    B11111,
    B11101,
    B11000,
    B11000,
    B11000,
    B11010
  },
  {
    B11111,
    B11111,
    B11111,
    B11111,
    B11101,
    B11000,
    B11000,
    B11000
  },
  {
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11101,
    B11000,
    B11000
  },
  {
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11101,
    B11000
  },
  {
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11101
  },
  {
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111,
    B11111
  }
};

const int frameCount = sizeof(frames) / sizeof(frames[0]);
const unsigned long frameDelay = 100;

int currentFrame = 0;
bool inverted = false;
byte nextSlot = 0;

unsigned long lastFrameTime = 0;

void showFrame(int index, bool invertPixels) {
  byte pattern[8];

  for (byte row = 0; row < 8; row++) {
    if (invertPixels) {
      pattern[row] = frames[index][row] ^ B11111;
    } else {
      pattern[row] = frames[index][row];
    }
  }

  lcd.createChar(nextSlot, pattern);
  lcd.setCursor(0, 0);
  lcd.write((uint8_t)nextSlot);

  nextSlot ^= 1;
}

void setup() {
  lcd.init();
  lcd.backlight();
  lcd.clear();
  lcd.noCursor();
  lcd.noBlink();

  showFrame(currentFrame, inverted);
  lastFrameTime = millis();
}

void loop() {
  unsigned long now = millis();

  if (now - lastFrameTime >= frameDelay) {
    lastFrameTime = now;

    currentFrame++;

    if (currentFrame >= frameCount) {
      currentFrame = 0;
      inverted = !inverted;
    }

    showFrame(currentFrame, inverted);
  }
}
const int frameCount = sizeof(frames) / sizeof(frames[0]);
const unsigned long frameDelay = 100;

int currentFrame = 0;
bool inverted = false;
byte nextSlot = 0;

unsigned long lastFrameTime = 0;

# 주요코드 설명
1. 변수 및 설정값 선언

sizeof 연산으로 frames 배열의 전체 프레임 수를 자동 계산함. frameDelay를 100ms로 설정해 프레임 전환 간격을 지정함. currentFrame에 현재 출력할 프레임 번호를 저장하고, inverted로 픽셀 반전 여부를 관리함. nextSlot은 CGRAM 슬롯 번호를 저장하며, lastFrameTime에 마지막 갱신 시각을 기록함.

2. showFrame() — 픽셀 반전 및 화면 출력

현재 프레임의 8개 행 데이터를 pattern 배열로 복사함. 반전 모드일 때는 ^ B11111 XOR 연산을 적용하여 가로 5픽셀의 0과 1을 뒤집음. lcd.createChar()로 패턴을 등록하고 (0, 0) 위치에 출력을 수행함. nextSlot ^= 1 연산으로 0번과 1번 슬롯을 교대로 사용하여 화면 수정 중 발생하는 깨짐 현상을 방지함.

3. setup() — 초기화 및 첫 프레임 출력

lcd.init()과 lcd.backlight()로 LCD를 초기화하고 백라이트를 켬. lcd.clear(), noCursor(), noBlink()를 실행해 커서를 숨기고 화면을 정리함. 원본 상태의 첫 프레임을 화면에 출력한 뒤, millis()로 현재 시간을 저장해 프레임 타이머의 기준점을 설정함.

4. loop() — 애니메이션 재생 및 반전 토글

millis()를 통해 이전 갱신 후 frameDelay(100ms) 이상 시간이 흐르면 실행함. 기준 시간을 업데이트하고 currentFrame을 1씩 증가시킴. 모든 프레임을 다 보여주면 번호를 0으로 되돌리고 inverted = !inverted로 반전 상태를 뒤집음. 변경된 값으로 showFrame()을 호출하여 화면을 지속적으로 갱신함.

 # 구현 영상 
https://github.com/MNDW100/MNDW1/issues/2#issue-6083969308
 # 고찰
 이번 프로젝트는 LCD의 첫 번째 칸이라는 작은 공간에서 지렁이가 흙을 먹고 땅속을 한 바퀴 돈 뒤 배설하는 모습을 픽셀의 변화로 표현하는 데 중점을 두었다. 지렁이 캐릭터를 그대로 그리는 대신, 흙이 채워지고 비워지는 패턴을 이용해 지렁이의 활동을 상징적으로 나타냈다. 제한된 화면 크기는 표현의 한계였지만, 어떤 변화를 보여 주고 어떤 세부 묘사를 덜어낼지 고민하게 해 주었다.

다만 애니메이션의 의도를 모르는 사람에게는 지렁이의 움직임보다 단순히 칸이 채워지고 비워지는 패턴으로 보일 수 있다. 반복되는 동작이 지렁이의 먹이 활동과 배설을 의미한다는 점도 한눈에 알아채기 어렵다. 장면마다 변화가 더 분명하게 드러나도록 프레임을 다듬거나, 프로젝트 설명과 함께 보여 주면 의도를 전달하는 데 도움이 될 것 같다.

코드에서는 millis()를 이용해 프레임 전환 시간을 확인하므로, delay()로 기다리는 동안 프로그램이 멈추는 방식보다 다른 작업을 함께 처리하기에 적합하다. 앞으로 버튼 입력이나 센서값 측정을 추가한다면, 애니메이션과 다른 기능이 동시에 자연스럽게 작동하는지 확인해 보고 싶다.

애니메이션의 부드러움은 프레임 수뿐 아니라 프레임 간격과 각 장면의 변화에도 영향을 받는다. 다음에는 프레임 속도를 조절하고, 움직임이 크게 바뀌는 구간에 중간 프레임을 추가해 보고 싶다. 실제 LCD에서도 잔상과 패턴의 가독성을 비교해 가장 잘 보이는 속도를 찾는다면 프로젝트를 더 완성도 있게 발전시킬 수 있을 것이다.
