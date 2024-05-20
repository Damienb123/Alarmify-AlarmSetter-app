# Alarmify-AlarmSetter-app

# Table of Contents

1. [Overview](#Overview)
2. [Purpose and Functionality](#Purpose-and-Functionality)
    - AlarmReceiver.java
    - MainActivity.java
    - activity_main.xml
    - themes.xml
    - colors.xml
3. [Setup Instructions](#Setup-Instructions)
4. [Usage Instructions](#Usage-Instructions)
5. [Image Visual](#Image-Visual)
6. [Video Walkthrough](#Video-Walkthrough)

## Overview

The Alarmify application is an Android app that allows users to set alarms using a **TimePicker** widget. When the alarm goes off, it vibrates the phone and plays the default alarm ringtone. The alarm can be toggled on and off using a **ToggleButton**.


## Purpose and Functionality

### AlarmReciever.java
This file defines a broadcast receiver that vibrates the phone and plays the default alarm ringtone when the alarm goes off.
```
package com.alarmsetter;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.media.Ringtone;
import android.media.RingtoneManager;
import android.net.Uri;
import android.os.Build;
import android.os.Vibrator;
import android.widget.Toast;

import androidx.annotation.RequiresApi;

public class AlarmReceiver extends BroadcastReceiver {
    @RequiresApi(api = Build.VERSION_CODES.Q)
    @Override
    public void onReceive(Context context, Intent intent) {
        // Use vibrator first
        Vibrator vibrator = (Vibrator) context.getSystemService(Context.VIBRATOR_SERVICE);
        vibrator.vibrate(4000);

        Toast.makeText(context, "Alarm! Wake up! Wake up!", Toast.LENGTH_LONG).show();
        Uri alarmUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_ALARM);
        if (alarmUri == null) {
            alarmUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
        }

        // Setting default ringtone
        Ringtone ringtone = RingtoneManager.getRingtone(context, alarmUri);

        // Play ringtone
        ringtone.play();
    }
}
```

### MainActivity.java
This file defines the main activity where the user can set the alarm time and toggle the alarm on or off.
```
package com.alarmsetter;

import android.app.AlarmManager;
import android.app.PendingIntent;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.TimePicker;
import android.widget.Toast;
import android.widget.ToggleButton;

import androidx.appcompat.app.AppCompatActivity;

import java.util.Calendar;

public class MainActivity extends AppCompatActivity {
    TimePicker alarmTimePicker;
    PendingIntent pendingIntent;
    AlarmManager alarmManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        alarmTimePicker = findViewById(R.id.timePicker);
        alarmManager = (AlarmManager) getSystemService(ALARM_SERVICE);
    }

    // OnToggleClicked() method is implemented to handle the alarm functionality
    public void OnToggleClicked(View view) {
        long time;
        if (((ToggleButton) view).isChecked()) {
            Toast.makeText(MainActivity.this, "ALARM ON", Toast.LENGTH_SHORT).show();
            Calendar calendar = Calendar.getInstance();

            // Get current time in hour and minute
            calendar.set(Calendar.HOUR_OF_DAY, alarmTimePicker.getCurrentHour());
            calendar.set(Calendar.MINUTE, alarmTimePicker.getCurrentMinute());

            // Using intent to call AlarmReceiver class which inherits BroadcastReceiver
            Intent intent = new Intent(this, AlarmReceiver.class);

            // Call broadcast using pendingIntent
            pendingIntent = PendingIntent.getBroadcast(this, 0, intent, PendingIntent.FLAG_IMMUTABLE);

            time = (calendar.getTimeInMillis() - (calendar.getTimeInMillis() % 60000));
            if (System.currentTimeMillis() > time) {
                // Setting time as AM and PM
                if (calendar.get(Calendar.AM_PM) == Calendar.AM)
                    time = time + (1000 * 60 * 60 * 12);
                else
                    time = time + (1000 * 60 * 60 * 24);
            }
            // Alarm rings continuously until toggle button is turned off
            alarmManager.setRepeating(AlarmManager.RTC_WAKEUP, time, 10000, pendingIntent);
        } else {
            alarmManager.cancel(pendingIntent);
            Toast.makeText(MainActivity.this, "ALARM OFF", Toast.LENGTH_SHORT).show();
        }
    }
}
```

### activity_main.xml
This file defines the layout for the main activity, including a **TimePicker** for setting the alarm time and a **ToggleButton** for turning the alarm on or off.
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TimePicker
        android:id="@+id/timePicker"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center" />

    <ToggleButton
        android:id="@+id/toggleButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_margin="20dp"
        android:checked="false"
        android:onClick="OnToggleClicked" />
</LinearLayout>
```
### themes.xml
This file defines the theme for the application.
```
<resources>
    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>
</resources>
```

### colors.xml
This file defines the colors used in the application.
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="colorPrimary">#3F51B5</color>
    <color name="colorPrimaryDark">#303F9F</color>
    <color name="colorAccent">#9D0F9B</color>
</resources>
```

## Setup Instructions

1. Clone or download the project repository.
2. Open the project in Android Studio.
3. Build and run the project on an Android device or emulator.

## Usage Instructions

1. Open the Alarmify application.
2. Set the desired alarm time using the **TimePicker**.
3. Toggle the alarm on by pressing the **ToggleButton**.
4. The alarm will vibrate the phone and play the default alarm ringtone when it goes off.
5. Toggle the alarm off by pressing the **ToggleButton** again.

## Image Visual
<img src="Main Screen.png" width=200> |
## Video Walkthrough
