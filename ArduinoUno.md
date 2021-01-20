## Arduino Uno 

The Arduino Uno connector is a standardised connector in Mbed, which has a set amount of exposed functionality. To achieve meaningful portability of application code across various Mbed boards that are Arduino Uno compliant, the pin names used for the connector pins are common across these boards.

The following diagram shows the Arduino Uno standard for Mbed boards:
![Static pinmap model](https://raw.githubusercontent.com/ARMmbed/mbed-os/feature-std-pin-name/docs/design-documents/hal/img/Arduino_Uno_Mbed.png) 

**Digital and Analog pins**

The Arduino Uno connector for Mbed boards supports both digital and analog pins. Please note that the Arduino Uno only supports analog inputs and does not support analog output functionality. Digital pin functionality such as GPIO output or input on the Arduino Uno header can be accessed from any of the pins labelled as ARDUINO_UNO_D0 to ARDUINO_UNO_D15 and ARDUINO_UNO_A0 to ARDUINO_UNO_A5. The analog input functionality on the Arduino Uno connector can only be accessed on pins ARDUINO_UNO_A0 to ARDUINO_UNO_A5. The full digital and analog pin alias list for the Arduino Uno connector can be seen below.  

    ARDUINO_UNO_D0 
    ARDUINO_UNO_D1 
    ARDUINO_UNO_D2 
    ARDUINO_UNO_D3 
    ARDUINO_UNO_D4 
    ARDUINO_UNO_D5 
    ARDUINO_UNO_D6 
    ARDUINO_UNO_D7 
    ARDUINO_UNO_D8 
    ARDUINO_UNO_D9 
    ARDUINO_UNO_D10 
    ARDUINO_UNO_D11 
    ARDUINO_UNO_D12 
    ARDUINO_UNO_D13 
    ARDUINO_UNO_D14 
    ARDUINO_UNO_D15 
    
    ARDUINO_UNO_A0 
    ARDUINO_UNO_A1 
    ARDUINO_UNO_A2 
    ARDUINO_UNO_A3 
    ARDUINO_UNO_A4 
    ARDUINO_UNO_A5 
    
**Arduino Uno digital out example**

A simple example on how to create an Arduino Uno digital out pin has been included below for further information on digital pins. For more details about digital functionality, please check the Mbed API.
     
    /*
    * Copyright (c) 2006-2020 Arm Limited and affiliates.
    * SPDX-License-Identifier: Apache-2.0
    */

    #include "mbed.h"

    DigitalOut pin0(ARDUINO_UNO_D0);

    int main()
    {
        // check that pin0 object is initialized and connected to a pin
        if (pin0.is_connected()) {
            printf("pin0 is initialized and connected!\n\r");
        }

        //Toggle ARDUINO_UNO_D0 pin
        while (1) {
            pin0 = 1;          // set ARDUINO_UNO_D0 pin to high
            printf("pin0 = %d \n\r", (uint8_t)pin0);
            ThisThread::sleep_for(500);

            pin0.write(0);     // set ARDUINO_UNO_D0 pin to low
            printf("pin0 = %d \n\r", pin0.read());
            ThisThread::sleep_for(500);
        }
    }
 
**Arduino Uno analog in example**

A simple example on how to create an Arduino Uno analog in pin has been included below for further information on analog pins. For more details about analog functionality, please check the Mbed API.

    /*
     * Copyright (c) 2017-2020 Arm Limited and affiliates.
     * SPDX-License-Identifier: Apache-2.0
     */

    #include "mbed.h"

    //Initialise pins to perform analog input and digital output functions
    AnalogIn   ain0(ARDUINO_UNO_A0);
    DigitalOut dout(LED1);

    int main(void)
    {
        while (1) {
            // test the voltage on the initialized analog pin
            //  and if greater than 0.3 * VCC set the digital pin
            //  to a logic 1 otherwise a logic 0
            if (ain > 0.3f) {
                dout = 1;
            } else {
                dout = 0;
            }

            // print the percentage and 16 bit normalized values
            printf("percentage: %3.3f%%\n", ain.read() * 100.0f);
            printf("normalized: 0x%04X \n", ain.read_u16());
            ThisThread::sleep_for(200);
        }
    }
    
**SPI pins**

The Arduino Uno connector for Mbed boards supports SPI functionality on specific pins and can be accessed using the pin alias's below  

    ARDUINO_UNO_SPI_CS    or ARDUINO_UNO_D10
    ARDUINO_UNO_SPI_MOSI  or ARDUINO_UNO_D11
    ARDUINO_UNO_SPI_MISO  or ARDUINO_UNO_D12
    ARDUINO_UNO_SPI_SCK   or ARDUINO_UNO_D13

if you need more SPI chip selects(CS) for your application, these can be controlled using additional digital out pins with the alias explained previously on this page 

**Arduino Uno SPI example**

A simple example of how to use the SPI functionality on the Arduino Uno connector is given below. Please note that most Mbed boards have a genuine SPI CS which can be included in the SPI initialisation. However, if you need to use more chip selects, then a different pin on the Arduino Uno header can be used as a digital out pin. For more details about SPI, please check the Mbed API.

    /*
     * Copyright (c) 2006-2020 Arm Limited and affiliates.
     * SPDX-License-Identifier: Apache-2.0
     */
    #include "mbed.h"

    SPI spi(ARDUINO_UNO_SPI_MOSI, ARDUINO_UNO_SPI_MISO, ARDUINO_UNO_SPI_SCK); // mosi, miso, sclk 
    DigitalOut cs(ARDUINO_UNO_SPI_CS); // cs

    int main()
    {
        // Chip must be deselected
        cs = 1;

        // Setup the spi for 8 bit data, high steady state clock,
        // second edge capture, with a 1MHz clock rate
        spi.format(8, 3);
        spi.frequency(1000000);

        // Select the device by seting chip select low
        cs = 0;

        // Send 0x8f, the command to read the WHOAMI register
        spi.write(0x8F);

        // Send a dummy byte to receive the contents of the WHOAMI register
        int whoami = spi.write(0x00);
        printf("WHOAMI register = 0x%X\n", whoami);

        // Deselect the device
        cs = 1;
    }


**I2C pins**    

The Arduino Uno connector for Mbed boards supports I2C functionality on specific pins and can be accessed using the pin alias's below. 

    ARDUINO_UNO_I2C_SDA or ARDUINO_UNO_D14
    ARDUINO_UNO_I2C_SCL or ARDUINO_UNO_D15

**Arduino Uno I2C example**

A simple example on how to enable and use the I2C functionality on the Arduino Uno connector is given below. For more details about I2C, please check the Mbed API.

    /*
     * Copyright (c) 2014-2020 Arm Limited and affiliates.
     * SPDX-License-Identifier: Apache-2.0
     */

    #include "mbed.h"

    // Read temperature from LM75BD

    I2C i2c(ARDUINO_UNO_I2C_SDA, ARDUINO_UNO_I2C_SCL);

    const int addr7bit = 0x48;      // 7 bit I2C address
    const int addr8bit = 0x48 << 1; // 8bit I2C address, 0x90

    int main()
    {
        char cmd[2];
        while (1) {
            cmd[0] = 0x01;
            cmd[1] = 0x00;
            i2c.write(addr8bit, cmd, 2);

            ThisThread::sleep_for(500);

            cmd[0] = 0x00;
            i2c.write(addr8bit, cmd, 1);
            i2c.read(addr8bit, cmd, 2);

            float tmp = (float((cmd[0] << 8) | cmd[1]) / 256.0);
            printf("Temp = %.2f\n", tmp);
        }
    }

**UART pins**    

The Arduino Uno connector for Mbed boards supports UART functionality on specific pins and can be accessed using the pin alias's below 

    ARDUINO_UNO_UART_TX or ARDUINO_UNO_D1
    ARDUINO_UNO_UART_RX or ARDUINO_UNO_D0

For more details about UART, please check the Mbed API.

**PWM/Timer pins**    

In the Arduino Uno standard, there are only 6 PWM and timers available on pins ARDUINO_UNO_D3, ARDUINO_UNO_D5, ARDUINO_UNO_D6, ARDUINO_UNO_D9, ARDUINO_UNO_D10 and ARDUINO_UNO_D11. However, Mbed boards may support the usage of PWM and timers functions on other pins as well.

**Arduino Uno PWM out example**

A simple example on how to enable and use the PWM functionality on the Arduino Uno connector is given below. For more details about PWM and timers, please check the Mbed API.  
  
    /*
     * Copyright (c) 2017-2020 Arm Limited and affiliates.
     * SPDX-License-Identifier: Apache-2.0
     */

    #include "mbed.h"

    Use a PWM capable pin, and see the generated signal on  a logic analyser.
    PwmOut pwm0(ARDUINO_UNO_D3);

    int main()
    {
        // specify period first, then everything else
        pwm0.period(4.0f);  // 4 second period
        pwm0.write(0.50f);  // 50% duty cycle
        while (1);          // ARDUINO_UNO_D3 should be producing a PWM output signal
    }
