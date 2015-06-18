# Preliminary performance data

## Test for NXP LPC1768
This is a classic Cortex-M3 with 512Kb NOR Flash and 32Kb SRAM. It runs at 96MHz.  

We compared toggling a pin through  .NET Micro Framework, the Mbed GPIO API using the online Mbed compiler,and using Zelig with minimal optimizations (*). 

## .NET Micro Framework
.NET MF does not run on the LPC1768 processor, for lack of RAM. We tried on an STM32 Cortex-M4, at 168Mhz, a much faster Cortex.

For .NET Micro Framework the application looks like the following:  



```
  public class Program
  {
      public static void Main()
      {
          OutputPort pin = new OutputPort( Cpu.Pin.GPIO_Pin1, true );
 
          while( true )
          {
              pin.Write( true  );
              pin.Write( false );
          }
      }          
  }
```

Measured performance are at **11.85Khz**. 



## Mbed
For Mbed the code is pretty simple:


```
  #include "mbed.h"  
 
  DigitalOut myOut(P0_0);  
 
  int main()  
  {  
      while(1) {
          myOut = 1;
          myOut = 0;
      }  
  }
```


The measured toggling speed is slightly below **3.85 Mhz**. 

## Zelig
For Zelig we easily wrap Mbed API in a managed object and interop to native code

The application looks like the following: 


```
  class Program   
  {   
      static void Main()   
      {   
          var dOut = new DigitalOut( PinName.D8 );   

          while(true)   
          {   
              dOut.write( 1 );   
              dOut.write( 0 );   
          }   
      }   
  }  


  public class DigitalOut   
  {   
      unsafe public DigitalOut( PinName pin )   
      {
          fixed( GPIOimpl** gpio_ptr = &gpio )   
          {   
              GPIO.tmp_gpio_alloc(gpio_ptr);   
          }   
   
          GPIO.gpio_init_out(gpio, pin);   
      }   

      unsafe public void write(int value)   
      {   
          GPIO.tmp_gpio_write(gpio, value);      
      }   

      private unsafe GPIOimpl* gpio;   
  }   

  public struct GPIO   
  {   

      [DllImport("C")]      
      public static unsafe extern void tmp_gpio_write(GPIOimpl* obj, int value);   

      [DllImport("C")]   
      public static unsafe extern void gpio_init(GPIOimpl* obj, PinName pin);   

      [DllImport("C")]   
      public static unsafe extern int tmp_gpio_alloc(GPIOimpl** obj);
  }
``` 


The measured performance is **3.45Mhz**. 

Please note that Zelig can interoperate at any level# Preliminary performance data

## Test for NXP LPC1768
This is a classic Cortex-M3 with 512Kb NOR Flash and 32Kb SRAM. It runs at 96MHz.  

We compared toggling a pin through  .NET Micro Framework, the Mbed GPIO API using the online Mbed compiler,and using Zelig with minimal optimizations (*). 

## .NET Micro Framework
.NET MF does not run on the LPC1768 processor, for lack of RAM. We tried on an STM32 Cortex-M4, at 168Mhz, a much faster Cortex.

For .NET Micro Framework the application looks like the following:  



```
  public class Program
  {
      public static void Main()
      {
          OutputPort pin = new OutputPort( Cpu.Pin.GPIO_Pin1, true );
 
          while( true )
          {
              pin.Write( true  );
              pin.Write( false );
          }
      }          
  }
```

Measured performance are at 11.85Khz. 



## Mbed
For Mbed the code is pretty simple:


```
  #include "mbed.h"  
 
  DigitalOut myOut(P0_0);  
 
  int main()  
  {  
      while(1) {
          myOut = 1;
          myOut = 0;
      }  
  }
```


The measured toggling speed is slightly below **3.85 Mhz**. 

## Zelig
For Zelig we easily wrap Mbed API in a managed object and interop to native code

The application looks like the following: 


```
  class Program   
  {   
      static void Main()   
      {   
          var dOut = new DigitalOut( PinName.D8 );   

          while(true)   
          {   
              dOut.write( 1 );   
              dOut.write( 0 );   
          }   
      }   
  }  


  public class DigitalOut   
  {   
      unsafe public DigitalOut( PinName pin )   
      {
          fixed( GPIOimpl** gpio_ptr = &gpio )   
          {   
              GPIO.tmp_gpio_alloc(gpio_ptr);   
          }   
   
          GPIO.gpio_init_out(gpio, pin);   
      }   

      unsafe public void write(int value)   
      {   
          GPIO.tmp_gpio_write(gpio, value);      
      }   

      private unsafe GPIOimpl* gpio;   
  }   

  public struct GPIO   
  {   

      [DllImport("C")]      
      public static unsafe extern void tmp_gpio_write(GPIOimpl* obj, int value);   

      [DllImport("C")]   
      public static unsafe extern void gpio_init(GPIOimpl* obj, PinName pin);   

      [DllImport("C")]   
      public static unsafe extern int tmp_gpio_alloc(GPIOimpl** obj);
  }
``` 


The measured performance is **3.45Mhz** !!  



### \(\*\) LLVM Optimizations
Only high level optimization and then LLVM with the following optimizations:

_%LLVM_BIN%\opt -scalarrepl -targetlibinfo -verify -mem2reg -constmerge -adce -globaldce -time-passes Microsoft.Zelig.Test.mbed.Simple.bc -o Microsoft.Zelig.Test.mbed.Simple_opt.bc_

_%LLVM_BIN%\llc -code-model=small -data-sections -relocation-model=pic -march=thumb -mcpu=cortex-m3 -filetype=obj -mtriple=Thumb-NoSubArch-UnknownVendor-UnknownOS-GNUEABI-ELF -o=Microsoft.Zelig.Test.mb_
_ed.Simple_opt.o Microsoft.Zelig.Test.mbed.Simple_opt.bc_



### \(\*\) LLVM Optimizations
Only high level optimization and then LLVM with the following optimizations:

_%LLVM_BIN%\opt -scalarrepl -targetlibinfo -verify -mem2reg -constmerge -adce -globaldce -time-passes Microsoft.Zelig.Test.mbed.Simple.bc -o Microsoft.Zelig.Test.mbed.Simple_opt.bc_

_%LLVM_BIN%\llc -code-model=small -data-sections -relocation-model=pic -march=thumb -mcpu=cortex-m3 -filetype=obj -mtriple=Thumb-NoSubArch-UnknownVendor-UnknownOS-GNUEABI-ELF -o=Microsoft.Zelig.Test.mb_
_ed.Simple_opt.o Microsoft.Zelig.Test.mbed.Simple_opt.bc_
