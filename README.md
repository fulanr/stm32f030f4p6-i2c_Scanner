# stm32f030f4p6-i2c_Scanner
i2c Scanner for minimal and cheap STM32F030F4P6 board 

Programmer tool:
ST-Link V2
(You can also use serial port to program (hex upload))

Use: 
1. STM32CubeMX - (configuration)
2. Atollic TrueSTUDIO for STM32 (programming)


![pins configuration using STM32CubeMX](https://raw.githubusercontent.com/fulanr/stm32f030f4p6-i2c_Scanner/master/STM32F030F4P6-Dev-Board.jpg)



![pins configuration using STM32CubeMX](https://raw.githubusercontent.com/fulanr/stm32f030f4p6-i2c_Scanner/master/pinout.png)

If you want to configure the board yourself, in main.c generated by STM32CubeMX, add this codes:

```
  /* USER CODE BEGIN 2 */

  char uart1Data[25] = "Connected to UART1\r\n";
   /*
    * Output to uart1
    * use screen or putty or whatever terminal software
    * 8N1 115200
    */
  HAL_UART_Transmit(&huart1, (uint8_t *)&uart1Data,sizeof(uart1Data), 0xFFFF);

  char uart1DataA[25] = "Scanning I2C bus:\r\n";
  HAL_UART_Transmit(&huart1, (uint8_t *)&uart1DataA,sizeof(uart1DataA), 0xFFFF);

  HAL_StatusTypeDef result;
  uint8_t i;
  for (i=1; i<128; i++)
  {
	  /*
 	   * the HAL wants a left aligned i2c address
 	   * &hi2c1 is the handle
 	   * (uint16_t)(i<<1) is the i2c address left aligned
 	   * retries 2
 	   * timeout 2
 	   */
	  result = HAL_I2C_IsDeviceReady(&hi2c1, (uint16_t)(i<<1), 2, 2);
 	  if (result != HAL_OK) // HAL_ERROR or HAL_BUSY or HAL_TIMEOUT
 	  {
 		  // No ACK received at that address
 		  char uart1DataB[1] = ".";
 		  HAL_UART_Transmit(&huart1, (uint8_t *)&uart1DataB,sizeof(uart1DataB), 0xFFFF);

 	  }
 	  if (result == HAL_OK)
 	  {
 		  char uart1DataD[4];
 		  sprintf(uart1DataD, "0x%X", i); // Received an ACK at that address
 		  HAL_UART_Transmit(&huart1, (uint8_t *)&uart1DataD,sizeof(uart1DataD), 0xFFFF);

 	  }
 	}




  /* USER CODE END 2 */
```

example scanner output (detected address of 0x3C for my 128x32 oled i2c connection):

![putty serial output](https://raw.githubusercontent.com/fulanr/stm32f030f4p6-i2c_Scanner/master/putty.png)
