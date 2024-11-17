#include <Adafruit_NeoPixel.h>
#include <WiFi.h>
#include <WebServer.h>

#define LED_PIN 27
#define NUM_LEDS 64

const char* ssid = "ESP32_LED_Display";
const char* password = "123456789";

Adafruit_NeoPixel leds = Adafruit_NeoPixel(NUM_LEDS, LED_PIN, NEO_GRB + NEO_KHZ800);

const uint8_t charMap[40][8] = {
  
  {B00000000, B00111100, B01100110, B01100110, B01111110, B01100110, B01100110, B01100110}, // A
  {B01111000, B01001000, B01001000, B01110000, B01001000, B01000100, B01000100, B01111100}, // B
  {B00000000, B00011110, B00100000, B01000000, B01000000, B01000000, B00100000, B00011110}, // C
  {B00000000, B00111000, B00100100, B00100010, B00100010, B00100100, B00111000, B00000000}, // D
  {B00000000, B00111100, B00100000, B00111000, B00100000, B00100000, B00111100, B00000000}, // E
  {B00000000, B00111100, B00100000, B00111000, B00100000, B00100000, B00100000, B00000000}, // F
  {B00000000, B00111110, B00100000, B00100000, B00101110, B00100010, B00111110, B00000000}, // G
  {B00000000, B00100100, B00100100, B00111100, B00100100, B00100100, B00100100, B00000000}, // H
  {B00000000, B00111000, B00010000, B00010000, B00010000, B00010000, B00111000, B00000000}, // I
  {B00000000, B00011100, B00001000, B00001000, B00001000, B00101000, B00111000, B00000000}, // J
  {B00000000, B00100100, B00101000, B00110000, B00101000, B00100100, B00100100, B00000000}, // K
  {B00000000, B00100000, B00100000, B00100000, B00100000, B00100000, B00111100, B00000000}, // L
  {B00000000, B00000000, B01000100, B10101010, B10010010, B10000010, B10000010, B00000000}, // M
  {B00000000, B00100010, B00110010, B00101010, B00100110, B00100010, B00000000, B00000000}, // N
  {B00000000, B00111100, B01000010, B01000010, B01000010, B01000010, B00111100, B00000000}, // O
  {B00000000, B00111000, B00100100, B00100100, B00111000, B00100000, B00100000, B00000000}, // P
  {B00000000, B00111100, B01000010, B01000010, B01000010, B01000110, B00111110, B00000001}, // Q
  {B00000000, B00111000, B00100100, B00100100, B00111000, B00100100, B00100100, B00000000}, // R
  {B00000000, B00111100, B00100000, B00111100, B00000100, B00000100, B00111100, B00000000}, // S
  {B00000000, B01111100, B00010000, B00010000, B00010000, B00010000, B00010000, B00000000}, // T
  {B00000000, B01000010, B01000010, B01000010, B01000010, B00100100, B00011000, B00000000}, // U
  {B00000000, B00100010, B00100010, B00100010, B00010100, B00010100, B00001000, B00000000}, // V
  {B00000000, B10000010, B10010010, B01010100, B01010100, B00101000, B00000000, B00000000}, // W
  {B00000000, B01000010, B00100100, B00011000, B00011000, B00100100, B01000010, B00000000}, // X
  {B00000000, B01000100, B00101000, B00010000, B00010000, B00010000, B00010000, B00000000}, // Y
  {B00000000, B00111100, B00000100, B00001000, B00010000, B00100000, B00111100, B00000000}, // Z

  
  {B00000000, B00111100, B01000010, B01000010, B01000010, B01000010, B00111100, B00000000}, // 0
  {B00000000, B00010000, B00110000, B00010000, B00010000, B00010000, B00111100, B00000000}, // 1
  {B00000000, B00111100, B01000010, B00000100, B00001000, B00010000, B00111100, B00000000}, // 2
  {B00000000, B00111100, B01000010, B00000100, B00000100, B01000010, B00111100, B00000000}, // 3
  {B00000000, B00001100, B00010100, B00100100, B01111100, B00000100, B00000100, B00000000}, // 4
  {B00000000, B01111100, B01000000, B01111100, B00000100, B00000100, B00111100, B00000000}, // 5
  {B00000000, B00111100, B01000000, B01111100, B01000100, B01000100, B00111100, B00000000}, // 6
  {B00000000, B01111100, B00010000, B00001000, B00001000, B00001000, B00001000, B00000000}, // 7
  {B00000000, B00111100, B01000100, B00111100, B01000100, B01000100, B00111100, B00000000}, // 8
  {B00000000, B00111100, B01000100, B00111100, B00000100, B00000100, B00111100, B00000000}, // 9

  
  {B00000000, B00011000, B00100100, B00001000, B00010000, B00000000, B00010000, B00000000}, // ?
  {B00000000, B00100000, B00010000, B00010000, B00000000, B00010000, B00010000, B00000000}, // !
  {B00000000, B00000000, B00000000, B00000000, B00000000, B00011000, B00011000, B00000000}  // .
};


