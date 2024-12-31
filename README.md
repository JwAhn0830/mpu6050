# mpu6050
제가 사용하려고 만든 mpu6050 라이브러리입니다.  
현재 필요한 기능들만 있어서, 나중에 추가적으로 필요한 기능들이 있다면 추가 할 예정입니다.


## 개발 환경
MCU : STM32F103RB  
IDE : STM32CubeIDE

## 구조체 및 함수 설명
### 1. 구조체
```
struct MPU6050_config
```
mpu6050을 작동시키기 위한 설정 변수들이 담겨져 있는 구조체입니다. 


#### public 멤버
``uint8_t clockSource``  :  PWR_MGMT_1 레지스터에 접근하는 변수입니다. 

``uint8_t sampleRate``   : SMPRT_DIV 레지스터에 접근하는 변수입니다.  

``uint8_t gyoScaleRange``   : GYRO_CONFIG 레지스터에 접근하는 변수입니다.

``uint8_t gyoScaleRange``   : ACCEL_CONFIG 레지스터에 접근하는 변수입니다.  

<br /><br /><br />
```
struct MPU6050
```
mpu6050의 자이로 및 가속도 데이터가 저장되는 구조체입니다.

``readAccel_MPU6050()``또는 ``readGyro_MPU6050()``함수가 호출 되면 해당 구조체의 멤버 변수들에 값들이 저장됩니다.


#### public 멤버
``int16_t Accel_X_RAW,  int16_t Accel_Y_RAW,  int16_t Accel_Z_RAW``  :  각각 X축, Y축, Z축의 가속도의 raw data입니다

``double Ax,  double Ay,  double Az``  :  각각 X축, Y축, Z축의 가속도를 ``accelDivider``변수의 값으로 나눈 값입니다.
``accelDivider``의 값은 ``uint8_t gyoScaleRange`` 의 설정에 따라 달라집니다.

앞서 설명한 두 구조체는 해당 라이브러리를 사용하는데 있어 **반드시** 선언해야합니다.

---
<br /><br /><br /><br />
### 2. 함수
```
void config_MPU6050_DEFAULT(MPU6050_config *config)
```
``struct MPU6050_config`` 구조체를 선언하면 내부 파라미터들을 초기화 해줘야하는데 이는 mpu6050에 대한 전반전인 이해가 있어야 가능합니다.  
이에 대한 이해가 없는 분들을 위해 디폴트 값으로 세팅하는 함수입니다.

해당 함수를 호출하면
1. sleep mode 해제
2. 내부 8MHz 오실레이터 사용
3. 자이로 센서의 측정범위 : ±250 deg/s 로 설정
4. 가속도 센서의 측정범위 : ±2g로 설정합니다.

파라미터로는
``struct MPU6050_config``의 구조체 포인터를 넘겨주면됩니다. 
<br /><br /><br />
```
uint8_t init_MPU6050(I2C_HandleTypeDef *I2Cx, MPU6050_config *config)
```
``struct MPU6050_config``에 대한 파라미터들로 mpu6050에 대한 기본적인 세팅을 하는 함수입니다.  
해당 함수를 호출해야 mpu6050센서가 작동하며 가속도와 자이로 값을 읽기 시작합니다.

파라미터로는 I2C 채널과 ``struct MPU6050_config``의 포인터입니다.

리턴 값

``MPU6050_OK : 0``  문제 없음

``WHO_AM_I_REG : 1`` WHO_AM_I 레지스터와 I2C 통신 실패

``PWR_MGMT_1_REG : 2`` PWR_MGMT_1 레지스터와 I2C 통신 실패

``SMPRT_DIV_REG : 3``  SMPRT_DIV 레지스터와 I2C 통신 실패

``GYRO_CONFIG_REG : 4``  GYRO_CONFIG 레지스터와 I2C 통신 실패 또는 ``MPU6050_config``구조체에서 gyroScaleRange 변수에 잘못된 값 할당

`` ACCEL_CONFIG_REG : 5``  ACCEL_CONFIG 레지스터와 I2C 통신 실패 또는  ``MPU6050_config``구조체에서 accelScaleRange 변수에 잘못된 값 할당
<br /><br /><br />

```
void readAccel_MPU6050(I2C_HandleTypeDef *I2Cx, MPU6050* data) 
```
가속도 값을 읽는 함수입니다. 각 값들은 
``
MPU6050
`` 구조체 변수들에 저장됩니다.
<br /><br /><br />


```
void readGyro_MPU6050(I2C_HandleTypeDef *I2Cx, MPU6050* data)
```
자이로 값을 읽는 함수입니다. 각 값들은 ``MPU6050`` 구조체 변수들에 저장됩니다.
<br /><br /><br />

## 예제
main.c을 통해 확인할 수 있습니다.

### 1. 예제코드 GPIO 환경
<img src="https://github.com/user-attachments/assets/f91129ae-a413-4818-b0e5-e9058255cdb7" width="50%" height="50%"/>


### 2. I2C 환경
<img src="https://github.com/user-attachments/assets/391eb350-7d9f-4f63-aa87-5e18187ab603" width="50%" height="50%"/>



