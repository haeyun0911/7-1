import RPi.GPIO as GPIO
import time
import gpiozero
from gpiozero import RGBLED

led = RGBLED(red=16, green=20, blue=21)

GPIO_TRIGGER = 19
GPIO_ECHO = 26

# LED 핀 번호를 정의해야 합니다 (예: GPIO 25번)
LED_PIN = 18 # LED가 연결된 GPIO 핀 번호 (예시)

def setup():
    """GPIO 핀 모드를 설정하고 초기 상태를 설정합니다."""
    GPIO.setmode(GPIO.BCM)
    
    # 초음파 센서 핀 설정
    GPIO.setup(GPIO_TRIGGER, GPIO.OUT)
    GPIO.setup(GPIO_ECHO, GPIO.IN)
    
    # LED 핀 설정
    GPIO.setup(LED_PIN, GPIO.OUT) # LED 핀을 출력 모드로 설정
    GPIO.output(LED_PIN, GPIO.LOW) # LED 초기화: 끔
    
    GPIO.output(GPIO_TRIGGER, False)
    time.sleep(0.5)
    print("GPIO 설정 완료!")

def measure_distance():
    """초음파 센서를 사용하여 거리를 측정하고 cm 단위로 반환합니다."""
    GPIO.output(GPIO_TRIGGER, True)
    time.sleep(0.00001)
    GPIO.output(GPIO_TRIGGER, False)
    
    start_time = time.time()
    end_time = time.time()
    
    timeout_start = time.time()
    while GPIO.input(GPIO_ECHO) == 0:
        start_time = time.time()
        if time.time() - timeout_start > 1:
            print("에코 핀이 High로 바뀌지 않습니다 (타임아웃).")
            return -1
                
    timeout_start = time.time()
    while GPIO.input(GPIO_ECHO) == 1:
        end_time = time.time()
        if time.time() - timeout_start > 1:
            print("에코 핀이 Low로 바뀌지 않습니다 (타임아웃).")
            return -1
                
    time_elapsed = end_time - start_time
    distance_cm = (time_elapsed * 34300)/2
    
    return distance_cm

if __name__ == '__main__':
    try:
        setup()
        print("메인 루프 시작 전")
        while True:
            distance = measure_distance()
            
            if distance != -1: # 유효한 거리일 경우에만 처리
                # 여기서 거리에 따라 LED를 제어합니다.
                if (distance < 50): # 50cm 미만
                    GPIO.output(LED_PIN, GPIO.HIGH) # LED 켜기
                    led.color = (1, 1, 1)
                    print(f"현재 거리: {distance:.2f} cm (50cm 미만 - LED 켜짐)")
                
                # else if 대신 elif 사용
                elif (distance < 100): # 50cm 이상 100cm 미만
                    GPIO.output(LED_PIN, GPIO.HIGH) # LED 켜기
                    time.sleep(0.2)
                    GPIO.output(LED_PIN, GPIO.LOW)  # LED 끄기
                    time.sleep(0.2)
                    led.color = (1, 1, 0)
                    print(f"현재 거리: {distance:.2f} cm (50cm ~ 100cm - LED 깜빡임)")
                
                else: # 100cm 이상
                    GPIO.output(LED_PIN, GPIO.LOW) # LED 끄기
                    led.color = (0, 1, 0)
                    print(f"현재 거리: {distance:.2f} cm (100cm 이상 - LED 꺼짐)")
            else:
                print("거리 측정 실패 (센서 문제 또는 타임아웃).")
            
            time.sleep(0.1) # LED 깜빡임 주기와 겹치지 않게 전체 루프 딜레이 조절
                            # 너무 짧으면 거리 측정이 불안정할 수 있으니 0.1~0.5초 사이로 조절해보세요
    except KeyboardInterrupt:
        print("\n프로그램을 종료합니다.")
        pass
    finally:
        GPIO.cleanup()
        print("GPIO가 정리되었습니다.")
