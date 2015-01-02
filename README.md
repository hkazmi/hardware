Hardware
========

How to use robotiky brain.

I'll fill out the details of the brain in more detail in future. But just to get you started create an account on https://developer.mbed.org/account/signup. Then create a new program for the LPC11U24 and use the below as a sample program.
```
#include "mbed.h" 
/**
 * Initialize the system
 *
 * @param  none
 * @return none
 *
 * @brief  Setup the microcontroller system.
 *         Initialize the System.
*/
extern "C" void $Sub$$SystemInit (void)
{
 //extern int stdio_retargeting_module;
// select the PLL input
    LPC_SYSCON->SYSPLLCLKSEL  = 0x0;                // Select PLL Input source 0=IRC, 1=OSC
    LPC_SYSCON->SYSPLLCLKUEN  = 0x01;               /* Update Clock Source      */
    LPC_SYSCON->SYSPLLCLKUEN  = 0x00;               /* Toggle Update Register   */
    LPC_SYSCON->SYSPLLCLKUEN  = 0x01;
    while (!(LPC_SYSCON->SYSPLLCLKUEN & 0x01));     /* Wait Until Updated       */
 
// Power up the system PLL
    LPC_SYSCON->SYSPLLCTRL    = 0x00000023;
    LPC_SYSCON->PDRUNCFG     &= ~(1 << 7);          /* Power-up SYSPLL          */
    while (!(LPC_SYSCON->SYSPLLSTAT & 0x01));       /* Wait Until PLL Locked    */
 
// Select the main clock source
    LPC_SYSCON->MAINCLKSEL    = 0x3;                // Select main Clock source, 0=IRC, 1=PLLin, 2=WDO, 3=PLLout
    LPC_SYSCON->MAINCLKUEN    = 0x01;               /* Update MCLK Clock Source */
    LPC_SYSCON->MAINCLKUEN    = 0x00;               /* Toggle Update Register   */
    LPC_SYSCON->MAINCLKUEN    = 0x01;
    while (!(LPC_SYSCON->MAINCLKUEN & 0x01));       /* Wait Until Updated       */
 
    LPC_SYSCON->SYSAHBCLKDIV  = 0x00000001;
 
    LPC_SYSCON->PDRUNCFG     &= ~(1 << 10);         /* Power-up USB PHY         */
    LPC_SYSCON->PDRUNCFG     &= ~(1 <<  8);         /* Power-up USB PLL         */
    LPC_SYSCON->USBPLLCLKSEL  = 0x0;                // 0=IRC, 1=System clock, only good for low speed
    LPC_SYSCON->USBPLLCLKUEN  = 0x01;               /* Update Clock Source      */
    LPC_SYSCON->USBPLLCLKUEN  = 0x00;               /* Toggle Update Register   */
    LPC_SYSCON->USBPLLCLKUEN  = 0x01;
 
    while (!(LPC_SYSCON->USBPLLCLKUEN & 0x01));     /* Wait Until Updated       */
    LPC_SYSCON->USBPLLCTRL    = 0x00000023;
 
    while (!(LPC_SYSCON->USBPLLSTAT   & 0x01));     /* Wait Until PLL Locked    */
    LPC_SYSCON->USBCLKSEL     = 0x00;               /* Select USB PLL           */
 
    LPC_SYSCON->USBCLKSEL     = 0x00000000;      /* Select USB Clock         */
    LPC_SYSCON->USBCLKDIV     = 0x00000001;      /* Set USB clock divider    */
 
    /* System clock to the IOCON needs to be enabled or
    most of the I/O related peripherals won't work. */
    LPC_SYSCON->SYSAHBCLKCTRL |= (1<<16);
    //stdio_retargeting_module = 1;
 
}
DigitalOut red(P1_2);
DigitalOut green(P1_23);
DigitalOut blue(P1_24);

DigitalIn lf1(P0_11);
DigitalIn lf2(P0_12);
DigitalIn lf3(P0_13);

AnalogIn lightsensor(P0_23);
DigitalOut leftReverse(P0_8);
DigitalOut leftForward(P0_9);
InterruptIn leftEncoder(P1_11);

DigitalOut rightReverse(P0_22);
DigitalOut rightForward(P0_21);
InterruptIn rightEncoder(P1_29);

Serial pc(USBTX, USBRX); // tx, rx

int main() {
    while(1) {
        blue = 1;
        wait(0.2);
        blue = 0;
        wait(0.2);
    }
}
```
