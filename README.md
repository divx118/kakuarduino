# kakuarduino
Automatically exported from code.google.com/p/kakuarduino

Introduction
Arduino library for communicating with KlikAanKlikUit (KaKu) devices using a 433Mhz tranceiver or with separate receiver and transmitter modules

The library works asynchronously using the Timer1 interrupt running at a 175us period.

There are separate receive and transmit buffers.

both the old type modules as the new type modules (automatic code) are supported

For these new type modules, the group and set dim level commands are also supported

Frames being sent are autmaically repeated five times (configurable) Received frame are filtered for duplicates. (unless they arrive more than ~10 seconds apart)

Details  
Main library functions:  

kk_init() initialize the library, starts the timer interrupt  
kk_available() Check if there is anything in the receive buffer  
kk_receive(&address,&unit,&cmd,&dimlevel) Get a frame from the receive buffer.  
kk_send(address,unit,cmd,dimlevel) Put a frame in the send buffer

example Arduino sketch:  

```c
#include <kk.h>

void setup() {
  Serial.begin(9600);
  kk_init();
}

char * parse(char **s)
  {
    char *p = *s;
    char *q;
    while (*p==' ') p++;
    q = p;
    while ((*p!=' ') && (*p!=0)) p++;
    if (*p==' ') *p++ = 0;
    *s = p;
    return q;
  }  
  
void loop() 
{
   unsigned long address;
   unsigned char unit,dimlevel,onoff;
   static char inbuf[20];
   static unsigned char i=0;
   char *p,*s,c;
   if (kk_available())
     {
           kk_receive(&address,&unit,&onoff,&dimlevel);
           Serial.print("A: ");
           Serial.print(address);
           Serial.print("U: ");
           Serial.print(unit);
           Serial.print("C: ");
           Serial.print((onoff==0)?"Off":(onoff==1)?"On ":"Dim");
           Serial.print("D: ");
           Serial.println(dimlevel);        
      }
   if  (Serial.available())
     {
       c = Serial.read();
       if (c=='\n')
         {
           inbuf[i++]=0;
           i=0;
           Serial.println(inbuf);        
           p=inbuf;
           if (strlen(p)>5)
             {
               s=parse(&p);
               address=atol(s);
               s=parse(&p);
               unit=atoi(s);
               s=parse(&p);
               onoff=0;
               if (     strcmp(s,"off")==0)
                 onoff=0;
               else if (strcmp(s,"on") ==0)
                 onoff=1;
               else if (strcmp(s,"dim")==0)
                 onoff=2;
               if (onoff==2)
                 {
                   s=parse(&p);
                   dimlevel=atoi(s);
                 }
               else
                   dimlevel=0;  
               Serial.print("a: ");
               Serial.print(address);
               Serial.print("u: ");
               Serial.print(unit);
               Serial.print("c: ");
               Serial.print((onoff==0)?"Off":(onoff==1)?"On ":"Dim");
               Serial.print("d: ");
               Serial.println(dimlevel);        
               kk_send(address,unit,onoff,dimlevel);
             }
         } 
       if ((i<19) && (c!=13))
         {
           inbuf[i++] = c;
         }
     }  
}
```
