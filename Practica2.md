# embebed-systems
proyects for class
#include <18F4620.h>                                                                                     
#fuses INTRC_IO, NOFCMEN, NOIESO, PUT, NOBROWNOUT, NOWDT
#fuses NOPBADEN, NOMCLR, STVREN, NOLVP, NODEBUG
#use fast_io(A)
#use fast_io(B)
#use fast_io(C)
#use fast_io(D)
#use fast_io(E)
#use delay(clock=16000000)
   
long operaciones(int op, int v1, int v2);
int estadoboton(int &boton);
void error();
int counter=4;
int counter2=8;
int counter3=32;
int flag=0;
int main (void)
 { 
    //declaramos los puertos como salidas o entradas
    set_tris_a(0x11000000); //first 5 pin of the output port are set in output and the other 2 on input
    set_tris_b(0xF0);       //los primeros pines de b estan en salida y los otros 4 estan en entrada
    set_tris_c(0xFF);       //todos los pines estan de manera de entrada del puerto c
    set_tris_d(0xFF);       //todos los pines estan de manera de entrada del puerto d
    set_tris_e(0x08);       // los 3 primeros pines de e estan de entrda y los demas en salida
    int v1= 0;              //Variable para obtener el valor del puerto d
    int v2=0;               //Variable para obtener el valor del puerto c
    int boton=0;            //variable guardar el estado del boton
    long opera=0;           //variable guardar el resultado de la operacion
   while (TRUE)
   {
    v1=input_d();                       //obtener valor de d
    v2=input_c();                       //obtener valor de c
    boton=estadoboton(boton);           //llama la funcion para obner un valor entre 0 y 4
    opera = operaciones(boton,v1,v2);   //manda los valores de v1 y v2 junto el boton para realizar la operacion correspondiente
    output_a(opera);                    //muestra el resultado en a
    output_b(opera>>6);                 // muestra el resultado en b
    output_e(opera>>10);                //muestra el resultado en e
   }
   return 0;
 }   
 

 
 long operaciones(int op, int v1, int v2)  //proocedimiento 
 {
    long result=0;                        //una variable para guardar el resultado
    if(op==1)                             //si el boton viene con un 1
    {
     result=(long)v1+(long)v2;            //suma
    }
    else if (op==2)                       //si el boton viene con un 2
    {
      result=(long)v1-(long)v2;           //resta
      if(result<0)
         {
         result= (!result)+1;
         }
    }
    else if(op==3) 
    {
      result=(long)v1*(long)v2;           //multiplicacion
    }
    else if(op==4)                  //si el boton viene con un 4 como estado
    {
       if (v2!=0)          
       {
          result=(long)v1/(long)v2; //ejecuta divicion
       }
       else
       {
     error();                 // muestra un error si se divide entre 0
       }
    }
    
    return result;
 }
 int estadoboton(int &boton) // procedimiento para sacar el resultado del control
  {
     if(input(pin_b4)==0 && input(pin_b5)==1 && input(pin_b6)==1 && input(pin_b7)==1) //si boton 1 esta en bajo y los demas en 1
     {
        boton=1;
     }
    else if(input(pin_b4)==1 && input(pin_b5)==0 && input(pin_b6)==1 && input(pin_b7)==1)
    {
        boton=2;
    } 
     else if(input(pin_b4)==1 && input(pin_b5)==1 && input(pin_b6)==0 && input(pin_b7)==1)
    {
        boton=3;
    } 
     else if(input(pin_b4)==1 && input(pin_b5)==1 && input(pin_b6)==1 && input(pin_b7)==0)
    {
        boton=4;
    } 
        delay_ms(30);
   return boton; 
  }
  
  void error()
  {
    if(counter>0)
      {
         output_E(counter);
         counter=counter >> 1;
         delay_ms(500);
         if(counter==0 && flag==0)
            counter2=8;
      }
      
      else if(counter2>0)
      {  
         flag=1;
         output_E(counter);
         output_B(counter2);
         counter2 >>= 1;
         delay_ms(500);
         if(counter2==0&&flag==1)
            counter3=32;  
      }
      else
      {
         flag=2;
         output_E(counter);
         output_B(counter2);
         output_A(counter3);
         counter3 >>=1;
         delay_ms(500);
         if(counter3==0&&flag==2)
         {
            counter=4;
            counter2=8;
            counter3=32;
            flag=0;         
         }         
      }
  }
  
