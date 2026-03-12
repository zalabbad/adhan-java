# Adhan Java

A high precision Islamic prayer time library for Java.

All astronomical calculations are high precision equations directly from the book [“Astronomical Algorithms” by Jean Meeus](http://www.willbell.com/math/mc1.htm). This book is recommended by the Astronomical Applications Department of the U.S. Naval Observatory and the Earth System Research Laboratory of the National Oceanic and Atmospheric Administration.

## About This Fork

This is a fork of the [Adhan](https://github.com/batoulapps/adhan-kotlin) library by Batoul Apps, which has since been converted to Kotlin. This fork:
- Maintains a pure Java implementation (no Kotlin dependency)
- Kept it simple and minimal to focus only on prayer times
- Repackaged for independent publishing on Maven Central

## Usage
### Gradle
```
implementation 'io.github.zphrio:adhan-java:1.0'
```

### Maven
```xml
<dependency>
   <groupId>io.github.zphrio</groupId>
   <artifactId>adhan-java</artifactId>
   <version>1.0</version>
</dependency>
```

To get prayer times, initialize a new `PrayerTimes` object passing in coordinates, date, and calculation parameters.
```java
PrayerTimes prayerTimes = new PrayerTimes(coordinates, date, params);
```

### Initialization parameters
#### Coordinates
Create a `Coordinates` object with the latitude and longitude for the location you want prayer times for.
```java
Coordinates coordinates = new Coordinates(35.78056, -78.6389);
```

#### Date
The date parameter passed in should be an instance of the `DateComponents` object. The year, month, and day values need to be populated. All other values will be ignored. The year, month and day values should be for the  local date that you want prayer times for. These date values are expected to be for the Gregorian calendar. There's also a convenience method for converting a `java.util.Date` to `DateComponents`.
```java
DateComponents date = new DateComponents(2015, 11, 1);
DateComponents date = DateComponents.from(new Date());
```

#### Calculation parameters
The rest of the needed information is contained within the `CalculationParameters` class. Instead of manually initializing this class, it is recommended to use one of the pre-populated instances in the `CalculationMethod` class. You can then further customize the calculation parameters if needed.
```java
CalculationParameters params =
     CalculationMethod.MUSLIM_WORLD_LEAGUE.getParameters();
params.madhab = Madhab.HANAFI;
params.adjustments.fajr = 2;
```

| Parameter | Description |
| --------- | ----------- |
| `method`    | CalculationMethod name |
| `fajrAngle` | Angle of the sun used to calculate Fajr |
| `ishaAngle` | Angle of the sun used to calculate Isha |
| `ishaInterval` | Minutes after Maghrib (if set, the time for Isha will be Maghrib plus ishaInterval) |
| `madhab` | Value from the Madhab object, used to calculate Asr |
| `highLatitudeRule` | Value from the HighLatitudeRule object, used to set a minimum time for Fajr and a max time for Isha |
| `adjustments` | PrayerAdjustments object with custom prayer time adjustments in minutes for each prayer time |

**CalculationMethod**

| Value | Description |
| ----- | ----------- |
| `MUSLIM_WORLD_LEAGUE` | Muslim World League. Fajr angle: 18, Isha angle: 17 |
| `EGYPTIAN` | Egyptian General Authority of Survey. Fajr angle: 19.5, Isha angle: 17.5 |
| `KARACHI` | University of Islamic Sciences, Karachi. Fajr angle: 18, Isha angle: 18 |
| `UMM_AL_QURA` | Umm al-Qura University, Makkah. Fajr angle: 18.5, Isha interval: 90. *Note: you should add a +30 minute custom adjustment for Isha during Ramadan.* |
| `DUBAI` | Method used in UAE. Fajr and Isha angles of 18.2 degrees. |
| `QATAR` | Modified version of Umm al-Qura used in Qatar. Fajr angle: 18, Isha interval: 90. |
| `KUWAIT` | Method used by the country of Kuwait. Fajr angle: 18, Isha angle: 17.5 |
| `MOONSIGHTING_COMMITTEE` | Moonsighting Committee. Fajr angle: 18, Isha angle: 18. Also uses seasonal adjustment values. |
| `SINGAPORE` | Method used by Singapore. Fajr angle: 20, Isha angle: 18. |
| `NORTH_AMERICA` | Referred to as the ISNA method. This method is included for completeness but is not recommended. Fajr angle: 15, Isha angle: 15 |
| `OTHER` | Fajr angle: 0, Isha angle: 0. This is the default value for `method` when initializing a `CalculationParameters` object. |

**Madhab**

| Value | Description |
| ----- | ----------- |
| `SHAFI` | Earlier Asr time |
| `HANAFI` | Later Asr time |

**HighLatitudeRule**

| Value | Description |
| ----- | ----------- |
| `MIDDLE_OF_THE_NIGHT` | Fajr will never be earlier than the middle of the night and Isha will never be later than the middle of the night |
| `SEVENTH_OF_THE_NIGHT` | Fajr will never be earlier than the beginning of the last seventh of the night and Isha will never be later than the end of the first seventh of the night |
| `TWILIGHT_ANGLE` | Similar to `SEVENTH_OF_THE_NIGHT`, but instead of 1/7, the fraction of the night used is fajrAngle/60 and ishaAngle/60 |


### Prayer Times
Once the `PrayerTimes` object has been initialized it will contain values for all five prayer times and the time for sunrise. The prayer times will be  Date object instances initialized with UTC values. To display these times for the local timezone, a formatting and timezone conversion formatter should be used, for example `java.text.SimpleDateFormat`.

```java
SimpleDateFormat formatter = new SimpleDateFormat("hh:mm a");
formatter.setTimeZone(TimeZone.getTimeZone("America/New_York"));
formatter.format(prayerTimes.fajr);
```

## Full Example
```java
final Coordinates coordinates = new Coordinates(35.78056, -78.6389);
final DateComponents dateComponents = DateComponents.from(new Date());
final CalculationParameters parameters = CalculationMethod.MUSLIM_WORLD_LEAGUE.getParameters();

SimpleDateFormat formatter = new SimpleDateFormat("hh:mm a");
formatter.setTimeZone(TimeZone.getTimeZone("America/New_York"));

PrayerTimes prayerTimes = new PrayerTimes(coordinates, dateComponents, parameters);
System.out.println("Fajr: " + formatter.format(prayerTimes.fajr));
System.out.println("Sunrise: " + formatter.format(prayerTimes.sunrise));
System.out.println("Dhuhr: " + formatter.format(prayerTimes.dhuhr));
System.out.println("Asr: " + formatter.format(prayerTimes.asr));
System.out.println("Maghrib: " + formatter.format(prayerTimes.maghrib));
System.out.println("Isha: " + formatter.format(prayerTimes.isha));
```