WebServer server(80);
String inputText = "";  // Holds the full input sentence
int scrollIndex = 0;    // Keeps track of the scroll position
bool displayOn = true;  // Tracks if display is turned off or on
bool lightBar = false;  // Tracks if light bar mode is on
bool lightShow = false; // Tracks if light show mode is on
bool rainbowOn = false; // Tracks if rainbow pattern is on
uint32_t lightBarColor = 0xFFFFFF; // Default color for light bar
uint8_t brightness = 255; // Brightness control

void setup() {
  Serial.begin(115200);
  leds.begin();
  leds.setBrightness(brightness); // Set initial brightness

  // Set up the ESP32 as an access point
  WiFi.softAP(ssid, password);

  // Wait for the AP to start
  delay(2000);
  IPAddress ip = WiFi.softAPIP();
  Serial.print("AP IP address: ");
  Serial.println(ip);

  // Serve the web page
  server.on("/", HTTP_GET, []() {
    String page = "<html><head>";
    page += "<style>body { font-family: Arial, sans-serif; text-align: center; background-color: #f0f8ff; padding: 30px; }";
    page += "button { padding: 15px 25px; margin: 10px; font-size: 18px; background-color: #4CAF50; color: white; border: none; cursor: pointer; border-radius: 5px; }";
    page += "button:hover { background-color: #45a049; }";
    page += "input[type=text] { width: 80%; padding: 12px; font-size: 18px; margin: 20px 0; border: 1px solid #ccc; border-radius: 4px; }";
    page += "input[type=color], button.brightness { padding: 10px; margin: 15px; font-size: 18px; border-radius: 50%; }";
    page += "form { max-width: 500px; margin: 0 auto; }";
    page += "</style></head><body>";
    page += "<h1>ESP32 LED Display Control</h1>";
    page += "<form id='displayForm'><input type='text' name='text' placeholder='Enter text' id='textInput'><br>";
    page += "<button type='button' onclick='sendText()'>Display Text</button><br>";
    page += "<button type='button' onclick='runRainbow()'>Rainbow Pattern</button>";
    page += "<button type='button' onclick='lightBarToggle()'>Light Bar</button>";
    page += "<button type='button' onclick='lightShowToggle()'>Light Show</button><br><br>";
    page += "Choose Color: <input type='color' id='colorPicker' value='#FFFFFF'><br><br>";
    page += "Brightness: ";
    page += "<button type='button' class='brightness' onclick='setBrightness(64)'>25%</button>";
    page += "<button type='button' class='brightness' onclick='setBrightness(128)'>50%</button>";
    page += "<button type='button' class='brightness' onclick='setBrightness(192)'>75%</button>";
    page += "<button type='button' class='brightness' onclick='setBrightness(255)'>100%</button><br><br>";
    page += "<button type='button' onclick='turnOff()' style='background-color:#f44336;'>Turn Off Display</button>";
    page += "</form><script>";
    page += "function sendText() { var text = document.getElementById('textInput').value;";
    page += "fetch('/displayText?text=' + text); }";
    page += "function turnOff() { fetch('/turnOff'); }";
    page += "function lightBarToggle() { var color = document.getElementById('colorPicker').value.substring(1);";
    page += "fetch('/lightBar?color=' + color); }";
    page += "function lightShowToggle() { fetch('/lightShow'); }";
    page += "function runRainbow() { fetch('/rainbow'); }";
    page += "function setBrightness(level) { fetch('/setBrightness?level=' + level); }";
    page += "</script></body></html>";
    server.send(200, "text/html", page);
  });

  // Handle display text
  server.on("/displayText", HTTP_GET, []() {
    if (server.hasArg("text")) {
      inputText = server.arg("text");
      inputText.toUpperCase(); // Convert to uppercase
      scrollIndex = 0;         // Reset scroll position
      displayOn = true;
      lightBar = false;
      lightShow = false; 
      rainbowOn = false; // Turn off rainbow
    }
    server.send(200, "text/plain", "Displaying text: " + inputText);
  });

  // Handle turn off display
  server.on("/turnOff", HTTP_GET, []() {
    displayOn = false;
    lightBar = false; 
    lightShow = false;
    rainbowOn = false; // Stop rainbow
    server.send(200, "text/plain", "Display turned off");
  });

  // Handle light bar
  server.on("/lightBar", HTTP_GET, []() {
    if (server.hasArg("color")) {
      String colorStr = server.arg("color");
      long colorVal = strtol(colorStr.c_str(), NULL, 16); // Convert hex to long
      lightBarColor = (uint32_t)colorVal;
      lightBar = true;
      displayOn = false; // Disable normal display when light bar is on
      lightShow = false; 
      rainbowOn = false; // Turn off rainbow
    }
    server.send(200, "text/plain", "Light bar activated with color: " + String(lightBarColor, HEX));
  });

  // Handle light show
  server.on("/lightShow", HTTP_GET, []() {
    lightShow = true;
    displayOn = false; 
    lightBar = false;
    rainbowOn = false; // Turn off rainbow
    server.send(200, "text/plain", "Light show activated");
  });

  // Handle rainbow pattern
  server.on("/rainbow", HTTP_GET, []() {
    rainbowOn = true;
    displayOn = false; 
    lightBar = false;  
    lightShow = false; 
    server.send(200, "text/plain", "Rainbow pattern activated");
  });

  // Handle brightness control
  server.on("/setBrightness", HTTP_GET, []() {
    if (server.hasArg("level")) {
      brightness = server.arg("level").toInt();
      leds.setBrightness(brightness);
      leds.show();
    }
    server.send(200, "text/plain", "Brightness set to: " + String(brightness));
  });

  // Start the web server
  server.begin();
}

