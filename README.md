# 🌬️ Low-Power Automatic Hand Dryer (STM32)

## ✅ Status: Fully Functional (100% Working)

This project implements a low-power automatic hand dryer using an STM32 microcontroller.
The system has been successfully tested and works reliably, achieving:

* ✔ Accurate hand detection
* ✔ Proper motor control (ON/OFF + PWM)
* ✔ Stable wake–sleep transitions
* ✔ Significant power savings

---

## 📌 Overview

The system operates in low-power STOP mode and wakes up periodically using the RTC wake-up timer. When active, it uses an ultrasonic sensor to detect hand presence and drives a motor accordingly.

* ✋ Hands detected → Motor runs
* ❌ No hands → Motor stops → System returns to sleep

---

## ⚡ Key Features

* 💤 Ultra Low Power Design

  * MCU spends most time in STOP mode
  * Wake-up via RTC interrupt

* ✋ Contactless Operation

  * Ultrasonic sensor (HC-SR04 style)
  * Real-time distance measurement

* 🌬️ Smart Motor Control

  * Motor runs only when needed
  * Stops immediately when hands leave

* 🎛️ PWM Speed Control

  * Motor speed varies with distance

* 🔁 Efficient Control Loop

  * Wake → Active sensing loop → Sleep

---

## 🧠 System Architecture

```text
SLEEP (STOP MODE)
      ↓ (RTC Wakeup)
WAKE
      ↓
ACTIVE LOOP:
    Measure distance
    → If hand detected → Motor ON
    → Else → Motor OFF → EXIT LOOP
      ↓
RETURN TO SLEEP
```

---

## 🔌 Hardware Components

* STM32 Microcontroller (STM32L4 series recommended)
* Ultrasonic Sensor (HC-SR04)
* Motor + Driver (e.g., L298N)
* External Power Supply (for motor)
* RTC (internal LSI clock)

---

## 🧩 Peripherals Used

| Peripheral | Function                        |
| ---------- | ------------------------------- |
| GPIO       | Trigger signal, motor direction |
| TIM2       | Input Capture (ultrasonic echo) |
| TIM3       | PWM (motor speed control)       |
| TIM6       | Microsecond delay               |
| UART2      | Debug output                    |
| RTC        | Wake-up timer                   |

---

## ⚙️ How It Works

### 1. Low-Power Sleep

```c
HAL_PWR_EnterSTOPMode(PWR_LOWPOWERREGULATOR_ON, PWR_STOPENTRY_WFI);
```

---

### 2. Wake-Up (RTC)

```c
HAL_RTCEx_SetWakeUpTimer_IT(&hrtc, 6000, RTC_WAKEUPCLOCK_RTCCLK_DIV16);
```

* LSI ≈ 32 kHz
* Divider = 16 → 2000 Hz
* 6000 ticks → **3 seconds wake interval**

---

### 3. Distance Measurement

```c
distance = (time * 0.034) / 2;
```

* Uses TIM2 input capture
* Measures ultrasonic echo pulse width

---

### 4. Decision Logic

```c
if(distance < THRESHOLD){
    motor_state = 1;
}else{
    motor_state = 0;
}
```

---

### 5. Motor Control (PWM)

```c
__HAL_TIM_SET_COMPARE(&htim3, TIM_CHANNEL_2, duty);
```

* Direction via GPIO
* Speed controlled via PWM

---

### 6. Exit Condition

```c
else {
    motor OFF;
    break;
}
```

* Stops motor
* Exits active loop
* Returns to sleep

---

## ⏱️ Performance & Power Efficiency

* Active time: ~14 ms
* Sleep interval: ~3 seconds

### Duty Cycle:

```text
14 ms / 3000 ms ≈ 0.47%
```

### Estimated Power Savings:

* ~177× reduction in MCU power consumption
* ~99.5% time spent in low-power mode

---

## 🧪 Debug Output

Example UART logs:

```text
I have just woken
The distance is : 14.02
Execution time is : 14
I am going to sleep
```

---

## ⚠️ Notes

* Threshold (~10–30 cm) may require tuning depending on environment
* Ultrasonic readings may vary slightly due to noise
* PWM frequency may be audible depending on configuration

---

## 🚀 Future Improvements

* 🔋 Battery life estimation & optimization
* 🎯 Adaptive threshold / filtering
* 🔇 Higher PWM frequency (>20 kHz) for silent operation
* 🤖 Fully automatic wake (no periodic polling)

---

## 📂 Source Code

Main implementation:
👉 

---

## 🎯 Conclusion

This project demonstrates a complete embedded system combining:

* Low-power design
* Real-time sensing
* Control systems
* Hardware interfacing

> ✔ The system is fully functional and stable
> ✔ Successfully integrates efficiency + real-world behavior

---

## 🙌 Author

Developed as part of hands-on embedded systems learning and system design exploration.

---
