# Open-Source AI Компаньйони на STM32

Дата дослідження: 2025-04-20

## Зміст

1. [STM32 AI Model Zoo](#1-stm32-ai-model-zoo)
2. [STM32N6 Face Recognition](#2-stm32n6-face-recognition)
3. [STM32N6 Object Detection](#3-stm32n6-object-detection)
4. [STM32 Speech Recognition (TFLite Micro)](#4-stm32-speech-recognition-tflite-micro)
5. [STM32 TFLM Demos](#5-stm32-tflm-demos)
6. [X-CUBE-AI MNIST](#6-x-cube-ai-mnist)
7. [STM32 Keyword Spotting (Edge Impulse)](#7-stm32-keyword-spotting-edge-impulse)
8. [STM32 Human Activity Recognition](#8-stm32-human-activity-recognition)
9. [OpenMV N6](#9-openmv-n6)
10. [STM32 Gesture Recognition](#10-stm32-gesture-recognition)

---

## 1. STM32 AI Model Zoo

**Посилання**: https://github.com/STMicroelectronics/stm32ai-modelzoo

### Принцип роботи

Офіційна бібліотека готових AI-моделей від STMicroelectronics. Містить колекцію попередньо натренованих та оптимізованих моделей для різних use cases: класифікація зображень, детекція об'єктів, розпізнавання осіб, pose estimation, semantic segmentation, audio event detection, speech enhancement, human activity recognition.

Моделі доступні у форматах TensorFlow, PyTorch та ONNX. Кожна модель супроводжується скриптами для transfer learning, квантизації, оцінки продуктивності та автоматичної генерації коду для STM32.

### AI моделі

- **Image Classification**: MobileNet, ResNet, EfficientNet варіанти
- **Object Detection**: YOLO (YOLOv5, YOLOv8, YOLO11), ST-YOLO-X
- **Face Detection**: CenterFace
- **Pose Estimation**: MoveNet, PoseNet
- **Semantic Segmentation**: DeepLab, U-Net
- **Audio**: YAMNet для audio event detection

### Де зберігається AI

- Моделі зберігаються у Flash пам'яті мікроконтролера
- Ваги квантизовані до INT8, INT4 (mixed precision)
- Розмір моделей: від кількох KB до кількох MB

### STM32 підтримка

- STM32H7 серія (STM32H747I-DISCO, NUCLEO-H743ZI2)
- STM32N6 серія з NPU (STM32N6570-DK)
- STM32MP2 серія (STM32MP257F-EV1)
- STM32U5 серія (B-U585I-IOT02A)
- STM32F4 серія (NUCLEO-F401RE)

### Вимоги до заліза

| Параметр | Мінімальні | Рекомендовані |
|----------|-----------|---------------|
| Flash | 512 KB | 2+ MB |
| RAM | 128 KB | 512+ KB |
| Частота | 80 MHz | 480+ MHz |

### Орієнтовна ціна

- **STM32H747I-DISCO**: ~$75
- **NUCLEO-H743ZI2**: ~$15
- **STM32N6570-DK**: ~$150 (з NPU)
- **B-U585I-IOT02A**: ~$50

### Юзкейси

- Smart home devices з розпізнаванням голосу/жестів
- Industrial predictive maintenance
- Security systems з face detection
- Health monitoring wearables
- Smart agriculture sensors

### Плюси

- Офіційна підтримка від ST
- Велика кількість готових моделей
- Автоматична генерація коду
- Документація та tutorials
- Підтримка останніх архітектур (PyTorch, TensorFlow 2.x)
- Docker-based setup для легкого старту

### Мінуси

- Прив'язка до екосистеми ST
- Деякі моделі вимагають потужніших MCU
- Складність налаштування для початківців
- Обмежена підтримка non-ST hardware

### Складність повторення

**Medium** — вимагає знайомства з STM32CubeIDE та базовими знаннями ML.

---

## 2. STM32N6 Face Recognition

**Посилання**: https://github.com/PeleAB/STM32N6-FaceRecognition

### Принцип роботи

Повноцінна система розпізнавання облич на базі STM32N6 з апаратним NPU (Neural Processing Unit). Система працює в реальному часі: детектує обличчя на відео, вирізає їх, створює embeddings та порівнює з базою відомих осіб.

Архітектура: Camera → ISP → Face Detection (NPU) → Face Cropping → Face Recognition (NPU) → Display/Stream

### AI моделі

- **Face Detection**: CenterFace (128×128 RGB input, INT8 quantized)
  - Inference time: ~9ms
- **Face Recognition**: MobileFaceNet (112×112 RGB aligned faces, INT8 quantized)
  - Output: 128-dimensional embeddings
  - Inference time: ~120ms

### Де зберігається AI

- Моделі у external flash (початкові адреси: 0x71000000, 0x72000000)
- Embeddings бази осіб у RAM
- Runtime завантаження весів у NPU

### STM32 підтримка

- **STM32N6570-DK** (єдиний officially supported)

### Вимоги до заліза

| Компонент | Специфікація |
|-----------|-------------|
| MCU | STM32N657xx з Neural-ART Accelerator |
| NPU | 600 GOPS |
| RAM | 4 MB вбудованої SRAM |
| External Flash | 1 GB Octo-SPI |
| Camera | IMX335, VD55G1, або VD66GY |
| Display | 800×480 LCD |

### Орієнтовна ціна

- **STM32N6570-DK**: ~$150
- **Camera module**: ~$30-50
- **LCD display**: ~$40-60
- **Разом**: ~$220-260

### Юзкейси

- Access control systems
- Smart doorbells
- Attendance tracking
- Personalized user interfaces
- Security monitoring

### Плюси

- Повністю on-device inference (no cloud)
- Висока продуктивність завдяки NPU
- Multi-face tracking
- PC streaming interface для моніторингу
- Готові скрипти для компіляції та прошивки

### Мінуси

- Висока вартість hardware
- Складний процес прошивки (4 окремих компоненти)
- Тільки один officially supported board
- Вимагає external flash для моделей

### Складність повторення

**Hard** — складна конфігурація, підписування бінарів, робота з external flash.

---

## 3. STM32N6 Object Detection

**Посилання**: https://github.com/STMicroelectronics/STM32N6-GettingStarted-ObjectDetection

### Принцип роботи

Офіційний приклад object detection від ST для STM32N6. Використовує NPU для прискорення інференсу нейронних мереж. Підтримує real-time детекцію з камери з виводом на LCD або USB/UVC stream.

Особливості: Dual DCMIPP pipelines, hardware-accelerated ISP, LTDC dual-layer display, development mode та boot from flash.

### AI моделі

- **st_yolo_x_nano_480_1.0_0.25_3_st_int8.tflite**
  - Inference time: 29ms (STM32N6570-DK)
- **quantized_tiny_yolo_v2_224_.tflite**
  - Inference time: 27-30ms (NUCLEO-N657X0-Q)

### Де зберігається AI

- Моделі у external flash (адреса 0x70100000)
- Runtime завантаження через FSBL

### STM32 підтримка

- STM32N6570-DK
- NUCLEO-N657X0-Q

### Вимоги до заліза

| Параметр | STM32N6570-DK | NUCLEO-N657X0-Q |
|----------|--------------|-----------------|
| Camera | IMX335 (в комплекті) | STEVAL-55G1MBI / VD55G1 |
| Display | Вбудований LCD | USB/UVC або X-NUCLEO-GFX01M2 SPI |
| External Flash | 1 GB | 512 MB |

### Орієнтовна ціна

- **STM32N6570-DK**: ~$150
- **NUCLEO-N657X0-Q**: ~$100
- **Додаткові модулі**: ~$30-50

### Юзкейси

- People counting
- Industrial quality control
- Smart retail analytics
- Security perimeter monitoring
- Autonomous robotics

### Плюси

- Офіційна підтримка ST
- Pre-built binaries для швидкого старту
- Детальна документація
- Підтримка multiple camera modules
- USB/UVC output для легкого тестування

### Мінуси

- Обмежена кількість моделей у прикладі
- Складний процес зміни моделі
- Вимагає STEdgeAI для конвертації
- Немає internal flash у STM32N6

### Складність повторення

**Medium-Hard** — добра документація, але складний процес прошивки.

---

## 4. STM32 Speech Recognition (TFLite Micro)

**Посилання**: https://github.com/stgloorious/stm32-speech-recognition

### Принцип роботи

Keyword spotting система на базі TensorFlow Lite for Microcontrollers. Розпізнає 6 команд: "yes", "no", "up", "down", "left", "right".

Pipeline: PDM Microphone → STFT (Short-Time Fourier Transform) → Spectrogram (124×129) → CNN → Classification

Модель квантизована до INT8 для швидкого інференсу на ресурсно-обмеженому мікроконтролері.

### AI моделі

- **CNN з квантизацією INT8**
  - Input: 124×129 spectrogram
  - Architecture: Conv2D + Dense layers
  - Accuracy: ~80%
  - Натренована на Google Speech Commands dataset

### Де зберігається AI

- Модель скомпільована у firmware
- Зберігається у Flash пам'яті
- Runtime завантаження у RAM

### STM32 підтримка

- STM32L475VGT (B-L475E-IOT01A2 Discovery board)

### Вимоги до заліза

| Параметр | Значення |
|----------|----------|
| MCU | STM32L475VGT (Cortex-M4, 80 MHz) |
| Flash | 1 MB |
| RAM | 128 KB |
| Microphone | PDM (on-board) |

### Орієнтовна ціна

- **B-L475E-IOT01A2**: ~$50

### Юзкейси

- Voice-controlled IoT devices
- Hands-free interfaces
- Smart home automation
- Industrial voice commands
- Accessibility devices

### Плюси

- Повністю open-source
- Детальна документація з поясненнями
- CMake-based build system
- Python scripts для оцінки
- Навчальний проєкт з поясненнями STFT

### Мінуси

- Тільки 6 команд (обмежений словник)
- Accuracy 80% (не ідеальна)
- Немає підтримки on-board microphone (потрібен external audio input)
- Тільки один supported board

### Складність повторення

**Easy-Medium** — добре документовано, але потрібне налаштування toolchain.

---

## 5. STM32 TFLM Demos

**Посилання**: https://github.com/PhilippvK/stm32-tflm-demos

### Принцип роботи

Колекція демонстраційних проєктів на базі TensorFlow Lite Micro для STM32. Містить кілька прикладів: Hello World (sine wave prediction), Micro Speech (keyword spotting), MNIST (handwritten digit recognition), Tiny Face (face detection).

Кожен приклад — окремий репозиторій з підтримкою STM32F413H-Discovery та STM32F769I-Discovery.

### AI моделі

- **Hello World**: Simple NN для передбачення sine wave
- **Micro Speech**: Keyword spotting (як у прикладі 4)
- **MNIST**: CNN для розпізнавання рукописних цифр
- **Tiny Face**: Легка модель для face detection

### Де зберігається AI

- Моделі скомпільовані у firmware
- TensorFlow Lite Micro runtime

### STM32 підтримка

- STM32F413H-Discovery (disco_f413zh)
- STM32F769I-Discovery (disco_f769ni)

### Вимоги до заліза

| Board | MCU | Flash | RAM | Features |
|-------|-----|-------|-----|----------|
| STM32F413H-DISCO | STM32F413ZH | 1.5 MB | 320 KB | Audio, LCD |
| STM32F769I-DISCO | STM32F769NI | 2 MB | 512 KB | Touch LCD, camera |

### Орієнтовна ціна

- **STM32F413H-DISCO**: ~$60
- **STM32F769I-DISCO**: ~$80

### Юзкейси

- Навчання TinyML
- Прототипування AI на MCU
- Демонстрації можливостей
- База для власних проєктів

### Плюси

- Кілька різних прикладів
- CMake-based build
- Підтримка tfite_micro_compiler для оптимізації
- Документація з debugging
- Підтримка multiple boards

### Мінуси

- Складна початкова налаштування (submodules, dependencies)
- Довгий час першого build
- Не оновлювалося останнім часом
- Старі версії TensorFlow

### Складність повторення

**Medium** — складна початкова конфігурація, але стабільна робота після налаштування.

---

## 6. X-CUBE-AI MNIST

**Посилання**: https://github.com/dimtass/stm32f746-x-cube-ai-mnist

### Принцип роботи

Класичний приклад розпізнавання рукописних цифр (MNIST) на STM32F7 з використанням X-CUBE-AI. Проєкт демонструє інтеграцію нейронної мережі з STM32CubeIDE.

Pipeline: Touchscreen input → Preprocessing → CNN inference → Display result

### AI моделі

- **CNN для MNIST**
  - Input: 28×28 grayscale
  - Квантизована модель
  - Висока accuracy (>95%)

### Де зберігається AI

- Модель у Flash через X-CUBE-AI
- Runtime у RAM

### STM32 підтримка

- STM32F746 (Discovery board з touchscreen)

### Вимоги до заліза

| Параметр | Значення |
|----------|----------|
| MCU | STM32F746NG (Cortex-M7, 216 MHz) |
| Flash | 1 MB |
| RAM | 340 KB |
| Display | 4.3" LCD з touchscreen |

### Орієнтовна ціна

- **STM32F746G-DISCO**: ~$65

### Юзкейси

- Навчання embedded AI
- Демонстрація touchscreen + AI
- Прототипування HMI з AI

### Плюси

- Простий та зрозумілий приклад
- Готовий для STM32CubeIDE
- Інтерактивний (touchscreen)
- Добре документовано

### Мінуси

- Проста задача (тільки MNIST)
- Обмежена практична цінність
- Старий приклад (можливо потребує оновлення)

### Складність повторення

**Easy** — відмінний стартовий проєкт для новачків.

---

## 7. STM32 Keyword Spotting (Edge Impulse)

**Посилання**: https://github.com/smlee00/STM32-Keyword-Spotting-with-Edge-Impulse

### Принцип роботи

Keyword spotting проєкт з використанням Edge Impulse — no-code/low-code платформи для TinyML. Модель тренується у хмарі, потім деплоється на STM32.

Edge Impulse надає: data collection, model training, optimization (quantization), та C code generation для STM32.

### AI моделі

- **CNN для audio classification**
  - Натренована через Edge Impulse
  - Квантизована (INT8)
  - Оптимізована для target MCU

### Де зберігається AI

- Модель у Flash
- Edge Impulse firmware SDK

### STM32 підтримка

- STM32L4 серія (NUCLEO-L476RG)
- Інші STM32 через Edge Impulse

### Вимоги до заліза

| Параметр | Значення |
|----------|----------|
| MCU | STM32L476RG (Cortex-M4, 80 MHz) |
| Flash | 1 MB |
| RAM | 128 KB |
| Microphone | External або on-board |

### Орієнтовна ціна

- **NUCLEO-L476RG**: ~$15
- **Microphone module**: ~$5-10

### Юзкейси

- Custom wake words
- Voice-controlled devices
- Industrial voice interfaces
- Smart home automation

### Плюси

- No-code тренування моделі
- Швидкий prototyping
- Edge Impulse екосистема
- Легке оновлення моделі

### Мінуси

- Залежність від Edge Impulse платформи
- Обмежена кастомізація
- Потрібен акаунт Edge Impulse
- Не повністю open-source workflow

### Складність повторення

**Easy** — найпростіший спосіб почати з voice AI на STM32.

---

## 8. STM32 Human Activity Recognition

**Посилання**: Частина STM32 AI Model Zoo — https://github.com/STMicroelectronics/stm32ai-modelzoo/tree/main/human_activity_recognition

### Принцип роботи

Розпізнавання людської активності (ходьба, біг, сидіння, стояння) з використанням даних з IMU (акселерометр + гіроскоп). Модель аналізує time-series дані та класифікує активність.

### AI моделі

- **CNN/LSTM для time-series classification**
  - Input: 3-axis accelerometer + gyroscope
  - Window size: 2-4 секунди
  - Output: 4-6 класів активності

### Де зберігається AI

- Модель у Flash
- IMU data у RAM (circular buffer)

### STM32 підтримка

- B-U585I-IOT02A (з вбудованим IMU)
- Інші STM32 з external IMU

### Вимоги до заліза

| Параметр | Значення |
|----------|----------|
| MCU | STM32U585 (Cortex-M33, 160 MHz) |
| Flash | 2 MB |
| RAM | 786 KB |
| Sensors | IIS2MDC (accel), ISM330DHCX (IMU) |

### Орієнтовна ціна

- **B-U585I-IOT02A**: ~$50

### Юзкейси

- Fitness trackers
- Elderly fall detection
- Industrial safety monitoring
- Sports analytics
- Healthcare wearables

### Плюси

- Низьке енергоспоживання (STM32U5)
- Вбудовані sensors у development kit
- Готові datasets
- Edge Impulse інтеграція

### Мінуси

- Потрібне зібрання власного dataset для custom activities
- Чутливість до placement пристрою
- Може плутати схожі активності

### Складність повторення

**Medium** — вимагає роботи з sensors та time-series даними.

---

## 9. OpenMV N6

**Посилання**: https://github.com/openmv/openmv (з підтримкою STM32N6)

### Принцип роботи

OpenMV — популярна платформа computer vision для мікроконтролерів. Версія N6 базується на STM32N6 з NPU, що дозволяє запускати складніші моделі в реальному часі.

Підтримує MicroPython, що робить prototyping надзвичайно швидким. Містить вбудовані бібліотеки для: face detection, object detection, color tracking, QR code detection, optical flow.

### AI моделі

- **Face Detection**: Haar cascades, CNN-based
- **Object Detection**: MobileNet SSD, YOLO
- **Classification**: MobileNet
- **Custom models**: Конвертація через STM32Cube.AI

### Де зберігається AI

- Моделі у Flash (MicroPython filesystem)
- Runtime завантаження у RAM/NPU

### STM32 підтримка

- OpenMV N6 (базується на STM32N6)

### Вимоги до заліза

| Параметр | Значення |
|----------|----------|
| MCU | STM32N6xx з NPU |
| NPU | 600 GOPS |
| RAM | 4 MB SRAM |
| Flash | 32 MB QSPI Flash |
| Camera | Global shutter, 1.6 MP |

### Орієнтовна ціна

- **OpenMV N6**: ~$100-120 (expected)

### Юзкейси

- Robotics vision
- Industrial inspection
- Smart cameras
- Educational projects
- Rapid prototyping

### Плюси

- MicroPython — швидкий prototyping
- Велика спільнота
- Вбудовані бібліотеки CV
- IDE з live video feed
- Підтримка NPU для швидкого inference

### Мінуси

- Нова платформа (N6 ще не широко доступний)
- Вища ціна порівняно з базовими STM32
- Обмежена memory для великих моделей
- MicroPython overhead

### Складність повторення

**Easy-Medium** — MicroPython спрощує розробку, але потрібне розуміння CV концепцій.

---

## 10. STM32 Gesture Recognition

**Посилання**: https://github.com/matflow/TinyML-gesture-recognition-STM32

### Принцип роботи

Розпізнавання жестів (рухів) за допомогою гіроскопа STM32F429I-Discovery. Модель класифікує 3 різні типи рухів на основі даних з IMU.

Pipeline: Gyroscope data → Preprocessing → CNN/LSTM → Classification

### AI моделі

- **CNN для time-series classification**
  - Input: 3-axis gyroscope
  - 3 класи жестів
  - TensorFlow Lite Micro

### Де зберігається AI

- Модель у Flash
- Gyro data у RAM

### STM32 підтримка

- STM32F429I-Discovery (з вбудованим гіроскопом)

### Вимоги до заліза

| Параметр | Значення |
|----------|----------|
| MCU | STM32F429ZI (Cortex-M4, 180 MHz) |
| Flash | 2 MB |
| RAM | 256 KB |
| Gyroscope | L3GD20 (on-board) |
| Display | 2.4" LCD |

### Орієнтовна ціна

- **STM32F429I-DISCO**: ~$40

### Юзкейси

- Gesture-controlled interfaces
- Gaming controllers
- Smart watch gestures
- Industrial control
- Accessibility devices

### Плюси

- Простий та зрозумілий приклад
- Вбудований гіроскоп
- Навчальний проєкт
- Демонстрація time-series ML

### Мінуси

- Тільки 3 жести
- Старий приклад (2019)
- Обмежена документація
- Може потребувати оновлення бібліотек

### Складність повторення

**Easy** — хороший стартовий проєкт для роботи з IMU + ML.

---

## Порівняльна таблиця

| Проєкт | Тип AI | STM32 | NPU | Складність | Ціна ($) | Юзкейс |
|--------|--------|-------|-----|------------|----------|--------|
| STM32 AI Model Zoo | Multiple | H7, N6, U5, F4 | Optional | Medium | 15-150 | Універсальний |
| STM32N6 Face Recognition | Face rec | N6 | Так | Hard | 220-260 | Security |
| STM32N6 Object Detection | Object det | N6 | Так | Medium-Hard | 100-150 | Vision |
| STM32 Speech Recognition | Audio | L4 | Ні | Easy-Medium | 50 | Voice control |
| STM32 TFLM Demos | Multiple | F4, F7 | Ні | Medium | 60-80 | Навчання |
| X-CUBE-AI MNIST | Vision | F7 | Ні | Easy | 65 | Навчання |
| STM32 Keyword Spotting | Audio | L4 | Ні | Easy | 15-25 | Voice control |
| STM32 Human Activity Rec | IMU | U5 | Ні | Medium | 50 | Wearables |
| OpenMV N6 | Vision | N6 | Так | Easy-Medium | 100-120 | Robotics |
| STM32 Gesture Recognition | IMU | F4 | Ні | Easy | 40 | Gesture control |

---

## Рекомендації за сценаріями

### Для початківців
1. **X-CUBE-AI MNIST** — найпростіший вхід
2. **STM32 Keyword Spotting** — no-code підхід
3. **STM32 Gesture Recognition** — робота з IMU

### Для voice AI
1. **STM32 Keyword Spotting (Edge Impulse)** — швидкий старт
2. **STM32 Speech Recognition** — повний контроль

### Для vision AI
1. **STM32 AI Model Zoo** — найбільше моделей
2. **OpenMV N6** — швидкий prototyping
3. **STM32N6 Object Detection** — production-ready

### Для production
1. **STM32N6 Face Recognition** — повноцінна система
2. **STM32 AI Model Zoo** — офіційна підтримка
3. **STM32N6 Object Detection** — оптимізовано для NPU

### Для навчання
1. **STM32 TFLM Demos** — кілька прикладів
2. **STM32 Speech Recognition** — детальні пояснення
3. **STM32 AI Model Zoo** — сучасні архітектури

---

## Джерела

1. STMicroelectronics. (2025). *STM32 AI Model Zoo*. GitHub. https://github.com/STMicroelectronics/stm32ai-modelzoo

2. PeleAB. (2025). *STM32N6 Face Recognition*. GitHub. https://github.com/PeleAB/STM32N6-FaceRecognition

3. STMicroelectronics. (2025). *STM32N6 Getting Started Object Detection*. GitHub. https://github.com/STMicroelectronics/STM32N6-GettingStarted-ObjectDetection

4. stgloorious. (2024). *STM32 Speech Recognition*. GitHub. https://github.com/stgloorious/stm32-speech-recognition

5. PhilippvK. (2023). *STM32 TFLM Demos*. GitHub. https://github.com/PhilippvK/stm32-tflm-demos

6. dimtass. (2020). *STM32F746 X-CUBE-AI MNIST*. GitHub. https://github.com/dimtass/stm32f746-x-cube-ai-mnist

7. smlee00. (2023). *STM32 Keyword Spotting with Edge Impulse*. GitHub. https://github.com/smlee00/STM32-Keyword-Spotting-with-Edge-Impulse

8. STMicroelectronics. (2025). *Human Activity Recognition*. STM32 AI Model Zoo. https://github.com/STMicroelectronics/stm32ai-modelzoo/tree/main/human_activity_recognition

9. OpenMV. (2025). *OpenMV N6*. https://openmv.io/products/openmv-cam-n6

10. matflow. (2019). *TinyML Gesture Recognition STM32*. GitHub. https://github.com/matflow/TinyML-gesture-recognition-STM32

---

*Дослідження проведено: 20 квітня 2025*
