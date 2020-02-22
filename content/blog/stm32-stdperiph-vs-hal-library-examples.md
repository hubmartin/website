---
title: "STM32 stdperiph vs HAL library example"
date: 2018-03-18T12:27:38+06:00
description : "This is meta description"
type: post
author: Martin Hubáček
tags: ["STM32"]
---

STM created new HAL libraries which could be used instead of Standard Peripheral Library. Sometimes there's no choice and you have to use the HAL. The new HAL lib is little more complicated but you have that feeling everytime you see something new. Some functionality in HAL it's not even implemented so I had to add som basic functions myself.

<!--more-->


### Stdperiph UART example

```
void uart1_init(void)
{
    USART_InitTypeDef USART_InitStructure;
    NVIC_InitTypeDef NVIC_InitStructure;
    GPIO_InitTypeDef GPIO_InitStructure;

    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB | RCC_APB2Periph_USART1 | RCC_APB2Periph_AFIO, ENABLE);

    //  Tx
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOB, &GPIO_InitStructure);

    // Rx
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_7;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
    GPIO_Init(GPIOB, &GPIO_InitStructure);


    // UART1 Init
    // You have to call StructInit function to init unused structure           // parameters because it was created on the stack
    USART_StructInit(&USART_InitStructure);
    USART_InitStructure.USART_BaudRate = 115200;
      USART_InitStructure.USART_WordLength = USART_WordLength_8b;
      USART_InitStructure.USART_StopBits = USART_StopBits_1;
      USART_InitStructure.USART_Parity = USART_Parity_No;
      USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;
      USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;
    USART_Init(USART1, &USART_InitStructure);

    USART_Cmd(USART1, ENABLE);

    USART_ITConfig(USART1, USART_IT_RXNE, ENABLE);

    /* Clear the SC_EXTI IRQ Pendinpbg Bit */
    NVIC_ClearPendingIRQ(USART1_IRQn);


      NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn;
      NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;
      NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;
      NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
      NVIC_Init(&NVIC_InitStructure);

}
```

### STM32 HAL uart example

```
// Apparently this init structure in HAL has now to be STATIC
// (not in function/on the stack)
UART_HandleTypeDef UartHandle;

// This function inits GPIO and it's called by HAL Lib
// in HAL_UART_Init by by the function uart1_init() below
HAL_UART_MspInit(UART_HandleTypeDef *huart)
{
  GPIO_InitTypeDef  GPIO_InitStruct;

  __HAL_RCC_GPIOA_CLK_ENABLE()
  __HAL_RCC_USART1_CLK_ENABLE()

  /* UART TX GPIO pin configuration  */
  GPIO_InitStruct.Pin       = GPIO_PIN_9;
  GPIO_InitStruct.Mode      = GPIO_MODE_AF_PP;
  GPIO_InitStruct.Pull      = GPIO_PULLUP;
  GPIO_InitStruct.Speed     = GPIO_SPEED_FREQ_HIGH;
  GPIO_InitStruct.Alternate = GPIO_AF7_USART1;

  HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);

  /* UART RX GPIO pin configuration  */
  GPIO_InitStruct.Pin = GPIO_PIN_10;
  GPIO_InitStruct.Alternate = GPIO_AF7_USART1;

  HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);

}
void uart1_init(void)
{
      UartHandle.Instance        = USART1;

      UartHandle.Init.BaudRate   = 115200;
      UartHandle.Init.WordLength = UART_WORDLENGTH_8B;
      UartHandle.Init.StopBits   = UART_STOPBITS_1;
      UartHandle.Init.Parity     = UART_PARITY_NONE;
      UartHandle.Init.HwFlowCtl  = UART_HWCONTROL_NONE;
      UartHandle.Init.Mode       = UART_MODE_TX_RX;
      UartHandle.AdvancedInit.AdvFeatureInit = UART_ADVFEATURE_NO_INIT;

      // Init function calls internally user function HAL_UART_MspInit
      // which is written below
      if(HAL_UART_Init(&UartHandle) != HAL_OK)
      {
        Error_Handler();
      }

      __HAL_UART_ENABLE_IT(&UartHandle, UART_IT_RXNE);
      HAL_NVIC_SetPriority(USART1_IRQn, 0, 0);
      HAL_NVIC_EnableIRQ(USART1_IRQn);
}

```