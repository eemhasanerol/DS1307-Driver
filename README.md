# DS1307 Driver (C)

A simple C driver library for the **DS1307 Real-Time Clock (RTC)**.  
Tested on **STM32F407** with custom low-level drivers.

---

## 📌 Features
- I²C communication (with user-provided low-level functions)  
- Read **time** (hours, minutes, seconds)  
- Read **date** (day, month, year, day of week)  
- Supports **12h / 24h mode**  
- **AM/PM** handling in 12h mode  
- Day-of-week support (1–7, Sunday = 1)  

---

## 📌 Usage Example
```c
#include "ds1307.h"

static int32_t platform_i2c_read(uint8_t dev, uint8_t reg, uint8_t *buf, uint16_t len) {
    return (I2C_MemRead(&hi2c1, dev, reg, buf, len) == STATUS_OK) ? DS1307_OK : DS1307_E_COMM;
}

static int32_t platform_i2c_write(uint8_t dev, uint8_t reg, const uint8_t *buf, uint16_t len) {
    return (I2C_MemWrite(&hi2c1, dev, reg, buf, len) == STATUS_OK) ? DS1307_OK : DS1307_E_COMM;
}

int main(void) {
    ds1307_dev_t rtc = {
        .dev_addr  = DS1307_I2C_ADDR,
        .i2c_read  = platform_i2c_read,
        .i2c_write = platform_i2c_write
    };

    /* Set initial time */
    rtc.time.seconds     = 0;
    rtc.time.minutes     = 30;
    rtc.time.hours       = 10;
    rtc.time.day_of_week = 2;   // Monday
    rtc.time.date        = 15;
    rtc.time.month       = 9;
    rtc.time.year        = 25;  // 2025
    rtc.time.time_format = DS1307_HOUR_24H;
    rtc.time.meridiem    = DS1307_AM;

    if (ds1307_init(&rtc) != DS1307_OK) {
        while (1); /* error */
    }

    while (1) {
        if (ds1307_get_time(&rtc) == DS1307_OK) {
            printf("Time: %02d:%02d:%02d | Date: %02d/%02d/20%02d | DOW: %d\n",
                   rtc.time.hours, rtc.time.minutes, rtc.time.seconds,
                   rtc.time.date, rtc.time.month, rtc.time.year,
                   rtc.time.day_of_week);
        }
        SysTick_Delay_ms(1000);
    }
}
