# DS1307 Driver (C)

A simple C driver library for the **DS1307 RTC (Real-Time Clock) module**.  
Tested on **STM32F407** with custom low-level drivers.

---

## 📌 Features
- I²C communication (with user-provided low-level functions)  
- Read and write **time & date**  
- 12h or 24h format support  
- Day of week tracking  
- AM/PM support in 12h mode  
- Basic RAM access (0x08–0x3F)  

---

## 📌 Usage Example
```c
#include "ds1307.h"

ds1307_dev_t rtc = {
    .dev_addr  = DS1307_I2C_ADDR,
    .i2c_read  = platform_i2c_read,
    .i2c_write = platform_i2c_write,
    .time = {
        .seconds     = 0,
        .minutes     = 30,
        .hours       = 10,
        .day_of_week = DS1307_MONDAY,
        .date        = 15,
        .month       = 9,
        .year        = 25,   // 2025
        .time_format = DS1307_HOUR_24H,
        .meridiem    = DS1307_AM
    }
};

if (ds1307_init(&rtc) == DS1307_OK) {
    while (1) {
        if (ds1307_get_time(&rtc) == DS1307_OK) {
            printf("%02u:%02u:%02u %02u/%02u/20%02u DOW:%u\n",
                   rtc.time.hours,
                   rtc.time.minutes,
                   rtc.time.seconds,
                   rtc.time.date,
                   rtc.time.month,
                   rtc.time.year,
                   rtc.time.day_of_week);
        }
    }
}
