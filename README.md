# IoT

Para almacenar datos de un sensor DHT11 en Firebase utilizando un ESP32 y luego visualizarlos en Google Sheets y AppSheet, sigue estos pasos. Primero, entenderemos cómo funcionan Firebase, el ESP32 y el DHT11.

## Funcionamiento de Firebase, ESP32 y DHT11

### Firebase
Firebase es una plataforma de desarrollo de aplicaciones que proporciona una base de datos en tiempo real. Esto significa que puedes enviar y recibir datos instantáneamente. Los datos se almacenan en formato JSON, lo que permite que múltiples usuarios accedan y actualicen la información al mismo tiempo.

### ESP32
El ESP32 es un microcontrolador con capacidad Wi-Fi y Bluetooth. Se utiliza comúnmente en proyectos de IoT (Internet de las Cosas) para recopilar datos de sensores y enviarlos a la nube.

### DHT11
El DHT11 es un sensor que mide la temperatura y la humedad. Puede enviar estos datos al ESP32, que luego los puede transmitir a Firebase.

## Pasos para la Implementación

### 1. Configuración de Firebase

1. **Crear un Proyecto en Firebase**:
   - Ve a [Firebase](https://firebase.google.com/) y crea una cuenta si no tienes una.
   - Haz clic en "Empezar" y crea un nuevo proyecto.

2. **Crear una Base de Datos**:
   - En el panel de control del proyecto, selecciona "Realtime Database".
   - Haz clic en "Crear base de datos" y selecciona "Modo de prueba" para facilitar el acceso durante el desarrollo.

3. **Obtener Credenciales**:
   - Ve a "Configuración del proyecto" y copia la **API Key** y el **URL de la base de datos**.

### 2. Configuración del Entorno de Desarrollo

1. **Instalar Arduino IDE**:
   - Descarga e instala [Arduino IDE](https://www.arduino.cc/en/software).

2. **Agregar Soporte para ESP32**:
   - Abre Arduino IDE, ve a "Archivo" -> "Preferencias" y agrega esta URL a "Gestor de URLs Adicionales": `https://dl.espressif.com/dl/package_esp32_index.json`.
   - Luego ve a "Herramientas" -> "Placa" -> "Gestor de placas" e instala la placa ESP32.

3. **Instalar Librerías Necesarias**:
   - Ve a "Programa" -> "Incluir Librería" -> "Gestionar Bibliotecas".
   - Busca e instala las librerías `Firebase ESP32` y `DHT sensor library`.

### 3. Conectar el DHT11 al ESP32

1. **Conexiones**:
   - Conecta el pin VCC del DHT11 al pin 3.3V del ESP32.
   - Conecta el pin GND del DHT11 al GND del ESP32.
   - Conecta el pin DATA del DHT11 a un pin digital (por ejemplo, GPIO 23).

### 4. Código para el ESP32

Utiliza este código como base:

```cpp
#include <WiFi.h>
#include <FirebaseESP32.h>
#include <DHT.h>

#define FIREBASE_HOST "YOUR_FIREBASE_URL"
#define FIREBASE_AUTH "YOUR_FIREBASE_SECRET"
#define WIFI_SSID "YOUR_WIFI_SSID"
#define WIFI_PASSWORD "YOUR_WIFI_PASSWORD"

#define DHTPIN 23 // Pin donde está conectado el DHT11
#define DHTTYPE DHT11 // Tipo de sensor

DHT dht(DHTPIN, DHTTYPE);
FirebaseData firebaseData;

void setup() {
    Serial.begin(115200);
    WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
    
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.println("Conectando a WiFi...");
    }
    
    Serial.println("Conectado a WiFi");
    Firebase.begin(FIREBASE_HOST, FIREBASE_AUTH);
    dht.begin();
}

void loop() {
    float h = dht.readHumidity();
    float t = dht.readTemperature();

    if (isnan(h) || isnan(t)) {
        Serial.println("Error al leer del DHT!");
        return;
    }

    // Enviar datos a Firebase
    Firebase.setFloat(firebaseData, "/temperatura", t);
    Firebase.setFloat(firebaseData, "/humedad", h);

    delay(2000); // Esperar 2 segundos antes de leer nuevamente
}
```

### 5. Visualización en Google Sheets

1. **Conectar Google Sheets con Firebase**:
   - Usa un script o complemento como `Firebase to Google Sheets` para importar los datos desde Firebase a Google Sheets.

2. **Crear Gráficas**:
   - Una vez que los datos estén en Google Sheets, selecciona los datos y usa la opción “Insertar” -> “Gráfico” para crear visualizaciones.

### 6. Usar AppSheet para Mostrar Gráficas

1. **Crear una Aplicación en AppSheet**:
   - Ve a [AppSheet](https://www.appsheet.com/) y crea una cuenta si no tienes una.
   - Conecta tu aplicación con Google Sheets donde están los datos.

2. **Configurar Visualizaciones**:
   - Usa las herramientas de AppSheet para crear gráficos basados en los datos importados desde Google Sheets.

Siguiendo estos pasos, podrás almacenar los datos del sensor DHT11 en Firebase, visualizarlos en Google Sheets y crear gráficas interactivas usando AppSheet.

Citations:
[1] https://github.com/mohamedamine99/esp32-iot-temperature-and-humidity-monitoring-with-firebase-real-time-data-base
[2] https://crea.ujaen.es/bitstream/10953.1/20065/1/Memoria%20TFG%20-%20Sergio%20Delgado%20Rodriguez-fdo%20-%20Sergio%20Delgado%20Rodr%C3%ADguez.pdf
[3] https://www.juanjobeunza.com/esp32-firebase/
[4] https://www.youtube.com/watch?v=_CGVnOXuqFQ
[5] https://www.youtube.com/watch?v=xP8NXpQtchU
[6] https://wokwi.com/projects/393823475155417089
[7] https://wokwi.com/projects/398168404739296257
[8] https://wokwi.com/projects/381759139910461441
