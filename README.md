#include <EEPROM.h>
#include "GravityTDS.h"
#include <LiquidCrystal.h>
#include <OneWire.h> 
#include <DallasTemperature.h>
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
 
#define ONE_WIRE_BUS 7
#define TdsSensorPin A1
 
OneWire oneWire(ONE_WIRE_BUS); 
GravityTDS gravityTds;
 
DallasTemperature sensors(&oneWire);
 
float tdsValue = 0;
 
void setup()
{
    Serial.begin(115200);
    lcd.begin(16,2);
    sensors.begin();
    gravityTds.setPin(TdsSensorPin);
    gravityTds.setAref(5.0);  //reference voltage on ADC, default 5.0V on Arduino UNO
    gravityTds.setAdcRange(1024);  //1024 for 10bit ADC;4096 for 12bit ADC
    gravityTds.begin();  //initialization
}
 
void loop()
{
    sensors.requestTemperatures();
 
    gravityTds.setTemperature(sensors.getTempCByIndex(0));  // set the temperature and execute temperature compensation
    gravityTds.update();  //sample and calculate
    tdsValue = gravityTds.getTdsValue();  // then get the value
    
    
    Serial.print(tdsValue,0);
    Serial.println("ppm");
    Serial.print("Temperature is: "); 
    Serial.print(sensors.getTempCByIndex(0));
    
    lcd.setCursor(0, 0);
    lcd.print("TDS:");
    lcd.print(tdsValue,0);
    lcd.print("PPM");

   delay(1500);
    if (tdsValue<=250)
    {
      lcd.print(" GOOD");
    }
    else
    {
      lcd.print(" BAD");
    }
     

    lcd.setCursor(0, 1);
    lcd.print("Temp: ");
    lcd.print(sensors.getTempCByIndex(0));
    lcd.print("C");
    
    delay(1000);
    lcd.clear();
}
