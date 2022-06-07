#include <WiFiManager.h> // https://github.com/tzapu/WiFiManager#include <WiFi.h>
#include <ESPAsyncWebServer.h>
#include <DHT.h>
#include <Wire.h>
#include <Adafruit_Sensor.h> // incluye librerias para sensor BMP280
#include <Adafruit_BMP280.h>
#include <WiFiManager.h>
#include <ESPmDNS.h>
#include "password.h"

// general
#define tiempoEsperado 2000
#define DEBUG 1
long unsigned tiempoActual = 0;
WiFiManager wm;

// LDR
#define LDR_pin 34 // el sensor LDR esta conectado en el pin D36
#define LDR_valor_in_min 0
#define LDR_valor_in_max 4095
#define LDR_valor_out_min 0
#define LDR_valor_out_max 100
float luz;

// DHT11
#define DHT_pin 5 // el sensor DHT11 esta conectado en el pin D5
float humedad;
DHT dht(DHT_pin, DHT11); // inicializa el sensor DHT11

// BMP280
Adafruit_BMP280 bmp;
float temperatura;
float presion;


void debugBMPTemperatura()
{
  temperatura = bmp.readTemperature();
  if (!bmp.begin())
  {                                         // si falla la comunicacion con el sensor mostrar
    Serial.println("BMP280 no encontrado"); // texto y detener flujo del programa                    // mediante bucle infinito
  }                                   
    Serial.print("temperatura: ");                                         
    Serial.print(temperatura);                                         
    Serial.println(" ºC");    
}

void debugBMPPesion()
{
  presion = bmp.readPressure() / 100;
  if (!bmp.begin())
  {                                         // si falla la comunicacion con el sensor mostrar
    Serial.println("BMP280 no encontrado"); // texto y detener flujo del programa                       // mediante bucle infinito
  }                
    Serial.print("Presion: ");                                         
    Serial.print(presion);                                         
    Serial.println(" hPa"); 
}

void debugLDR()
{
  luz = analogRead(LDR_pin);
  luz = map(luz, LDR_valor_in_min, LDR_valor_in_max, LDR_valor_out_min, LDR_valor_out_max); // remplaza un cierto numero de valores por otros                                       
  Serial.print("Luz: ");                                         
  Serial.print(luz);                                         
  Serial.println("%"); 
}

void debugDHTHumedad()
{
  // Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
  humedad = dht.readHumidity();
  if (isnan(humedad))
  {
    Serial.println("Error al leer el sensor DHT11");
  }                 
    Serial.print("Humedad: ");                                         
    Serial.print(humedad);                                         
    Serial.println("%");   
}
String leerBMPTemperatura()
{
  temperatura = bmp.readTemperature();
  if (!bmp.begin())
  {                                         // si falla la comunicacion con el sensor mostrar
    Serial.println("BMP280 no encontrado"); // texto y detener flujo del programa
    return "--";                            // mediante bucle infinito
  }
  else
  {      
    return String(temperatura);
  }
}

String leerBMPPresion()
{
  presion = bmp.readPressure() / 100;
  if (!bmp.begin())
  {                                         
    Serial.println("BMP280 no encontrado"); 
    return "--";                            
  }
  else
  {                            
    return String(presion);
  }
}

String leerLDR()
{
  luz = analogRead(LDR_pin);
  luz = map(luz, LDR_valor_in_min, LDR_valor_in_max, LDR_valor_out_min, LDR_valor_out_max); // remplaza un cierto numero de valores por otros     
  return String(luz);
}

String leerDHTHumedad()
{
  humedad = dht.readHumidity();
  if (isnan(humedad))
  {
    Serial.println("Error al leer el sensor DHT11");
    return "--";
  }
  else
  {                        
    return String(humedad);
  }
}

AsyncWebServer server(80);

const char index_html[] PROGMEM = R"rawliteral(
<!DOCTYPE HTML><html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.7.2/css/all.css" integrity="sha384-fnmOCqbTlWIlj8LyTjo7mOUStjsKC4pOpQbqyi7RrhN7udi9RwhKkMHpvLbHG9Sr" crossorigin="anonymous">
  <style>
    html {
     font-family: Arial;
     display: inline-block;
     margin: 0px auto;
     text-align: center;
    }
    h2 { font-size: 3.0rem; }
    p { font-size: 3.0rem; }
    .units { font-size: 1.2rem; }
    .sensor-labels{
      font-size: 1.5rem;
      vertical-align:middle;
      padding-bottom: 15px;
    }
  </style>
