[![Foo](https://img.shields.io/badge/Version-1.0-brightgreen.svg?style=flat-square)](#versions)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD$%E2%82%AC%20%D0%9D%D0%B0%20%D0%BF%D0%B8%D0%B2%D0%BE-%D1%81%20%D1%80%D1%8B%D0%B1%D0%BA%D0%BE%D0%B9-orange.svg?style=flat-square)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/badge/README-ENGLISH-blueviolet.svg?style=flat-square)](https://github-com.translate.goog/GyverLibs/SevenSegmentsDisp?_x_tr_sl=ru&_x_tr_tl=en) 
[![Foo](https://img.shields.io/badge/ПОДПИСАТЬСЯ-НА%20ОБНОВЛЕНИЯ-brightgreen.svg?style=social&logo=telegram&color=blue)](https://t.me/GyverLibs)


# SevenSegmentsDisp
**Библиотека в активной разработке, планируется поддержка большинства модулей дисплеев.**   
Быстрая и функциональная библиотка для модулей семисегментного дисплея TM74HC595
построенных на базе одноименных сдвиговых регистров, без динамической индикации.
Для обеспечения нормальной работы необходимо вручную обслуживать динамическую
индикацию дисплея, вызывая метод .tickManual() с периодом 500-5000 мкс, используя 
прерывания таймера или .tick() в чистом от блокирующих функций loop.
- Обслуживание динамчиеской индикации встроенным таймером на micros()
- Ручное обслуживание динамчиеской индикации при необходимости  
- Поддержка чисел со знаком минус
- Вывод int с нулями и без нулей слева
- Вывод float с указанной точностью с нулями слева и без
- Вывод float с автоматическим выбором точности
- Вывод символа на нужную позицию, не трогает остальные знакоместа
- Вывод массива символов (0 - 4 шт.) с выравниванием по левому / правому краю
- Вывод 4х символов на дисплей вручную  
- Вывод бегущих строк (блокирующая функция)
- Управление любой из точек дисплея
- Очистка дисплея
- Обнаружение и вывод ошибки переполнения дисплея (-OVF и OVF)
- Программная регулирвока яркости дисплея

### Совместимость
Совместима со всеми Arduino платформами (используются Arduino-функции)

## Содержание
- [Установка](#install)
- [Инициализация](#init)
- [Использование](#usage)
- [Пример](#example)
- [Версии](#versions)
- [Баги и обратная связь](#feedback)

<a id="install"></a>
## Установка
- Библиотеку можно найти по названию **SevenSegmentsDisp** и установить через менеджер библиотек в:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Скачать библиотеку](https://github.com/GyverLibs/SevenSegmentsDisp/archive/refs/heads/main.zip) .zip архивом для ручной установки:
    - Распаковать и положить в *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Распаковать и положить в *C:\Program Files\Arduino\libraries* (Windows x32)
    - Распаковать и положить в *Документы/Arduino/libraries/*
    - (Arduino IDE) автоматическая установка из .zip: *Скетч/Подключить библиотеку/Добавить .ZIP библиотеку…* и указать скачанный архив
- Читай более подробную инструкцию по установке библиотек [здесь](https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Обновление
- Рекомендую всегда обновлять библиотеку: в новых версиях исправляются ошибки и баги, а также проводится оптимизация и добавляются новые фичи
- Через менеджер библиотек IDE: найти библиотеку как при установке и нажать "Обновить"
- Вручную: **удалить папку со старой версией**, а затем положить на её место новую. "Замену" делать нельзя: иногда в новых версиях удаляются файлы, которые останутся при замене и могут привести к ошибкам!


<a id="init"></a>
## Инициализация
```cpp
// указать пины
Disp595 (uint8_t dio, uint8_t sclk, uint8_t rclk);
```

<a id="usage"></a>
## Использование
```cpp
void tick(void);												// Обслуживание динамической индикации со встроенным таймером на micros()
void tickManual(void);											// Обслуживание динамической индикации вручную (для помещения в прерывание таймера и т.д.)  	
void displayIntNulls(int16_t data);								// Вывод int с нулями слева
void displayInt(int16_t data);									// Вывод int без нулей слева
void displayFloatNulls(float data, uint8_t base);				// Вывод float с нулями слева, указанием кол-ва знаков (1 - 3)
void displayFloatNulls(float data);							    // Вывод float с нулями слева, 1 знак после запятой 
void displayFloat(float data, uint8_t base);				    // Вывод float без нулей слева, указанием кол-ва знаков (1 - 3) 
void displayFloat(float data);				                    // Вывод float без нулей слева, 1 знак после запятой 
void displayByte(uint8_t a, uint8_t b, uint8_t c, uint8_t d);	// Прямой вывод 4х байтов (символов) на дисплей
void point(uint8_t pos, bool state);							// Добавление / удаление точки на позиции 0-3 (слева)
void clear(void);												// Очистка дисплея		
void brightness(uint8_t bright)									// Установка значения яркости (0 - 7), по умолчанию максимум (если обьявлена DISP595_BRIGHTNESS) 
void runningString(uint8_t *str, uint8_t size, uint16_t ms)	    // Вывод бегущей строки размера size, с задержкой ms, внутри функции tick НЕ ОБСЛУЖИВАЕТСЯ
void runningString(uint8_t *str, uint8_t size, uint16_t ms, bool useTick);	// Аналогично, но можно вручную включить / выключить встроенный в функцию tick()

Дефайны, которые можно обьявить ПЕРЕД подключением библиотеки:
DISP595_BRIGHTNESS -> Обьявить для подключения программной регулирвоки яркости дисплея
DISP595_BRIGHTNESS_DEPTH число -> Глубина программной яркости, по умолчанию 8 градаций, больше - плавнее регулировка, сильнее мерацание дисплея без повышения частоты индикации.
DISP595_FAST_PERIOD число -> Период в мкс динамической индикации во втроенном таймере метода tick для режима С ИСПОЛЬЗОВАНИЕМ РЕГУЛИРОВКИ ЯРКОСТИ 
DISP595_SLOW_PERIOD число -> То же самое, но для стандартного режима (постоянная яркость)

Константы:
ALIGN_LEFT - выравнивание неполной строки по левому краю
ALIGN_RIGHT - выравнивание неполной строки по правому краю
```

<a id="example"></a>
## Пример
Остальные примеры смотри в **examples**!
```cpp
/*
   Демонстрация всего функционала, вывод разных данных на дисплей TM74HC595
   Обслуживание динамической индикации берет на себя аппаратный Timer 2
   https://alexgyver.ru/lessons/timer-isr/
*/

#define DATA_PIN   11
#define SCLK_PIN   10
#define RCLK_PIN   12
#define DISP_PERIOD 3000  // Период динамической индикации в мкс (500-6000)
// #define DISP_PERIOD 500  // Если используется регулировка яркости, дергать tick() надо чаще!

// #define DISP595_BRIGHTNESS           // Использовать регулировку яркости
// #define DISP595_BRIGHTNESS_DEPTH 10  // Глубину яркости можно изменить (по умолч. 8), больше - плавнее яркость, но сильнее мерцание дисплея
        
#include <GyverTimers.h>
#include <SevenSegmentsDisp.h>

Disp595 disp(DATA_PIN, SCLK_PIN, RCLK_PIN);

void setup() {
  Timer2.setPeriod(DISP_PERIOD);  // Заводим Timer 2 на прерывания с нужным периодом
  Timer2.enableISR();             // Включаем прерывания Timer 2
}

void loop() {

  /* Вывод встроенных и кастомных символов, сырых данных на элеменыт дисплея */
  disp.displayBytes(_H, _E, _L, _P); // Прямой вывод данных (символов) на дисплей
  disp.point(0, true);               // ПОСЛЕ вывода данных можно добавить / удалить точки
  disp.point(3, true);               // Указываем позицию точки слева (0 - 3) и включаем / выключаем ее (true / false)
  delay(1000);

  /*
     Программное управление яркостью дисплея
     Чем больше глубина яркости - тем чаще нужно дергать tick()!
     (если DISP595_BRIGHTNESS обьявлен перед подключением)
  */
  /*
    disp.brightnessDepth(8); // Установка "глубины" яркости - количества градаций, по умолчанию 8 градаций

    for (uint8_t i = 7; i > 0; i--) {
      disp.brightness(i);               // Яркость в диапазоне 0-7
      delay(500);
    }

    delay(1000);

    for (uint8_t i = 0; i < 8; i++) {
      disp.brightness(i);               // Яркость в диапазоне 0-7
      delay(500);
    }
  */


  /* Вывод одиночных символов на нужную позицию СЛЕВА */
  disp.displayByte(_L, 3);           // Заменяем 3й символ с 'P' на 'L'
  delay(1000);


  /* Вывод массива символов на дисплей с выравниванием по левому / правому краю */
  uint8_t data[] = {_D, _A};
  disp.clear();
  disp.displayBytes(data, sizeof(data));                // Вывод массива символов с выравниванием по левому краю
  delay(1000);

  disp.clear();
  disp.displayBytes(data, sizeof(data), ALIGN_RIGHT);   // Вывод массива символов с выравниванием по правому краю
  delay(1000);

  disp.clear();
  disp.displayBytes(data, sizeof(data), ALIGN_LEFT);    // Вывод массива символов с выравниванием по левому краю
  delay(1000);


  /* Вывод бегущих строк */
  uint8_t str[] = {_1, _2, _3, _4, _5, _empty, _t, _E, _S, _t};
  disp.runningString(str, sizeof(str), 300); // Вывод бегущей строки, указываем массив символов, размер, задержку в мс


  /* Вывод целых чисел без нулей слева */
  for (int8_t i = -50; i < 50; i++) {
    disp.displayInt(i);
    delay(100);
  }


  /* Вывод целых чисел c нулями слева */
  for (int8_t i = 50; i > -50; i--) {
    disp.displayIntNulls(i);
    delay(100);
  }
  
  /* Вывод чисел с плавающей точкой без нулей слева */
  disp.displayFloat(-12.34);      // Без указания точности, дробных знаков - 1
  delay(1000);
  disp.displayFloat(-12.34, 1);   // Или же кол-во знаков можно ввести вручную, от 1 до 3
  delay(1000);
  disp.displayFloat(12.34, 2);    // Выводить можно как положительные, так и отрицательные числа
  delay(1000);
  disp.displayFloat(1.234, 3);    // Но при выводе 3х десятичных знаков, места под знак минуса не достаточно
  delay(1000);


  /* Вывод чисел с плавающей точкой с нулями слева */
  disp.displayFloat(-3.14);       // Тут все аналоично, но с нулями слева
  delay(1000);
  disp.displayFloat(-1.34, 1);
  delay(1000);
  disp.displayFloat(3.1415, 2);
  delay(1000);
  disp.displayFloat(3.1415, 3);
  delay(1000);


  /* Вывод числа + символов */
  for (uint8_t i = 0; i < 30; i++) {
    int value = random(0, 99);
    disp.displayInt(value);         // СНАЧАЛА выводим число int
    disp.displayByte(_t, 0);        // Пустые символы можем вручную заполнить символами
    disp.displayByte(_equal, 1);
    delay(200);
  }


  /* Вывод числа + массива символов */
  uint8_t strData[] = {_P, _H};
  disp.displayFloat(1.2, 1);                     // СНАЧАЛА выводим число float (заняли всего 2 символа)
  disp.displayBytes(strData, sizeof(strData));   // Пустые символы можем заполнить массивом символов
  delay(1000);

  /* Очистка дисплея */
  disp.clear();                    // Буфер дисплея очищается
  delay(3000);
}

ISR(TIMER2_A) {        // Прерывание Timer 2
  disp.tickManual();   // Обслуживание динамической индикации дисплея
}
```

<a id="versions"></a>
## Версии
- v1.0

<a id="feedback"></a>
## Баги и обратная связь
При нахождении багов создавайте **Issue**, а лучше сразу пишите на почту [alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
Библиотека открыта для доработки и ваших **Pull Request**'ов!


При сообщении о багах или некорректной работе библиотеки нужно обязательно указывать:
- Версия библиотеки
- Какой используется МК
- Версия SDK (для ESP)
- Версия Arduino IDE
- Корректно ли работают ли встроенные примеры, в которых используются функции и конструкции, приводящие к багу в вашем коде
- Какой код загружался, какая работа от него ожидалась и как он работает в реальности
- В идеале приложить минимальный код, в котором наблюдается баг. Не полотно из тысячи строк, а минимальный код
