---
title: CubeMX之PWM
date: 2019-10-26 16:31:05
tags:
- 嵌入式搬砖手册
categories:
- 学习
- 嵌入式
- CubeMX
---
使能一个PWM，并产生一个呼吸灯。  
<!--more-->
脉冲宽度调制（PWM），是英文“Pulse Width Modulation”的缩写，简称脉宽调试。是利用微处理器的数字输出来对模拟电路进行控制的一种非常有效的技术。广泛应用在从测量、通信到功率控制与变换的许多领域中。  
stm32中有定时器会不停的自加，直到等于Pulse值就会变为低电平，而当等于ARR寄存器(Period)的值时，定时器就会重新清零，不停的循环往复。  
更详细的原理本文不再叙述，涉及到的PWM模式问题以及死区、DMA等等问题，之后单独开一篇文章讲述，这里不再叙述。  
对于PWM更详细概念在[PWM的概念]()一节中进行叙述，这里只讲解如何使用。  
同[使能一个GPIO章节](https://www.qm-k.xyz/2019/10/25/CubeMX%E4%B9%8BGPIO/)的配置,创建工程、使能外部高速晶振、配置时钟树，最后打开Timers，选择要用来输出PWM的定时器，并选择要输出的通道，一般一个定时器都有四个通道(当然也有两通道的，或者不能用作输出的)：  
{%asset_img Snipaste_2019-11-01_19-35-44.jpg %}  
只有高级定时器可以输出PWM，普通定时器是不能输出的，这个在cubemx中可以很明确的看出来，像下面这个就是不能用作输出的，只能用作定时：  
{%asset_img Snipaste_2019-11-01_19-39-15.jpg %}  
下边主要讲一下配置PWM输出的参数：  
{%asset_img Snipaste_2019-11-01_19-54-40.jpg %}  
由于我使用的板子是F407的，配置系统时钟为168MHz，通过查看`stm32f407xx.h`，可以确定TIM1挂载在APB2,时钟频率为168MHz。  
{%asset_img Snipaste_2019-11-01_20-02-10.jpg %}  
{%asset_img Snipaste_2019-11-01_20-08-50.jpg %}  
此处设置为1680分频。经过分频后的时钟频率为100000Hz，若要设置PWM周期为20ms,则ARR寄存器的值为2000-1。其他参数为默认不用修改。其中Pulse的为设置脉宽，即为捕获/比较寄存器（TIMx_CCRx）。通过修改它的值可以修改占空比。
{%asset_img Snipaste_2019-11-01_20-47-40.jpg %}  
生成代码，添加应用程序：  
在主函数中可以看到有`MX_TIM1_Init()`函数，右键`go to definition`,可以看到初始化函数(固件库版本不同可能不一致，但是不影响，原理一样)：  
```
/* TIM1 init function */
void MX_TIM1_Init(void)
{
  TIM_MasterConfigTypeDef sMasterConfig = {0};
  TIM_OC_InitTypeDef sConfigOC = {0};
  TIM_BreakDeadTimeConfigTypeDef sBreakDeadTimeConfig = {0};

  htim1.Instance = TIM1;
  htim1.Init.Prescaler = 1680-1;
  htim1.Init.CounterMode = TIM_COUNTERMODE_UP;
  htim1.Init.Period = 20000-1;          //这里为了让波形更明显调大了值，真实使用不会这莫大，要根据实际的控制频率来设置
  htim1.Init.ClockDivision = TIM_CLOCKDIVISION_DIV1;
  htim1.Init.RepetitionCounter = 0;
  htim1.Init.AutoReloadPreload = TIM_AUTORELOAD_PRELOAD_DISABLE;
  if (HAL_TIM_PWM_Init(&htim1) != HAL_OK)
  {
    Error_Handler();
  }
  sMasterConfig.MasterOutputTrigger = TIM_TRGO_RESET;
  sMasterConfig.MasterSlaveMode = TIM_MASTERSLAVEMODE_DISABLE;
  if (HAL_TIMEx_MasterConfigSynchronization(&htim1, &sMasterConfig) != HAL_OK)
  {
    Error_Handler();
  }

//就是这一段，很重要
  sConfigOC.OCMode = TIM_OCMODE_PWM1;
  sConfigOC.Pulse = 0;
  sConfigOC.OCPolarity = TIM_OCPOLARITY_HIGH;
  sConfigOC.OCNPolarity = TIM_OCNPOLARITY_HIGH;
  sConfigOC.OCFastMode = TIM_OCFAST_DISABLE;
  sConfigOC.OCIdleState = TIM_OCIDLESTATE_RESET;
  sConfigOC.OCNIdleState = TIM_OCNIDLESTATE_RESET;
  if (HAL_TIM_PWM_ConfigChannel(&htim1, &sConfigOC, TIM_CHANNEL_1) != HAL_OK)
  {
    Error_Handler();
  }
//

  sBreakDeadTimeConfig.OffStateRunMode = TIM_OSSR_DISABLE;
  sBreakDeadTimeConfig.OffStateIDLEMode = TIM_OSSI_DISABLE;
  sBreakDeadTimeConfig.LockLevel = TIM_LOCKLEVEL_OFF;
  sBreakDeadTimeConfig.DeadTime = 0;
  sBreakDeadTimeConfig.BreakState = TIM_BREAK_DISABLE;
  sBreakDeadTimeConfig.BreakPolarity = TIM_BREAKPOLARITY_HIGH;
  sBreakDeadTimeConfig.AutomaticOutput = TIM_AUTOMATICOUTPUT_DISABLE;
  if (HAL_TIMEx_ConfigBreakDeadTime(&htim1, &sBreakDeadTimeConfig) != HAL_OK)
  {
    Error_Handler();
  }
  HAL_TIM_MspPostInit(&htim1);

}

```
上边那一串代码中用注释框起来的代码就是我们在应用程序中要用的代码了。
```
	void user_pwm_setvalue(uint16_t value)
{
    TIM_OC_InitTypeDef sConfigOC;
    sConfigOC.OCMode = TIM_OCMODE_PWM1;
    sConfigOC.Pulse = value;                //Pulse就是用于修改脉冲宽度的变量，其他的直接照着初始化代码复制即可
    sConfigOC.OCPolarity = TIM_OCPOLARITY_HIGH;
    sConfigOC.OCNPolarity = TIM_OCNPOLARITY_HIGH;
    sConfigOC.OCFastMode = TIM_OCFAST_DISABLE;
    sConfigOC.OCIdleState = TIM_OCIDLESTATE_RESET;
    sConfigOC.OCNIdleState = TIM_OCNIDLESTATE_RESET;
    if (HAL_TIM_PWM_ConfigChannel(&htim1, &sConfigOC, TIM_CHANNEL_1) != HAL_OK)
    {
      Error_Handler();
    }
    HAL_TIM_PWM_Start(&htim1, TIM_CHANNEL_1);//开启PWM输出，在cubemx自动生成的初始化中是
                                             //不会默认使能输出的，这一点一定要注意  
}
```
在main()函数中添加HAL_TIM_PWM_Start(&htim4, TIM_CHANNEL_1)开启定时器PWM输出。在while循环中不断修改脉宽
```
  /* Initialize all configured peripherals */
  MX_GPIO_Init();
  MX_TIM1_Init();
  /* USER CODE BEGIN 2 */
	HAL_TIM_PWM_Start(&htim1,TIM_CHANNEL_1);
  /* USER CODE END 2 */

  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
		HAL_Delay(10);                  //如果要呼吸灯效果是不能这么短的延时的，这个延时长度只是为了让波形更明显
		if(pwm_value == 0) step = 500;
		if(pwm_value == 20000) step = -500; //这里同样是为了让波形更明显
		pwm_value += step;
		user_pwm_setvalue(pwm_value);
  }
  /* USER CODE END 3 */
```
此时可以对输出引脚进行测量，如果不知道是哪一个引脚可以看CubeMX右侧的Pinout view，这里不再叙述，方法很多。  
如果所有操作没问题，就可以看到：
{%asset_img ADS00003.BMP %}  
此时在对应管脚上接入一个led就可以看到呼吸灯的效果(此时应修改上边几处标注的值才会有一个好效果)。  
会出现像下边这样比较规则且逐渐增大的波形：
{%asset_img ADS00002.BMP %}  