</head>
<body>
  <h2>ESP32 Servidor de estacion meteorologica</h2>
  <p>
    <i class="fas fa-thermometer-half" style="color:#059e8a;"></i> 
    <span class="sensor-labels">Temperatura</span> 
    <span id="temperatura">%TEMPERATURA%</span>
    <sup class="units">&deg;C</sup>
  </p>
  <p>
    <i class="fas fa-tint" style="color:#00add6;"></i> 
    <span class="sensor-labels">Humedad</span>
    <span id="humedad">%HUMEDAD%</span>
    <sup class="units">&percnt;</sup>
  </p>
  <p>
    <i class="fas fa-sun" style="color:#ff8000;"></i> 
    <span class="sensor-labels">Luz</span>
    <span id="luz">%LUZ%</span>
    <sup class="units">&percnt;</sup>
  </p>
  <p>
    <i class="fas fa-wind" style="color:#0080ff;"></i> 
    <span class="sensor-labels">Presion</span>
    <span id="presion">%PRESION%</span>
    <sup class="units">hPa</sup>
  </p>
</body>
<script>
setInterval(function ( ) {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      document.getElementById("temperatura").innerHTML = this.responseText;
    }
  };
  xhttp.open("GET", "/temperatura", true);
  xhttp.send();
}, 10000 ) ;

setInterval(function ( ) {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      document.getElementById("presion").innerHTML = this.responseText;
    }
  };
  xhttp.open("GET", "/presion", true);
  xhttp.send();
}, 10000 ) ;

setInterval(function ( ) {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      document.getElementById("humedad").innerHTML = this.responseText;
    }
  };
  xhttp.open("GET", "/humedad", true);
  xhttp.send();
}, 10000 ) ;

setInterval(function ( ) {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      document.getElementById("luz").innerHTML = this.responseText;
    }
  };
  xhttp.open("GET", "/luz", true);
  xhttp.send();
}, 10000 ) ;
</script>
</html>)rawliteral";

// Replaces placeholder with DHT values
String processor(const String &var)
{
  // Serial.println(var);
  if (var == "TEMPERATURA")
  {
    return leerBMPTemperatura();
  }
  else if (var == "HUMEDAD")
  {
    return leerDHTHumedad();
  }
  else if (var == "LUZ")
  {
    return leerLDR();
  }
  else if (var == "PRESION")
  {
    return leerBMPPresion();
  }
  return String();
}

void retraso(long unsigned *tiempoActual)
{
  if (millis() >= tiempoEsperado + *tiempoActual)
  {
   *tiempoActual += millis();
   debugBMPTemperatura();
   debugBMPPresion();
   debugLDR();
   debugDHTHumedad();
  }
}


void setup() {
    Serial.begin(115200);
    dht.begin();
    if(!DEBUG){
        WiFi.mode(WIFI_STA);
        
        // reset settings - wipe stored credentials for testing
        // these are stored by the esp library
        //wm.resetSettings();
        // Automatically connect using saved credentials,
        
        if(!wm.autoConnect(ssid,password)) {
            Serial.println("Failed to connect");
            ESP.restart();
        } 
        else {
            //if you get here you have connected to the WiFi    
            Serial.println("connected...yeey :)");
        }
        if(!MDNS.begin("sacachispas")){
          Serial.println("Error en el mDNS");
          return;
        }
        Serial.println("mDNS configurado");
      
        // Route for root / web page
        server.on("/", HTTP_GET, [](AsyncWebServerRequest *request)
                  { request->send_P(200, "text/html", index_html, processor); });
        server.on("/temperatura", HTTP_GET, [](AsyncWebServerRequest *request)
                  { request->send_P(200, "text/plain", leerBMPTemperatura().c_str()); });
        server.on("/humedad", HTTP_GET, [](AsyncWebServerRequest *request)
                  { request->send_P(200, "text/plain", leerDHTHumedad().c_str()); });
        server.on("/luz", HTTP_GET, [](AsyncWebServerRequest *request)
                  { request->send_P(200, "text/plain", leerLDR().c_str()); });
        server.on("/presion", HTTP_GET, [](AsyncWebServerRequest *request)
                  { request->send_P(200, "text/plain", leerBMPPresion().c_str()); });
    
        // Start server
        server.begin();
        
        MDNS.addService("http","tcp",80);
    }
}

void loop() {
  if(DEBUG){
    retraso(&tiempoActual);
  }
}