void loop() {
  server.handleClient();

  if (displayOn) {
    scrollText(); 
    delay(150);   
  } else if (lightBar) {
    runLightBar(); 
    delay(100);
  } else if (lightShow) {
    runLightShow(); 
    delay(100);
  } else if (rainbowOn) {
    runRainbowPattern();
  } else {
    clearDisplay(); 
  }
}

void runLightBar() {
  for (int i = 0; i < NUM_LEDS; i++) {
    leds.setPixelColor(i, lightBarColor); 
  }
  leds.show();
}

void runLightShow() {
  static uint8_t lightShowPattern = 0;
  static unsigned long lastChange = 0;

  if (millis() - lastChange > 1000) { 
    lightShowPattern = (lightShowPattern + 1) % 3; 
    lastChange = millis();
  }

  switch (lightShowPattern) {
    case 0:
      heartPattern();
      break;
    case 1:
      checkerboardPattern();
      break;
    case 2:
      gradientPattern();
      break;
  }
}

void runRainbowPattern() {
  for (int i = 0; i < 256 * 5; i++) { 
    for (int j = 0; j < leds.numPixels(); j++) {
      leds.setPixelColor(j, Wheel((j + i) & 255)); 
    }
    leds.show();
    delay(50); 
  }
}

void heartPattern() {
  static const int heartShape[64] = {
    // Heart shape data here...
  };

  for (int i = 0; i < NUM_LEDS; i++) {
    if (heartShape[i]) {
      leds.setPixelColor(i, leds.Color(255, 0, 0)); 
    } else {
      leds.setPixelColor(i, leds.Color(0, 0, 0)); 
    }
  }
  leds.show();
}

void checkerboardPattern() {
  for (int i = 0; i < NUM_LEDS; i++) {
    int row = i / 8;
    int col = i % 8;
    if ((row + col) % 2 == 0) {
      leds.setPixelColor(i, leds.Color(0, 255, 0)); 
    } else {
      leds.setPixelColor(i, leds.Color(0, 0, 0)); 
    }
  }
  leds.show();
}

void gradientPattern() {
  static unsigned long lastChange = 0;
  static int offset = 0;

  if (millis() - lastChange > 50) { 
    offset = (offset + 1) % 256;
    lastChange = millis();
  }

  for (int i = 0; i < NUM_LEDS; i++) {
    int pos = (i * 256 / NUM_LEDS + offset) % 256;
    leds.setPixelColor(i, Wheel(pos));
  }
  leds.show();
}

void clearDisplay() {
  for (int i = 0; i < NUM_LEDS; i++) {
    leds.setPixelColor(i, leds.Color(0, 0, 0)); 
  }
  leds.show();
}

void scrollText() {
  if (inputText.length() == 0) {
    return; 
  }

  for (int y = 0; y < 8; y++) {
    for (int x = 0; x < 8; x++) {
      int charIndex = (scrollIndex + x) / 8;
      int charColumn = (scrollIndex + x) % 8;

      if (charIndex < inputText.length()) {
        char currentChar = inputText[charIndex];
        uint8_t index = getCharIndex(currentChar);
        if (index != 255 && (charMap[index][y] & (1 << (7 - charColumn)))) {
          uint32_t color = Wheel(((x + scrollIndex) * 256 / (inputText.length() * 8)) % 256);
          leds.setPixelColor(y * 8 + x, color);
        } else {
          leds.setPixelColor(y * 8 + x, leds.Color(0, 0, 0));
        }
      } else {
        leds.setPixelColor(y * 8 + x, leds.Color(0, 0, 0));
      }
    }
  }

  leds.show();
  scrollIndex++;
  if (scrollIndex >= inputText.length() * 8) {
    scrollIndex = 0;
  }
}

uint8_t getCharIndex(char c) {
  if (c >= 'A' && c <= 'Z') {
    return c - 'A';
  } else if (c >= '0' && c <= '9') {
    return c - '0' + 26;
  } else if (c == '?') {
    return 36;
  } else if (c == '!') {
    return 37;
  } else if (c == ',') {
    return 38;
  } else if (c == '.') {
    return 39;
  }
  return 255;
}

uint32_t Wheel(byte WheelPos) {
  WheelPos = 255 - WheelPos;
  if (WheelPos < 85) {
    return leds.Color(255 - WheelPos * 3, 0, WheelPos * 3);
  } else if (WheelPos < 170) {
    WheelPos -= 85;
    return leds.Color(0, WheelPos * 3, 255 - WheelPos * 3);
  } else {
    WheelPos -= 170;
    return leds.Color(WheelPos * 3, 255 - WheelPos * 3, 0);
  }
}
