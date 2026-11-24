# Ex.No:4 Develop a simple application to display the avaliable sensor in android mobile devices using Sensor Manager in android studio.


## AIM:

To develop a sensor application to use the sensor manager class to identify and get the list of available sensors on a device. in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Giraffe)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as Sensor and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Display avaliable sensor in android mobile devices.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to print the avaliable sensor in android mobile devices”.
Developed by: Nithya shree B
Registeration Number : 212223220071
*/
```
### MainActivity.java
```java
package com.example.audioplayer;

import android.media.MediaPlayer;
import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import android.widget.Button;
import android.widget.SeekBar;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowCompat;
import androidx.core.view.WindowInsetsCompat;

import java.util.Locale;

public class MainActivity extends AppCompatActivity {

    private MediaPlayer mediaPlayer;
    private SeekBar seekBar;
    private TextView tvCurrentTime, tvTotalTime;
    private Button btnPlayPause, btnStop, btnRewind, btnForward;

    private final Handler handler = new Handler(Looper.getMainLooper());
    private Runnable updateSeekBarRunnable;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        WindowCompat.enableEdgeToEdge(getWindow());
        setContentView(R.layout.activity_main);

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

        // Initialize UI components
        seekBar = findViewById(R.id.seekBar);
        tvCurrentTime = findViewById(R.id.tvCurrentTime);
        tvTotalTime = findViewById(R.id.tvTotalTime);
        btnPlayPause = findViewById(R.id.btnPlayPause);
        btnStop = findViewById(R.id.btnStop);
        btnRewind = findViewById(R.id.btnRewind);
        btnForward = findViewById(R.id.btnForward);

        // Initialize MediaPlayer with res/raw/land_down_under.mp3
        mediaPlayer = MediaPlayer.create(this, R.raw.land_down_under);

        if (mediaPlayer != null) {
            int duration = mediaPlayer.getDuration();
            seekBar.setMax(duration);
            tvTotalTime.setText(formatTime(duration));

            // SeekBar update runnable
            updateSeekBarRunnable = new Runnable() {
                @Override
                public void run() {
                    if (mediaPlayer != null && mediaPlayer.isPlaying()) {
                        int currentPosition = mediaPlayer.getCurrentPosition();
                        seekBar.setProgress(currentPosition);
                        tvCurrentTime.setText(formatTime(currentPosition));
                        handler.postDelayed(this, 500);
                    }
                }
            };

            // Play / Pause Button Listener
            btnPlayPause.setOnClickListener(v -> {
                if (mediaPlayer.isPlaying()) {
                    mediaPlayer.pause();
                    btnPlayPause.setText("Play");
                    handler.removeCallbacks(updateSeekBarRunnable);
                } else {
                    mediaPlayer.start();
                    btnPlayPause.setText("Pause");
                    handler.post(updateSeekBarRunnable);
                }
            });

            // Stop Button Listener
            btnStop.setOnClickListener(v -> {
                if (mediaPlayer.isPlaying() || mediaPlayer.getCurrentPosition() > 0) {
                    mediaPlayer.pause();
                    mediaPlayer.seekTo(0);
                    seekBar.setProgress(0);
                    tvCurrentTime.setText(formatTime(0));
                    btnPlayPause.setText("Play");
                    handler.removeCallbacks(updateSeekBarRunnable);
                }
            });

            // Rewind 10 Seconds
            btnRewind.setOnClickListener(v -> {
                int newPos = Math.max(0, mediaPlayer.getCurrentPosition() - 10000);
                mediaPlayer.seekTo(newPos);
                seekBar.setProgress(newPos);
                tvCurrentTime.setText(formatTime(newPos));
            });

            // Fast Forward 10 Seconds
            btnForward.setOnClickListener(v -> {
                int newPos = Math.min(mediaPlayer.getDuration(), mediaPlayer.getCurrentPosition() + 10000);
                mediaPlayer.seekTo(newPos);
                seekBar.setProgress(newPos);
                tvCurrentTime.setText(formatTime(newPos));
            });

            // User seeking using SeekBar
            seekBar.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
                @Override
                public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                    if (fromUser && mediaPlayer != null) {
                        mediaPlayer.seekTo(progress);
                        tvCurrentTime.setText(formatTime(progress));
                    }
                }

                @Override
                public void onStartTrackingTouch(SeekBar seekBar) {}

                @Override
                public void onStopTrackingTouch(SeekBar seekBar) {}
            });

            // Completion listener
            mediaPlayer.setOnCompletionListener(mp -> {
                btnPlayPause.setText("Play");
                seekBar.setProgress(0);
                tvCurrentTime.setText(formatTime(0));
                handler.removeCallbacks(updateSeekBarRunnable);
            });
        }
    }

    private String formatTime(int millis) {
        int seconds = (millis / 1000) % 60;
        int minutes = (millis / (1000 * 60)) % 60;
        return String.format(Locale.getDefault(), "%02d:%02d", minutes, seconds);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (mediaPlayer != null) {
            if (mediaPlayer.isPlaying()) {
                mediaPlayer.stop();
            }
            mediaPlayer.release();
            mediaPlayer = null;
        }
        handler.removeCallbacks(updateSeekBarRunnable);
    }
}
```
### activity_main.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center_horizontal"
    android:padding="24dp"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tvHeader"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Audio Player"
        android:textSize="24sp"
        android:textStyle="bold"
        android:paddingBottom="24dp" />

    <!-- Cover Art Placeholder -->
    <LinearLayout
        android:layout_width="160dp"
        android:layout_height="160dp"
        android:background="#E0E0E0"
        android:gravity="center"
        android:layout_marginBottom="24dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="🎵"
            android:textSize="64sp" />
    </LinearLayout>

    <!-- Song Info -->
    <TextView
        android:id="@+id/tvSongTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Land Down Under"
        android:textSize="20sp"
        android:textStyle="bold"
        android:layout_marginBottom="4dp" />

    <TextView
        android:id="@+id/tvArtist"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Men at Work"
        android:textSize="14sp"
        android:textColor="#666666"
        android:layout_marginBottom="32dp" />

    <!-- Seekbar with time labels -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center_vertical">

        <TextView
            android:id="@+id/tvCurrentTime"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="00:00"
            android:textSize="12sp" />

        <SeekBar
            android:id="@+id/seekBar"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:paddingStart="8dp"
            android:paddingEnd="8dp" />

        <TextView
            android:id="@+id/tvTotalTime"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="00:00"
            android:textSize="12sp" />
    </LinearLayout>

    <!-- Playback Controls -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center"
        android:layout_marginTop="24dp">

        <Button
            android:id="@+id/btnRewind"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="-10s"
            android:layout_marginEnd="8dp" />

        <Button
            android:id="@+id/btnPlayPause"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Play"
            android:layout_marginEnd="8dp" />

        <Button
            android:id="@+id/btnStop"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Stop"
            android:layout_marginEnd="8dp" />

        <Button
            android:id="@+id/btnForward"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="+10s" />

    </LinearLayout>

</LinearLayout>
```
## OUTPUT
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/82df6ed0-4dff-4061-a971-7bcc94085308" />



## RESULT
Thus a Simple Android Application to display the avaliable sensor in android mobile devices using Sensor Manager in Android Studio is developed and executed successfully.
