#include <OneWire.h> //TC controller
#include <Wire.h>
#include <DallasTemperature.h> 
#include <SD.h>
#include "RTClib.h"

// define the Real Time Clock object
RTC_DS1307 rtc; 

//Select logger pin
const int chipSelect = 10;

// Data wire is plugged into port 2 on the Arduino
#define ONE_WIRE_BUS 2

// Setup a oneWire instance to communicate with any OneWire devices (not just Maxim/Dallas temperature ICs)
OneWire oneWire(ONE_WIRE_BUS);

// Pass our oneWire reference to Dallas Temperature. 
DallasTemperature sensors(&oneWire);

// arrays to hold device address
DeviceAddress insideThermometer;

//***************************************************
// LED SETUP
// Pin 13 has an LED connected on most Arduino boards.
// give it a name:
int led = 13;
//***************************************************
//Temperature Setpoint
int TempDes = 27;

// this is a function
// this is always the first function an arduino code calls

// type that is returned
void setup(void)
{

  // start serial port
  Serial.begin(9600);
  Serial.println("Dallas Temperature IC Control Library Demo");

#ifdef AVR
  Wire.begin();
#else
  Wire1.begin(); // Shield I2C pins connect to alt I2C bus on Arduino Due
#endif
  rtc.begin();

  if (! rtc.isrunning()) {
    Serial.println("RTC is NOT running!");
    // following line sets the RTC to the date & time this sketch was compiled
    rtc.adjust(DateTime(F(__DATE__), F(__TIME__)));
    // This line sets the RTC with an explicit date & time, for example to set
    // January 21, 2014 at 3am you would call:
    // rtc.adjust(DateTime(2014, 1, 21, 3, 0, 0));
  }

  // locate devices on the bus
  Serial.print("Locating devices...");
  sensors.begin();
  Serial.print("Found ");
  Serial.print(sensors.getDeviceCount(), DEC);
  Serial.println(" devices.");

  // report parasite power requirements
  Serial.print("Parasite power is: "); 
  if (sensors.isParasitePowerMode()) Serial.println("ON");
  else Serial.println("OFF");

  //set pin mode for logger
  pinMode(10, OUTPUT);

  // initialize pin for relay/LED
  pinMode(led, OUTPUT);     

  // see if the card is present and can be initialized:
  if (!SD.begin(chipSelect)) {
    Serial.println("Card failed, or not present");
    // don't do anything more:
    return;

    //*****************************************************

    // assign address manually.  the addresses below will beed to be changed
    // to valid device addresses on your bus.  device address can be retrieved
    // by using either oneWire.search(deviceAddress) or individually via
    // sensors.getAddress(deviceAddress, index)
    //insideThermometer = { 0x28, 0x1D, 0x39, 0x31, 0x2, 0x0, 0x0, 0xF0 };

    // Method 1:
    // search for devices on the bus and assign based on an index.  ideally,
    // you would do this to initially discover addresses on the bus and then 
    // use those addresses and manually assign them (see above) once you know 
    // the devices on your bus (and assuming they don't change).
    if (!sensors.getAddress(insideThermometer, 0)) Serial.println("Unable to find address for Device 0"); 

    // method 2: search()
    // search() looks for the next device. Returns 1 if a new address has been
    // returned. A zero might mean that the bus is shorted, there are no devices, 
    // or you have already retrieved all of them.  It might be a good idea to 
    // check the CRC to make sure you didn't get garbage.  The order is 
    // deterministic. You will always get the same devices in the same order
    //
    // Must be called before search()
    //oneWire.reset_search();
    // assigns the first address found to insideThermometer
    //if (!oneWire.search(insideThermometer)) Serial.println("Unable to find address for insideThermometer");

    //  // show the addresses we found on the bus
    //  Serial.print("Device 0 Address: ");
    //  printAddress(insideThermometer);
    //  Serial.println();
  }
  // set the resolution to 9 bit (Each Dallas/Maxim device is capable of several different resolutions)
  sensors.setResolution(insideThermometer, 9);

  Serial.print("Device 0 Resolution: ");
  Serial.print(sensors.getResolution(insideThermometer), DEC); 
  Serial.println();
}

// function to print the temperature for a device
int getTemperature(DeviceAddress deviceAddress)
{
  // make a string for assembling the data to log:

  //Type var_name = something


  // i++ means i = i + 1
  // for (int i = 0; i < numberOfDevices; i++) {

  //} 


  // method 2 - faster
  int tempC = sensors.getTempC(deviceAddress);

  return tempC;

  
}

void logToFile(String stringToLog) {
  File dataFile = SD.open("datalog1.txt", FILE_WRITE);

    // if the file is available, write to it:
    if (dataFile) {
      dataFile.println(stringToLog);
      dataFile.close();
      // print to the serial port too:
      Serial.println(stringToLog);
    }  
    // if the file isn't open, pop up an error:
    else {
      Serial.println("error opening datalog.csv");
    }

}

void loop(void)
{ 
  //Time
  
//  DateTime now = rtc.now();
//  Serial.print(now.year(), DEC);
//  Serial.print('/');
//  Serial.print(now.month(), DEC);
//  Serial.print('/');
//  Serial.print(now.day(), DEC);
//  Serial.print(' ');
//  Serial.print(now.hour(), DEC);
//  Serial.print(':');
//  Serial.print(now.minute(), DEC);
//  Serial.print(':');
//  Serial.print(now.second(), DEC);
//  Serial.println();
  
  
  // call sensors.requestTemperatures() to issue a global temperature 
  // request to all devices on the bus
  //  Serial.print("Requesting temperatures...");
  sensors.requestTemperatures(); // Send the command to get temperatures
  //  Serial.println("DONE");

  String dataString = "";
  for (int i = 0; i < 1; i++) {
    int temp = getTemperature(insideThermometer); // Use a simple function to print out the data
    dataString += temp;
    dataString += ", ";
    
    
//    if (sensors.getAddress(insideThermometer, i)) {
//      
//    } else {
//      Serial.print("error getting device: ");
////      Serial.print(insideThermometer);
////      Serial.pring(", ");
//      Serial.println(i);
//    }
  }
  
  // log it here
  logToFile(dataString);
  
  delay(1000);

}

// function to print a device address
void printAddress(DeviceAddress deviceAddress)
{
  for (uint8_t i = 0; i < 8; i++)

  {
    if (deviceAddress[i] < 16) Serial.print("0");
    Serial.print(deviceAddress[i], HEX);
  }

}
