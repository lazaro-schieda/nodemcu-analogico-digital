# nodemcu-analogico-digital
toma valores tanto digitales como analógicos y los carga a la web de visualización de thinger.io

//TP INSTRUMENTACION SEGUNDO SEMESTRE DEL 2018 UNGS TECNICATURA EN AUTOMATIZACION Y CONTROL PROFESOR ING. BRESSAN
//ENVIO DE DATOS ANALOGICOS Y DIGITALES,DE FORMA REMOTA MEDIANTE UNA RED WIFI, DEJANDO LA IP ESTATICA.

//LAZARO EMANUEL SCHIEDA
//JOSE LUIS ENCINA
//GUSTAVO ACUÑA
//CRISTIAN CHAPARRO


#include <ESP8266WiFi.h>
#include <ThingerESP8266.h>//<ThingerESP8266laza.h>              //#include <ThingerESP8266.h> libreria original, solo se modifico para que muestre ip, por monitor serial


#define USERNAME "xxxxxxxxxx"                                    //nombre de usuario de thinger.io
#define DEVICE_ID "xxxxxxx"                                      //espacio donde cargo los datos
#define DEVICE_CREDENTIAL "xxxxxxxxxxxx"                         //contraseña para entrar 

#define SSID  "xxxxxx"                                           //nombre de red wifi
#define SSID_PASSWORD "xxxxxxxxxx"                               // contraseña de red wifi

ThingerESP8266 thing(USERNAME, DEVICE_ID, DEVICE_CREDENTIAL);    //acreditacion en en la web de thinger

int sensor;
int potenciometro;
byte boton2;
byte boton;

void setup()
{
  pinMode(16, OUTPUT);                                           //salida digital1
  Serial.begin(115200);                                          //velocidad del puerto serie
    
  pinMode(D2, INPUT);                                            //entrada digital1
  pinMode(A0, INPUT);                                            //entrada analogica de 0 a 1v
  
  thing.add_wifi(SSID, SSID_PASSWORD);                           //se logue en la red wifi
   
  WiFi.config(IPAddress(192,168,x,x), IPAddress(192,168,x,x), IPAddress(255,255,255,0), IPAddress(192,168,x,x)); //cambio a ip estatica
  //          IPAddress(ip estatica),   IPAddress(gateway),      IPAddress(mac),           IPAddress(dns));
            
  Serial.print("NodeMCU IP Address : ");                         //imprime por puerto serie la ip en uso
  
  Serial.println(WiFi.localIP() );
  
  
  thing["led"] << digitalPin(16);                                //solo prende un led, a travez de la web  
  
}

void loop() 
{
  thing.handle();
  
  potenciometro=analogRead(A0);                                  //leo y escalo el valor de la entrada analogica
  sensor=(potenciometro-79)*0.864;                               //leo y escalo el valor de la entrada analogica
  thing["sensor"] >> outputValue(sensor);                       //subo el valor de la entrada anlogica  ala web
  
  thing["digital1"] >> outputValue(digitalRead(D2));             //leo y subo el valor de la entrada digital
  
  Serial.println(potenciometro);                                 //imprimo el estado del valor real de la entrada analogica
  Serial.print("sensor: ");
  Serial.println(sensor);                                        //imprimo el estado del valor de la entrada analogica ya escalada
  Serial.println(WiFi.localIP() );
  
  delay (1000);                                                  //tiempo de espera para estavilizacion del sistema
}
