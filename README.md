# stm32h743zi_ethernet_lwip

Nucleo STM32H743 을 이용한 Ethernet 예제입니다.

1. ethernetif.c RAM link script 설정

  .lwip_sec (NOLOAD) : {
    . = ABSOLUTE(0x30040000);
    *(.RxDecripSection) 
    
    . = ABSOLUTE(0x30040060);
    *(.TxDecripSection)
    
    . = ABSOLUTE(0x30040200);
    *(.RxArraySection) 
  } >RAM_D2 AT> FLASH
    

2. MPU_Config

static void MPU_Config(void)
{
  MPU_Region_InitTypeDef MPU_InitStruct;

  /* Disable the MPU */
  HAL_MPU_Disable();

  /* Configure the MPU attributes as Device not cacheable
     for ETH DMA descriptors */
     
  MPU_InitStruct.Enable = MPU_REGION_ENABLE;
  
  MPU_InitStruct.BaseAddress = 0x30040000;
  
  MPU_InitStruct.Size = MPU_REGION_SIZE_256B;
  
  MPU_InitStruct.AccessPermission = MPU_REGION_FULL_ACCESS;
  
  MPU_InitStruct.IsBufferable = MPU_ACCESS_BUFFERABLE;
  
  MPU_InitStruct.IsCacheable = MPU_ACCESS_NOT_CACHEABLE;
  
  MPU_InitStruct.IsShareable = MPU_ACCESS_NOT_SHAREABLE;
  
  MPU_InitStruct.Number = MPU_REGION_NUMBER0;
  
  MPU_InitStruct.TypeExtField = MPU_TEX_LEVEL0;
  
  MPU_InitStruct.SubRegionDisable = 0x00;
  
  MPU_InitStruct.DisableExec = MPU_INSTRUCTION_ACCESS_ENABLE;

  HAL_MPU_ConfigRegion(&MPU_InitStruct);

  /* Configure the MPU attributes as Normal Non Cacheable
     for LwIP RAM heap which contains the Tx buffers */
  MPU_InitStruct.Enable = MPU_REGION_ENABLE;
  
  MPU_InitStruct.BaseAddress = 0x30044000;
  
  MPU_InitStruct.Size = MPU_REGION_SIZE_16KB;
  
  MPU_InitStruct.AccessPermission = MPU_REGION_FULL_ACCESS;
  
  MPU_InitStruct.IsBufferable = MPU_ACCESS_NOT_BUFFERABLE;
  
  MPU_InitStruct.IsCacheable = MPU_ACCESS_NOT_CACHEABLE;
  
  MPU_InitStruct.IsShareable = MPU_ACCESS_SHAREABLE;
  
  MPU_InitStruct.Number = MPU_REGION_NUMBER1;
  
  MPU_InitStruct.TypeExtField = MPU_TEX_LEVEL1;
  
  MPU_InitStruct.SubRegionDisable = 0x00;
  
  MPU_InitStruct.DisableExec = MPU_INSTRUCTION_ACCESS_ENABLE;
  

  HAL_MPU_ConfigRegion(&MPU_InitStruct);
  

  /* Enable the MPU */
  HAL_MPU_Enable(MPU_PRIVILEGED_DEFAULT);
  
}


3. while 문에 MX_LWIP_Process(); 추가

4. system_stm32h7xx.c 에 #define DATA_IN_D2_SRAM 추가






    
